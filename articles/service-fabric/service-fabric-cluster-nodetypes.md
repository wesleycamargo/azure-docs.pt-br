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
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# A relação entre os tipos de nó do Service Fabric e os conjuntos de escala da máquina virtual

Os conjuntos de dimensionamento de máquina virtual são um recurso de Computação do Azure que você pode usar para implantar e gerenciar uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó definido em um cluster do Service Fabric é configurado como um Conjunto de Escala de VM separado. Cada tipo de nó pode ser escalado verticalmente para cima ou para baixo de forma independente, tem conjuntos diferentes de portas abertas e pode ter métricas de capacidade diferente.

A captura de tela abaixo mostra um cluster com dois tipos de nó: FrontEnd e BackEnd. Cada tipo de nó tem cinco nós.

![Captura de tela de um cluster com dois tipos de nó][NodeTypes]

## Mapeando instâncias de conjunto de escala de VM para nós

Como você pode ver acima, as instâncias de conjunto de escala da VM começam da instância 0 e vão subindo. A numeração está refletida nos nomes. Por exemplo, BackEnd_0 é a instância 0 do Conjunto de Escala da VM de BackEnd. Esse Conjunto de Escala de VM em particular tem cinco instâncias, chamadas BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando você escala um conjunto de escala de VM verticalmente, uma nova instância é criada. O novo nome da instância do conjunto de escala da VM geralmente será o nome do conjunto de escala da VM mais o número de instância seguinte. Em nosso exemplo, é BackEnd\_5.


## Mapeamento de balanceadores de carga de conjunto de escala da VM para cada tipo de nó/conjunto de escala da VM

Se você tiver implantado o cluster do portal ou usado o modelo do Resource Manager de exemplo que fornecemos, quando obtiver uma lista de todos os recursos em um Grupo de Recursos, verá os balanceadores de carga para cada tipo de nó ou Conjunto de Escala de VM.

O nome seria algo como: **LB-&lt;NodeType name&gt;**. Por exemplo, LB-sfcluster4doc-0, conforme mostrado nesta captura de tela:


![Recursos][Resources]


## Conexão remota a uma instância de conjunto de escala da VM ou a um nó de cluster
Cada tipo de Nó definido em um cluster é configurado como um Conjunto de Escala de VM separado. Isso significa que os tipos de nó podem ser escalados verticalmente para cima ou para baixo de forma independente e podem ser compostos por diferentes SKUs de VM. Ao contrário das máquinas virtuais de instância única, as instâncias de conjunto de escala da VM não recebem um endereço IP virtual próprio. Assim, pode ser complicado procurar um endereço IP e uma porta que você queira usar para fazer a conexão remota com uma instância específica.

Aqui estão as etapas que você pode seguir para descobri-los.

### Etapa 1: descobrir o endereço IP virtual do tipo de nó e as regras NAT de entrada para RDP

Para obtê-lo, você precisa obter os valores de regras NAT de entrada definidos como parte da definição de recurso para **Microsoft.Network/loadBalancers**.

No portal, navegue até a folha Balanceador de carga e então até **Configurações**.

![LBBlade][LBBlade]


Em **Configurações**, clique em **Regras NAT de entrada**. Isso lhe dá o endereço IP e a porta que você pode usar para fazer a conexão remota com a instância de conjunto de escala da VM. Na captura de tela a seguir, é **104.42.106.156** e **3389**

![NATRules][NATRules]

### Etapa 2: descobrir a porta que você pode usar para fazer conexão remota com o nó/instância do conjunto de escala de VM específico

Neste documento, falei sobre como as instâncias de escala da VM mapeiam para os nós. Nós usaremos isso para descobrir a porta exata.

As portas são alocadas em ordem crescente de instância do Conjunto de Escala de VM. Portanto, em meu exemplo, para o tipo de nó FrontEnd, as portas para cada uma das cinco instâncias são as mostradas a seguir. Agora, você precisa fazer o mesmo mapeamento para a instância do Conjunto de Escala de VM.

|**Instância do Conjunto de Escala de VM**|**Porta**|
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

Quando você estiver configurando o cluster usando um modelo do Resource Manager, poderá especificar o intervalo em **inboundNatPools**.

Vá para a definição de recurso para **Microsoft.Network/loadBalancers**. Lá, você encontra a descrição de **inboundNatPools**. Substitua os valores *frontendPortRangeStart* e *frontendPortRangeEnd*.

![InboundNatPools][InboundNatPools]


### Depois da implantação de cluster
Isso é um pouco mais complicado e pode resultar na reciclagem das VMs. Agora, você terá que definir novos valores usando o Azure PowerShell. Verifique se o Azure PowerShell versão 1.0 ou posterior está instalado em seu computador. Se não tiver feito isso antes, sugiro fortemente que você execute as etapas descritas em [Como instalar e configurar o Azure PowerShell.](../powershell-install-configure.md)

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

<!---HONumber=AcomDC_0921_2016-->