---
title: Consumir um aplicativo gerenciado do Azure | Microsoft Docs
description: Descreve como um cliente cria um aplicativo gerenciado do Azure dos arquivos publicados.
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/17/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 041254b07584a52ae92e603f60a439050b747af1
ms.contentlocale: pt-br
ms.lasthandoff: 05/18/2017


---
# <a name="consume-an-azure-managed-application"></a>Consumir um aplicativo gerenciado do Azure

Conforme descrito no artigo [Visão geral de aplicativos gerenciados](managed-application-overview.md), há dois cenários na experiência de ponta a ponta. Um é o publicador ou ISV que queira criar um aplicativo gerenciado para uso pelos clientes. O outro é o cliente final ou o cliente do aplicativo gerenciado. Este artigo aborda o segundo cenário e explica como um cliente final pode consumir um aplicativo gerenciado fornecido por um ISV.

No momento, você pode usar a CLI do Azure ou portal do Azure para consumir um aplicativo gerenciado. 

## <a name="create-the-managed-application-using-cli"></a>Criar o aplicativo gerenciado usando a CLI 

Você deve obter a ID de definição do dispositivo para o dispositivo que deseja consumir.

Há duas maneiras de criar um aplicativo gerenciado usando a CLI do Azure. Uma é usando o comando de implantação de modelo regular e a outra é usando um novo comando fornecido apenas para essa finalidade.

### <a name="create-using-template-deployment-command"></a>Criar usando o comando de implantação de modelo

Implante o arquivo applianceMainTemplate.json que o fornecedor criou.

Crie dois grupos de recursos. O primeiro grupo de recursos é onde o recurso do dispositivo é criado (Microsoft.Solutions/appliances). O segundo grupo de recursos contém todos os recursos definidos em mainTemplate.json. Esse grupo de recursos é gerenciado pelo ISV.

```azurecli
az group create --name mainResourceGroup --location westcentralus    
az group create --name managedResourceGroup --location westcentralus
```

> [!NOTE]
> Use `westcentralus` como o local do grupo de recursos.
>


Em seguida, use o seguinte comando para implantar o applianceMainTemplate.json no mainResourceGroup:

```azurecli
az group deployment create --name managedAppDeployment --resourceGroup mainResourceGroup --templateUri  
```

Quando o modelo anterior é executado, ele solicita os valores dos parâmetros que são definidos no modelo. Além dos parâmetros necessários para o provisionamento de recursos em um modelo, você precisa de dois valores de parâmetro principais:

- managedResourceGroupId - a ID do grupo de recursos onde os recursos definidos no applianceMainTemplate.json são criados. A ID está no formato `/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}`. No exemplo anterior, é a ID do `managedResourceGroup`.
- applianceDefinitionId - a ID do recurso de definição do aplicativo gerenciado. Esse valor é fornecido pelo ISV. 

> [!NOTE] 
> O ISV deve conceder acesso ao grupo de recursos onde o recurso de definição do dispositivo é criado. O recurso de definição do dispositivo é criado na assinatura do ISV. Portanto, um usuário, grupo de usuários ou aplicativo no locatário do cliente precisa ter acesso de leitura a esse recurso. 

Depois que a implantação for concluída com êxito, veja se o recurso de dispositivo foi criado em **mainResourceGroup**. O recurso storageAccount é criado em **managedResourceGroup**.

### <a name="create-the-managed-application-using-create-command"></a>Criar o aplicativo gerenciado usando o comando create

Você pode usar o comando `az managedapp create` para criar um aplicativo gerenciado da definição de aplicativo gerenciado. 

```azurecli
az managedapp create --name ravtestappliance401 --location "westcentralus" 
    --kind "Servicecatalog" --resource-group "ravApplianceCustRG401" 
       --managedapp-definition-id "/subscriptions/{guid}/resourceGroups/ravApplianceDefRG401/providers/Microsoft.Solutions/applianceDefinitions/ravtestAppDef401" 
       --managed-rg-id "/subscriptions/{guid}/resourceGroups/ravApplianceCustManagedRG401" 
       --parameters "{\"storageAccountName\": {\"value\": \"ravappliancedemostore1\"}}" 
       --debug
```

**appliance-definition-Id** - a ID do recurso da definição de aplicativo criada na etapa anterior. Para obter essa ID, execute o seguinte comando:

```azurecli
az appliance definition show -n ravtestAppDef1 -g ravApplianceRG2
```

Esse comando retorna a definição do dispositivo. Você precisa do valor da propriedade **Id**.

**managed-rg-id** - a ID do grupo de recursos onde todos os recursos definidos no applianceMainTemplate.json são criados. Esse grupo de recursos é o grupo de recursos gerenciados e é gerenciado pelo editor. Se não existir, ele será criado para você.

**resource-group** - grupo de recursos onde o recurso appliance é criado. O recurso Microsoft.Solutions/appliance reside nesse grupo de recursos. 

**parameters** - os parâmetros necessários para os recursos definidos em applianceMainTemplate.json.

## <a name="create-the-managed-application-using-portal"></a>Criar o aplicativo gerenciado usando o Portal

O suporte para consumir aplicativos gerenciados publicados por ISVs também está no portal. Você pode seguir as etapas abaixo:

Selecione o Aplicativo Gerenciado do Catálogo de Serviço na folha Criar no Portal do Azure-

![](./media/managed-application-consumption/create-service-catalog-managed-application.png)

Em seguida, você verá a lista de ofertas de vários ISVs/parceiros. Selecione aquele que você deseja criar e clique em "Criar"

![](./media/managed-application-consumption/select-offer.png)

Depois de clicar em criar, forneça os parâmetros necessários para provisionar os recursos na folha aberta. 

![](./media/managed-application-consumption/input-parameters.png)

Depois de fornecer os valores, clique em OK. O modelo é validado em relação às entradas que você forneceu. Se a validação for bem-sucedida, a implantação de modelo será iniciada. Depois que a implantação for concluída, os recursos apropriados definidos no modelo serão provisionados no grupo de recursos gerenciado que você forneceu.

## <a name="known-issues"></a>Problemas conhecidos

Esta versão prévia inclui os seguintes problemas:

* Se você receber uma mensagem 500 Erro interno do servidor durante a criação do dispositivo, o problema provavelmente é intermitente. Se você encontrar esse problema, repita a operação.
* Um novo grupo de recursos é necessário para o grupo de recursos gerenciado. O uso de um grupo de recursos existente faz com que a implantação falhe.
* O grupo de recursos que contém o recurso Microsoft.Solutions/appliances deve ser criado na localização **westcentralus**.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](managed-application-overview.md).
* Para entender a experiência de fornecedor, consulte [Criar e publicar um aplicativo gerenciado do Azure](managed-application-publishing.md).
