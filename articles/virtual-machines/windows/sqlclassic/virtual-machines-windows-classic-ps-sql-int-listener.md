---
title: Configurar um ouvinte de ILB para grupos de disponibilidade AlwaysOn | Microsoft Docs
description: "Este tutorial usa os recursos criados com o modelo de implantação clássico e cria um ouvinte para grupo de disponibilidade AlwaysOn no Azure usando um ILB (Balanceador de Carga Interno)."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/01/2017
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 0c23ee550d8ac88994e8c7c54a33d348ffc24372
ms.openlocfilehash: 8e59988f24748a82d4e143295bab9bdaa65cf8e4
ms.lasthandoff: 01/11/2017


---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Configurar um ouvinte de ILB para grupos de disponibilidade AlwaysOn no Azure
> [!div class="op_single_selector"]
> * [Ouvinte interno](virtual-machines-windows-classic-ps-sql-int-listener.md)
> * [Ouvinte externo](virtual-machines-windows-classic-ps-sql-ext-listener.md)
> 
> 

## <a name="overview"></a>Visão geral
Este tópico mostra como configurar um ouvinte para um grupo de disponibilidade AlwaysOn usando um **ILB (Balanceador de carga interno)**.

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

Para configurar um ouvinte de ILB para um grupo de disponibilidade AlwaysOn no modelo do Resource Manager, veja [Configurar um balanceador de carga interno para um grupo de disponibilidade AlwaysOn no Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

O seu grupo de disponibilidade pode conter somente réplicas locais, somente no Azure ou locais e no Azure para configurações híbridas. As réplicas do Azure podem residir na mesma região ou em várias regiões usando várias redes virtuais (VNets). As etapas a seguir pressupõem que você já tenha [configurado um grupo de disponibilidade](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md), mas não configurou um ouvinte.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Diretrizes e limitações para ouvintes internos
Observe as seguintes diretrizes no ouvinte do grupo de disponibilidade no Azure usando o ILB:

* Há suporte para o ouvinte do grupo de disponibilidade no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* Há suporte a somente um ouvinte de grupo de disponibilidade que tem suporte por cada serviço de nuvem, porque o ouvinte é configurado ao ILB, e há apenas um ILB por serviço de nuvem. No entanto, é possível criar vários ouvintes externos. Para obter mais informações, veja [Configurar um ouvinte externo para grupos de disponibilidade AlwaysOn no Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Determine a acessibilidade do ouvinte
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo se concentra em criar um ouvinte que usa um **Balanceador de carga interno (ILB)**. Se você precisar de um ouvinte externo/público, consulte a versão deste artigo que fornece as etapas para configurar um [ouvinte externo](virtual-machines-windows-classic-ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar pontos de extremidade da VM com balanceamento de carga com retorno de servidor direto
Para o ILB, você deve criar primeiro o balanceador de carga interno. Isso é feito no script a seguir.

Você deve criar um ponto de extremidade de carga equilibrada para cada VM que hospeda uma réplica do Azure. Se você tiver réplicas em várias regiões, cada réplica para essa região deve estar no mesmo serviço de nuvem no mesmo VNet. Criação de réplicas do Grupo de Disponibilidade que abrangem várias regiões do Azure requer configurar diversas VNets. Para saber mais sobre como configurar através de conectividade VNet cruzada, consulte [Configurar conectividade VNet para VNet](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. No portal do Azure, navegue até cada VM hospedando uma réplica e visualize os detalhes.
2. Clique na guia **Pontos de extremidade** para cada uma das VMs.
3. Verifique se o **Nome** e a **Porta Pública** do ponto de extremidade do ouvinte que você deseja usar não esteja em uso. No exemplo abaixo, o nome é "MyEndpoint" e a porta é "1433".
4. No seu cliente local, baixe e instale [o último módulo do PowerShell](https://azure.microsoft.com/downloads/).
5. Inicie o **PowerShell do Azure**. Uma nova sessão do PowerShell é aberta com os módulos administrativos do Azure carregados.
6. Executar **Get-AzurePublishSettingsFile**. Esse cmdlet direciona você a um navegador para baixar um arquivo de configurações de publicação para um diretório local. É possível que seja solicitado para inserir as suas credenciais para a assinatura do Aure.
7. Execute o comando **Import-AzurePublishSettingsFile** com o caminho do arquivo de configurações de publicação que você baixou:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Após o arquivo de configurações de publicação tiver sido importado, você pode gerenciar sua assinatura do Azure na sessão do PowerShell.
    
1. Para o **ILB**, atribua um endereço IP estático. Em primeiro lugar, examine a configuração atual de VNet executando o seguinte comando:
   
        (Get-AzureVNetConfig).XMLConfiguration
2. Anote o nome da **Sub-rede** que contém as VMs que hospedam as réplicas. Ele será usado no parâmetro **$SubnetName** no script.
3. Em seguida, anote o nome do **VirtualNetworkSite** e do **AddressPrefix** inicial da sub-rede que contém as VMs que hospedam as réplicas. Procure um endereço IP disponível, passando os dois valores para o comando **Test-AzureStaticVNetIP** e examinando os **AvailableAddresses**. Por exemplo, se a VNet fosse chamada de *MyVNet* e tivesse um intervalo de endereços de sub-rede iniciado em *172.16.0.128*, o comando a seguir listaria os endereços disponíveis:
   
        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
4. Escolha um dos endereços disponíveis e use-o no parâmetro **$ILBStaticIP** do script a seguir.
5. Copie o script do PowerShell abaixo em um editor de texto e defina os valores de variáveis para se adequar ao seu ambiente (Observe que os padrões foram fornecidos para alguns parâmetros). Observe que as implantações existentes que usam grupos de afinidade não podem adicionar ILB. Para saber mais sobre os requisitos do ILB, consulte [Balanceador de Carga Interno](../../../load-balancer/load-balancer-internal-overview.md). Além disso, se o seu grupo de disponibilidade abrange regiões do Azure, você deve executar o script uma vez em cada datacenter para o serviço de nuvem e os nós que residem naquele datacenter.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value
   
        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP
   
        # Configure a load balanced endpoint for each node in $AGNodes using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }
6. Depois de ter definido as variáveis, copie o script do editor de texto para a sua sessão do Azure PowerShell para executá-lo. Se o prompt ainda mostrar >>, digite ENTER novamente para certificar-se de que o script comece a ser executado. Observação

> [!NOTE]
> O portal clássico do Azure não dá suporte ao balanceador de carga interno neste momento. Assim, você não verá o ILB ou os pontos de extremidade no portal clássico do Azure. No entanto, **Get-AzureEndpoint** retorna um endereço IP interno se o Balanceador de Carga estiver sendo executado nele. Caso contrário, retornará null.
> 
> 

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Verifique se KB2854082 está instalado, se necessário
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abra as portas de firewall em nós do grupo de disponibilidade
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Crie o ouvinte do grupo de disponibilidade

Crie o ouvinte do grupo de disponibilidade em duas etapas. Primeiro, crie o recurso de cluster de ponto de acesso do cliente e configure as dependências. Depois disso, configure os recursos de cluster com o PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Criar o ponto de acesso de cliente e configurar as dependências do cluster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configurar os recursos de cluster no PowerShell
1. Para o ILB, você deve usar o endereço IP do Balanceador de carga interno (ILB) criado anteriormente. Use o script a seguir para obter esse endereço IP no PowerShell.
   
        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress
2. Em uma das VMs, copie o script do PowerShell de seu sistema operacional em um editor de texto e defina as variáveis para os valores que você anotou anteriormente.
   
    Para o Windows Server 2012 ou superior, use o seguinte script:
   
        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)
   
        Import-Module FailoverClusters
   
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   
    Para o Windows Server 2008 R2, use o seguinte script:
   
        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)
   
        Import-Module FailoverClusters
   
        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255
3. Assim que você tiver definido as variáveis, abra uma janela elevada do Windows PowerShell, depois copie o script do editor de texto e cole-o na sua sessão do Azure PowerShell para executá-lo. Se o prompt ainda mostrar >>, digite ENTER novamente para certificar-se de que o script comece a ser executado.
4. Repita esse procedimento em cada VM. Esse script configura o recurso de endereço IP com o endereço IP do serviço de nuvem e define outros parâmetros como a porta de investigação. Quando o recurso de endereço IP é colocado online, ele pode responder à sondagem na porta de investigação do ponto de extremidade com balanceamento de carga criado anteriormente neste tutorial.

## <a name="bring-the-listener-online"></a>Coloque o ouvinte online
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de acompanhamento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testar o ouvinte do grupo de disponibilidade (na mesma VNet)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]


