---
title: Fazer referência a uma rede virtual existente em um modelo do conjunto de dimensionamento do Azure | Microsoft Docs
description: Saiba como adicionar uma rede virtual a um modelo existente do conjunto de dimensionamento de máquinas virtuais do Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8b75b9898eb767866c0843594a82570cfb65d122
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868946"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Adicionar referência a uma rede virtual existente em um modelo do conjunto de dimensionamento do Azure

Este artigo mostra como modificar a [modelo de conjunto de dimensionamento básico](virtual-machine-scale-sets-mvss-start.md) para implantar em uma rede virtual existente em vez de criar um novo.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo

Em um [artigo anterior](virtual-machine-scale-sets-mvss-start.md) tivéssemos criado um modelo de conjunto de dimensionamento básico. Agora podemos usar esse modelo anterior e modificá-lo para criar um modelo que implanta um conjunto de dimensionamento em uma rede virtual existente. 

Primeiro, adicione um parâmetro `subnetId`. Essa cadeia de caracteres será passada para a configuração do conjunto de dimensionamento, permitindo que o conjunto de dimensionamento identifique a sub-rede pré-criada na qual implantará máquinas virtuais. Essa cadeia de caracteres deve estar no formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Por exemplo implantar o conjunto de dimensionamento em uma rede virtual existente com o nome `myvnet`, sub-rede `mysubnet`, grupo de recursos `myrg` e assinatura`00000000-0000-0000-0000-000000000000`, o subnetId seria: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Em seguida, exclua o recurso de rede virtual da matriz `resources`, pois você usará uma rede virtual existente e não precisará implantar uma nova.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

A rede virtual já existe antes do modelo ser implantado e, portanto, não é necessário especificar uma cláusula dependsOn do conjunto de dimensionamento para a rede virtual. Exclua as seguintes linhas:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Por fim, passe o `subnetId` parâmetro definido pelo usuário (em vez de usar `resourceId` para obter a ID de uma rede virtual na mesma implantação, que é o que o básico conjunto de dimensionamento viável modelo faz).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
