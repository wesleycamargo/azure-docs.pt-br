---
title: "Configurar Ouvintes de Grupos de Disponibilidade AlwaysOn – Microsoft Azure | Microsoft Docs"
description: "Configure os Ouvintes de Grupos de Disponibilidade no modelo do Azure Resource Manager, usando um balanceador de carga interno com um ou mais endereços IP."
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/28/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 1430807db46326779866f57bca3982e5f9448951
ms.lasthandoff: 03/07/2017


---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Configurar um ou mais ouvintes grupo de disponibilidade AlwaysOn – Resource Manager
Este tópico mostra como:

* Criar um balanceador de carga interno para grupos de disponibilidade do SQL Server usando cmdlets do PowerShell.
* Adicionar mais endereços IP a um balanceador de carga para mais de um grupo de disponibilidade. 

Um ouvinte de grupo de disponibilidade é um nome de rede virtual à qual os clientes se conectam para acessar o banco de dados. Em máquinas virtuais do Azure, um balanceador de carga mantém o endereço IP para o ouvinte. O balanceador de carga encaminha o tráfego para a instância do SQL Server que está escutando na porta de investigação. Normalmente, um grupo de disponibilidade usa um balanceador de carga interno. Um balanceador de carga interno do Azure pode hospedar um ou vários endereços IP. Cada endereço IP usa uma porta de investigação específica. Este documento mostra como usar o PowerShell para criar um balanceador de carga ou adicionar endereços IP a um balanceador de carga existente para grupos de disponibilidade do SQL Server. 

A capacidade de atribuir vários endereços IP a um balanceador de carga interno é nova no Azure e está disponível somente no modelo do Resource Manager. Para concluir essa tarefa, você precisa ter um grupo de disponibilidade do SQL Server implantado em máquinas virtuais do Azure no modelo do Resource Manager. As máquinas virtuais do SQL Server devem pertencer ao mesmo conjunto de disponibilidade. Você pode usar o [modelo da Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar automaticamente o grupo de disponibilidade no Azure Resource Manager. Este modelo cria automaticamente o grupo de disponibilidade, incluindo o balanceador de carga interno para você. Se preferir, você poderá [configurar manualmente um grupo de disponibilidade AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Este tópico exige que os grupos de disponibilidade já estejam configurados.  

Os tópicos relacionados incluem:

* [Configurar os Grupos de Disponibilidade AlwaysOn na VM do Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Configurar uma conexão de rede virtual com rede virtual usando o PowerShell e o Azure Resource Manager](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Configurar as regras do Firewall do Windows
Configure o Firewall do Windows para permitir acesso ao SQL Server. As regras de firewall permitem conexões TCP com as portas por instância do SQL Server, bem como a investigação do ouvinte. Para obter instruções detalhadas, confira [Configurar um Firewall do Windows para acesso ao Mecanismo de Banco de Dados](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Crie uma regra de entrada para a porta do SQL Server e para a porta de investigação.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Script de exemplo: criar um balanceador de carga interno usando o PowerShell
> [!NOTE]
> Se você criou o grupo de disponibilidade com o [modelo Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), o balanceador de carga interno já foi criado. 
> 
> 

O script do PowerShell a seguir cria um balanceador de carga interno, configura regras de balanceamento de carga e define um endereço IP para o balanceador de carga. Para executar o script, abra o ISE do Windows PowerShell e cole o script no respectivo painel. Use `Login-AzureRMAccount` para fazer logon no PowerShell. Se você tiver várias assinaturas do Azure, use `Select-AzureRmSubscription ` para definir a assinatura. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a> Script de exemplo: adicionar um endereço IP a um balanceador de carga existente com o PowerShell
Para usar mais de um grupo de disponibilidade, inclua um endereço IP adicional ao balanceador de carga. Cada endereço IP requer sua própria regra de balanceamento de carga, porta de investigação e porta de front-end.

A porta de front-end é a que os aplicativos usam para se conectar à instância do SQL Server. Endereços IP para grupos de disponibilidade diferentes podem usar a mesma porta de front-end.

> [!NOTE]
> Para grupos de disponibilidade do SQL Server, cada endereço IP exige uma porta de investigação específica. Por exemplo, se um endereço IP em um balanceador de carga usa a porta de investigação 59999, nenhum outro endereço IP nesse balanceador de carga pode usar essa porta de investigação.

* Para saber mais sobre os limites do balanceador de carga, confira **IP privado front-end por balanceador de carga** em [Limites de Rede – Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Para saber mais sobre os limites de grupo de disponibilidade, confira [Restrições (Grupos de Disponibilidade)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

O script a seguir adiciona um novo endereço IP a um balanceador de carga existente. O ILB usa a porta do ouvinte para a porta de front-end do balanceamento de carga. Essa porta pode ser aquela que o SQL Server está escutando. Para instâncias padrão do SQL Server, a porta é a 1433. A regra de balanceamento de carga para um grupo de disponibilidade requer um IP flutuante (retorno de servidor direto), de modo que a porta de back-end é igual à porta de front-end. Atualize as variáveis para o seu ambiente. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```

## <a name="configure-the-listener"></a>Configurar o ouvinte

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]


<!------------------------------- The content below is duplicated. Pointing to the link. Thinking about an include. 

## Configure the cluster to use the load balancer IP address
The next step is to configure the listener on the cluster, and bring the listener online. To accomplish this, do the following: 

1. Create the availability group listener on the failover cluster  
2. Bring the listener online

## Create the availability group listener on the failover cluster

The availability group listener is an IP address and network name that the SQL Server availability group listens on. To create the availability group listener, do the following steps:

1. [Get the name of the cluster network resource](#getnet).

1. [Add the client access point](#addcap).

1. [Configure the IP resource for the availability group](#congroup).

1. [Make the availability group resource dependent on the listener resource name](#listname).

1. [Set the cluster parameters in PowerShell](#setparam).

The following sections provide detailed instructions for each of these steps. 

### <a name="getnet">Get the name of the cluster network resource</a> 

1. Use RDP to connect to the Azure virtual machine that hosts the primary replica. 

1. Open Failover Cluster Manager.

1. Select the **Networks** node, and note the cluster network name. Use this name in the `$ClusterNetworkName` variable in the PowerShell script.

### <a name="addcap">Add the client access point</a>

1. Expand the cluster name, and then click **Roles**.

1. In the **Roles** pane, right-click the availability group name and then select **Add Resource** > **Client Access Point**.

1. In the **Name** box, create a name for this new listener. 

   The name for the new listener is the network name that applications will use to connect to databases in the SQL Server availability group.
   
   To finish creating the listener, click **Next** twice, and then click **Finish**. Do not bring the listener or resource online at this point.
   
### <a name="congroup">Configure the IP resource for the availability group</a>

1. Click the **Resources** tab, then expand the Client Access Point you just created. Right-click the IP resource and click properties. Note the name of the IP address. You will use this name in the `$IPResourceName` variable in the PowerShell script.

1. Under **IP Address** click **Static IP Address** and set the static IP address to the same address that you used when you set the load balancer IP address on the Azure portal. 

1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 

### <a name="listname">Make the availability group resource dependent on the listener resource</a>

1. In Failover Cluster Manager click **Roles** and click your Availability Group. 

1. On the **Resources** tab, right-click the availability resource group and click **Properties**. 

1. Click the **Dependencies** tab. Set a dependency on the listener resource name. If there are multiple resources listed, verify that the IP addresses have OR, not AND, dependencies. Click **OK**. 

1. Right-click the listener name and click **Bring Online**. 

### <a name="setparam">Set the cluster parameters in PowerShell</a>

Set the cluster parameters. To do this, update the following PowerShell script. Set the variables with the values for your environment. Run the PowerShell script on one of the cluster nodes.  
    
   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```
> [!NOTE]
> If your SQL Servers are in separate regions, you need to run the PowerShell script twice. The first time use the `$ILBIP` and `$ProbePort` from the first region. The second time, use the `$ILBIP` and `$ProbePort` from the second region. The cluster network name, and the cluster IP resource name are the same. 

## Set the listener port in SQL Server Management Studio

1. Launch SQL Server Management Studio and connect to the primary replica.

1. Navigate to **AlwaysOn High Availability** | **Availability Groups** | **Availability Group Listeners**. 

1. You should now see the listener name that you created in Failover Cluster Manager. Right-click the listener name and click **Properties**.

1. In the **Port** box, specify the port number for the availability group listener by using the $EndpointPort you used earlier (1433 was the default), then click **OK**.

You now have a SQL Server availability group in Azure virtual machines running in Resource Manager mode. 
-------------------------------->

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>Definir a porta do ouvinte no SQL Server Management Studio

1. Inicie o SQL Server Management Studio e conecte-se à réplica principal.

1. Navegue até **Alta Disponibilidade do AlwaysOn** | **Grupos de Disponibilidade** | **Ouvintes do Grupo de Disponibilidade**. 

1. Agora você deve ver o nome do ouvinte que você criou no Gerenciador de Cluster de Failover. Clique com o botão direito do mouse no nome do ouvinte e clique em **Propriedades**.

1. Na caixa **Porta**, especifique o número da porta para o ouvinte do grupo de disponibilidade usando o $EndpointPort usado anteriormente (1433 era o padrão) e depois clique em **OK**.

## <a name="test-the-connection-to-the-listener"></a>Testar a conexão com o ouvinte

Para testar a conexão:

1. RDP para um SQL Server que está na mesma rede virtual, mas não tem a réplica. Isso pode ser outro SQL Server no cluster.

1. Use o utilitário **sqlcmd** para testar a conexão. Por exemplo, o script a seguir estabelece uma conexão de **sqlcmd** com a réplica primária por meio do ouvinte com autenticação do Windows:
   
    ```
    sqlmd -S <listenerName> -E
    ```
   
    Se o ouvinte estiver usando uma porta diferente da porta padrão (1433), especifique a porta na cadeia de conexão. Por exemplo, o comando sqlcmd a seguir conecta-se a um ouvinte na porta 1435: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

A conexão SQLCMD se conecta automaticamente a qualquer instância do SQL Server que hospede a réplica primária. 

> [!NOTE]
> Verifique se a porta especificada está aberta no firewall dos servidores SQL. Ambos os servidores exigem uma regra de entrada para a porta TCP que você usa. Veja [Adicionar ou Editar Regra de Firewall](http://technet.microsoft.com/library/cc753558.aspx) para obter mais informações. 
> 
> 

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações
Observe as diretrizes a seguir no ouvinte do grupo de disponibilidade no Azure usando o balanceador de carga interno:

* Com um balanceador de carga interno, você só pode acessar ao ouvinte de dentro da mesma rede virtual.


## <a name="for-more-information"></a>Para obter mais informações
Para saber mais, confira [Configure Always On availability group in Azure VM manually](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md) (Configurar grupo de disponibilidade Always On na VM do Azure manualmente).

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell
Use os cmdlets do PowerShell a seguir para criar um balanceador de carga interno para máquinas virtuais do Azure.

* [New-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) cria um balanceador de carga. 
* [New-AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) cria uma configuração de IP de front-end para um balanceador de carga. 
* [New-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) cria uma configuração de regra para um balanceador de carga. 
* [New-AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) cria uma configuração de pool de endereços de back-end para um balanceador de carga. 
* [New-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) cria uma configuração de investigação para um balanceador de carga.
* [Remove-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx) remove um balanceador de carga de um grupo de recursos do Azure.

