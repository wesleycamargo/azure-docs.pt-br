---
title: 'Início Rápido do Azure: Configurar um aplicativo Web do Azure para ler um segredo do Key Vault | Microsoft Docs'
description: Início Rápido que mostra como configurar um aplicativo ASP.NET Core para ler um segredo do Key Vault
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 07a7bc5713f093e34a775aacab27094780ac6c7e
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247818"
---
# <a name="quickstart-set-and-read-a-secret-from-key-vault-in-a-net-web-app"></a>Início Rápido: Definir e ler um segredo do Key Vault em um aplicativo Web do .NET

Neste tutorial, você examinará as etapas necessárias para obter um aplicativo Web do Azure para ler informações do Key Vault usando identidades de serviço gerenciadas. Você aprenderá como:

> [!div class="checklist"]
> * Criar um Cofre de Chaves.
> * Armazenar um segredo no Cofre de Chaves.
> * Recupere um segredo do Key Vault.
> * Criar um aplicativo Web do Azure.
> * [Habilitar identidades de serviço gerenciadas](../active-directory/managed-service-identity/overview.md).
> * Conceda as permissões necessárias para o aplicativo Web ler dados do Key Vault.

Antes de continuarmos, leia os [conceitos básicos](key-vault-whatis.md#basic-concepts), especialmente sobre a [Identidade de Serviço Gerenciada](../active-directory/managed-service-identity/overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* No Windows:
  * [Visual Studio 2017 versão 15.7.3 ou posterior](https://www.microsoft.com/net/download/windows) com as seguintes cargas de trabalho:
    * Desenvolvimento Web e ASP.NET
    * Desenvolvimento multiplataforma com o .NET Core
  * [SDK do .NET Core 2.1 ou posterior](https://www.microsoft.com/net/download/windows)

* No Mac:
  * https://visualstudio.microsoft.com/vs/mac/

* Todas as plataformas:
  * Baixe o GIT [aqui](https://git-scm.com/downloads).
  * Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
  * [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) É necessário usar a CLI do Azure versão 2.0.4 ou posterior. Disponível para Windows, Mac e Linux

## <a name="login-to-azure"></a>Logon no Azure

   Para fazer logon no Azure usando a CLI, você pode digitar:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Selecione um nome para o grupo de recursos e preencha o espaço reservado.
O exemplo a seguir cria um grupo de recursos denominado *<YourResourceGroupName>* no local *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

O grupo de recursos que você acabou de criar é usado ao longo deste artigo.

## <a name="create-an-azure-key-vault"></a>Criar um Cofre de chaves do Azure

Em seguida, você cria um Cofre de Chaves no grupo de recursos criado na etapa anterior. Forneça as seguintes informações:

* Nome do cofre: **selecione um nome para o Key Vault aqui**. O nome do Cofre de Chaves deve ser uma sequência de 3 a 24 caracteres que contenha somente 0 a 9, a a z, A a Z, e -.
* Nome do grupo de recursos: **selecione um nome para o grupo de recursos aqui**.
* O local: **Leste dos EUA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Estamos adicionando um segredo para ajudar a ilustrar como isso funciona. Você pode estar armazenando uma cadeia de conexão SQL ou qualquer outra informação que precise manter com segurança, mas disponibilizar para o aplicativo. Neste tutorial, a senha será chamada **AppSecret** e armazenará o valor de **MySecret** nela.

Digite os comandos abaixo para criar um segredo no Cofre de Chaves chamado **AppSecret** que armazenará o valor **MySecret**:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Para exibir o valor contido no segredo como texto sem formatação:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Este comando mostra as informações secretas, incluindo o URI. Após concluir essas etapas, você deverá ter um URI para um segredo em um Azure Key Vault. Anote essa informação. Ela será necessária em uma etapa posterior.

## <a name="clone-the-repo"></a>Clonar o repositório

Clone o repositório a fim de fazer uma cópia local para que você possa editar a fonte executando o seguinte comando:

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Abrir e editar a solução

Edite o arquivo program.cs para executar o exemplo com o nome específico de seu cofre de chaves.

1. Navegue até a pasta key-vault-dotnet-core-quickstart
2. Abra o arquivo chave-cofre-dotnet-core-QuickStart no Visual Studio 2017
3. Abra o arquivo Program.cs e atualize o espaço reservado <YourKeyVaultName> com o nome do cofre de chaves criado anteriormente.

A solução usa as bibliotecas NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

## <a name="run-the-app"></a>Execute o aplicativo

No menu principal do Visual Studio 2017, escolha Depurar > Iniciar sem Depuração. Quando o navegador aparecer, navegue até a página Sobre. O valor para o AppSecret é exibido.

## <a name="publish-the-web-application-to-azure"></a>Publicar o aplicativo Web no Azure

Estamos publicando esse aplicativo do Azure para vê-lo ao vivo como um aplicativo Web e também verificar se conseguimos buscar o valor do segredo

1. No Visual Studio, selecione o projeto **key-vault-dotnet-core-quickstart**.
2. Selecione **Publicar**, em seguida, **Iniciar**.
3. Crie um novo **Serviço de Aplicativo**, selecione **Publicar**.
4. Altere o nome do aplicativo para "keyvaultdotnetcorequickstart"
5. Selecione **Criar**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-managed-service-identities-msi"></a>Habilitar MSI (Identidades de Serviço Gerenciadas)

O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais e outras chaves e segredos, mas seu código precisa autenticar no Azure Key Vault para recuperá-los. A MSI (Identidade de Serviço Gerenciada) facilita fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

1. Retornar para a CLI do Azure
2. Execute o comando assign-identity para criar a identidade para esse aplicativo:

```azurecli
az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
```

>[!NOTE]
>Esse comando é o equivalente de ir para o portal e alternar **Identidade de serviço gerenciada** para **On** nas propriedades do aplicativo Web.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Atribuir permissões ao aplicativo para ler segredos do Key Vault

Anote a saída quando você [publicar o aplicativo no Azure] []. Ela deve ter o seguinte formato:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Em seguida, execute o comando usando o nome do seu Key Vault e o valor do PrincipalId copiado acima:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get

```

## <a name="next-steps"></a>Próximas etapas

* [Página inicial do Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Documentação do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [SDK do Azure para .NET](https://github.com/Azure/azure-sdk-for-net)
* [Referência de API REST do Azure](https://docs.microsoft.com/rest/api/keyvault/)
