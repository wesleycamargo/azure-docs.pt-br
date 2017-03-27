---
title: "Conjuntos de dimensionamento de máquinas virtuais do Azure: modelo de rede virtual existente | Microsoft Docs"
description: Saiba como criar um modelo de conjunto de dimensionamento com a rede virtual existente
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/06/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: ddb3e1789e49d138e744c2238679236134b69324
ms.lasthandoff: 03/09/2017


---

# <a name="about-this-article"></a>Sobre este artigo

Este artigo mostra como modificar o [modelo de conjunto de dimensionamento mínimo viável](./virtual-machine-scale-sets-mvss-start.md) para implantar em uma rede virtual existente em vez de criar uma nova.

## <a name="modifying-the-minimum-viable-scale-set-to-deploy-into-an-existing-virtual-network"></a>Modificando o conjunto de dimensionamento mínimo viável para implantar em uma rede virtual existente

Nosso modelo de conjunto de dimensionamento mínimo viável pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), e nosso modelo para implantar o conjunto de dimensionamento em uma rede virtual existente pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Vamos examinar a comparação usada para criar esse modelo (`git diff master minimum-viable-scale-set`), parte por parte:

Primeiro, vamos adicionar um parâmetro `subnetId`. Essa cadeia de caracteres será passada para a configuração do conjunto de dimensionamento, permitindo que o conjunto de dimensionamento identifique a sub-rede pré-criada onde implantar máquinas virtuais. Essa cadeia de caracteres deve estar no formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Por exemplo implantar o conjunto de dimensionamento em uma rede virtual existente com o nome `myvnet`, sub-rede `mysubnet`, grupo de recursos `myrg` e assinatura`00000000-0000-0000-0000-000000000000`, o subnetId seria: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

Em seguida, podemos excluir o recurso de rede virtual da matriz `resources`, já que estamos usando uma rede virtual existente e não precisamos implantar uma nova.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2016-12-01",
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

A rede virtual já existe antes do modelo ser implantado e, portanto, não é necessário especificar uma cláusula dependsOn do conjunto de dimensionamento para a rede virtual. Assim, podemos excluir estas linhas:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Por fim, podemos passar o parâmetro `subnetId` definido pelo usuário (em vez de usar `resourceId` para obter a id de uma rede virtual na mesma implantação, que é o que o conjunto de dimensionamento mínimo viável faz).

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

