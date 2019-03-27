---
title: Tutorial – Usar o Azure Key Vault com uma máquina virtual do Windows no .NET | Microsoft Docs
description: Neste tutorial, você vai configurar um aplicativo do ASP.NET Core para ler um segredo no cofre de chaves.
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: c66a7d7af2a73e26878b92f34e0f42ce0b3ae7f2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437490"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Tutorial: Usar o Azure Key Vault com uma máquina virtual do Windows no .NET

O Azure Key Vault ajuda a proteger segredos como chaves de API, as cadeias de conexão de banco de dados que você precisa para acessar seus aplicativos, serviços e recursos de TI.

Neste tutorial, você aprenderá como fazer um aplicativo de console ler as informações no Azure Key Vault. Para isso, você usará identidades gerenciadas para recursos do Azure. 

Este tutorial mostra como:

> [!div class="checklist"]
> * Crie um grupos de recursos.
> * Crie um cofre da chave.
> * Adicionar um segredo ao cofre de chaves.
> * Recuperar um segredo do cofre de chaves.
> * Crie uma máquina virtual do Azure.
> * Habilitar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para a Máquina Virtual.
> * Atribuir permissões à identidade da VM.

Antes de começar, leia [Conceitos básicos do Key Vault](key-vault-whatis.md#basic-concepts). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Este tutorial requer que você execute a CLI do Azure localmente. Você deve ter a CLI do Azure versão 2.0.4 ou posterior instalada. Execute `az --version` para encontrar a versão. Caso precise instalar ou atualizar a CLI, confira [Instalar a CLI 2.0 do Azure](https://review.docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>Sobre a Identidade de Serviço Gerenciada

O Azure Key Vault armazena credenciais com segurança para que elas não sejam exibidas no seu código. No entanto, você precisa se autenticar no Azure Key Vault para recuperar as chaves. Para fazer a autenticação no Key Vault, você precisa de uma credencial. Esse é um dilema clássico de inicialização. A MSI (Identidade de Serviço Gerenciada) resolve esse problema fornecendo uma _identidade de inicialização_ que simplifica o processo.

Ao habilitar a MSI para um serviço do Azure como Máquinas Virtuais do Azure, Serviço de Aplicativo do Azure ou Azure Functions, o Azure cria uma [entidade de serviço](key-vault-whatis.md#basic-concepts). A MSI faz isso para a instância do serviço no Azure AD (Azure Active Directory) e injeta as credenciais da entidade de serviço nessa instância. 

![MSI](media/MSI.png)

Em seguida, para obter um token de acesso, o código chama um serviço de metadados local disponível no recurso do Azure. Para se autenticar em um serviço do Azure Key Vault, o código usa o token de acesso obtido do ponto de extremidade da MSI local. 

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Para fazer logon no Azure usando a CLI do Azure, digite:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos usando o comando [az group create](/cli/azure/group#az-group-create). 

Em seguida, selecione um nome para o grupo de recursos e preencha o espaço reservado. O exemplo a seguir cria um grupo de recursos no local Oeste dos EUA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Você usará o grupo de recursos recém criado neste tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chave

Para criar um cofre de chaves no grupo de recursos que você criou na etapa anterior, forneça as seguintes informações:

* Nome do cofre de chaves: uma cadeia de caracteres de 3 a 24 caracteres contendo somente números (0 – 9), letras (a – z, A – Z) e hifens (-)
* Nome do grupo de recursos
* Localização: **Oeste dos EUA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
Nesse ponto, sua conta do Azure é a única autorizada a executar operações nesse novo cofre de chaves.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos adicionando um segredo para ajudar a ilustrar como isso funciona. O segredo pode ser uma cadeia de conexão SQL ou qualquer outra informação que você precise manter em segurança e disponível para o aplicativo.

Para criar um segredo no cofre de chaves chamado **AppSecret**, insira o seguinte comando:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Esse segredo armazena o valor **MySecret**.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual
Você pode criar uma máquina virtual usando um dos seguintes métodos:

* [A CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [O portal do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade à VM
Nesta etapa, você criará uma identidade atribuída pelo sistema para a máquina virtual executando o seguinte comando na CLI do Azure:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Observe a identidade atribuída pelo sistema que é exibida no código a seguir. A saída do comando anterior seria: 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões à identidade da VM
Agora você pode atribuir as permissões de identidade criadas anteriormente para o cofre de chaves, executando o seguinte comando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Faça logon na máquina virtual

Para fazer logon na máquina virtual, siga as instruções em [Conectar e fazer logon em uma máquina virtual do Azure executando o Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="install-net-core"></a>Instalar o .NET Core

Para instalar o .NET Core, acesse a página [Downloads do .NET](https://www.microsoft.com/net/download).

## <a name="create-and-run-a-sample-net-app"></a>Criar e executar um aplicativo .NET de exemplo

Abra um prompt de comando.

Você pode exibir "Olá, Mundo" no console executando os comandos a seguir:

```
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app"></a>Editar o aplicativo de console

Abra o arquivo *Program.cs* e adicione esses pacotes:

```
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Edite o arquivo de classe para conter o código no processo de duas etapas a seguir:

1. Efetue fetch de um token do ponto de extremidade da MSI local na VM. Isso também efetua fetch de um token no Azure AD.
1. Passe o token para o cofre de chaves e, em seguida, efetue fetch do segredo. 

```
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
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
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
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
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

O código anterior mostra como realizar operações com o Azure Key Vault em uma máquina virtual do Windows.

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, exclua a máquina virtual e o cofre de chaves.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [API REST do Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
