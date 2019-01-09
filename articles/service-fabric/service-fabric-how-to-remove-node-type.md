---
title: Como remover um tipo de nó no Azure Service Fabric | Microsoft Docs
description: Saiba como remover um tipo de nó no Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: v-steg
manager: JeanPaul.Connick
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/26/2018
ms.author: v-steg
ms.openlocfilehash: 3704a356763b16a30285baee1aabffdd3aa3f8aa
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53981336"
---
# <a name="remove-a-service-fabric-node-type"></a>Remover o tipo de nó do Service Fabric

Use [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) para remover um tipo de nó do Service Fabric.

As duas operações que ocorrem quando Remove-AzureRmServiceFabricNodeType é chamado são:
1.  O Conjunto de dimensionamento de máquinas virtuais (VMSS) por trás do tipo de nó é excluído.
2.  Para todos os nós dentro desse tipo de nó, todo o estado para esse nó é removido do sistema. Se houver serviços nesse nó, em seguida, os serviços são primeiro movidos para outro nó. Se o gerenciador de cluster não puder localizar um nó para o serviço/réplica, a operação é atrasada/bloqueada.

> [!NOTE]
> Não é recomendável usar Remove-AzureRmServiceFabricNodeType para remover um tipo de nó de um cluster de produção a ser usado com frequência. Nesse caso, é um comando muito perigoso, pois ele basicamente exclui o recurso de conjunto de dimensionamento de máquinas virtuais por trás do tipo de nó. Ao chamar Remove-AzureRmServiceFabricNodeType, o sistema não tem como saber se você se importa com a remoção sendo segura. 

## <a name="durability-characteristics"></a>Características de durabilidade
Segurança terá prioridade sobre velocidade ao usar Remove-AzureRmServiceFabricNodeType. As [características de durabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) Ouro ou Prata são recomendadas porque:
- Bronze não dá nenhuma garantia sobre como salvar informações de estado.
- A durabilidade Ouro ou Prata intercepta todas as alterações para VMSS.
- Ouro também dá controle sobre as atualizações do Azure sob VMSS.

O Service Fabric "orquestra" alterações subjacentes e atualiza de forma que os dados não sejam perdidos. No entanto, quando você remove um nó com durabilidade Bronze, você pode perder informações de estado. Se você estiver removendo um tipo de nó primário e seu aplicativo estiver sem estado, Bronze é aceitável. Quando você executar cargas de trabalho com monitoramento de estado na produção, a configuração mínima deve ser Prata. Da mesma forma, cenários de produção do tipo de nó primário devem ser sempre Prata ou Ouro.

### <a name="more-about-bronze-durability"></a>Mais informações sobre durabilidade de Bronze

Ao remover um tipo de nó que é Bronze, todos os nós no tipo de nó diminuem imediatamente. O Service Fabric não intercepta todas as atualizações VMSS de nós de Bronze, assim, todas as VMs ficam inativas imediatamente. Se você tiver qualquer coisa com o monitoamento de estado em nós, os dados são perdidos. Agora, mesmo se você estivesse sem estado, todos os nós no Service Fabric participam do anel, portanto, uma vizinhança inteira pode ser perdida, o que pode afetar o cluster em si.

Ao contrário de remoção de um único nó, pois, teoricamente, você pode remover um nó por vez, aguardar até que as réplicas e os serviços se movam, aguardar até que o sistema se estabilize, antes de remover outro nó e assim por diante.  No entanto, se você remover vários nós simultaneamente ao mesmo tempo, o cluster pode ir para baixo (já que o Service Fabric não intercepta todas as atualizações VMSS com durabilidade Bronze).

## <a name="recommended-node-type-removal-process"></a>Recomendado o processo de remoção do tipo de nó

Para remover o tipo de nó de maneira mais segura e rápida:
1.  Se você estiver usando a durabilidade de Bronze ou não quiser que o sistema mova os aplicativos que contenham informações seguras que serão perdidas como parte da exclusão do tipo de nó, primeiro, esvazie os dados com estado dos nós que serão afetados pela remoção do tipo de nó.
2.  Execute [Remove-ServiceFabricNodeState](https://docs.microsoft.com/powershell/module/servicefabric/remove-servicefabricnodestate?view=azureservicefabricps) em cada um dos nós que você deseja remover.
3.  Execute [Remove-AzureRmVmss](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#remove-vms-from-a-scale-set) para VMs que serão afetadas pela remoção de tipo de nó.
4. Execute [Remove-AzureRmServiceFabricNodeType](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/remove-azurermservicefabricnodetype) para remover o tipo de nó.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre o cluster [características de durabilidade](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Saiba mais sobre [tipos de nós e Conjuntos de Dimensionamento de Máquinas Virtuais](service-fabric-cluster-nodetypes.md).
- Saiba mais sobre [Dimensionamento do cluster do Service Fabric](service-fabric-cluster-scaling.md).