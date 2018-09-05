---
title: 'Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do Node | Microsoft Docs'
description: 'Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do Node'
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: a9ae1fb3243c31eb92231320c5ced93d80301a0d
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42917427"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-net-web-app"></a>Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET

Neste tutorial, siga as etapas necessárias para obter um aplicativo Web do Azure para ler informações do Azure Key Vault usando identidades de serviço gerenciadas. Você aprenderá como:

> [!div class="checklist"]
> * Crie um cofre da chave.
> * Armazenar um segredo no cofre de chaves.
> * Recuperar um segredo do cofre de chaves.
> * Criar um aplicativo Web do Azure.
> * [Habilitar identidades de serviço gerenciadas](../active-directory/managed-service-identity/overview.md).
> * Conceder as permissões necessárias para o aplicativo Web ler dados do cofre de chaves.

Antes de continuarmos, leia os [conceitos básicos](key-vault-whatis.md#basic-concepts).

>[!NOTE]
>O Key Vault é um repositório central para armazenar segredos de forma programática. Mas, para fazer isso, os aplicativos e usuários precisam autenticar primeiro o Key Vault, ou seja, introduzir um segredo. Para seguir as melhores práticas de segurança, esse primeiro segredo deve ser girado periodicamente. 
>
>Com a [Identidade de Serviço Gerenciada](../active-directory/managed-service-identity/overview.md), os aplicativos executados no Azure recebem uma identidade gerenciada automaticamente pelo Azure. Isso ajuda a resolver o *problema de introdução do segredo* para que os usuários e aplicativos possam seguir as melhores práticas e não precisem se preocupar em girar o primeiro segredo.

## <a name="prerequisites"></a>Pré-requisitos

* No Windows:
  * [Visual Studio 2017 versão 15.7.3 ou posterior](https://www.microsoft.com/net/download/windows) com as seguintes cargas de trabalho:
    * Desenvolvimento Web e ASP.NET
    * Desenvolvimento multiplataforma com o .NET Core
  * [SDK do .NET Core 2.1 ou posterior](https://www.microsoft.com/net/download/windows)

* No Mac:
  * Confira as [Novidades do Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

* Todas as plataformas:
  * Git ([download](https://git-scm.com/downloads)).
  * Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
  * [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versão 2.0.4 ou posterior. Disponível para Windows, Mac e Linux.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Para fazer logon no Azure usando a CLI do Azure, digite:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Selecione um nome para o grupo de recursos e preencha o espaço reservado.
O exemplo a seguir cria um grupo de recursos no local Leste dos EUA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

O grupo de recursos que você acabou de criar é usado ao longo deste artigo.

## <a name="create-a-key-vault"></a>Criar um cofre de chave

Em seguida, você cria um cofre de chaves no grupo de recursos criado na etapa anterior. Forneça as seguintes informações:

* Nome do cofre de chaves: o nome deve ser uma cadeia com 3 a 24 caracteres e deve conter apenas (0 a 9, a a z, A a Z e -).
* Nome do grupo de recursos.
* Local: **Leste dos EUA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
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

## <a name="clone-the-repo"></a>Clonar o repositório

Clone o repositório para fazer uma cópia local em que você possa editar o código-fonte. Execute o comando a seguir:

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Abrir e editar a solução

Edite o arquivo program.cs para executar o exemplo com o nome específico de seu cofre de chaves:

1. Navegue até a pasta key-vault-dotnet-core-quickstart.
2. Abra o arquivo key-vault-dotnet-core-quickstart.sln no Visual Studio 2017.
3. Abra o arquivo Program.cs e atualize o espaço reservado *YourKeyVaultName* com o nome do cofre de chaves criado anteriormente.

A solução usa as bibliotecas NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

## <a name="run-the-app"></a>Execute o aplicativo

No menu principal do Visual Studio 2017, escolha **Depurar** > **Iniciar** sem depuração. Quando o navegador aparecer, navegue até a página **Sobre**. O valor para o **AppSecret** é exibido.

## <a name="publish-the-web-application-to-azure"></a>Publicar o aplicativo Web no Azure

Publique esse aplicativo no Azure para vê-lo funcionando como um aplicativo Web e também verificar se é possível buscar o valor do segredo:

1. No Visual Studio, selecione o projeto **key-vault-dotnet-core-quickstart**.
2. Selecione **Publicar** > **Iniciar**.
3. Crie um novo **Serviço de Aplicativo** e selecione **Publicar**.
4. Altere o nome do aplicativo para **keyvaultdotnetcorequickstart**.
5. Selecione **Criar**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-managed-service-identities"></a>Habilitar identidades de serviço gerenciadas

O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais e outras chaves e segredos, mas seu código precisa autenticar no Azure Key Vault para recuperá-los. A Identidade de Serviço Gerenciada torna isso mais fácil fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

1. Retorne para a CLI do Azure.
2. Execute o comando assign-identity para criar a identidade para esse aplicativo:

   ```azurecli
   az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
   ```

>[!NOTE]
>O comando neste procedimento é o equivalente de ir para o portal e alternar **Identidade de serviço gerenciada** para **Ativo** nas propriedades do aplicativo Web.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Atribuir permissões ao aplicativo para ler segredos do Key Vault

Anote a saída quando você publicar o aplicativo no Azure. Ela deve ter o seguinte formato:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Em seguida, execute o comando usando o nome do seu cofre de chaves e o valor de **PrincipalId** copiado acima:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get

```

Agora, ao executar o aplicativo, você verá o valor do segredo recuperado.

## <a name="next-steps"></a>Próximas etapas

* [Página inicial do Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Documentação do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [SDK do Azure para .NET](https://github.com/Azure/azure-sdk-for-net)
* [Referência de API REST do Azure](https://docs.microsoft.com/rest/api/keyvault/)
