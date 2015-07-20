<properties 
	pageTitle="Configurar um ambiente de nuvem híbrida simulado para testes" 
	description="Aprenda a criar um ambiente de nuvem híbrida simulado para testes profissionais de TI ou testes de desenvolvimento, usando duas redes virtuais do Azure e uma conexão VNet para VNet." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="josephd"/>

# Configurar um ambiente de nuvem híbrida simulado para testes

Este tópico orienta a criação de um ambiente de nuvem híbrida simulado com o Microsoft Azure para testes, usando duas redes virtuais separadas do Azure. Use essa configuração como uma alternativa ao [Configurar um ambiente de nuvem híbrida para testes](virtual-networks-setup-hybrid-cloud-environment-testing.md) quando você não tiver uma conexão direta com a Internet e um endereço IP público disponível. Veja abaixo a configuração resultante.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)

Isto simula um ambiente de produção de nuvem híbrida. Ele consiste em:

- Uma rede simulada e simplificada local, hospedada em uma rede virtual do Azure (a rede virtual TestLab).
- Uma rede virtual simulada entre locais, hospedada no Azure (TestVNET).
- Uma conexão VNet para VNet entre as duas redes virtuais.
- Um controlador de domínio secundário na rede virtual TestVNET.

Isto proporciona uma base e um ponto de partida comum com os quais é possível:

- Desenvolver e testar aplicativos em um ambiente de nuvem híbrida simulado.
- Criar configurações de teste de computadores, alguns dentro da rede virtual TestLab e outros na rede virtual TestVNET, para simular cargas de TI baseadas em nuvem híbrida.

Há quatro fases principais para configurar esse ambiente de teste de nuvem híbrida:

1.	Configurar a rede virtual TestLab.
2.	Criar a rede virtual entre locais.
3.	Criar a conexão VPN de VNet para VNet.
4.	Configurar o DC2. 

Se ainda não tiver uma assinatura do Azure, você poderá se inscrever para uma avaliação gratuita em [Teste o Azure](http://azure.microsoft.com/pricing/free-trial/). Se tiver uma assinatura do MSDN, consulte [Benefício do Azure para assinantes do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE]Máquinas virtuais e gateways de redes virtuais no Azure geram custos monetários contínuos quando estão em execução. Esse custo é cobrado em sua avaliação gratuita, assinatura do MSDN ou assinatura paga. Para reduzir os custos de executar esse ambiente de teste quando ele não estiver em uso, confira [Minimizando os custos contínuos desse ambiente](#costs) neste tópico para saber mais.


## Fase 1: Configurar a rede virtual TestLab

Use as instruções no [Ambiente de teste de configuração de base](../virtual-machines/virtual-machines-base-configuration-test-environment.md) para configurar os computadores DC1, APP1 e CLIENT1 em uma rede virtual do Azure denominada TestLab.

No Portal de Gerenciamento do Azure em seu computador local, conecte-se ao DC1 com as credenciais CORP\\User1. Para configurar o domínio CORP para que computadores e usuários utilizem seu controlador de domínio local para autenticação, execute estes comandos em um prompt de comando do nível de administrador do Windows PowerShell.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet –Name "10.0.0.0/8" –Site "TestLab"
	New-ADReplicationSubnet –Name "192.168.0.0/16" –Site "TestVNET"

Esta é a configuração atual.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_1.png)
 
## Fase 2: Criar a rede virtual TestVNET

Primeiro, crie uma nova rede virtual chamada TestVNET.

1.	Na barra de tarefas do Portal de Gerenciamento do Azure, clique em **Novo > Serviços de Rede > Rede Virtual > Criação Personalizada**.
2.	Na página Detalhes da Rede Virtual, digite **TestVNET** em **Nome**.
3.	Em **Local**, selecione o local apropriado.
4.	Clique na seta Avançar.
5.	Na página Servidores DNS e Conectividade VPN, em **Servidores DNS**, digite **DC1** em **Selecionar ou digitar o nome** e clique na seta Avançar.
6.	Na página Espaços de Endereço de Rede Virtual:
	- Em **Espaço de Endereço**, em **IP Inicial**, selecione ou digite **192.168.0.0**.
	- Em **Sub-redes**, clique em **Subnet-1** e substitua o nome por **TestSubnet**. 
	- Na coluna **CIDR (Contagem de Endereços)** de TestSubnet, clique em **/24 (256)**.
7.	Clique no ícone Concluído. Aguarde a rede virtual ser criada antes de continuar.

Em seguida, use as instruções em [Como instalar e configurar o PowerShell do Azure para instalar o PowerShell do Azure no computador local](../install-configure-powershell.md).

Em seguida, crie um novo serviço de nuvem para a rede virtual TestVNET. Você deve escolher um nome exclusivo. Por exemplo, você poderia nomeá-lo como**TestVNET-***UniqueSequence*, em que *UniqueSequence* é uma abreviação de sua organização. Por exemplo, se a sua organização se chamasse Tailspin Toys, você poderia chamar o serviço de nuvem de **TestVNET-Tailspin**.

Você pode testar a exclusividade do nome com este comando do PowerShell do Azure em seu computador local.

	Test-AzureName -Service <Proposed cloud service name>

Se este comando retornar "False", o nome proposto é exclusivo. Crie o serviço de nuvem com este comando.

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

Esta é a configuração atual.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_2.png)
 
##Fase 3: Criar a conexão de VNet para VNet

Primeiro, crie redes locais que representam o espaço de endereço de cada rede virtual.

1.	No Portal de Gerenciamento do Azure em seu computador local, clique em **Novo > Serviços de Rede > Rede Virtual > Adicionar Rede Local**.
2.	Na página Especificar detalhes da rede local, digite **TestLabLNet** em **Nome**, digite **131.107.0.1** em **Endereço IP do Dispositivo VPN** e clique na seta para a direita.
3.	Na página Especificar o espaço de endereço, em **IP Inicial**, digite **10.0.0.0**.
4.	Em **CIDR (Contagem de Endereços)**, selecione **/24 (256)** e clique na marca de seleção.
5.	Clique em **Novo > Serviços de Rede > Rede Virtual > Adicionar Rede Local**.
6.	Na página Especificar detalhes da rede local, digite **TestVNETLNet** em **Nome**, digite **131.107.0.2** em **Endereço IP do Dispositivo VPN** e clique na seta para a direita.
7.	Na página Especificar o espaço de endereço, em **IP Inicial**, digite **192.168.0.0**.
8.	Em **CIDR (Contagem de Endereços)**, selecione **/24 (256)** e clique na marca de seleção.

Observe que os endereços IP do dispositivo VPN 131.107.0.1 e 131.107.0.2 são apenas valores de espaço reservado temporários até que você configure os gateways para as duas redes virtuais.

Em seguida, configure cada rede virtual para usar uma conexão VPN site a site e a rede local correspondente à outra rede virtual.

1.	No Portal de Gerenciamento do Azure em seu computador local, clique em **Redes** no painel esquerdo e verifique se a coluna **Status** para **TestLab** está definida como **Criada**.
2.	Clique em **TestLab** e, em seguida, em **Configurar**. Na página TestLab, na seção **Conectividade Site a Site**, clique em **Conectar à rede local**. 
3.	Em **Rede Local**, selecione **TestVNETLNet**.
4.	Clique em **Salvar** na barra de tarefas. Em alguns casos, talvez seja necessário clicar em **Adicionar sub-rede de Gateway** para criar uma sub-rede usada pelo gateway de VPN do Azure.
5.	Clique em **Redes** no painel esquerdo e verifique se a coluna **Status** para TestVNET está definida como **Criada**.
6.	Clique em **TestVNET** e, em seguida, em **Configurar**. Na página TestVNET, na seção **Conectividade Site a Site**, clique em **Conectar à rede local**. 
7.	Em **Rede Local**, selecione **TestLabLNet**.
8.	Clique em **Salvar** na barra de tarefas. Em alguns casos, talvez seja necessário clicar em **Adicionar sub-rede de Gateway** para criar uma sub-rede usada pelo gateway de VPN do Azure.

Em seguida, crie gateways de rede virtual para as duas redes virtuais.

1.	No Portal de Gerenciamento do Azure, na página **Redes**, clique em **TestLab**. Na página Painel, você deve ver um status de **O gateway não foi criado**.
2.	Na barra de tarefas, clique em **Criar Gateway** e, em seguida, em **Roteamento Dinâmico**. Clique em **Sim** quando solicitado. Aguarde até que o gateway esteja concluído e seu status seja alterado para **Conectando**. Isso pode levar alguns minutos.
3.	Na página Painel, observe o **Endereço IP do Gateway**. Esse é o endereço IP público do gateway VPN do Azure para a rede virtual TestLab. Registre esse endereço IP, pois você precisará dele para configurar a conexão de VNet para VNet.
4.	Na barra de tarefas, clique em **Gerenciar Chave** e clique no ícone de cópia ao lado da chave para copiá-la para a área de transferência. Cole essa chave em um documento e salve-o. Você precisa desse valor de chave para configurar a conexão de VNet para VNet.
5.	Na página Redes, clique em **TestVNET**. Na página Painel, você deve ver um status de **O gateway não foi criado**.
6.	Na barra de tarefas, clique em **Criar Gateway** e, em seguida, em **Roteamento Dinâmico**. Clique em **Sim** quando solicitado. Aguarde até que o gateway esteja concluído e seu status seja alterado para **Conectando**. Isso pode levar alguns minutos.
7.	Na página Painel, observe o **Endereço IP do Gateway**. Esse é o endereço IP público do gateway VPN do Azure para a rede virtual TestVNET. Registre esse endereço IP, pois você precisará dele para configurar a conexão de VNet para VNet.

Em seguida, configure as redes locais TestLabLNet e TestVNETLNet com os endereços IP públicos obtidos na criação dos gateways de rede virtual.

1.	No Portal de Gerenciamento do Azure, na página Redes, clique em **Redes Locais**. 
2.	Clique em **TestLabLNet** e, em seguida, em **Editar** na barra de tarefas.
3.	Na página Especificar detalhes da rede local, digite o endereço IP do gateway de rede virtual para a rede virtual TestLab (obtido na etapa 3 do procedimento anterior) em **Endereço IP do Dispositivo VPN (opcional)** e clique na seta para a direita.
4.	Na página Especificar o espaço de endereço, clique na marca de seleção.
5.	Na página Redes Locais, clique em **TestVNETLNet** e, em seguida, em **Editar** na barra de tarefas.
6.	Na página Especificar detalhes da rede local, digite o endereço IP do gateway de rede virtual para a rede virtual TestVNET (obtido na etapa 7 do procedimento anterior) em **Endereço IP do Dispositivo VPN (opcional)** e clique na seta para a direita.
7.	Na página Especificar o espaço de endereço, clique na marca de seleção.

Em seguida, você configurará a chave pré-compartilhada para que ambos os gateways usem o mesmo valor, que é o valor de chave determinado pelo Portal de Gerenciamento do Azure para a rede virtual TestLab. Execute estes comandos em um prompt de comando do Azure PowerShell com o computador local, preenchendo o valor de chave pré-compartilhada da TestLab.

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet –SharedKey $preSharedKey

Em seguida, na página Rede do Portal de Gerenciamento do Azure no computador local, clique na rede virtual **TestLab**, clique em **Painel** e, em seguida, clique em **Conectar** na barra de tarefas. Aguarde até que a rede virtual TestLab mostre um estado conectado.

Esta é a configuração atual.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_3.png)
 
## Fase 4: configurar o DC2

Primeiro, crie uma máquina virtual do Azure para o DC2. Execute estes comandos no prompt de comando do PowerShell do Azure em seu computador local.

	$ServiceName="<Your cloud service name from Phase 2>"
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC2."
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Em seguida, faça logon na nova máquina virtual DC2.

1.	No painel esquerdo do Portal de Gerenciamento do Azure, clique em **Máquinas Virtuais** e, em seguida, em **Executando** na coluna **Status** do DC2.
2.	Na barra de tarefas, clique em **Conectar**. 
3.	Quando solicitado a abrir DC2.rdp, clique em **Abrir**.
4.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota, clique em **Conectar**.
5.	Quando solicitado a fornecer credenciais, use estas:
- Nome: **DC2\**[Nome da conta de administrador local]
- Senha: [senha da conta de administrador local]
6.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota referindo-se aos certificados, clique em **Sim**.

Em seguida, configure uma regra do Firewall do Windows para permitir o tráfego e testar a conectividade básica. Em um prompt de comando com nível de administrador do Windows PowerShell no DC2, execute estes comandos.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 10.0.0.4. Este é um teste de tráfego pela conexão Vnet para Vnet.

Em seguida, adicione o disco de dados extra como um novo volume com a letra da unidade F:.

1.	No painel esquerdo do Gerenciador do Servidor, clique em **Serviços de Arquivo e Armazenamento** e, em seguida, clique em **Discos**.
2.	No painel de conteúdo, no grupo **Discos**, clique em **disco 2** (com a **Partição** definida como **Desconhecida**).
3.	Clique em **Tarefas**, e, em seguida, em **Novo Volume**.
4.	Na página Antes de começar do Assistente de Novo Volume, clique em **Avançar**.
5.	Na página Selecione o servidor e o disco, clique em **Disco 2** e, em seguida, em **Avançar**. Quando solicitado, clique em **OK**.
6.	Na página Especifique o tamanho do volume, clique em **Avançar**.
7.	Na página Atribuir a uma letra da unidade ou pasta, clique em **Avançar**.
8.	Na página Selecionar configurações do sistema de arquivos, clique em **Avançar**.
9.	Na página Confirmar seleções, clique em **Criar**.
10.	Ao concluir, clique em **Fechar**.

Em seguida, configure o DC2 como um controlador de domínio de réplica para o domínio corp.contoso.com. Execute estes comandos no prompt de comando do Windows PowerShell no DC2.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Observe que você será solicitado a fornecer a senha de CORP\\User1 e uma senha do Modo de Restauração dos Serviços de Diretório (DSRM) e reiniciar o DC2.

Agora que a rede virtual TestVNET tem seu próprio servidor DNS (DC2), você deve configurar a rede virtual TestVNET para usar esse servidor DNS.

1.	No painel esquerdo do Portal de Gerenciamento do Azure, clique em **Redes** e, em seguida, em **TestVNET**.
2.	Clique em **Configurar**.
3.	Em **Servidores DNS**, remova a entrada 10.0.0.4.
4.	Em **Servidores DNS**, adicione uma entrada com o nome **DC2** e endereço IP **192.168.0.4**. 
5.	Na barra de comandos na parte inferior, clique em **Salvar** e, em seguida, em **Sim** quando solicitado. Aguarde a atualização da rede TestVNet se concluir.

Esta é a configuração atual.

![](./media/virtual-networks-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)
 
Seu ambiente de nuvem híbrida simulado agora está pronto para testes.

Você também pode criar estas configurações nesse ambiente de teste:

- [Farm de intranet do SharePoint](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)
- [Aplicativo LOB baseado na Web](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)
- [Servidor de Sincronização de Diretórios (DirSync) do Office 365](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

## Recursos adicionais

[Configurar um ambiente de nuvem híbrida para teste](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Configurar uma conexão VNet a VNet](http://msdn.microsoft.com/library/azure/dn690122.aspx)

[Ambiente de teste de configuração básica](../virtual-machines/virtual-machines-base-configuration-test-environment.md)

[Ambientes de teste de nuvem híbrida do Azure](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)

## <a id="costs"></a>Minimizando os custos contínuos desse ambiente

Para minimizar os custos de executar máquinas virtuais nesse ambiente, realize seus testes e demonstração necessários o mais rápido possível e, então, exclua ou desligue as máquinas virtuais quando você não as estiver usando. Por exemplo, você poderia usar um runbook e automação do Azure para desligar automaticamente as máquinas virtuais nas redes virtuais TestLab e Test_VNET ao final de cada dia útil. Para saber mais, confira [Introdução à automação do Azure](../automation-create-runbook-from-samples.md). Ao reiniciar as máquinas virtuais na sub-rede Corpnet, inicie o DC1 primeiro.

Um gateway VPN do Azure é implementado como um conjunto de duas máquinas virtuais do Azure, o que gera um custo monetário contínuo. Para obter detalhes, confira [Preços - Rede Virtual](http://azure.microsoft.com/pricing/details/virtual-network/). Para minimizar os custos dos dois gateways VPN (um para TestLab e outro para TestVNET), crie o ambiente de teste e realize seus testes e demonstração necessários o mais rápido possível ou exclua os gateways com estas etapas.
 
1.	No Portal de Gerenciamento do Azure em seu computador local, clique em **Redes** no painel esquerdo, clique em **TestLab** e, em seguida, clique em **Painel**.
2.	Na barra de tarefas, clique em **Excluir Gateway**. Clique em **Sim** quando solicitado. Aguarde até que o gateway seja excluído e seu status seja alterado para **O Gateway Não Foi Criado**.
3.	Clique em **Redes** no painel esquerdo, clique em **TestVNET** e, em seguida, clique em **Painel**.
4.	Na barra de tarefas, clique em **Excluir Gateway**. Clique em **Sim** quando solicitado. Aguarde até que o gateway seja excluído e seu status seja alterado para **O Gateway Não Foi Criado**.

Se excluir os gateways e quiser restaurar esse ambiente de teste, você deverá primeiro criar novos gateways.

1.	No Portal de Gerenciamento do Azure em seu computador local, clique em **Redes** no painel esquerdo e, em seguida, clique em **TestLab**. Na página Painel, você deve ver um status de **O gateway não foi criado**.
2.	Na barra de tarefas, clique em **Criar Gateway** e, em seguida, em **Roteamento Dinâmico**. Clique em **Sim** quando solicitado. Aguarde até que o gateway esteja concluído e seu status seja alterado para **Conectando**. Isso pode levar alguns minutos.
3.	Na página Painel, observe o **Endereço IP do Gateway**. Esse é o novo endereço IP público do gateway VPN do Azure para a rede virtual TestLab. Você precisa desse endereço IP para reconfigurar a rede local TestLabLNet.
4.	Na barra de tarefas, clique em **Gerenciar Chave** e clique no ícone de cópia ao lado da chave para copiá-la para a área de transferência. Cole esse valor de chave em um documento e salve-o. Você precisa desse valor de chave para reconfigurar o gateway VPN para a rede virtual TestVNET.
5.	No Portal de Gerenciamento do Azure em seu computador local, clique em **Redes** no painel esquerdo e, em seguida, clique em **TestVNET**. Na página Painel, você deve ver um status de **O gateway não foi criado**.
6.	Na barra de tarefas, clique em **Criar Gateway** e, em seguida, em **Roteamento Dinâmico**. Clique em **Sim** quando solicitado. Aguarde até que o gateway esteja concluído e seu status seja alterado para Conectando. Isso pode levar alguns minutos.
7.	Na página Painel, observe o **Endereço IP do Gateway**. Esse é o novo endereço IP público do gateway VPN do Azure para a rede virtual TestVNET. Você precisa desse endereço IP para reconfigurar a rede local TestVNETLNet.

Em seguida, configure as redes locais TestLabLNet e TestVNETLNet com os novos endereços IP públicos obtidos na criação dos gateways de rede virtual.

1.	No Portal de Gerenciamento do Azure, na página Redes, clique em **Redes Locais**. 
2.	Clique em **TestLabLNet** e, em seguida, em **Editar** na barra de tarefas.
3.	Na página Especificar detalhes da rede local, digite o endereço IP do gateway de rede virtual para a rede virtual TestLab (obtido na etapa 3 do procedimento anterior) em **Endereço IP do Dispositivo VPN (opcional)** e clique na seta para a direita.
4.	Na página Especificar o espaço de endereço, clique na marca de seleção.
5.	Na página Redes Locais, clique em **TestVNETLNet** e, em seguida, em **Editar** na barra de tarefas.
6.	Na página Especificar detalhes da rede local, digite o endereço IP do gateway de rede virtual para a rede virtual TestVNET (obtido na etapa 7 do procedimento anterior) em **Endereço IP do Dispositivo VPN (opcional)** e clique na seta para a direita.
7.	Na página Especificar o espaço de endereço, clique na marca de seleção.

Em seguida, configure a chave pré-compartilhada para que ambos os gateways usem o mesmo valor, que é o valor de chave determinado pelo Portal de Gerenciamento do Azure para a rede virtual TestLab. Execute estes comandos em um prompt de comando do Azure PowerShell com o computador local, preenchendo o valor de chave pré-compartilhada da TestLab.

	$preSharedKey="<The preshared key for the TestLab virtual network>"
	Set-AzureVNetGatewayKey -VNetName TestVNET -LocalNetworkSiteName TestLabLNet –SharedKey $preSharedKey

Em seguida, na página Rede do Portal de Gerenciamento do Azure, clique na rede virtual **TestLab** e, em seguida, clique em **Conectar** na barra de tarefas. Aguarde até que a rede virtual TestLab mostre um estado conectado à rede local TestVNET.
 

<!---HONumber=July15_HO2-->