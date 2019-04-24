---
title: Adicionar um tipo de nó a um cluster do Azure Service Fabric | Microsoft Docs
description: Aprenda como dimensionar um cluster do Service Fabric adicionando um conjunto de dimensionamento de máquinas virtuais.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/13/2019
ms.author: aljo
ms.openlocfilehash: ed5bf829e2fbff6c286acdb21a8d0158148483d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506715"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Dimensione um cluster do Service Fabric adicionando um conjunto de dimensionamento de máquinas virtuais
Este artigo descreve como dimensionar um cluster do Azure Service Fabric adicionando um novo tipo de nó a um cluster existente. Um cluster do Service Fabric é um conjunto de computadores físicos ou virtuais conectados via rede, nos quais os microsserviços são implantados e gerenciados. Uma máquina ou VM que faz parte de um cluster é chamada de nó. Conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que você usa para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Azure é [configurado como um conjunto de dimensionamento separado](service-fabric-cluster-nodetypes.md). Então, cada tipo de nó pode ser gerenciado separadamente. Depois de criar um cluster do Service Fabric, você pode dimensionar um cluster horizontalmente adicionando um novo tipo de nó (conjunto de dimensionamento de máquinas virtuais) a um cluster existente.  É possível dimensionar o cluster a qualquer momento, mesmo quando as cargas de trabalho estiverem em execução no cluster.  Na medida em que o cluster for dimensionado, os aplicativos também serão dimensionados automaticamente.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Adicionar mais um conjunto de dimensionamento a um cluster existente
Adicionar um novo tipo de nó (que é apoiado por um conjunto de dimensionamento de máquinas virtuais) em um cluster existente é semelhante à [atualização do tipo de nó primário](service-fabric-scale-up-node-type.md), exceto que você não usará o mesmo NodeTypeRef; obviamente não desabilitar qualquer ativamente usadas conjuntos de dimensionamento de máquinas virtuais, e você não perderá a disponibilidade do cluster se você não atualizar o tipo de nó primário. 

A propriedade NodeTypeRef é declarada dentro das propriedades de extensão do Service Fabric de conjunto de dimensionamento de máqunas virtuais:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Além disso, você precisará adicionar esse novo tipo de nó para o recurso de cluster do Service Fabric:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Próximos passos
* Aprenda como [expandir o tipo de nó primário](service-fabric-scale-up-node-type.md)
* Saiba mais sobre [escalabilidade de aplicativo](service-fabric-concepts-scalability.md).
* [Reduzir horizontalmente ou escalar horizontalmente um cluster do Azure](service-fabric-tutorial-scale-cluster.md).
* [Dimensionar um cluster do Azure de forma programática](service-fabric-cluster-programmatic-scaling.md) usando a SDK fluente de computação do Azure.
* [Dimensione um cluster autônomo para dentro ou para fora](service-fabric-cluster-windows-server-add-remove-nodes.md).

