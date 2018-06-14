---
title: Elemento de interface do usuário SizeSelector do Azure | Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft.Compute.SizeSelector para o Portal do Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: d1b4974c78a5cdb7b4eb885797319b283be2d393
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260833"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Elemento de interface do usuário Microsoft.Compute.SizeSelector
Um controle para selecionar um tamanho de uma ou mais instâncias de máquina virtual.

## <a name="ui-sample"></a>Exemplo de interface do usuário
![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Comentários
- `recommendedSizes` deve conter pelo menos um tamanho. O primeiro tamanho recomendado é usado como padrão.
- Se um tamanho recomendado não estiver disponível no local selecionado, o tamanho será ignorado automaticamente. O próximo tamanho recomendado será usado.
- Os tamanhos não especificados em `constraints.allowedSizes` ficam ocultos e os tamanhos não especificados em `constraints.excludedSizes` são mostrados.
`constraints.allowedSizes` e `constraints.excludedSizes` são opcionais, mas não podem ser usados simultaneamente. A lista de tamanhos disponíveis pode ser determinada chamando [Listar tamanhos de máquinas virtuais disponíveis para uma assinatura](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region).
- `osPlatform` deve ser especificada e pode ser **Windows** ou **Linux**. Ela é usada para determinar os custos de hardware das máquinas virtuais.
- `imageReference` é omitida para imagens próprias, mas fornecida para imagens de terceiros. Ela é usada para determinar os custos de software das máquinas virtuais.
- `count` é usado para definir o multiplicador apropriado para o elemento. Ele dá suporte a um valor estático, como **2**, ou a um valor dinâmico de outro elemento, como `[steps('step1').vmCount]`. O valor padrão é **1**.
- O `numAvailabilityZonesRequired` pode ser 1, 2 ou 3.

## <a name="sample-output"></a>Saída de exemplo
```json
"Standard_D1"
```

## <a name="next-steps"></a>Próximas etapas
* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
