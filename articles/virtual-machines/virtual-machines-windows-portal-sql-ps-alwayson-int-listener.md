<properties
   pageTitle="Configurar Ouvintes de Grupos de Disponibilidade AlwaysOn – Microsoft Azure"
   description="Configure os Ouvintes de Grupos de Disponibilidade no modelo do Azure Resource Manager, usando um balanceador de carga interno com um ou mais endereços IP."
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="MikeRayMSFT"/>

# Configurar um ou mais ouvintes grupo de disponibilidade AlwaysOn – Resource Manager 

Este tópico mostra como fazer duas coisas:

- Criar um balanceador de carga interno para grupos de disponibilidade do SQL Server usando cmdlets do PowerShell.

- Adicionar endereços IP adicionais a um balanceador de carga para dar suporte a mais de um grupo de disponibilidade do SQL Server.

> **Importante** A capacidade de atribuir vários endereços IP a um balanceador de carga interno é nova no Azure e está disponível somente no modelo do Resource Manager. Suporte para múltiplos endereços IP em um balanceador de carga interno é um recurso de preview e está sujeito aos termos de preview no contrato de licença (por exemplo, o contrato corporativo, Contrato do Microsoft Azure ou Contrato de Assinatura do Microsoft Online), bem como quaisquer [Termos Complementares de Uso para Preview do Microsoft Azure aplicáveis](http://azure.microsoft.com/support/legal/preview-supplemental-terms/).

No SQL Server, um ouvinte de grupo de disponibilidade é um nome de rede virtual ao qual os clientes se conectam para acessar um banco de dados na réplica primária ou secundária. Em máquinas virtuais do Azure, um balanceador de carga mantém o endereço IP para o ouvinte. O balanceador de carga encaminha o tráfego para a instância do SQL Server que está escutando na porta de investigação. Na maioria dos casos, um grupo de disponibilidade usa um balanceador de carga interno. Um balanceador de carga interno do Azure pode hospedar um ou vários endereços IP. Cada endereço IP usa uma porta de investigação específica. Este documento mostra como usar o PowerShell para criar um novo balanceador de carga ou adicionar endereços IP a um balanceador de carga existente para grupos de disponibilidade do SQL Server.

Para concluir essa tarefa, você precisa ter um grupo de disponibilidade do SQL Server implantado em máquinas virtuais do Azure no modelo do Resource Manager. As máquinas virtuais do SQL Server devem pertencer ao mesmo conjunto de disponibilidade. Você pode usar o [modelo da Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar automaticamente o grupo de disponibilidade no Azure Resource Manager. Este modelo cria automaticamente o grupo de disponibilidade, incluindo o balanceador de carga interno para você. Se preferir, você poderá [configurar manualmente um grupo de disponibilidade AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Este tópico exige que os grupos de disponibilidade já estejam configurados.

Os tópicos relacionados incluem:

 - [Configurar os Grupos de Disponibilidade AlwaysOn na VM do Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
 
 - [Configurar uma conexão de rede virtual com rede virtual usando o PowerShell e o Azure Resource Manager ](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[AZURE.INCLUDE [Iniciar sua sessão do PowerShell](../../includes/sql-vm-powershell.md)]

## Configurar as regras do Firewall do Windows

Configure o Firewall do Windows para permitir acesso ao SQL Server. Você precisará configurar o firewall para permitir conexões TCP com o uso de portas pela instância do SQL Server, bem como a porta usada pela investigação ouvinte. Para obter instruções detalhadas, consulte [Configurar um Firewall do Windows para Acesso ao Mecanismo de Banco de Dados](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Crie uma regra de entrada para a porta do SQL Server e para a porta de investigação.

## Script de exemplo: criar um balanceador de carga interno usando o PowerShell

> [AZURE.NOTE] Se você criou o grupo de disponibilidade com a carga do [modelo Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), não é necessário concluir esta etapa.

O script do PowerShell a seguir cria um balanceador de carga interno, configura regras de balanceamento de carga e define um endereço IP para o balanceador de carga. Para executar o script, abra o ISE do Windows PowerShell e cole o script no respectivo painel. Use `Login-AzureRMAccount` para fazer logon do PowerShell. Se você tiver várias assinaturas do Azure, use `Select-AzureRmSubscription ` para definir a assinatura.

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

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

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

## Script de exemplo: adicionar um endereço IP a um balanceador de carga existente com o PowerShell

Para usar mais de um grupo de disponibilidade, use o PowerShell para adicionar um endereço IP adicional a um balanceador de carga existente. Cada endereço IP requer sua própria regra de balanceamento de carga, porta de investigação e porta de front-end.

A porta de front-end é a que os aplicativos usam para se conectar à instância do SQL Server. Endereços IP para grupos de disponibilidade diferentes podem usar a mesma porta de front-end.

>[AZURE.NOTE] Para grupos de disponibilidade do SQL Server, cada endereço IP exige uma porta de investigação específica. Por exemplo, se um endereço IP em um balanceador de carga usa a porta de investigação 59999, nenhum outro endereço IP nesse balanceador de carga pode usar essa porta de investigação.

- Para obter informações sobre limites do balanceador de carga, veja **IP privado de front-end por balanceador de carga** em [Limites de Rede – Azure Resource Manager](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

- Para obter informações sobre limites de grupo de disponibilidade, veja [Restrições (Grupos de Disponibilidade)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

O script a seguir adiciona um novo endereço IP a um balanceador de carga existente. Atualize as variáveis para o seu ambiente. O ILB usa a porta do ouvinte para a porta de front-end de balanceamento de carga. Essa porta pode ser aquela que o SQL Server está escutando. Para instâncias padrão do SQL Server, essa é a porta 1433. A regra de balanceamento de carga para um grupo de disponibilidade requer um IP flutuante (retorno de servidor direto), de modo que a porta de back-end é igual à porta de front-end.

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



## Configure o cluster para usar o endereço IP do balanceador de carga 

A próxima etapa é configurar o ouvinte no cluster e colocar o ouvinte online. Para realizar esta tarefa, faça o seguinte:

1. Crie o ouvinte do grupo de disponibilidade no cluster de failover

1. Coloque o ouvinte online

## 1\. Crie o ouvinte do grupo de disponibilidade no cluster de failover

Nesta etapa, você adiciona um ponto de acesso do cliente ao cluster de failover com o Gerenciador de Cluster de Failover e, em seguida, usa o PowerShell para configurar o recurso de cluster para escutar na porta de investigação.

- Use o RDP para se conectar à máquina virtual do Azure que hospeda a réplica primária.

- Abra o Gerenciador de Cluster de Failover.

- Selecione o nó **Redes** e observe o nome da rede do cluster. Use esse nome na variável `$ClusterNetworkName` no script do PowerShell.

- Expanda o nome do cluster e, em seguida, clique em **Funções**.

- No painel **Funções**, clique com o botão direito do mouse no nome do grupo de disponibilidade e, em seguida, selecione**Adicionar recurso**>**Ponto de acesso para o cliente**.

- Na caixa **Nome**, crie um nome para este novo ouvinte e clique em **Próximo** duas vezes e, em seguida, clique em**Concluir**. Não coloque o ouvinte ou o recurso online neste momento.

 >[AZURE.NOTE] O nome para o novo ouvinte é o nome da rede que o aplicativo usará para se conectar aos bancos de dados no grupo de disponibilidade do SQL Server.

- Clique a guia **recursos**, em seguida, expanda o ponto de acesso do cliente que você acabou de criar. Clique com o botão direito do mouse no recurso de IP e clique em Propriedades. Observe o nome do endereço IP. Você usará esse nome na variável `$IPResourceName` no script do PowerShell.

- Em **Endereço IP**, clique em **Endereço IP estático** e defina-o para o mesmo endereço que você usou ao definir o endereço IP do balanceador de carga no Portal do Azure.

- Desabilite o NetBIOS para este endereço e clique em **OK**. Repita essa etapa para cada recurso IP se sua solução abrange diversas VNets do Azure.

- Torne o recurso de grupo de disponibilidade do SQL Server dependente do endereço IP. Clique com o botão direito do mouse no recurso no gerenciador de cluster, que está na guia **Recursos** em **Outros Recursos**.

- No nó de cluster que hospeda a réplica primária, abra um ISE do PowerShell ISE elevado e cole os seguintes comandos em um novo script. Na guia **Dependências**, clique no nome do ouvinte.
        
    ```powershell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>

    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

- Atualize as variáveis e execute o script do PowerShell para configurar o endereço IP e a porta para o novo ouvinte.

 >[AZURE.NOTE] Se seus servidores SQL estiverem em regiões separadas, você precisará executar o script do PowerShell duas vezes. Na primeira vez, use o nome de rede de cluster, o nome do recurso IP do cluster e o endereço IP do balanceador de carga do primeiro grupo de recursos. Na segunda vez, use o nome de rede de cluster, o nome do recurso IP do cluster e o endereço IP do balanceador de carga do segundo grupo de recursos.

Agora o cluster tem um recurso de ouvinte do grupo de disponibilidade.

## 2\. Coloque o ouvinte online

Com o recurso de ouvinte do grupo de disponibilidade configurado, você pode colocar o ouvinte online para que os aplicativos possam se conectar aos bancos de dados no grupo de disponibilidade com o ouvinte.

- Navegue de volta ao Gerenciador de Cluster de Failover. Expanda**funções**e realce seu grupo de disponibilidade. Na guia **Recursos**, clique com o botão direito do mouse no nome do ouvinte e clique em **Propriedades**.

- Selecione a guia **Dependências**. Se houver vários recursos listados, verifique se os endereços IP têm dependências OR, e não AND. Clique em **OK**.

- Clique com o botão direito do mouse no nome do ouvinte e clique em **Trazer online**.


- Quando o ouvinte estiver online, da guia **recursos**, clique com o botão direito do mouse no grupo de disponibilidade e clique em**propriedades**.

- Crie uma dependência no recurso de nome de ouvinte (não o nome de recursos de endereço IP). Clique em **OK**.


- Inicie o SQL Server Management Studio e conecte-se à réplica principal.


- Navegue até **Alta Disponibilidade do AlwaysOn** | **Grupos de Disponibilidade** | **Ouvintes do Grupo de Disponibilidade**.


- Agora você deve ver o nome do ouvinte que você criou no Gerenciador de Cluster de Failover. Clique com o botão direito do mouse no nome do ouvinte e clique em **Propriedades**.


- Na caixa **Porta**, especifique o número da porta para o ouvinte do grupo de disponibilidade usando o $EndpointPort usado anteriormente (1433 era o padrão) e depois clique em **OK**.

Agora você tem um grupo de disponibilidade do SQL Server em máquinas virtuais do Azure, em execução no modo de Resource Manager.

## Testar a conexão com o ouvinte

Para testar a conexão:

1. RDP para um SQL Server que está na mesma rede virtual, mas não tem a réplica. Isso pode ser outro SQL Server no cluster.

1. Use o utilitário **sqlcmd** para testar a conexão. Por exemplo, o script a seguir estabelece uma conexão de **sqlcmd** com a réplica primária por meio do ouvinte com autenticação do Windows:

        sqlmd -S <listenerName> -E

    Se o ouvinte estiver usando uma porta diferente da porta padrão (1433), especifique a porta na cadeia de conexão. Por exemplo, o comando sqlcmd a seguir conecta-se a um ouvinte na porta 1435:
    
        sqlcmd -S <listenerName>,1435 -E

A conexão SQLCMD se conecta automaticamente a qualquer instância do SQL Server que hospede a réplica primária.

>[AZURE.NOTE] Verifique se a porta especificada está aberta no firewall dos servidores SQL. Ambos os servidores exigem uma regra de entrada para a porta TCP que você usa. Veja [Adicionar ou Editar Regra de Firewall](http://technet.microsoft.com/library/cc753558.aspx) para obter mais informações.

## Diretrizes e limitações

Observe as diretrizes a seguir no ouvinte do grupo de disponibilidade no Azure usando o balanceador de carga interno:

- Com um balanceador de carga interno, você só pode acessar ao ouvinte de dentro da mesma rede virtual.

## Para obter mais informações

Para obter mais informações, veja [Configurar grupos de disponibilidade AlwaysOn na VM do Azure manualmente](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

### Cmdlets do PowerShell

Use os cmdlets do PowerShell a seguir para criar um balanceador de carga interno para máquinas virtuais do Azure.

- `New-AzureRmLoadBalancer` cria um balanceador de carga. Veja [New-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) para obter mais informações.

- `New-AzureRMLoadBalancerFrontendIpConfig` cria uma configuração de IP front-end para um balanceador de carga. Veja [New-AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) para obter mais informações.

- `New-AzureRmLoadBalancerRuleConfig` cria uma configuração de regra para um balanceador de carga. Veja [New-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) para obter mais informações.

- `New-AzureRMLoadBalancerBackendAddressPoolConfig` cria uma configuração de pool de endereços de back-end para um balanceador de carga. Veja [New-AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) para obter mais informações.

- `New-AzureRmLoadBalancerProbeConfig` cria uma configuração de investigação para um balanceador de carga. Veja [New-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) para obter mais informações.

Se você precisar remover um balanceador de carga de um grupo de recursos do Azure, deverá usar `Remove-AzureRmLoadBalancer`. Para obter mais informações, veja [Remove-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx).

<!---HONumber=AcomDC_0928_2016-->