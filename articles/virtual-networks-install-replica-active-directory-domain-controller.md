<properties urlDisplayName="Replica domain controller" pageTitle="Instalar um controlador de domínio de réplica no Azure" metaKeywords="" description="A tutorial that teaches you how to install a domain controller from your Corp Active Directory forest on your Azure virtual machine." metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller in Azure Virtual Networks" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha" />




#Instalar um controlador de domínio de réplica do Active Directory em Redes Virtuais do Azure

Este tutorial explica as etapas para instalar um controlador de domínio adicional de sua floresta do Active Directory Corp em uma máquina virtual (VM) na [Rede Virtual do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx). Neste tutorial, a rede virtual da VM está conectada à rede de sua empresa. Para obter orientações conceituais sobre a instalação de Serviços de Domínio Active Directory (AD DS) na rede virtual do Azure, consulte [Diretrizes para a implantação do Active Directory do Windows Server em máquinas virtuais do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156090.aspx). (a página pode estar em inglês).

##Sumário##

* [Pré-requisitos](#Prerequisites)
* [Etapa 1: verificar o endereço IP estático de YourPrimaryDC](#verifystaticip)
* [Etapa 2: Instalar a floresta Corp](#installforest)
* [Etapa 3: Criar sub-redes e sites](#subnets)
* [Etapa 4: Instalar um controlador de domínio adicional no CloudSite](#cloudsite)
* [Etapa 5: validar a instalação](#validate)
* [Etapa 6: provisionar uma máquina virtual que seja associada ao domínio na inicialização](#provisionvm)
* [Etapa 7: fazer backup do controlador de domínio](#backup)
* [Etapa 8: Testar a autenticação e a autorização](#test)


<h2><a id="Prerequisites"></a>Pré-requisitos</h2>

-	[Configure um VPN site a site no Portal de Gerenciamento](http://msdn.microsoft.com/pt-br/library/dn133795.aspx) configurado entre a rede virtual Azure e a rede Corp.
-	Crie um serviço de nuvem na rede virtual.
-	Implante duas VMs no Serviço de Nuvem que façam parte da rede virtual (especifique a sub-rede onde você deseja colocar a máquina virtual). Para obter mais informações, consulte [Adicionar uma máquina virtual a uma rede virtual (a página pode estar em inglês)](http://azure.microsoft.com/pt-br/documentation/articles/virtual-networks-add-virtual-machine/). Uma VM deve ter tamanho L ou superior para que dois discos de dados possam ser anexados a ela. Os discos de dados são necessários para armazenar:
	- O banco de dados e os logs do Active Directory.
	- Backups de estado do sistema.
-	Uma rede Corp com duas VMs (YourPrimaryDC e FileServer).
-	Infraestrutura do DNS (Sistema de Nomes de Domínio) implantada, se for necessário que os usuários externos resolvam nomes de contas no Active Directory. Nesse caso, você deve criar uma delegação de zona DNS antes de instalar o servidor DNS no controlador de domínio ou permitir que o Assistente de Instalação dos Serviços de Domínio Active Directory crie a delegação. Para obter mais informações sobre como criar uma delegação de zona DNS, consulte [Criar uma delegação de zona](http://technet.microsoft.com/library/cc753500.aspx).
-	No controlador de domínio que você instala em uma VM do Azure, defina as configurações do resolvedor de cliente DNS da seguinte forma:
	- Servidor DNS preferencial: servidor DNS local
	- Servidor DNS alternativo: endereço de loopback ou, se possível, outro servidor DNS executando em um controlador de domínio na mesma rede virtual.

<div class="dev-callout"> 
<b>Observação</b>
<p>Você precisa fornecer sua própria infraestrutura DNS para dar suporte ao AD DS na Rede Virtual do Azure. A infraestrutura DNS fornecida pelo Azure para esta versão não dá suporte a alguns recursos exigidos pelo AD DS, como o registro de registros de recursos SRV. </p>
</div>

<div class="dev-callout"> 
<b>Observação</b>
<p>Se já tiver concluído as etapas em <a href="/pt-br/manage/services/networking/active-directory-forest/">Instalar uma nova floresta do Active Directory no Azure</a>, você poderá precisar remover o AD DS do controlador de domínio na Rede Virtual do Azure antes de começar este tutorial. Para obter mais informações sobre como remover o AD DS, consulte <a href="http://technet.microsoft.com/pt-br/library/cc771844(v=WS.10).aspx">Removendo de um domínio o controlador de domínio</a>.</p>
</div>


<h2><a id="verifystaticip"></a>Etapa 1: verificar o endereço IP estático de YourPrimaryDC</h2>

1. Faça logon em YourPrimaryDC na rede Corp.

2. No Gerenciador de Servidores, clique em Exibir Conexões de Rede.

3. Clique com o botão direito do mouse na conexão de rede local e clique em Propriedades.

4. Clique em Protocolo IP versão 4 (TCP/IPv4) e clique em Propriedades.

5. Verifique se um endereço IP estático está atribuído ao servidor. 

	![VerifystaticIPaddressyourPrimaryDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/VerifystaticIP.png)


<h2><a id="installforest"></a>Etapa 2: Instalar a floresta Corp</h2>

1. Na sessão RDP da máquina virtual, clique em **iniciar**, digite **dcpromo** e pressione ENTER.

	![InstallCorpForest1](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest1.png)


2. Na página de Boas-Vindas, clique em **Avançar**.

	![InstallCorpForest2](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest2.png)



3. Na página Compatibilidade do Sistema Operacional, clique em **Avançar**.

	![InstallCorpForest3](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest3.png)

4. Na página Escolher uma Configuração de Implantação, clique em **Criar um novo domínio em uma nova floresta** e em **Avançar**. 

	![InstallCorpForest4](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest4.png)


5. Na página Nomear o Domínio Raiz da Floresta, digite **corp.contoso.com** o FQDN (nome de domínio totalmente qualificado) do domínio raiz da floresta e clique em **Avançar**. 

	![InstallCorpForest5](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest5.png)


6. Na página Definir Nível Funcional da Floresta, clique em **Windows Server 2008 R2** e em **Avançar**.

	![InstallCorpForest6](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest6.png)

7. Na página Opções Adicionais do Controlador de Domínio, clique em **Servidor DNS** e clique em **Avançar**.

	![InstallCorpForest7](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest7.png)

8. Se o seguinte aviso de delegação DNS aparecer, clique em **Sim**.

	![InstallCorpForest8](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest8.png)


9. Na página Local para o banco de dados, os arquivos de log e o SYSVOL do Active Directory, digite ou selecione o local dos arquivos e clique em **Avançar**.

	![InstallCorpForest9](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest9.png)


10. Na página Administrador da Restauração dos Serviços de Diretório, digite e confirme a senha do DSRM e clique em **Avançar**.

	![InstallCorpForest10](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest10.png)


11. Na página Resumo, confirme suas seleções e clique em **Avançar**. 

	![InstallCorpForest11](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest11.png)

12. Após a conclusão do assistente de instalação de Active Directory, clique em **Concluir**e em **Reiniciar agora** para concluir a instalação. 

	![InstallCorpForest12](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest12.png)



<h2><a id="subnets"></a>Etapa 3: Criar sub-redes e sites</h2>

1. No YourPrimaryDC, clique em Iniciar, clique em Ferramentas Administrativas e, em seguida, clique em Sites e Serviços do Active Directory.
2. Clique em **Sites**, clique com o botão direito do mouse em **Sub-redes** e, em seguida, clique em **Nova Sub-Rede**.

	![CreateSubnetsandSites1](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites1.png)

3. Em **Prefixo::**, digite **10.1.0.0/24**, selecione o objeto do site **Default-First-Site-Name** e clique em **OK**.

	![CreateSubnetsandSites2](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites2.png)

4. Clique com o botão direito do mouse em **Sites** e clique em **Novo Site**.

	![CreateSubnetsandSites3](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites3.png)


5. Em Nome, digite **CloudSite**, selecione **DEFAULTIPSITELINK** e clique em **OK**. 

	![CreateSubnetsandSites4](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites4.png)


6. Clique em **OK** para confirmar que o site foi criado. 

	![CreateSubnetsandSites5](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites5.png)

7. Clique com o botão direito do mouse em **Sub-redes** e clique em **Nova Sub-rede**.

	![CreateSubnetsandSites6](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites6.png)

8. Em **Prefixo::**, digite **10.4.2.0/24**,, selecione o objeto do site **CloudSite** e clique em **OK**.

	![CreateSubnetsandSites7](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites7.png)


<h2><a id="cloudsite"></a>Etapa 4: Instalar um controlador de domínio adicional no CloudSite</h2>

1. Faça logon na YourVMachine, clique em **Iniciar**, digite **dcpromo** e pressione ENTER.

	![AddDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC1.png)

2. Na página de Boas-Vindas, clique em **Avançar**.

	![AddDC2](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC2.png)


3. Na página Compatibilidade do Sistema Operacional, clique em **Avançar**.

	![AddDC3](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC3.png)

4. Na página Escolher uma Configuração de Implantação, clique em **Floresta existente**, clique em **Adicionar um controlador de domínio a um domínio existente** e clique em **Avançar**.

	![AddDC4](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC4.png)


5. Na página Credenciais da Rede, verifique se você está instalando o controlador de domínio no domínio **corp.contoso.com** e digite as credenciais de um membro do grupo Administradores do Domínio (ou use corp\credenciais do administrador). 

	![AddDC5](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC5.png)


6. Na página Selecionar um Domínio, clique em **Avançar**. 

	![AddDC6](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC6.png)


7. Na página Selecionar Site, verifique se CloudSite está selecionado e clique em **Avançar**.

	![AddDC7](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC7.png)

8. Na página Opções Adicionais do Controlador de Domínio, clique em **Avançar**. 

	![AddDC8](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC8.png)


9. No aviso de atribuição de IP estático, clique em **Sim, o computador usará o endereço IP atribuído automaticamente pelo servidor DHCP (não recomendado)**

	**Importante** 

Embora o endereço IP na rede virtual do Azure seja dinâmico, a concessão dele tem a duração da VM. Portanto, não será necessário definir um endereço IP estático no controlador de domínio instalado na rede virtual. Definir um endereço IP estático na VM causará falhas de comunicação.


	![AddDC9](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC9.png)

10. Quando receber uma solicitação sobre o aviso da delegação DNS, clique em **Sim**.

	![AddDC10](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC10.png)


11. Na página Local para o banco de dados, os arquivos de log e o SYSVOL do Active Directory, clique em Procurar e digite ou selecione um local no disco de dados para os arquivos do Active Directory e clique em **Avançar**. 

	![AddDC11](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC11.png)

12. Na página Administrador da Restauração dos Serviços de Diretório, digite e confirme a senha do DSRM e clique em **Avançar**.

	![AddDC12](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC12.png)

13. Na página de Resumo, clique em **Avançar**.

	![AddDC13](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC13.png)

14. Após a conclusão do assistente de instalação de Active Directory, clique em **Concluir**e em **Reiniciar agora** para concluir a instalação. 

	![AddDC14](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC14.png)


<h2><a id="validate"></a>Etapa 5: validar a instalação</h2>

1. Reconecte-se à VM.

2. Clique em **Iniciar**, clique com o botão direito no **Prompt de comando** e clique em **Executar como administrador**. 

3. Digite o comando a seguir e pressione ENTER:  'Dcdiag /c /v'

4. Verifique se os testes foram executados com êxito. 

Após a configuração do DC, execute o cmdlet do Windows PowerShell a seguir para provisionar máquinas virtuais adicionais e fazer com que elas ingressem automaticamente no domínio quando provisionadas. As configurações do resolvedor do cliente DNS das VMs devem ser definidas quando as VMs forem provisionadas. Substitua os nomes corretos para seu domínio, nome da VM e assim por diante. 

Para obter mais informações sobre como usar o Windows PowerShell, consulte [Introdução ao Azure PowerShell](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156055.aspx) e [Windows Azure Management Cmdlets (Cmdlets de gerenciamento do Microsoft Azure)](http://msdn.microsoft.com/pt-br/library/windowsazure/jj152841) (as páginas podem estar em inglês).


<h2><a id="provisionvm"></a>Etapa 6: provisionar uma máquina virtual que seja associada ao domínio na inicialização</h2>

1. Para criar uma máquina virtual adicional que ingresspu ao domínio durante a primeira inicialização, abra o ISE do PowerShell do Azure, cole o script a seguir, substitua os espaços reservados por seus próprios valores e execute-o. 

	Para determinar o endereço IP interno de controlador de domínio, clique no nome da rede virtual onde ele está sendo executado. 

	No exemplo a seguir, o endereço IP interno do controlador de domínio é 10.4.3.1. O Add-AzureProvisioningConfig também utiliza um parâmetro -MachineObjectOU que, quando especificado (requer um nome distinto completo no Active Directory), permite as configurações da Política de Grupo em todas as máquinas virtuais naquele contêiner.

	Após o provisionamento das máquinas virtuais, faça logon especificando uma conta de domínio usando o formato de nome UPN, como administrador@corp.contoso.com. 

		#Deploy a new VM and join it to the domain
		#-------------------------------------------
		#Specify my DC's DNS IP (10.4.3.1)
		$myDNS = New-AzureDNS -Name 'ContosoDC13' -IPAddress '10.4.3.1'
		
		# OS Image to Use
		$image = 'MSFT__Sql-Server-11EVAL-11.0.2215.0-08022012-pt-br-30GB.vhd'
		$service = 'myazuresvcindomainM1'
		$AG = 'YourAffinityGroup'
		$vnet = 'YourVirtualNetwork'
		$pwd = 'p@$$w0rd'
		$size = 'Small'
		
		#VM Configuration
		$vmname = 'MyTestVM1'
		$MyVM1 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
		    Add-AzureProvisioningConfig -WindowsDomain -Password $pwd -Domain 'corp' -DomainPassword 'p@$$w0rd' -DomainUserName 'Administrator' -JoinDomain 'corp.contoso.com'|
		    Set-AzureSubnet -SubnetNames 'BackEnd'
		
		New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM1 -DnsSettings $myDNS -VNetName $vnet
		

<h2><a id="backup"></a>Etapa 7: fazer backup do controlador de domínio</h2>


1. Conectar-se a YourVMachine.

2. Clique em **Iniciar**, em **Gerenciador do Servidor**, em **Adicionar Recursos** e selecione **Recursos de Backup do Windows Server**. Siga as instruções para instalar o backup do Windows Server.

3. Clique em **Iniciar**, clique em **Backup do Windows Server** e em **Backup único**.
 
4. Clique em **Opções diferentes**e depois em **Avançar**.

5. Clique em **Servidor completo**e **Avançar**.

6. Clique em **Unidades locais**e depois em **Avançar**.

7. Selecione a unidade de destino que não hospeda os arquivos do sistema operacional ou o banco de dados do Active Directory e clique em Avançar.

	![BackupDC](./media/virtual-networks-install-replica-active-directory-domain-controller/BackupDC.png)


8. Confirme as configurações selecionadas e clique em **Backup**.

<h2><a id="test"></a>Etapa 8: Testar a autenticação e a autorização</h2>

1. Para testar a autenticação e a autorização, crie uma conta de usuário de domínio no Active Directory. 
Faça logon na VM cliente em cada site e crie uma pasta compartilhada na VM

2. Teste o acesso à pasta compartilhada usando diferentes contas, grupos e permissões. 

## Consulte também

-  [Rede Virtual do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx)

-  [Windows Azure IaaS para profissionais de TI: (01) Conceitos básicos sobre máquina virtual](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Windows Azure IaaS para profissionais de TI: (05) Criando redes virtuais e conectividade entre instalações](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [PowerShell do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156055.aspx)

-  [Cmdlets gerenciamento do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj152841)
