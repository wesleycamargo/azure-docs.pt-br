---
title: "Fazer referência a uma imagem personalizada no modelo do conjunto de dimensionamento do Azure | Microsoft Docs"
description: "Saiba como adicionar uma imagem personalizada a um modelo existente do conjunto de dimensionamento de máquinas virtuais do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2017
ms.author: negat
ms.openlocfilehash: 28d2c080048a7f82e83ad9c1794c9757b330a8c7
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2017
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Adicionar uma imagem personalizada a um modelo de conjunto de dimensionamento do Azure

Esse artigo mostra como modificar o [modelo do conjunto de dimensionamento mínimo viável](./virtual-machine-scale-sets-mvss-start.md) para a implantação de uma imagem personalizada.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo

O modelo do conjunto de dimensionamento mínimo viável pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json) e o modelo para implantar o conjunto de dimensionamento de uma imagem personalizada pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json). Vamos examinar a comparação usada para criar esse modelo (`git diff minimum-viable-scale-set custom-image`), parte por parte:

### <a name="creating-a-managed-disk-image"></a>Criando uma imagem de disco gerenciada

Se você já tiver uma imagem de disco gerenciado personalizado (um recurso do tipo `Microsoft.Compute/images`), você poderá ignorar esta seção.

Primeiro, adicione um parâmetro `sourceImageVhdUri`, que é o URI para o blob generalizado no Armazenamento do Azure que contém a imagem personalizada a ser usada para a implantação.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Em seguida, adicione um recurso do tipo `Microsoft.Compute/images`, que é a imagem do disco gerenciado no blob generalizado localizado no URI `sourceImageVhdUri`. Essa imagem deve estar na mesma região que o conjunto de dimensionamento que a utiliza. Nas propriedades da imagem, especifique o tipo do sistema operacional, a localização do blob (do parâmetro `sourceImageVhdUri`) e o tipo de conta de armazenamento:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

No recurso de conjunto de dimensionamento, adicione uma cláusula `dependsOn` referindo-se à imagem personalizada para garantir que a imagem seja criada antes que o conjunto de dimensionamento tente implantar usando essa imagem:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Alterando as propriedades do conjunto de dimensionamento para usar a imagem de disco gerenciado

Na `imageReference` do conjunto de dimensionamento `storageProfile`, em vez especificar o editor, a oferta, o SKU e a versão de uma imagem de plataforma, especifique o `id` do recurso `Microsoft.Compute/images`:

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Neste exemplo, use a função `resourceId` para obter a ID do recurso da imagem criada no mesmo modelo. Se você já criou a imagem do disco gerenciado com antecedência, forneça a ID da imagem. Essa ID deve estar no formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
