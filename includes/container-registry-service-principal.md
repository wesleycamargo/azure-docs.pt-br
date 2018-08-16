---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 08/03/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 2174ae44f8e78763c1939aee5e6b86c95a0924ce
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39514019"
---
## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Para criar uma entidade de serviço com acesso ao registro de contêiner, execute o script a seguir no [Azure Cloud Shell](../articles/cloud-shell/overview.md) ou uma instalação local da [CLI do Azure](/cli/azure/install-azure-cli). O script é formatado para o shell do Bash.

Antes de executar o script, atualize a variável `ACR_NAME` com o nome do registro de contêiner. O valor `SERVICE_PRINCIPAL_NAME` deve ser exclusivo dentro do seu locatário do Azure Active Directory. Se você receber um erro "`'http://acr-service-principal' already exists.`", especifique um nome diferente para a entidade de serviço.

Como opção, modifique o valor `--role` no comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] se quiser conceder permissões diferentes.

Depois de executar o script, anote a **ID** e a **senha** da entidade de serviço. Quando você tiver suas credenciais, poderá configurar os aplicativos e serviços para se autenticarem no registro de contêiner como a entidade de serviço.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh --> [!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Usar uma entidade de serviço existente

Para conceder acesso de registro a uma entidade de serviço existente, você precisa atribuir uma nova função à entidade de serviço. Assim como ocorre ao criar uma nova entidade de serviço, você pode conceder acesso de pull, push e pull e proprietário.

O script a seguir usa o comando [az role assignment create][az-role-assignment-create] para conceder permissões *pull* a uma entidade de serviço especificada na variável `SERVICE_PRINCIPAL_ID`. Ajuste o valor `--role` se você desejar conceder um nível diferente de acesso.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh --> [!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
