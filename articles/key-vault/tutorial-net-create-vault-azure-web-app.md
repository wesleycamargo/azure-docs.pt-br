---
title: Tutorial – Como usar o Azure Key Vault com o aplicativo Web do Azure no .NET | Microsoft Docs
description: 'Tutorial: Configurar um aplicativo ASP.NET Core para ler um segredo do Key Vault'
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 9cc22e158a9473b7b60f7e8bcb57174abc1fb8cc
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218545"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-web-app-in-net"></a>Tutorial: Como usar o Azure Key Vault com o aplicativo Web do Azure no .NET

O Azure Key Vault ajuda a proteger segredos, como chaves de API, cadeias de conexão de banco de dados necessárias para acessar seus aplicativos, serviços e recursos de TI.

Neste tutorial, siga as etapas necessárias para fazer um aplicativo Web do Azure ler informações do Azure Key Vault usando identidades gerenciadas de recursos do Azure. Este tutorial se baseia em [aplicativos Web do Azure](../app-service/app-service-web-overview.md). A seguir, você aprenderá a:

> [!div class="checklist"]
> * Criar um cofre de chaves.
> * Armazenar um segredo no cofre de chaves.
> * Recuperar um segredo do cofre de chaves.
> * Criar um aplicativo Web do Azure.
> * Habilitar uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) para o aplicativo Web.
> * Conceder as permissões necessárias para o aplicativo Web ler dados do cofre de chaves.
> * Executar o aplicativo Web no Azure

Antes de continuarmos, leia os [conceitos básicos](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Pré-requisitos

* No Windows:
  * [SDK do .NET Core 2.1 ou posterior](https://www.microsoft.com/net/download/windows)

* No Mac:
  * Confira as [Novidades do Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

* Todas as plataformas:
  * Git ([download](https://git-scm.com/downloads)).
  * Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
  * [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versão 2.0.4 ou posterior. Disponível para Windows, Mac e Linux.
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>O que é a Identidade de Serviço Gerenciada e como ela funciona?
 Antes de continuarmos, vamos entender o que é a MSI. O Azure Key Vault pode armazenar credenciais com segurança para que elas não precisem estar em seu código, mas, para recuperá-las, você precisa fazer a autenticação no Azure Key Vault. Para fazer a autenticação no Key Vault, você precisa de uma credencial! Um problema clássico de inicialização. Com a mágica do Azure e do Azure AD, a MSI fornece uma "identidade de inicialização" que simplifica tudo.

Veja como ela funciona! Quando você habilita a MSI para um serviço do Azure, como Máquinas Virtuais, Serviço de Aplicativo ou Functions, o Azure cria uma [Entidade de Serviço](key-vault-whatis.md#basic-concepts) para a instância do serviço no Azure Active Directory e injeta as credenciais para a Entidade de Serviço na instância do serviço. 

![MSI](media/MSI.png)

Em seguida, seu código chama um serviço de metadados local disponível no recurso do Azure para obter um token de acesso.
Seu código usa o token de acesso obtido do MSI_ENDPOINT local para fazer a autenticação em um serviço Azure Key Vault. 

Agora vamos começar o tutorial.

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

Para exibir o valor contido no segredo como texto sem formatação:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Esse comando mostra as informações secretas, incluindo o URI. Após concluir essas etapas, você deverá ter um URI para um segredo em um cofre de chaves. Anote essa informação. Ela será necessária em uma etapa posterior.

## <a name="create-a-net-core-web-app"></a>Criar um novo aplicativo Web do .NET Core

Siga este [tutorial](../app-service/app-service-web-get-started-dotnet.md) para criar um aplicativo Web do .NET Core e **publicá-lo** no Azure **OU** assista ao vídeo abaixo
> [!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Abrir e editar a solução

1. Navegar até Páginas > Arquivo About.cshtml.cs.
2. Instalar esses dois pacotes NuGet
    - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
    - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)
3. Importar o que está abaixo para o arquivo About.cshtml.cs

    ```
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
    ```
4. O código na classe AboutModel deve ter esta aparência
    ```
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The below 4 lines of code shows you how to use AppAuthentication library to fetch secrets from your Key Vault*/
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;             

                /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
                do
                {
                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                    retry = false;
                } 
                while(retry && (retries++ < 10));
            }
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
                if((int)keyVaultException.Response.StatusCode == 429)
                    retry = true;
            }            
        }

        // This method implements exponential backoff incase of 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```


## <a name="run-the-app"></a>Execute o aplicativo

No menu principal do Visual Studio 2017, escolha **Depurar** > **Iniciar** com/sem depuração. Quando o navegador aparecer, navegue até a página **Sobre**. O valor para o **AppSecret** é exibido.

## <a name="enable-a-managed-identity-for-the-web-app"></a>Habilitar uma identidade gerenciada para o aplicativo Web

O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais e outras chaves e segredos, mas seu código precisa autenticar para o Key Vault para recuperá-los. [Visão geral das identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) facilita a solução desse problema, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

1. Retorne para a CLI do Azure.
2. Execute o comando assign-identity para criar a identidade para esse aplicativo: 

   ```azurecli
   az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
   ```
   Tenha em mente que você precisa substituir <YourAppName> pelo nome do aplicativo publicado no Azure, ou seja, se o nome do aplicativo publicado era MyAwesomeapp.azurewebsites.net, substitua <YourAppName> por MyAwesomeapp
 
 A saída do comando acima tem esta aparência. Anote a PrincipalId quando você publicar o aplicativo no Azure. Ela deve ter o seguinte formato:
   ```
   {
     "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
     "type": "SystemAssigned"
   }
  ```
>[!NOTE]
>O comando neste procedimento é o equivalente a acessar o [portal](https://portal.azure.com) e mudar a configuração **Identidade/Atribuída pelo sistema** para **Ativada** nas propriedades do aplicativo Web.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Atribuir permissões ao aplicativo para ler segredos do Key Vault
        
Em seguida, execute o comando usando o nome do seu cofre de chaves e o valor de **PrincipalId** copiado acima:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

## <a name="publish-the-web-application-to-azure"></a>Publicar o aplicativo Web no Azure

Publique esse aplicativo no Azure mais uma vez para vê-lo funcionando como um aplicativo Web e também verificar se é possível buscar o valor do segredo.

1. No Visual Studio, selecione o projeto **key-vault-dotnet-core-quickstart**.
2. Selecione **Publicar** > **Iniciar**.
3. Selecione **Criar**.

No comando acima, você está dando permissões para a Identidade (MSI) do Serviço de Aplicativo para executar operações **get** e **list** no Key Vault. <br />
Agora, ao executar o aplicativo, você verá o valor do segredo recuperado. 

E isso é tudo, pessoal. Você agora criou com êxito um aplicativo Web no .NET que armazena e agrupa seus segredos do Key Vault.
