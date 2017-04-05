---
title: "Saiba mais sobre os modelos do conjunto de dimensionamento de máquinas virtuais do Azure | Microsoft Docs"
description: "Saiba como criar um modelo mínimo viável do conjunto de dimensionamento para Conjuntos de Dimensionamento de Máquinas Virtuais"
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
ms.date: 2/14/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 2a8b770521e67ff9a621ad9fb2fa97e3f9462e31
ms.lasthandoff: 03/24/2017

---

# <a name="learn-about-virtual-machine-scale-set-templates"></a>Saiba mais sobre os modelos do conjunto de dimensionamento de máquinas virtuais

Os [modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) são uma excelente maneira de implantar grupos de recursos relacionados. Esta série de tutoriais mostra como criar um modelo de conjunto de dimensionamento mínimo viável e como modificar esse modelo para adaptá-lo a vários cenários. Todos os exemplos foram obtidos neste [repositório GitHub](https://github.com/gatneil/mvss). Esse modelo é projetado para ser simples. Para obter exemplos mais completos de modelos de conjunto de dimensionamento, consulte o [repositório GitHub de Modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) e pesquise as pastas que contêm a cadeia de caracteres `vmss`.

## <a name="a-minimum-viable-scale-set"></a>Um conjunto de dimensionamento mínimo viável

Nosso modelo de conjunto de dimensionamento mínimo viável pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json). Se já estiver familiarizado com modelos, não tenha receio de ir para a seção [Próximas etapas](#next-steps) para ver como modificar esse modelo para outros cenários. No entanto, se tiver menos familiarizado com modelos, você poderá achar útil essa descrição parte por parte. Para começar, vamos examinar o modelo básico, parte por parte.

Primeiro, definimos o `$schema` e o `contentVersion` do modelo. `$schema` define a versão do idioma do modelo e é usado para o realce de sintaxe do Visual Studio e para recursos de validação semelhantes. Na verdade, `contentVersion` não é usado pelo Azure. Em vez disso, ele se destina a ajudá-lo a manter o controle de qual é essa versão do modelo.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

Em seguida, definimos dois parâmetros, `adminUsername` e `adminPassword`. Parâmetros são valores especificados pelo usuário no momento da implantação. `adminUsername` é simplesmente um `string`, mas porque `adminPassword` é um segredo, fornecemos a ele o tipo `securestring`. Veremos adiante que esses parâmetros são passados na configuração do conjunto de dimensionamento.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```

Os modelos do Resource Manager também permitem definir variáveis para serem usadas posteriormente no modelo. Neste exemplo, não usamos nenhuma variável e, portanto, deixamos o objeto JSON vazio.

```json
  "variables": {},
```

Em seguida, temos os recursos do modelo, nos quais definimos o que realmente queremos implantar. Ao contrário do `parameters` e `variables` (que são objetos JSON), `resources` é uma lista JSON de objetos JSON.

```json
   "resources": [
```

Todos os recursos exigem um `type`, `name`, `apiVersion` e `location`. Nosso primeiro recurso é do tipo `Microsft.Network/virtualNetwork` com o nome `myVnet` e a apiVersion `2016-03-30`. Para descobrir a última versão de API de um tipo de recurso, consulte a [documentação da API REST do Azure](https://docs.microsoft.com/rest/api/).

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

Para especificar o local da rede virtual, estamos usando uma [função de modelo do Resource Manager](../azure-resource-manager/resource-group-template-functions.md), que deve ser colocada entre aspas e colchetes desta forma: `"[<template-function>]"`. Nesse caso, usamos a função resourceGroup, que não usa nenhum argumento e retorna um objeto JSON com metadados sobre o grupo de recursos no qual esta implantação está sendo implantada. O grupo de recursos é definido pelo usuário no momento da implantação. Em seguida, indexamos nesse objeto JSON com `.location` para obter o local do objeto JSON.

```json
       "location": "[resourceGroup().location]",
```


Cada recurso do Resource Manager tem sua própria seção `properties` para configurações específicas ao recurso. Nesse caso, estamos especificando que a rede virtual deve ter uma sub-rede que usa o intervalo de endereços IP privado `10.0.0.0/16`. Um conjunto de dimensionamento sempre está contido em uma sub-rede. Ele não pode abranger sub-redes.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

Além das propriedades `type`, `name`, `apiVersion` e `location` obrigatórias, opcionalmente, cada recurso pode ter uma lista `dependsOn` de cadeias de caracteres que especifica o que outros recursos dessa implantação devem concluir antes de implantar esse recurso. Nesse caso, há somente um elemento nessa lista, a rede virtual acima. Especificamos essa dependência porque o conjunto de dimensionamento precisa que a rede exista antes de criar as VMs. Dessa forma, o conjunto de dimensionamento pode fornecer a essas VMs endereços IP privados do intervalo de endereços IP especificado nas propriedades de rede anteriormente. O formato de cada cadeia de caracteres na lista dependsOn é `<type>/<name>` (os mesmos `type` e `name` que usamos anteriormente na definição de recurso de rede virtual).

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2016-04-30-preview",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```

O conjunto de dimensionamento precisa saber qual tamanho de VM será criado (o “nome de SKU”) e quantas dessas VMs serão criadas (a “capacidade de SKU”). Para ver quais tamanhos de VM estão disponíveis, consulte a [documentação Tamanhos de VM](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

O conjunto de dimensionamento também precisa saber como manipular atualizações no conjunto de dimensionamento. Atualmente, há duas opções, `Manual` e `Automatic`. Para obter mais informações sobre as diferenças entre as duas, consulte a documentação sobre [como atualizar um conjunto de dimensionamento](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

O conjunto de dimensionamento também precisa saber em qual sistema operacional as VMs serão colocadas. Aqui, criamos as VMs com uma imagem do Ubuntu 16.04-LTS totalmente corrigida.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

Como o conjunto de dimensionamento está implantando várias VMs, em vez de especificar cada nome de VM, especificamos um `computerNamePrefix`. O conjunto de dimensionamento acrescenta um índice a esse prefixo para cada VM e, portanto, os nomes VM estão no formato `<computerNamePrefix>_<auto-generated-index>`. Neste trecho, também vemos que estamos usando os parâmetros de antes para definir o nome de usuário do administrador e a senha para todas as VMs no conjunto de dimensionamento. Fazemos isso com a função de modelo `parameters`, que usa uma cadeia de caracteres que especifica o parâmetro que desejamos consultar e que gera o valor para esse parâmetro.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

Por fim, precisamos especificar a configuração de rede para as VMs no conjunto de dimensionamento. Nesse caso, só precisamos especificar a ID da sub-rede criada anteriormente para que o conjunto de dimensionamento saiba que as interfaces de rede serão colocadas nessa sub-rede. Podemos obter a ID da rede virtual que contém a sub-rede usando a função de modelo `resourceId`. Essa função usa o tipo e o nome de um recurso e retorna o identificador totalmente qualificado desse recurso (essa ID está no formato `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`). No entanto, o identificador da rede virtual não é suficiente. Devemos especificar a sub-rede específica na qual as VMs do conjunto de dimensionamento devem estar e, portanto, concatenamos `/subnets/mySubnet` para a ID da rede virtual. O resultado é a ID totalmente qualificada da sub-rede. Fazemos essa concatenação com a função `concat`, que usa uma série de cadeias de caracteres e retorna sua concatenação.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
