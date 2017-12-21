---
title: Criar aplicativo gerenciado pelo Azure com CLI do Azure | Microsoft Docs
description: "Mostra como criar um aplicativo gerenciado do Azure destinado aos membros de sua organização."
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: 35059603096279f7d58da1c1b40dd2ab3f1b5c38
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>Criar e implementar um aplicativo gerenciado pelo Azure com a CLI do Azure

Este artigo fornece uma introdução ao trabalho com aplicativos gerenciados. Você pode adicionar uma definição de aplicativo gerenciado a um catálogo interno para usuários em sua organização. Em seguida, você implanta esse aplicativo gerenciado para sua assinatura. Para simplificar a introdução, já compilamos os arquivos do seu aplicativo gerenciado. Um arquivo define a infraestrutura da solução. Um segundo arquivo que define a interface do usuário para implantar o aplicativo gerenciado por meio do portal. Esses arquivos estão disponíveis por meio do GitHub. Você aprenderá a compilar esses arquivos no tutorial [Criar aplicativo de catálogo de serviço](publish-service-catalog-app.md).

Quando tiver terminado, você tem três grupos de recursos contendo diferentes partes do aplicativo gerenciado.

| Grupo de recursos | Contém: | Descrição |
| -------------- | -------- | ----------- |
| appDefinitionGroup | A definição de aplicativo gerenciado. | O editor cria esse grupo de recursos e a definição do aplicativo gerenciado. Qualquer pessoa com acesso à definição do aplicativo gerenciado pode implantá-lo. |
| applicationGroup | A instância do aplicativo gerenciado. | O consumidor cria esse grupo de recursos e a instância do aplicativo gerenciado. O consumidor pode atualizar o aplicativo gerenciado por meio dessa instância. |
| infrastructureGroup | Os recursos do aplicativo gerenciado. | Esse grupo de recursos é criado automaticamente com a criação do aplicativo gerenciado. O editor tem acesso a esse grupo de recursos para gerenciar o aplicativo. O consumidor tem acesso limitado ao grupo de recursos. |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Criar um grupo de recursos para definição

Sua definição de aplicativo gerenciado existe em um grupo de recursos. O grupo de recursos do Azure é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Para criar um grupo de recursos, use o seguinte comando:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Criar a definição de aplicativo gerenciado

Ao definir o aplicativo gerenciado, você deve selecionar um usuário, grupo ou aplicativo que gerencia os recursos em nome do consumidor. Essa identidade tem permissões no grupo de recursos gerenciado de acordo com a função atribuída. Geralmente, você cria um grupo do Azure Active Directory para gerenciar os recursos. No entanto, para este artigo, use sua própria identidade.

Para obter a ID de objeto de sua identidade, forneça seu nome de usuário principal no comando a seguir:

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

Em seguida, é necessária a ID de definição de função da função RBAC interna para conceder acesso ao usuário. O comando a seguir mostra como obter a ID de definição de função para a função Proprietário:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

Agora, crie o recurso de definição de aplicativo gerenciado. O aplicativo gerenciado contém somente uma conta de armazenamento.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

Quando o comando for concluído, você tem uma definição de aplicativo gerenciado no seu grupo de recursos. 

Alguns dos parâmetros usados no exemplo anterior são:

* **resource-group**: o nome do grupo de recursos no qual a definição de aplicativo gerenciado é criada.
* **lock-level**: o tipo de bloqueio colocado no grupo de recursos gerenciado. Ela impede que o cliente execute operações indesejáveis no grupo de recursos. Atualmente, ReadOnly é o único nível de bloqueio com suporte. Quando ReadOnly é especificado, o cliente pode ler somente os recursos presentes no grupo de recursos gerenciados.
* **authorizations**: descreve a ID da entidade e a ID de definição de função que são usadas para conceder permissão ao grupo de recursos gerenciado. Ele é especificado no formato `<principalId>:<roleDefinitionId>`. Vários valores também podem ser especificados para essa propriedade. Se houver a necessidade de vários valores, eles deverão ser especificados no formulário `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Vários valores são separados por um espaço.
* **package-file-uri**: o local de um pacote .zip que contém os arquivos necessários. No mínimo, o pacote contém os arquivos **mainTemplate.json** e **createUiDefinition.json**. **mainTemplate.json**: define os recursos do Azure que são provisionados como parte do aplicativo gerenciado. O modelo não é diferente de um modelo normal do Resource Manager. O **createUiDefinition.json** gera a interface do usuário para os usuários que criam o aplicativo gerenciado por meio do portal.

## <a name="create-resource-group-for-managed-application"></a>Criar grupo de recursos para aplicativo gerenciado

Agora você está pronto para implantar o aplicativo gerenciado. 

Para conter o aplicativo gerenciado implantado, você precisa de um grupo de recursos. Use **westcentralus** para o local.

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>Implantar o aplicativo gerenciado

Você implanta o aplicativo com os seguintes comandos:

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"storage\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

Alguns dos parâmetros usados no exemplo anterior são:

* **managedapp-definition-id**: a ID da definição que você criou anteriormente neste artigo.
* **managed-rg-id**: a ID do grupo de recursos para os recursos associados ao aplicativo gerenciado. O comando cria esse grupo de recursos. Ele **não deve existir antes de executar o comando**. Esse grupo de recursos é gerenciado pelo editor. 
* **resource-group**: o grupo de recursos em que o recurso de aplicativo gerenciado é criado.
* **parameters**: os parâmetros necessários para os recursos associados com o aplicativo gerenciado.

Depois que a implantação for concluída com êxito, veja se o aplicativo gerenciado foi criado em applicationGroup. O recurso storageAccount é criado em infrastructureGroup.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados](overview.md).
* Para obter exemplos dos arquivos, consulte [Exemplos de aplicativo gerenciado](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* Para saber como criar um arquivo de definição de interface do usuário para um aplicativo gerenciado, consulte [Introdução a CreateUiDefinition](create-uidefinition-overview.md).
