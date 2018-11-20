---
title: Tutorial – Como usar o Azure Key Vault com a máquina virtual do Azure com Linux no .NET | Microsoft Docs
description: 'Tutorial: Configurar um aplicativo ASP.NET Core para ler um segredo do Key Vault'
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: d5596343a293d333dac9ca7e31a9fbc3363f3fd9
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688211"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-linux-virtual-machine-in-net"></a>Tutorial: Como usar o Azure Key Vault com a máquina virtual do Azure com Linux no .NET

O Azure Key Vault ajuda a proteger segredos, como chaves de API, cadeias de conexão de banco de dados necessárias para acessar seus aplicativos, serviços e recursos de TI.

Neste tutorial, siga as etapas necessárias para fazer um aplicativo Web do Azure ler informações do Azure Key Vault usando identidades gerenciadas de recursos do Azure. Este tutorial se baseia em [aplicativos Web do Azure](../app-service/app-service-web-overview.md). A seguir, você aprenderá a:

> [!div class="checklist"]
> * Criar um cofre da chave.
> * Armazenar um segredo no cofre de chaves.
> * Recuperar um segredo do cofre de chaves.
> * Criar uma Máquina Virtual do Azure.
> * Habilitar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para a Máquina Virtual.
> * Conceder as permissões necessárias para o aplicativo de console ler dados do cofre de chaves.
> * Recuperar segredos do Key Vault

Antes de continuarmos, leia os [conceitos básicos](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Pré-requisitos
* Todas as plataformas:
  * Git ([download](https://git-scm.com/downloads)).
  * Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
  * [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versão 2.0.4 ou posterior. Disponível para Windows, Mac e Linux.

Este tutorial faz uso da Identidade de Serviço Gerenciada

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>O que é a Identidade de Serviço Gerenciada e como ela funciona?
Antes de continuarmos, vamos entender o que é a MSI. O Azure Key Vault pode armazenar credenciais com segurança para que elas não precisem estar em seu código, mas, para recuperá-las, você precisa fazer a autenticação no Azure Key Vault. Para fazer a autenticação no Key Vault, você precisa de uma credencial! Um problema clássico de inicialização. Com a mágica do Azure e do Azure AD, a MSI fornece uma "identidade de inicialização" que simplifica tudo.

Veja como ela funciona! Quando você habilita a MSI para um serviço do Azure, como Máquinas Virtuais, Serviço de Aplicativo ou Functions, o Azure cria uma [Entidade de Serviço](key-vault-whatis.md#basic-concepts) para a instância do serviço no Azure Active Directory e injeta as credenciais para a Entidade de Serviço na instância do serviço. 

![MSI](media/MSI.png)

Em seguida, seu código chama um serviço de metadados local disponível no recurso do Azure para obter um token de acesso.
Seu código usa o token de acesso obtido do MSI_ENDPOINT local para fazer a autenticação em um serviço Azure Key Vault. 

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Para fazer logon no Azure usando a CLI do Azure, digite:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Selecione um nome para o grupo de recursos e preencha o espaço reservado.
O exemplo a seguir cria um grupo de recursos no local Oeste dos EUA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

O grupo de recursos que você acabou de criar é usado ao longo deste artigo.

## <a name="create-a-key-vault"></a>Criar um cofre de chave

Em seguida, você cria um cofre de chaves no grupo de recursos criado na etapa anterior. Forneça as seguintes informações:

* Nome do cofre de chaves: o nome deve ser uma cadeia com 3 a 24 caracteres e deve conter apenas (0 a 9, a a z, A a Z e -).
* Nome do grupo de recursos.
* Local: **Oeste dos EUA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos adicionando um segredo para ajudar a ilustrar como isso funciona. Você pode estar armazenando uma cadeia de conexão SQL ou qualquer outra informação que precise manter com segurança, mas disponibilizar para o aplicativo.

Digite os comandos a seguir para criar um segredo no cofre de chaves chamado **AppSecret**. Esse segredo armazenará o valor **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Criar uma Máquina Virtual
Siga os links a seguir para criar uma Máquina Virtual do Windows

[CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli) 

[Powershell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)

[Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-identity-to-virtual-machine"></a>Atribuir uma identidade à Máquina Virtual
Nesta etapa, estamos criando um sistema de identidade é atribuído à máquina virtual executando o comando a seguir na CLI do Azure

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observe o systemAssignedIdentity mostrado abaixo. A saída do comando acima seria 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-vm-identity-permission-to-key-vault"></a>Conceder permissão de Identidade de VM ao Key Vault
Agora, podemos dar à permissão criada acima a permissão de identidade para o Key Vault executando o seguinte comando

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="login-to-the-virtual-machine"></a>Fazer logon na Máquina Virtual

Você pode seguir este [tutorial](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)

## <a name="install-net-core"></a>Instalar o .NET Core

Você pode instalar o .NET Core seguindo as etapas deste [artigo](https://www.microsoft.com/net/download)

## <a name="create-and-run-sample-dot-net-app"></a>Criar e executar o aplicativo de exemplo em Dot Net

Abra um prompt de comando

Após executar os comandos abaixo, você deverá ver "Olá, Mundo" impresso no console

```
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-console-app"></a>Editar aplicativo de console
Abra o arquivo Program.cs e adicione esses pacotes
```
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```
Depois, altere o arquivo de classe para conter o código abaixo. É um processo de duas etapas. 
1. Buscar um token no ponto de extremidade do MSI local na VM que, por sua vez, busca um token no Azure Active Directory
2. Passe o token para o Key Vault e busque o seu segredo 

```
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
            var token = GetToken();

            // Step 2: Fetch the secret value from Key Vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }

        static string FetchSecretValueFromKeyVault(string token)
        {
            WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];
                Console.WriteLine(ojObject.Value);                
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```


O código acima mostra como realizar operações com o Azure Key Vault em uma Máquina Virtual do Azure com Linux. 



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [API REST do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
