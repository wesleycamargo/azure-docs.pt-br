---
title: Início Rápido - Definir e recuperar um segredo do Azure Key Vault usando um Aplicativo Web do Node | Microsoft Docs
description: Início Rápido - Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do Node
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
ms.openlocfilehash: 860294ebc7fbadd3eeefc4298ec740ca7f704587
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714387"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-a-node-web-app"></a>Início Rápido - Definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web do .NET 

Este início rápido mostra como armazenar um segredo no Key Vault e como recuperá-lo usando um aplicativo Web. Para ver o valor do segredo, seria necessário executar isso no Azure. O início rápido usa Node.js e identidades gerenciadas para recursos do Azure.

> [!div class="checklist"]
> * Criar um Cofre de Chaves.
> * Armazenar um segredo no Cofre de Chaves.
> * Recupere um segredo do Key Vault.
> * Criar um aplicativo Web do Azure.
> * Habilitar uma [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) para o aplicativo Web.
> * Conceda as permissões necessárias para o aplicativo Web ler dados do Key Vault.

Antes de prosseguir, familiarize-se com o [conceitos básicos](key-vault-whatis.md#basic-concepts).

>[!NOTE]
Para entender o porquê do tutorial abaixo ser a melhor prática, precisamos entender alguns conceitos. O Key Vault é um repositório central para armazenar segredos de forma programática. Mas, para fazer isso, os aplicativos/usuários precisam autenticar primeiro o Key Vault, ou seja, apresentar um segredo. Para seguir as melhores práticas de segurança, esse primeiro segredo também deve ser girado periodicamente. Mas com as [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md), os aplicativos executados no Azure recebem uma identidade gerenciada automaticamente pelo Azure. Isso ajuda a resolver o **Problema de Introdução do Segredo**, no qual os usuários/aplicativos podem seguir as melhores práticas e não precisam se preocupar em girar o segredo primeiro

## <a name="prerequisites"></a>Pré-requisitos

* [Node JS](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 ou posterior
* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="login-to-azure"></a>Logon no Azure

Para fazer logon no Azure usando a CLI, você pode digitar:

```azurecli
az login
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Selecione um nome para o grupo de recursos e preencha o espaço reservado.
O exemplo a seguir cria um grupo de recursos denominado *<YourResourceGroupName>* no local *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

O grupo de recursos que você acabou de criar é usado ao longo deste tutorial.

## <a name="create-an-azure-key-vault"></a>Criar um Cofre de chaves do Azure

Em seguida, crie um Key Vault no grupo de recursos criado na etapa anterior. Embora “ContosoKeyVault” seja utilizado como o nome do Key Vault ao longo deste artigo, você deverá usar um nome exclusivo. Forneça as seguintes informações:

* Nome do cofre: **selecione um nome para o Key Vault aqui**.
* Nome do grupo de recursos: **selecione um nome para o grupo de recursos aqui**.
* O local: **Leste dos EUA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Nesse ponto, sua conta do Azure é a única autorizada a executar qualquer operação nesse novo cofre.

## <a name="add-a-secret-to-key-vault"></a>Adicione um segredo ao cofre de chaves

Estamos adicionando um segredo para ajudar a ilustrar como isso funciona. Você pode estar armazenando uma cadeia de conexão SQL ou qualquer outra informação que precise manter com segurança, mas disponibilizar para o aplicativo. Neste tutorial, a senha será chamada **AppSecret** e armazenará o valor de **MySecret** nela.

Digite os comandos abaixo para criar um segredo no Cofre de Chaves chamado **AppSecret** que armazenará o valor **MySecret**:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Para exibir o valor contido no segredo como texto sem formatação:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Este comando mostra as informações secretas, incluindo o URI. Após concluir essas etapas, você deverá ter um URI para um segredo em um Azure Key Vault. Anote essas informações. Ela será necessária em uma etapa posterior.

## <a name="clone-the-repo"></a>Clonar o repositório

Clone o repositório a fim de fazer uma cópia local para que você possa editar a fonte executando o seguinte comando:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Instalar dependências

Aqui, vamos instalar as dependências. Execute os comandos a seguir cd key-vault-node-quickstart npm install

Este projeto usou 2 módulos de nó:

* [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 
* [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)

## <a name="publish-the-web-application-to-azure"></a>Publicar o aplicativo Web no Azure

Abaixo estão as etapas que precisamos realizar

- A 1ª etapa é criar um Plano do [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/). Você pode armazenar vários aplicativos Web nesse plano.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
- Em seguida, criamos um aplicativo Web. No exemplo a seguir, substitua <nome_da_aplicação> por um nome de aplicativo exclusivo globalmente (os caracteres válidos são a-z, 0-9 e -). O tempo de execução é definido como NODE|6.9. Para ver todos os tempos de execução com suporte, execute az webapp list-runtimes
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
    Navegue até o aplicativo Web criado recentemente e verá um aplicativo Web funcionando. Substitua <app_name> por um nome exclusivo do aplicativo.

    ```
    http://<app name>.azurewebsites.net
    ```
    O comando acima também cria um aplicativo habilitado para Git que permite a implantação no Azure do git local. 
    O git local está configurado com a URL 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'

- Criar um usuário de implantação Após a conclusão do comando anterior, é possível adicionar um Azure remoto ao repositório Git local. Substitua <url> pela URL do Git remoto que você obteve de Habilitar o Git para seu aplicativo.

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

Anote ou copie a saída do comando acima. Ela deve estar no formato:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Em seguida, execute o comando usando o nome do seu Key Vault e o valor do PrincipalId copiado acima:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Implantar o aplicativo Node no Azure e recuperar o valor do segredo

Agora, tudo está definido. Execute o seguinte comando para implantar o aplicativo no Azure

```
git push azure master
```

Depois de fazer isso, quando você navegar por https://<app_name>.azurewebsites.net, verá o valor do segredo.
Substitua o nome <YourKeyVaultName> pelo nome do cofre

## <a name="next-steps"></a>Próximas etapas

* [Página inicial do Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Documentação do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [SDK do Azure para Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Referência de API REST do Azure](https://docs.microsoft.com/rest/api/keyvault/)
