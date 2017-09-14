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
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 8c9e91d122591a19d34d944e2d9aaeb327cdafe4
ms.contentlocale: pt-br
ms.lasthandoff: 09/05/2017

---
# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>A relação entre os tipos de nó do Service Fabric e os conjuntos de escala da máquina virtual
Conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure. Eles podem ser usados para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Service Fabric está configurado como um conjunto de dimensionamento de máquinas virtuais separado. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente.

A captura de tela abaixo mostra um cluster com dois tipos de nó: FrontEnd e BackEnd.  Cada tipo de nó tem cinco nós.

![Captura de tela de um cluster com dois tipos de nó][NodeTypes]

## <a name="mapping-virtual-machine-scale-set-instances-to-nodes"></a>Mapeando instâncias de conjuntos de dimensionamento de máquinas virtuais para nós
Como você pode ver acima, as instâncias de conjunto de dimensionamento de máquinas virtuais começam da instância 0 e vão subindo. A numeração está refletida nos nomes. Por exemplo, BackEnd_0 é a instância 0 do conjunto de dimensionamento de máquinas virtuais de BackEnd. Esse conjunto de dimensionamento de máquinas virtuais específico tem cinco instâncias, chamadas BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando você aumenta um conjunto de dimensionamento de máquinas virtuais, uma nova instância é criada. O nome da nova instância do conjunto de dimensionamento de máquinas virtuais geralmente será o nome do conjunto de dimensionamento de máquinas virtuais mais o número de instância seguinte. Em nosso exemplo, é BackEnd_5.

## <a name="mapping-virtual-machine-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Mapeando balanceadores de carga de conjuntos de dimensionamento de máquinas virtuais para cada tipo de nó/conjunto de dimensionamento de VM
Se você tiver implantado o cluster do portal ou usado o modelo do Resource Manager, você poderá obter uma lista de todos os recursos em um grupo de recursos. Você ver os balanceadores de carga de cada conjunto de dimensionamento de máquinas virtuais ou tipo de nó.

O nome seria algo como: **LB-&lt;nome do NodeType&gt;**. Por exemplo, LB-sfcluster4doc-0, conforme mostrado nesta captura de tela:

![Recursos][Resources]

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Conectar remotamente a uma instância do conjunto de dimensionamento de máquinas virtuais ou a um nó de cluster
Cada tipo de nó definido em um cluster está configurado como um conjunto de dimensionamento de máquinas virtuais separado.  Isso significa que os tipos de nó podem ser escalados verticalmente para cima ou para baixo de forma independente. Além disso, eles podem ser compostos por diferentes SKUs de VM. Ao contrário das VMs de instância única, as instâncias de conjunto de dimensionamento de máquinas virtuais não recebem um endereço IP virtual próprio. Assim, pode ser complicado procurar um endereço IP e uma porta que você queira usar para fazer a conexão remota com uma instância específica.

Aqui estão as etapas que você pode seguir para descobri-los.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Etapa 1: descobrir o endereço IP virtual do tipo de nó e as regras NAT de entrada para RDP
Para obtê-lo, você precisa obter os valores de regras NAT de entrada definidos como parte da definição de recurso para **Microsoft.Network/loadBalancers**.

No portal, navegue até a folha Balanceador de carga e então até **Configurações**.

![LBBlade][LBBlade]

Em **Configurações**, clique em **Regras NAT de entrada**. Isso lhe dá o endereço IP e a porta que você pode usar para fazer a conexão remota com a instância de conjunto de dimensionamento de máquinas virtuais. Na captura de tela a seguir, é **104.42.106.156** e **3389**

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-virtual-machine-scale-set-instancenode"></a>Etapa 2: descobrir a porta que você pode usar para fazer conexão remota com o nó/instância do conjunto de dimensionamento de máquinas virtuais específico
Neste documento, falei sobre como as instâncias de conjunto de dimensionamento de máquinas virtuais mapeiam para os nós. Nós usamos isso para descobrir a porta exata.

As portas são alocadas em ordem crescente de instância do conjunto de dimensionamento de máquinas virtuais. Portanto, em meu exemplo, para o tipo de nó FrontEnd, as portas para cada uma das cinco instâncias são as mostradas a seguir. Agora, você precisa fazer o mesmo mapeamento para a instância do conjunto de dimensionamento de máquinas virtuais.

| **Instância do conjunto de dimensionamento de máquinas virtuais** | **Porta** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

### <a name="step-3-remote-connect-to-the-specific-virtual-machine-scale-set-instance"></a>Etapa 3: conectar-se remotamente à instância do conjunto de dimensionamento de máquinas virtuais específico
Na captura de tela abaixo, usei a conexão de área de trabalho remota para me conectar com o FrontEnd_1:

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Como alterar os valores de intervalo da porta RDP
### <a name="before-cluster-deployment"></a>Antes da implantação de cluster
Quando você estiver configurando o cluster usando um modelo do Resource Manager, poderá especificar o intervalo em **inboundNatPools**.

Vá para a definição de recurso para **Microsoft.Network/loadBalancers**. Lá, você encontra a descrição de **inboundNatPools**.  Substitua os valores *frontendPortRangeStart* e *frontendPortRangeEnd*.

![inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>Depois da implantação de cluster
Após a implantação de cluster isso é um pouco mais complicado e pode resultar na reciclagem das VMs. Defina os novos valores usando o Azure PowerShell. Verifique se o Azure PowerShell versão 1.0 ou posterior está instalado em seu computador. Se você não tem o Azure PowerShell 1.0 ou posterior, sugiro fortemente que você execute as etapas descritas em [Como instalar e configurar o Azure PowerShell.](/powershell/azure/overview)

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

## <a name="how-to-change-the-rdp-username--password-for-nodes"></a>Como alterar o nome de usuário RDP e senha para nós

As etapas a seguir descrevem como alterar a senha para todos os nós de um determinado tipo de nó. Essas alterações serão aplicadas a todos os nós atuais e futuros no conjunto de dimensionamento de máquinas virtuais.

### <a name="step-1-open-powershell-with-elevated-privileges-administrator-mode"></a>Etapa 1: abra o PowerShell com privilégios elevados (modo de administrador). 
### <a name="step-2-run-the-following-commands-to-log-in-and-select-your-subscription-for-the-session-change-the-subscriptionid-parameter-to-your-subscription-id"></a>Etapa 2: execute os comandos a seguir para efetuar logon e selecione sua assinatura para a sessão. Altere o `SUBSCRIPTIONID` parâmetro para sua ID da assinatura. 

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
```

### <a name="step-3-run-the-following-script-with-the-appropriate-nodetypename-resourcegroup-username-and-password-values-the-username-and-password-values-will-be-the-new-credentials-that-should-be-used-in-future-rdp-sessions"></a>Etapa 3: execute o script a seguir com os valores `NODETYPENAME`, `RESOURCEGROUP`, `USERNAME` e `PASSWORD` apropriados. Os valores `USERNAME` e `PASSWORD` serão as novas credenciais que deverão ser usadas em futuras sessões RDP. 

```powershell
$nodeTypeName = 'NODETYPENAME'
$resourceGroup = 'RESOURCEGROUP'
$publicConfig = @{'UserName' = 'USERNAME'}
$privateConfig = @{'Password' = 'PASSWORD'}
$extName = 'VMAccessAgent'
$publisher = 'Microsoft.Compute'
$node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
$node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
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

