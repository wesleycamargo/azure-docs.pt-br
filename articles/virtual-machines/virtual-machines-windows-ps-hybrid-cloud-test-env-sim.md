---
title: "Ambiente de teste de nuvem híbrida simulado | Microsoft Docs"
description: "Crie um ambiente de nuvem híbrida simulado para testes profissionais de TI ou testes de desenvolvimento, usando duas redes virtuais do Azure e uma conexão Rede Virtual para Rede Virtual."
services: virtual-machines-windows
documentationcenter: 
author: JoeDavies-MSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ca5bf294-8172-44a9-8fed-d6f38d345364
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: josephd
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 1322af70f643bcc191e3d69fe4a174adc9cd7e8d


---
# <a name="set-up-a-simulated-hybrid-cloud-environment-for-testing"></a>Configurar um ambiente de nuvem híbrida simulado para testes
Este artigo explica como criar um ambiente simulado de nuvem híbrida com o Microsoft Azure usando duas redes virtuais do Azure. Veja abaixo a configuração resultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

Isso simula um ambiente de produção de nuvem híbrida e consiste em:

* Uma rede simulada e simplificada local, hospedada em uma rede virtual do Azure (a rede virtual TestLab).
* Uma rede virtual simulada entre locais, hospedada no Azure (TestVNET).
* Uma conexão VNet para VNet entre as duas redes virtuais.
* Um controlador de domínio secundário na rede virtual TestVNET.

Isto proporciona uma base e um ponto de partida comum com os quais é possível:

* Desenvolver e testar aplicativos em um ambiente de nuvem híbrida simulado.
* Criar configurações de teste de computadores, alguns dentro da rede virtual TestLab e outros na rede virtual TestVNET, para simular cargas de TI baseadas em nuvem híbrida.

Há quatro fases principais para configurar esse ambiente de teste de nuvem híbrida:

1. Configurar a rede virtual TestLab.
2. Criar a rede virtual entre locais.
3. Criar a conexão VPN de VNet para VNet.
4. Configurar o DC2. 

Essa configuração exige uma assinatura do Azure. Se você tiver uma assinatura do MSDN ou do Visual Studio, confira [Crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

> [!NOTE]
> Máquinas virtuais e gateways de redes virtuais no Azure geram custos monetários contínuos quando estão em execução. Esse custo é cobrado em sua assinatura paga ou do MSDN. Um gateway de VPN do Azure é implementado como um conjunto de duas máquinas virtuais do Azure. Para minimizar os custos, crie o ambiente de teste e execute seus testes e demonstrações necessários o mais rápido possível.
> 
> 

## <a name="phase-1-configure-the-testlab-virtual-network"></a>Fase 1: Configurar a rede virtual TestLab
Use as instruções no tópico [Base Configuration test environment (Ambiente de teste da Configuração de Base)](https://technet.microsoft.com/library/mt771177.aspx) para configurar os computadores DC1, APP1 e CLIENT1 na rede virtual do Azure chamada TestLab. 

Em seguida, inicie um prompt do Azure PowerShell.

> [!NOTE]
> O comando a seguir define o uso do Azure PowerShell 1.0 e posterior.
> 
> 

Entre na sua conta.

    Login-AzureRMAccount

Obtenha o nome da sua assinatura usando o comando a seguir.

    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Defina sua assinatura do Azure. Use a mesma assinatura que você usou para compilar a configuração de base na Fase 1. Substitua tudo que estiver entre aspas, incluindo os caracteres < e >, pelo nome correto.

    $subscr="<subscription name>"
    Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Em seguida, adicione uma sub-rede de gateway à rede virtual TestLab de sua configuração de base, que será usada para hospedar o gateway do Azure.

    $rgName="<name of your resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
    Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

Em seguida, solicite um endereço IP público para atribuir o gateway à rede virtual TestLab.

    $gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

Depois, crie seu gateway.

    $vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
    New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Lembre-se de que a criação de novos gateways pode levar 20 minutos ou mais.

No portal do Azure em seu computador local, conecte-se ao DC1 com as credenciais CORP\User1. Para configurar o domínio CORP para que computadores e usuários utilizem seu controlador de domínio local para autenticação, execute estes comandos em um prompt de comando no nível de administrador do Windows PowerShell no DC1.

    New-ADReplicationSite -Name "TestLab" 
    New-ADReplicationSite -Name "TestVNET"
    New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
    New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Esta é a configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph1.png)

## <a name="phase-2-create-the-testvnet-virtual-network"></a>Fase 2: Criar a rede virtual TestVNET
Primeiro, crie a rede virtual TestVNET e proteja-a com um grupo de segurança de rede.

    $rgName="<name of the resource group that you used for your TestLab virtual network>"
    $locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
    $locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
    $testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
    $gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
    New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
    $rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
    New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
    $vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
    $nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
    Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

Em seguida, solicite um endereço IP público para ser alocado para o gateway para a rede virtual TestVNET e crie seu gateway.

    $gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
    New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Esta é a configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph2.png)

## <a name="phase-3-create-the-vnet-to-vnet-connection"></a>Fase 3: Criar a conexão de VNet para VNet
Primeiro, obtenha uma chave pré-compartilhada aleatória, criptograficamente forte de 32 caracteres do seu administrador de rede ou segurança. Você também pode usar as informações em [Create a random string for an IPsec preshared key (Criar uma cadeia de caracteres aleatória para uma chave pré-compartilhada IPsec)](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) para obter uma chave pré-compartilhada.

Em seguida, use estes comandos para criar a conexão VPN VNet para VNet, que pode levar algum tempo para ser concluída.

    $sharedKey="<pre-shared key value>"
    $gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
    $gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
    New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
    New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

Depois de alguns minutos, a conexão deve ser estabelecida.

Esta é a configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph3.png)

## <a name="phase-4-configure-dc2"></a>Fase 4: configurar o DC2
Em primeiro lugar, crie uma máquina virtual para DC2. Execute estes comandos no prompt de comando do Azure PowerShell em seu computador local.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<the storage account name for the base configuration>"
    $vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
    $pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
    $vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, conecte-se à nova máquina virtual DC2 do portal do Azure.

Em seguida, configure uma regra do Firewall do Windows para permitir o tráfego e testar a conectividade básica. Em um prompt de comando com nível de administrador do Windows PowerShell no DC2, execute estes comandos.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc1.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 10.0.0.4. Este é um teste de tráfego pela conexão de Rede Virtual para Rede Virtual.

Em seguida, adicione o disco de dados extra no DC2 como um novo volume com a letra de unidade F:.

1. No painel esquerdo do Gerenciador do Servidor, clique em **Serviços de Arquivo e Armazenamento** e, em seguida, clique em **Discos**.
2. No painel de conteúdo, no grupo **Discos**, clique em **disco 2** (com a **Partição** definida como **Desconhecida**).
3. Clique em **Tarefas** e, em seguida, em **Novo Volume**.
4. Na página Antes de começar do Assistente de Novo Volume, clique em **Avançar**.
5. Na página Selecione o servidor e o disco, clique em **Disco 2** e, em seguida, em **Avançar**. Quando solicitado, clique em **OK**.
6. Na página Especifique o tamanho do volume, clique em **Avançar**.
7. Na página Atribuir a uma letra da unidade ou pasta, clique em **Avançar**.
8. Na página Selecionar configurações do sistema de arquivos, clique em **Avançar**.
9. Na página Confirmar seleções, clique em **Criar**.
10. Ao concluir, clique em **Fechar**.

Em seguida, configure o DC2 como um controlador de domínio de réplica para o domínio corp.contoso.com. Execute estes comandos no prompt de comando do Windows PowerShell no DC2.

    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Observe que você será solicitado a fornecer a senha de CORP\User1 e uma senha do Modo de Restauração dos Serviços de Diretório (DSRM) e reiniciar o DC2.

Agora que a rede virtual TestVNET tem seu próprio servidor DNS (DC2), você deve configurar a rede virtual TestVNET para usar esse servidor DNS.

1. No painel esquerdo do portal do Azure, clique no ícone das redes virtuais e em **TestVNET**.
2. Na guia **Configurações**, clique em **Servidores DNS**.
3. Em **Servidor DNS primário**, digite **192.168.0.4** para substituir 10.0.0.4.
4. Clique em **Salvar**.

Esta é a configuração atual. 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

Seu ambiente de nuvem híbrida simulado agora está pronto para testes.

## <a name="next-step"></a>Próxima etapa
* Configure um [aplicativo de linha de negócios baseado na Web](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nesse ambiente.




<!--HONumber=Nov16_HO3-->


