<properties
   pageTitle="Abrir portas para uma VM usando o PowerShell | Microsoft Azure"
   description="Saiba como abrir uma porta/criar um ponto de extremidade para sua VM do Windows usando o modelo de implantação do Azure Resource Manager e o Azure PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="iainfou"/>

# Abrir portas para uma VM no Azure usando PowerShell
[AZURE.INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## Comandos rápidos
Para criar um Grupo de Segurança de Rede e as regras de ACL, você precisa [ter a versão mais recente do Azure PowerShell instalada](../powershell-install-configure.md). Você também pode [executar essas etapas usando o Portal do Azure](virtual-machines-windows-nsg-quickstart-portal.md).

Primeiro, você precisa criar uma regra para permitir o tráfego de HTTP na porta TCP 80, inserindo seu próprio nome e a descrição:

```
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name http-rule -Description "Allow HTTP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

Em seguida, crie seu Grupo de Segurança de Rede e atribua a regra de HTTP que você acabou de criar da seguinte forma, inserindo seu próprio nome do grupo de recursos e a localização:

```
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `
    -Name "TestNSG" -SecurityRules $httprule
```

Agora, vamos atribuir seu Grupo de Segurança de Rede a uma sub-rede. Primeiro, selecione a rede virtual:

```
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
```

Associe seu Grupo de Segurança de Rede à sua sub-rede:

```
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet `
    -NetworkSecurityGroup $nsg
```

Por fim, atualize sua rede virtual para que as alterações entrem em vigor:

```
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## Mais informações sobre os Grupos de Segurança de Rede
Os comandos rápidos aqui permitem que você coloque tudo em funcionamento com o tráfego que flui para sua VM. Os Grupos de Segurança de Rede fornecem muitos recursos excelentes e granularidade para controlar o acesso aos recursos. Você pode ler mais sobre a [criação de um Grupo de Segurança de Rede e as regras ACL aqui](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Você pode definir Grupos de Segurança de Rede e regras de ACL como parte dos modelos do Azure Resource Manager. Leia mais sobre a [criação de Grupos de Segurança de Rede com modelos](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se precisar usar o encaminhamento de porta para mapear uma porta externa exclusiva para uma porta interna em sua VM, use um balanceador de carga e regras de NAT (Conversão de Endereços de Rede). Por exemplo, talvez você queira expor a porta TCP 8080 externamente e direcionar o tráfego para a porta TCP 80 em uma VM. Você pode aprender sobre a [criação de um balanceador de carga para a Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

- [Visão geral do Gerenciador de Recursos do Azure](../resource-group-overview.md)
- [O que é um NSG (grupo de segurança de rede)?](../virtual-network/virtual-networks-nsg.md)
- [Visão Geral do Azure Resource Manager para Balanceadores de Carga](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0907_2016-->