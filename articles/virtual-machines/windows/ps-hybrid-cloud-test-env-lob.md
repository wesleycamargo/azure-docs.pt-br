---
title: "Ambiente de teste de aplicativos de LOB (linha de negócios ) | Microsoft Docs"
description: "Aprenda a criar um aplicativo de linha de negócios baseado na Web em um ambiente de nuvem híbrida para testes profissionais de TI ou testes de desenvolvimento."
services: virtual-machines-windows
documentationcenter: 
author: JoeDavies-MSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 92d2d8ce-60ed-4512-95e5-a7fe3b0ca00b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: josephd
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 8e9a380458bfede80ed0fc1ee7e62b0caec09501
ms.lasthandoff: 03/31/2017


---
# <a name="set-up-a-web-based-lob-application-in-a-hybrid-cloud-for-testing"></a>Configurar um aplicativo LOB baseado na Web em uma nuvem híbrida para teste
Este tópico explica a criação de um ambiente de nuvem híbrida simulado para testar um aplicativo LOB (linha de negócios) baseado na Web, hospedado no Microsoft Azure. Veja abaixo a configuração resultante.

![](./media/ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Essa configuração consiste em:

* Uma rede local simulada hospedada no Azure (a VNet TestLab).
* Uma rede virtual entre locais hospedada no Azure (TestVNET).
* Uma conexão VPN de VNet para VNet.
* Um servidor LOB baseado na Web, um SQL Server e um controlador de domínio secundário na rede virtual TestVNET.

Esta configuração proporciona uma base e um ponto de partida comum com os quais é possível:

* Desenvolver e testar aplicativos LOB hospedados no IIS (Serviços de Informações da Internet) com um back-end de banco de dados do SQL Server 2014 no Azure.
* Realizar testes dessa carga de trabalho simulada de TI baseada em nuvem híbrida.

Há três fases principais para configurar esse ambiente de teste de nuvem híbrida:

1. Configurar um ambiente de nuvem híbrida simulado.
2. Configurar o computador do servidor SQL (SQL1).
3. Configurar o servidor LOB (LOB1).

Essa carga de trabalho requer uma assinatura do Azure. Se você tiver uma assinatura do MSDN ou do Visual Studio, confira [Crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

Para obter um exemplo de um aplicativo LOB de produção hospedado no Azure, consulte o plano gráfico da arquitetura de **Aplicativos de linha de negócios** em [Diagramas e planos gráficos de arquitetura de software Microsoft](http://msdn.microsoft.com/dn630664).

## <a name="phase-1-set-up-the-simulated-hybrid-cloud-environment"></a>Fase 1: Configurar um ambiente de nuvem híbrida simulado
Crie o [ambiente de teste de nuvem híbrida simulado](ps-hybrid-cloud-test-env-sim.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Como esse ambiente de teste não exige a presença do servidor APP1 na sub-rede Corpnet, você pode desligá-lo por enquanto.

Esta é a configuração atual.

![](./media/ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)

## <a name="phase-2-configure-the-sql-server-computer-sql1"></a>Fase 2: Configurar o computador do servidor SQL (SQL1)
No portal do Azure, inicie o computador DC2, se necessário.

Em seguida, crie uma máquina virtual para o SQL1 com estes comandos em um prompt de comando do Azure PowerShell em seu computador local. Antes de executar estes comandos, preencha os valores variáveis e remova os caracteres < e >.

    $rgName="<your resource group name>"
    $locName="<the Azure location of your resource group>"
    $saName="<your storage account name>"

    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty

    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Use o portal do Azure para se conectar ao SQL1 usando a conta de administrador local do SQL1.

Em seguida, configure regras de Firewall do Windows para permitir testes de conectividade básica e o tráfego do SQL Server. Em um prompt de comando com nível de administrador do Windows PowerShell no SQL1, execute estes comandos.

    New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 192.168.0.4.

Em seguida, adicione o disco de dados extra no SQL1 como um novo volume com a letra de unidade F:.

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

Execute estes comandos no prompt de comando do Windows PowerShell no SQL1:

    md f:\Data
    md f:\Log
    md f:\Backup

Em seguida, integre o SQL1 ao domínio CORP do Windows Server Active Directory com estes comandos no prompt do Windows PowerShell no SQL1.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Use a conta CORP\User1 quando for solicitado a fornecer credenciais da conta de domínio para o comando **Add-Computer**.

Após a reinicialização, use o portal do Azure para se conectar ao SQL1 *com a conta de administrador local do SQL1*.

Em seguida, configure o SQL Server 2014 para usar a unidade F: para novos bancos de dados e permissões de conta de usuário.

1. Na tela inicial, digite **Gerenciamento do SQL Server** e, em seguida, clique em **SQL Server 2014 Management Studio**.
2. Em **Conectar ao Servidor**, clique em **Conectar**.
3. No painel de árvore do Pesquisador de Objetos, clique com o botão direito do mouse em **SQL1**e clique em **Propriedades**.
4. Na janela **Propriedades do Servidor**, clique em **Configurações de Banco de Dados**.
5. Localize os **Locais padrão de banco de dados** e defina estes valores: 
   * Para **Dados**, digite o caminho **f:\Data**.
   * Para **Log**, digite o caminho **f:\Log**.
   * Para **Backup**, digite o caminho **f:\Backup**.
   * Aviso: somente novos bancos de dados usam esses locais.
6. Clique em **OK** para fechar a janela.
7. No painel de árvore do **Pesquisador de Objetos**, abra **Segurança**.
8. Clique com o botão direito do mouse em **Logons** e clique em **Novo Logon**.
9. Em **Nome de Logon**, digite **CORP\User1**.
10. Na página **Funções de Servidor**, clique em **sysadmin** e, em seguida, clique em **OK**.
11. Feche o Microsoft SQL Server Management Studio.

Esta é a configuração atual.

![](./media/ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)

## <a name="phase-3-configure-the-lob-server-lob1"></a>Fase 3: Configurar o servidor LOB (LOB1)
Primeiro, crie uma máquina virtual para LOB1 com estes comandos no prompt de comando do Azure PowerShell em seu computador local.

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<your storage account name>"

    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, use o portal do Azure para se conectar ao LOB1 com as credenciais da conta de administrador local de LOB1.

Em seguida, configure uma regra do Firewall do Windows para permitir o tráfego e testar a conectividade básica. Em um prompt de comando com nível de administrador do Windows PowerShell no LOB1, execute estes comandos.

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 192.168.0.4.

Em seguida, integre a LOB1 ao domínio CORP do Active Directory com estes comandos no prompt do Windows PowerShell.

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

Use a conta CORP\User1 quando for solicitado a fornecer credenciais da conta de domínio para o comando **Add-Computer**.

Após a reinicialização, use o portal do Azure para conectar-se ao LOB1 com a conta e senha CORP\User1.

Em seguida, configure o LOB1 para IIS e teste o acesso do CLIENT1.

1. No Gerenciador de Servidores, clique em **Adicionar funções e recursos**.
2. Na página **Antes de Começar**, clique em **Avançar**.
3. Na página **Selecionar tipo de instalação**, clique em **Avançar**.
4. Na página **Selecionar servidor de destino**, clique em **Avançar**.
5. Na página **Funções do servidor**, clique em **Servidor Web (IIS)** na lista **Funções**.
6. Quando solicitado, clique em **Adicionar Recursos** e depois em **Avançar**.
7. Na página **Selecionar recursos**, clique em **Avançar**.
8. Na página **Servidor Web (IIS)**, clique em **Avançar**.
9. Na página **Selecionar serviços da função**, marque ou desmarque as caixas de seleção dos serviços de que você precisa para testar seu aplicativo LOB e, em seguida, clique em **Avançar**.
10. Na página **Confirmar seleções da instalação**, clique em **Instalar**.
11. Aguarde a conclusão da instalação dos componentes e clique em **Fechar**.
12. No portal do Azure, conecte-se ao computador CLIENT1 com as credenciais da conta CORP\User1 e inicie o Internet Explorer.
13. Na barra de endereços, digite **http://lob1/** e pressione ENTER. Você verá a página da Web do IIS 8 padrão.

Esta é a configuração atual.

![](./media/ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Este ambiente agora está pronto para que você implante seu aplicativo baseado na Web no LOB1 e teste a funcionalidade do CLIENT1 na sub-rede Corpnet.

## <a name="next-step"></a>Próxima etapa
* Adicionar uma nova máquina virtual usando o [portal do Azure](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


