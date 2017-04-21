---
title: "Marcar recursos do Azure para organização lógica | Microsoft Docs"
description: "Mostra como aplicar marcas para organizar os recursos do Azure para cobrança e gerenciamento."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0e1ee94504ebff235c1da9128e0ac68c2b28bc59
ms.openlocfilehash: 2f56314769d90a1f0f9ebb5ece9c8e54b23b8936
ms.lasthandoff: 02/21/2017


---
# <a name="use-tags-to-organize-your-azure-resources"></a>Usar marcações para organizar seus recursos do Azure
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

> [!NOTE]
> Você só pode aplicar marcas em recursos com suporte a operações do Gerenciador de Recursos. Se você tiver criado uma máquina virtual, uma rede virtual ou um armazenamento por meio do modelo de implantação clássica (como por meio do portal clássico do Azure), não poderá aplicar uma marcação a esse recurso. Para dar suporte à marcação, implante esses recursos novamente por meio do Resource Manager. Todos os outros recursos oferecem suporte à marcação.
> 
> 

## <a name="ensure-tag-consistency-with-policies"></a>Garantir a consistência de marcação com as políticas

Políticas de recursos permitem que você crie regras padrão para sua organização. Você pode criar políticas que garantem que os recursos sejam marcados com os valores apropriados. Para obter mais informações, consulte [Aplicar políticas de recursos para marcas](resource-manager-policy-tags.md).

## <a name="templates"></a>Modelos

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>Portal
[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell
[!INCLUDE [resource-manager-tag-resources-powershell](../../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli-20"></a>CLI do Azure 2.0

Com a CLI do Azure 2.0, você pode adicionar marcas de recursos e grupo de recursos e recursos de consulta por valores de marcação.

Ao aplicar marcas a um recurso ou grupo de recursos, você pode substituir as marcas existentes nesse recurso ou grupo de recursos. Portanto, você deve usar uma abordagem diferente com base em se o recurso ou o grupo de recursos tem marcas existentes que você deseja preservar. Para adicionar marcas à:

* grupo de recursos sem marcas existentes.

  ```azurecli
  az group update -n TagTestGroup --set tags.Environment=Test tags.Dept=IT
  ```

* recurso sem marcas existentes.

  ```azurecli
  az resource tag --tags Dept=IT Environment=Test -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
  ``` 

Para adicionar marcas a um recurso que já tenha marcas, primeiro recupere as marcas existentes: 

```azurecli
az resource show --query tags --output list -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
```

Isso retorna o seguinte formato:

```
Dept        : Finance
Environment : Test
```

Reaplicar as marcas existentes para o recurso e, em seguida, adicione as novas marcas.

```azurecli
az resource tag --tags Dept=Finance Environment=Test CostCenter=IT -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
``` 

Para obter os grupos de recursos com uma marca específica, use `az group list`.

```azurecli
az group list --tag Dept=IT
```

Para obter todos os recursos com marca e valor específicos, use `az resource list`.

```azurecli
az resource list --tag Dept=Finance
```

## <a name="azure-cli-10"></a>CLI 1.0 do Azure
[!INCLUDE [resource-manager-tag-resources-cli](../../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>API REST
O portal e o PowerShell usam a [API REST do Gerenciador de Recursos](https://docs.microsoft.com/rest/api/resources/) em segundo plano. Se você precisar integrar a marcação a outro ambiente, você pode obter marcas com um GET na ID do recurso e atualizar o conjunto de marcas com uma chamada de PATCH.

## <a name="tags-and-billing"></a>Marcas e cobrança
Marcas permitem que você agrupe os dados de cobrança. Por exemplo, se você estiver executando várias VMs para organizações diferentes, use as marcas para a utilização do grupo por centro de custo. Você também pode usar marcas para categorizar os custos pelo ambiente de tempo de execução, como por exemplo, o uso de cobrança para VMs em execução no ambiente de produção.


Você pode recuperar as informações sobre as marcações por meio das [APIs RateCard e Uso de Recursos do Azure](../billing/billing-usage-rate-card-overview.md) ou do arquivo de uso CSV (com valores separados por vírgula). Baixe o arquivo de uso no [portal de contas do Azure](https://account.windowsazure.com/) ou no [portal de EA](https://ea.azure.com). Para saber mais sobre o acesso programático às informações de cobrança, confira [Obter informações sobre o consumo de recursos do Microsoft Azure](../billing/billing-usage-rate-card-overview.md). Para operações de API REST, confira [Referência da API REST de cobrança do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).


Quando você baixa o CSV de uso para serviços que dão suporte a marcações com cobrança, as marcações aparecerão na coluna **Marcações** . Para saber mais, confira [Entenda sua fatura do Microsoft Azure](../billing/billing-understand-your-bill.md).

![Ver as marcas de cobranças](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Próximas etapas
* É possível aplicar restrições e convenções em sua assinatura com políticas personalizadas. A política que você definir pode exigir que todos os recursos tenham uma valor para uma marcação específica. Para saber mais, confira [Usar a Política para gerenciar recursos e controlar o acesso](resource-manager-policy.md).
* Para obter uma introdução ao uso do Azure PowerShell ao implantar recursos, confira [Usando o Azure PowerShell com o Azure Resource Manager](powershell-azure-resource-manager.md).
* Para obter uma introdução ao uso da CLI do Azure ao implantar recursos, confira [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](xplat-cli-azure-resource-manager.md).
* Para obter uma introdução ao uso do portal, confira [Usando o portal do Azure para gerenciar os recursos do Azure](resource-group-portal.md)  
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).


