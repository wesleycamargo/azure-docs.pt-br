---
title: "Tipos de nós do Service Fabric e Conjuntos de Dimensionamento de VMs | Microsoft Docs"
description: "Descreve como os tipos de nó do Service Fabric se relacionam com os conjuntos de escala da VM e como fazer a conexão remota com uma instância de conjunto de escala da VM ou um nó de cluster."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: chackdan
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 32119a6ef586d616407c69e89a0d0f05758438bc
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017


---
# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>A relação entre os tipos de nó do Service Fabric e os conjuntos de escala da máquina virtual
Os conjuntos de dimensionamento de máquina virtual são um recurso de Computação do Azure que você pode usar para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Service Fabric é configurado como um Conjunto de Escala de VM separado. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente.

A captura de tela abaixo mostra um cluster com dois tipos de nó: FrontEnd e BackEnd.  Cada tipo de nó tem cinco nós.

![Captura de tela de um cluster com dois tipos de nó][NodeTypes]

## <a name="mapping-vm-scale-set-instances-to-nodes"></a>Mapeando instâncias de conjunto de escala de VM para nós
Como você pode ver acima, as instâncias de conjunto de escala da VM começam da instância 0 e vão subindo. A numeração está refletida nos nomes. Por exemplo, BackEnd_0 é a instância 0 do conjunto de dimensionamento da VM de BackEnd. Esse conjunto de escala da VM específico tem cinco instâncias, chamadas BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando você escala um conjunto de escala de VM verticalmente, uma nova instância é criada. O novo nome da instância do conjunto de escala da VM geralmente será o nome do conjunto de escala da VM mais o número de instância seguinte. Em nosso exemplo, é BackEnd_5.

## <a name="mapping-vm-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Mapeamento de balanceadores de carga de conjunto de escala da VM para cada tipo de nó/conjunto de escala da VM
Se você tiver implantado o cluster do portal ou usado o modelo do Resource Manager de exemplo que fornecemos, quando obtiver uma lista de todos os recursos em um Grupo de Recursos, verá os balanceadores de carga para cada tipo de nó ou Conjunto de Escala de VM.

O nome seria algo como: **LB-&lt;nome do NodeType&gt;**. Por exemplo, LB-sfcluster4doc-0, conforme mostrado nesta captura de tela:

![Recursos][Resources]

## <a name="remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node"></a>Conexão remota a uma instância de conjunto de escala da VM ou a um nó de cluster
Cada tipo de Nó definido em um cluster é configurado como um Conjunto de Escala de VM separado.  Isso significa que os tipos de nó podem ser escalados verticalmente para cima ou para baixo de forma independente e podem ser compostos por diferentes SKUs de VM. Ao contrário das máquinas virtuais de instância única, as instâncias de conjunto de escala da VM não recebem um endereço IP virtual próprio. Assim, pode ser complicado procurar um endereço IP e uma porta que você queira usar para fazer a conexão remota com uma instância específica.

Aqui estão as etapas que você pode seguir para descobri-los.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Etapa 1: descobrir o endereço IP virtual do tipo de nó e as regras NAT de entrada para RDP
Para obtê-lo, você precisa obter os valores de regras NAT de entrada definidos como parte da definição de recurso para **Microsoft.Network/loadBalancers**.

No portal, navegue até a folha Balanceador de carga e então até **Configurações**.

![LBBlade][LBBlade]

Em **Configurações**, clique em **Regras NAT de entrada**. Isso lhe dá o endereço IP e a porta que você pode usar para fazer a conexão remota com a instância de conjunto de escala da VM. Na captura de tela a seguir, é **104.42.106.156** e **3389**

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-vm-scale-set-instancenode"></a>Etapa 2: descobrir a porta que você pode usar para fazer conexão remota com o nó/instância do conjunto de escala de VM específico
Neste documento, falei sobre como as instâncias de escala da VM mapeiam para os nós. Nós usaremos isso para descobrir a porta exata.

As portas são alocadas em ordem crescente de instância do Conjunto de Escala de VM. Portanto, em meu exemplo, para o tipo de nó FrontEnd, as portas para cada uma das cinco instâncias são as mostradas a seguir. Agora, você precisa fazer o mesmo mapeamento para a instância do Conjunto de Escala de VM.

| **Instância do Conjunto de Escala de VM** | **Porta** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

### <a name="step-3-remote-connect-to-the-specific-vm-scale-set-instance"></a>Etapa 3: conectar-se remotamente à instância do conjunto de escala da VM específica
Na captura de tela abaixo, usei a conexão de área de trabalho remota para me conectar com o FrontEnd_1:

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Como alterar os valores de intervalo da porta RDP
### <a name="before-cluster-deployment"></a>Antes da implantação de cluster
Quando você estiver configurando o cluster usando um modelo do Resource Manager, poderá especificar o intervalo em **inboundNatPools**.

Vá para a definição de recurso para **Microsoft.Network/loadBalancers**. Lá, você encontra a descrição de **inboundNatPools**.  Substitua os valores *frontendPortRangeStart* e *frontendPortRangeEnd*.

![inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>Depois da implantação de cluster
Isso é um pouco mais complicado e pode resultar na reciclagem das VMs. Agora, você terá que definir novos valores usando o Azure PowerShell. Verifique se o Azure PowerShell versão 1.0 ou posterior está instalado em seu computador. Se não tiver feito isso antes, sugiro fortemente que você execute as etapas descritas em [Como instalar e configurar o Azure PowerShell.](/powershell/azure/overview)

Entre na sua conta do Azure. Se o comando do PowerShell falhar por algum motivo, verifique se o Azure PowerShell foi instalado corretamente.

```
Login-AzureRmAccount
```

Execute o seguinte para obter detalhes sobre o balanceador de carga e ver os valores para a descrição de **inboundNatPools**:

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

Agora defina os valores desejados para *frontendPortRangeEnd* e *frontendPortRangeStart*.

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## <a name="next-steps"></a>Próximas etapas
* [Visão geral do recurso "Implantar em qualquer lugar" e comparação com clusters gerenciados do Azure](service-fabric-deploy-anywhere.md)
* [Segurança de cluster](service-fabric-cluster-security.md)
* [ SDK do Service Fabric e introdução](service-fabric-get-started.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

