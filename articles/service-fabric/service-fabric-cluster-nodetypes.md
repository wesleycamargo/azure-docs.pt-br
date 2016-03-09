<properties
   pageTitle="Tipos de nós do Service Fabric e conjuntos de escala da VM | Microsoft Azure"
   description="Descreve como os tipos de nó do Service Fabric se relacionam com os conjuntos de escala da VM e como fazer a conexão remota com uma instância de conjunto de escala da VM ou um nó de cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/17/2016"
   ms.author="chackdan"/>


# A relação entre os tipos de nó do Service Fabric e os conjuntos de escala da máquina virtual

Os conjuntos de dimensionamento de máquina virtual são um recurso de Computação do Azure que você pode usar para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Service Fabric está configurado como um conjunto de escala da VM separado. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente.

A captura de tela abaixo mostra um cluster com dois tipos de nó: FrontEnd e BackEnd. Cada tipo de nó tem cinco nós.

![Captura de tela de um cluster com dois tipos de nó][NodeTypes]

## Mapeando instâncias de conjunto de escala de VM para nós

Como você pode ver acima, as instâncias de conjunto de escala da VM começam da instância 0 e vão subindo. A numeração está refletida nos nomes. Por exemplo, BackEnd\_0 é a instância 0 do conjunto de escala da VM de BackEnd. Esse conjunto de escala da VM específico tem cinco instâncias, chamadas BackEnd\_0, BackEnd\_1, BackEnd\_2, BackEnd\_3 e BackEnd\_4.

Quando você escala um conjunto de escala de VM verticalmente, uma nova instância é criada. O novo nome da instância do conjunto de escala da VM geralmente será o nome do conjunto de escala da VM mais o número de instância seguinte. Em nosso exemplo, ele será BackEnd\_5.


## Mapeamento de balanceadores de carga de conjunto de escala da VM para cada tipo de nó/conjunto de escala da VM

Se você implantou o cluster do portal ou usou o modelo ARM de exemplo que fornecemos, quando obtiver uma lista de todos os recursos em um grupo de recursos, verá os balanceadores de carga para cada conjunto de escala da VM ou tipo de nó.

O nome seria algo como: **LB-&lt;NodeType name&gt;**. Por exemplo, LB-sfcluster4doc-0, conforme mostrado nesta captura de tela:


![Recursos][Resources]


## Conexão remota a uma instância de conjunto de escala da VM ou a um nó de cluster
Cada tipo de nó que é definido em um cluster está configurado como um conjunto de escala da VM separado. Isso significa que os tipos de nó podem ser escalados verticalmente para cima ou para baixo de forma independente e podem ser compostos por diferentes SKUs de VM. Ao contrário das máquinas virtuais de instância única, as instâncias de conjunto de escala da VM não recebem um endereço IP virtual próprio. Assim, pode ser complicado procurar um endereço IP e uma porta que você queira usar para fazer a conexão remota com uma instância específica.

Aqui estão as etapas que você pode seguir para descobri-los.

### Etapa 1: descobrir o endereço IP virtual do tipo de nó e as regras NAT de entrada para RDP

Para obtê-lo, você precisa obter os valores de regras NAT de entrada que foram definidos como parte da definição de recurso de **Microsoft.Network/loadBalancers**.

No portal, navegue até a folha Balanceador de carga e até **Configurações**.

![LBBlade][LBBlade]


Em **Configurações**, clique em **Regras NAT de entrada**. Isso lhe dá o endereço IP e a porta que você pode usar para fazer a conexão remota com a instância de conjunto de escala da VM. Na captura de tela abaixo, é **104.42.106.156** e **3389**

![NATRules][NATRules]

### Etapa 2: descobrir a porta que você pode usar para fazer conexão remota com o nó/instância do conjunto de escala de VM específico

Neste documento, falei sobre como as instâncias de escala da VM mapeiam para os nós. Nós usaremos isso para descobrir a porta exata.

As portas são alocadas em ordem crescente de instância do conjunto de escala da VM. Portanto, no meu exemplo, para o tipo de nó FrontEnd, as portas para cada uma das cinco instâncias serão as mostradas abaixo. Agora, você precisa fazer o mesmo mapeamento para a sua instância do conjunto de escala da VM.

|**Instância VMSS**|**Porta**|
|-----------------------|--------------------------|
|FrontEnd\_0|3389|
|FrontEnd\_1|3390|
|FrontEnd\_2|3391|
|FrontEnd\_3|3392|
|FrontEnd\_4|3393|
|FrontEnd\_5|3394|


### Etapa 3: conectar-se remotamente à instância do conjunto de escala da VM específica

Na captura de tela abaixo, usei a conexão de área de trabalho remota para me conectar com o FrontEnd\_1:

![RDP][RDP]

## Como alterar os valores de intervalo da porta RDP

### Antes da implantação de cluster

Quando você estiver configurando o cluster usando um modelo do ARM, poderá especificar o intervalo em **inboundNatPools**.

Vá para a definição de recurso a fim de obter **Microsoft.Network/loadBalancers**. Lá, você encontrará a descrição de **inboundNatPools**. Substitua os valores *frontendPortRangeStart* e *frontendPortRangeEnd*.

![InboundNatPools][InboundNatPools]


### Depois da implantação de cluster
Isso é um pouco mais complicado e pode resultar na reciclagem das VMs. Agora, você terá que definir novos valores usando o Azure PowerShell. Verifique se o Azure PowerShell versão 1.0 ou posterior está instalado em seu computador. Se não tiver feito isso antes, sugiro veementemente que você execute as etapas descritas em [Como instalar e configurar o Azure PowerShell.](../powershell-install-configure.md)

Entre na sua conta do Azure. Se o comando do PowerShell falhar por algum motivo, verifique se o Azure PowerShell foi instalado corretamente.

```
Login-AzureRmAccount
```

Execute o seguinte para obter detalhes sobre o balanceador de carga e você encontrará a descrição de **inboundNatPools**:

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

Agora defina *frontendPortRangeEnd* e *frontendPortRangeStart* como os valores desejados.

```
$PropertiesObject = @{
	#Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## Próximas etapas

- [Visão geral do recurso "Implantar em qualquer lugar" e comparação com clusters gerenciados do Azure](service-fabric-deploy-anywhere.md)
- [Segurança de cluster](service-fabric-cluster-security.md)
- [ SDK do Service Fabric e introdução](service-fabric-get-started.md)


<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

<!---HONumber=AcomDC_0224_2016-->