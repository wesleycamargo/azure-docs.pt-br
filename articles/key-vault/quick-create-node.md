---
title: 'Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do Node | Microsoft Docs'
description: Neste início rápido, você define e recupera um segredo do Azure Key Vault usando um aplicativo Web do Node
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.workload: identity
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 1e234b599325da0626c83a57d86ff977b88b5577
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991267"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Início rápido: Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web Node 

Este início rápido mostra como armazenar um segredo no Azure Key Vault e como recuperá-lo usando um aplicativo Web. Usar o Key Vault ajuda a proteger as informações. Para ver o valor do segredo, será necessário executar esse início rápido no Azure. O início rápido usa Node.js e identidades gerenciadas para recursos do Azure. Você aprenderá como:

* Crie um cofre da chave.
* Armazenar um segredo no cofre de chaves.
* Recuperar um segredo do cofre de chaves.
* Criar um aplicativo Web do Azure.
* Habilitar uma [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) para o aplicativo Web.
* Conceder as permissões necessárias para o aplicativo Web ler dados do cofre de chaves.

Antes de prosseguir, familiarize-se com os [conceitos básicos do Key Vault](key-vault-whatis.md#basic-concepts).

> [!NOTE]
> O Key Vault é um repositório central para armazenar segredos de forma programática. Mas, para fazer isso, os aplicativos e usuários precisam autenticar primeiro o Key Vault, ou seja, introduzir um segredo. Seguindo as melhores práticas de segurança, esse primeiro segredo deve ser girado periodicamente. 
>
> Com as [identidades de serviço gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md), os aplicativos executados no Azure obtêm uma identidade gerenciada automaticamente pelo Azure. Isso ajuda a resolver o *problema de introdução do segredo* para que os usuários e aplicativos possam seguir as melhores práticas e não precisem se preocupar em girar o primeiro segredo.

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 ou posterior. Este Início Rápido requer que você execute a CLI do Azure localmente. Execute `az --version` para encontrar a versão. Caso precise instalar ou atualizar a CLI, confira [Instalar a CLI 2.0 do Azure](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Para fazer logon no Azure usando a CLI do Azure, digite:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos usando o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Selecione um nome para o grupo de recursos e preencha o espaço reservado.
O exemplo a seguir cria um grupo de recursos na região Leste dos EUA.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

O grupo de recursos que você acabou de criar é usado ao longo deste artigo.

## <a name="create-a-key-vault"></a>Criar um cofre de chave

Em seguida, você cria um cofre de chaves no grupo de recursos criado na etapa anterior. Embora este artigo use "ContosoKeyVault" como o nome, você precisa usar um nome exclusivo. Forneça as seguintes informações:

* Nome do cofre de chaves.
* Nome do grupo de recursos. O nome precisa ser uma cadeia de 3 a 24 caracteres e conter apenas 0-9, a-z, A-Z e um hífen (-).
* Localização: **Leste dos EUA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos adicionando um segredo para ajudar a ilustrar como isso funciona. Você pode estar armazenando uma cadeia de conexão SQL ou qualquer outra informação que precise manter com segurança, mas disponibilizar para o aplicativo. Neste tutorial, a senha será chamada **AppSecret** e armazenará o valor de **MySecret** nela.

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
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Instalar dependências

Execute os comandos a seguir para instalar as dependências:

```
cd key-vault-node-quickstart
npm install
```

Este projeto usa dois módulos de nó: [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) e [azure-keyvault](https://www.npmjs.com/package/azure-keyvault).

## <a name="publish-the-web-app-to-azure"></a>Publicar aplicativo Web no Azure

Criar um plano do [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/). Você pode armazenar vários aplicativos Web nesse plano.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
A seguir, crie um aplicativo Web. No exemplo a seguir, substitua `<app_name>` por um nome do aplicativo exclusivo globalmente (os caracteres válidos são de a a z, 0 a 9 e -). O tempo de execução é definido como NODE|6.9. Para ver todos os tempos de execução com suporte, execute `az webapp list-runtimes`.

    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```
Quando o aplicativo Web for criado, a CLI do Azure mostrará um resultado semelhante ao seguinte exemplo:

    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
Navegue até o aplicativo Web criado recentemente e veja o aplicativo Web funcionando. Substitua `<app_name>` por um nome de aplicativo exclusivo.

    ```
    http://<app name>.azurewebsites.net
    ```
O comando anterior também cria um aplicativo habilitado para Git que permite a implantação do repositório do git local no Azure. O repositório do git local está configurado com a URL: https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git.

Depois de concluir o comando anterior, você poderá adicionar um Azure remoto a seu repositório Git local. Substitua `<url>` com a URL do repositório Git.

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>Habilitar uma identidade gerenciada para o aplicativo Web

O Azure Key Vault fornece uma maneira de armazenar com segurança as credenciais e outras chaves e segredos, mas seu código precisa autenticar para o Key Vault para recuperá-los. [Visão geral das identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) facilita a solução desse problema, fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter que todas as credenciais no seu código.

Execute o comando assign-identity para criar a identidade para esse aplicativo:

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

Esse comando é o equivalente a acessar o portal e mudar a configuração **Identidade/Atribuída pelo sistema** para **Habilitada** nas propriedades do aplicativo Web.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Atribuir permissões ao aplicativo para ler segredos do Key Vault

Registre a saída do comando anterior. Ela deve estar no formato:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Em seguida, execute o comando usando o nome do seu cofre de chaves e o valor de **principalId**:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Implante o aplicativo Node no Azure e recupere o valor do segredo

Execute o seguinte comando para implantar o aplicativo no Azure:

```
git push azure master
```

Depois de fazer isso, quando você navegar por https://<app_name>.azurewebsites.net, verá o valor do segredo. Assegure-se de substituir o nome <YourKeyVaultName> pelo nome do cofre.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [SDK do Azure para o Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
