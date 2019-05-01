---
title: Os tipos de nó do Service Fabric e os conjuntos de dimensionamento da máquina virtual | Microsoft Docs
description: Saiba como os tipos de nó do Service Fabric se relacionam com os conjuntos de dimensionamento da máquina virtual e como fazer a conexão remotamente com uma instância do conjunto de dimensionamento da VM ou um nó de cluster.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: a5f8735df2b230de2b0ddcdcccff09430bada9e3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64684685"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Tipos de nó do Service Fabric e os conjuntos de dimensionamento da máquina virtual
[Conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets) são um recurso de computação do Azure. Você pode usar os conjuntos de dimensionamento para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó que você define em um cluster do Azure Service Fabric configura uma escala separada.  O tempo de execução do Service Fabric instalado em cada máquina virtual no conjunto de dimensionamento em extensão da máquina de Virtual do Microsoft. Cada tipo de nó pode ser escalado vertical ou horizontalmente de forma independente, ter a SKU de sistema operacional em execução em cada nó de cluster, ter conjuntos diferentes de portas abertas e usar métricas de capacidade diferentes.

A figura abaixo mostra um cluster com dois tipos de nó, denominados FrontEnd e BackEnd. Cada tipo de nó tem cinco nós.

![Um cluster com dois tipos de nó][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapeando instâncias de conjuntos de dimensionamento de máquinas virtuais para nós
Conforme mostrado na figura anterior, as instâncias do conjunto de dimensionamento começam na instância 0 e, em seguida, aumentam em 1. A numeração é refletida nos nomes do nó. Por exemplo, o nó BackEnd_0 é a instância 0 do conjunto de dimensionamento de BackEnd. Esse conjunto de dimensionamento específico tem cinco instâncias, chamadas BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando você escala um conjunto de dimensionamento verticalmente, uma nova instância é criada. O novo nome da instância do conjunto de dimensionamento geralmente será o nome do conjunto de dimensionamento mais o número de instância seguinte. Em nosso exemplo, é BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mapear balanceadores de carga do conjunto de dimensionamento para tipos de nó e conjuntos de dimensionamento
Se você tiver implantado o cluster no portal do Azure ou usado o modelo do exemplo do Azure Resource Manager, todos os recursos em um grupo de recursos serão listados. Você pode ver os balanceadores de carga para cada conjunto de dimensionamento ou tipo de nó. O nome do balanceador de carga usa o seguinte formato: **LB -&lt;nome do tipo de nó&gt;**. Um exemplo é LB-sfcluster4doc-0, conforme mostrado na figura a seguir:

![Recursos][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Extensão de máquina Virtual do Service Fabric
Extensão de máquina Virtual do Service Fabric é usado para inicializar o Service Fabric para máquinas virtuais do Azure e configurar a segurança de nó.

A seguir está um trecho de código de extensão da máquina de Virtual de malha do serviço:

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

A seguir estão as descrições de propriedade:

| **Nome** | **Valores permitidos** | ** --- ** | **Diretrizes ou descrição resumida** |
| --- | --- | --- | --- |
| Nome | string | --- | nome exclusivo para a extensão |
| tipo | "ServiceFabricLinuxNode" or "ServiceFabricWindowsNode | --- | Identifica malha de serviço do sistema operacional estiver inicializando para |
| autoUpgradeMinorVersion | true ou false | --- | Habilitar atualização automática de versões secundárias do tempo de execução do SF |
| publicador | Microsoft.Azure.ServiceFabric | --- | nome do publicador de extensão do Service Fabric |
| clusterEndpont | string | --- | URI:port ao ponto de extremidade de gerenciamento |
| nodeTypeRef | string | --- | nome do tipo de nó |
| durabilityLevel | Bronze, Prata, ouro e Platina | --- | tempo permitido para pausar a infraestrutura imutável do Azure |
| enableParallelJobs | true ou false | --- | Habilitar ParallelJobs de computação, como remover a VM e reinicie a VM na mesma escala definida em paralelo |
| nicPrefixOverride | string | --- | Prefixo de sub-rede, como "10.0.0.0/24" |
| commonNames | string[] | --- | Nomes comuns dos certificados de cluster instalado |
| x509StoreName | string | --- | Nome do Store onde se encontra o certificado de cluster instalado |
| typeHandlerVersion | 1,1 | --- | Versão da extensão. 1.0 versão de clássico de extensão são recomendadas para atualizar para 1.1 |

## <a name="next-steps"></a>Próximas etapas
* Consulte [visão geral do recurso "Implantar em qualquer lugar" e comparação com clusters gerenciados do Azure](service-fabric-deploy-anywhere.md).
* Saiba mais sobre [segurança de cluster](service-fabric-cluster-security.md).
* [Conectar-se remotamente](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) uma instância específica do conjunto de dimensionamento
* [Atualizar os valores de intervalo de porta RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) nas VMs de cluster após a implantação
* [Alterar o nome de usuário administrador e a senha](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) para as VMs do cluster

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
