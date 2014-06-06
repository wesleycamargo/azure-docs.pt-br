<properties linkid="manage-linux-howto-linux-agent" urlDisplayName="Preparar uma distribuição" pageTitle="Preparar uma distribuição do Linux no Azure" metaKeywords="Azure Git CodePlex, site do Azure CodePlex, site do Azure Git" description="Saiba como usar o Git para publicar em um site do Azure, bem como permitir implantações contínuas do GitHub e do CodePlex." metaCanonical="" services="virtual-machines" documentationCenter="" title="Preparar uma máquina virtual do Linux para o Azure" authors=""  solutions="" writer="kathydav" manager="jeffreyg" editor="tysonn"  />





# Preparar uma máquina virtual do Linux para o Azure 

Uma máquina virtual no Azure executa o sistema operacional que você escolhe ao criar a máquina virtual. o Azure armazena o sistema operacional de uma máquina virtual em um disco rígido virtual no formato VHD (um arquivo .vhd). Um VHD de um sistema operacional que foi preparado para duplicação é chamado de uma imagem. Este artigo mostra como criar sua própria imagem, carregando um arquivo .vhd com um sistema operacional que você instalou e generalizou. Para obter mais informações sobre discos e imagens no Azure, consulte [Gerenciar discos e imagens (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/jj672979.aspx)

**Observação**: ao criar uma máquina virtual, você pode personalizar as configurações do sistema operacional para facilitar a execução de seu aplicativo. A configuração que você definir é armazenada em disco nessa máquina virtual. Para obter instruções, consulte [Como criar uma máquina virtual personalizada](/pt-br/manage/windows/how-to-guides/custom-create-a-vm/).

**Importante**: o SLA da plataforma do Azure se aplica a máquinas virtuais com o sistema operacional Linux somente quando uma das distribuições endossadas é usada com os detalhes de configuração conforme especificado [neste artigo](http://support.microsoft.com/kb/2805216). Todas as distribuições de Linux fornecidas na Galeria de imagens do Azure são distribuições endossadas com a configuração necessária.


##Pré-requisitos##
Este artigo pressupõe que você tenha os seguintes itens:

- **Um certificado de gerenciamento** - Você criou um certificado de gerenciamento para a assinatura para a qual você deseja carregar um VHD e exportou o certificado para um arquivo .cer. Para obter mais informações sobre a criação de certificados, consulte [Criar um certificado de gerenciamento para o Azure (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/gg551722.aspx) 

- **Sistema operacional Linux instalado em um arquivo .vhd.**  - Você instalou um sistema operacional que suporta Linux em um disco rígido virtual. Existem várias ferramentas que criam arquivos .vhd. Você pode usar uma solução de virtualização como o Hyper-V para criar o arquivo .vhd e instalar o sistema operacional. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/pt-br/library/hh846766.aspx) 

	**Importante**: Não há suporte para o formato VHDX mais recente no Azure. É possível converter o disco para o formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.

	Para obter uma lista de distribuições endossadas, consulte [Linux no Azure-Distribuições endossadas](../linux-endorsed-distributions). Como alternativa, consulte a seção no final deste artigo referente a [informações para distribuições não endossadas][].

- **Ferramenta de linha de comando do Linux Azure.** Se você estiver usando um sistema operacional Linux para criar sua imagem, use essa ferramenta para carregar o arquivo VHD. Para baixar a ferramenta, consulte [Ferramentas de Linha de Comando do Azure para Linux e Mac](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409).

- **cmdlet Add-AzureVhd**, que é parte do módulo do PowerShell no Azure. Para baixar o módulo, consulte [Downloads do Azure (a página pode estar em inglês)](/pt-br/develop/downloads/). Para obter informações de referência, consulte [Add-AzureVhd](http://msdn.microsoft.com/pt-br/library/windowsazure/dn205185.aspx).

Para todas as distribuições, observe o seguinte:

O Azure Linux Agent (Waagent) não é compatível com o NetworkManager. A configuração de rede deve usar o arquivo ifcfg-eth0 e ser controlável pelos scripts ifup/ifdown. O Waagent não será instalado se o pacote NetworkManager for detectado.

Não há suporte para a NUMA devido a um bug nas versões de kernel do Linux abaixo de 2.6.37. A instalação do waagent desabilitará automaticamente a NUMA na configuração GRUB para a linha de comando de kernel do Linux.

O agente Linux do Azure exige que o pacote python-pyasn1 esteja instalado.

É recomendável que você não crie uma partição SWAP no momento da instalação. Você pode configurar o espaço SWAP usando o agente de Linux do Azure. Também não é recomendável usar o kernel do Linux predominante com uma máquina virtual do Azure sem o patch disponível no [site da Microsoft](http://go.microsoft.com/fwlink/?LinkID=253692&clcid=0x409) (muitas distribuições/kernels atuais já podem incluir esta correção).

Todos os VHDs devem ter tamanhos que são múltiplos de 1 MB.

Esta tarefa inclui as seguintes etapas:

- [Etapa 1: preparar a imagem a ser carregada] []
- [Etapa 2: criar uma conta de armazenamento no Azure] []
- [Etapa 3: preparar a conexão com o Azure] []
- [Etapa 4: carregar a imagem no Azure] []

## <a id="prepimage"> </a>Etapa 1: preparar a imagem a ser carregada ##

### Preparar o sistema operacional CentOS 6.2 + ###

Você deve concluir as etapas de configuração específicas do sistema operacional da máquina virtual para ser executada com Azure.

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela para a máquina virtual.

3. Desinstale o NetworkManager executando o seguinte comando:

		rpm -e --nodeps NetworkManager

	**Observação:** Se o pacote ainda não foi instalado, esse comando falhará com uma mensagem de erro. Isso é esperado.

4.	Crie um arquivo chamado **network** no diretório `/etc/sysconfig/`, contendo o seguinte texto:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

5.	Crie um arquivo chamado **ifcfg-eth0** no diretório `/etc/sysconfig/network-scripts/`, contendo o seguinte texto:

		DEVICE=eth0
		ONBOOT=yes
		DHCP=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

6. Habilite o serviço de rede executando o seguinte comando:

		chkconfig network on

7. CentOS 6.2 ou 6.3: instale os drivers dos Serviços de Integração Linux

	**Observação:** A etapa é válida somente para CentOS 6.2 e 6.3.  No CentOS 6.4+, Serviços de Integração Linux já está disponível no kernel.

	a) Obtenha o arquivo .iso que contém os drivers dos Serviços de Integração Linux do [Centro de Download](http://www.microsoft.com/pt-br/download/details.aspx?id=34603).

	b) No Gerenciador do Hyper-V, no painel **Ações**, clique em **Configurações**.

	![Abrir configurações do Hyper-V](./media/virtual-machines-linux-how-to-prepare/settings.png)

	c) No painel **Hardware**, clique em **Controlador IDE 1**.

	![Adicionar unidade de DVD com mídia de instalação](./media/virtual-machines-linux-how-to-prepare/installiso.png)

	d) Na caixa **Controlador IDE**, clique em **Unidade de DVD** e em **Adicionar**.

	e) Selecione o **Arquivo de imagem**, navegue até **Linux IC v3.2.iso** e clique em **Abrir**.

	f) Na página **Configurações**, clique em **OK**.

	g) Clique em **Conectar** para abrir a janela para a máquina virtual.

	h) Na janela Prompt de Comando, digite os comandos a seguir:

		mount /dev/cdrom /media
		/media/install.sh
		reboot

8. Instale o python-pyasn1 executando o seguinte comando:

		sudo yum install python-pyasn1

9. Substitua o arquivo /etc/yum.repos.d/CentOS-Base.repo com o seguinte texto

		[openlogic]
		name=CentOS-$releasever - openlogic packages for $basearch
		baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
		enabled=1
		gpgcheck=0
		
		[base]
		name=CentOS-$releasever - Base
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#released updates
		[updates]
		name=CentOS-$releasever - Updates
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#additional packages that may be useful
		[extras]
		name=CentOS-$releasever - Extras
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#additional packages that extend functionality of existing packages
		[centosplus]
		name=CentOS-$releasever - Plus
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#contrib - packages by Centos Users
		[contrib]
		name=CentOS-$releasever - Contrib
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

10.	Adicione as linhas a seguir ao /etc/yum.conf

		http_caching=packages
		exclude=kernel*

11. Desabilite o módulo yum "fastestmirror" editando o arquivo "/etc/yum/pluginconf.d/fastestmirror.conf", e em [main] digite o seguinte

		set enabled=0

12.	Execute o comando a seguir para limpar os metadados yum atuais:

		yum clean all

13. Para o CentOS 6.2 e 6.3, execute os comandos a seguir para atualizar o kernel de uma VM em execução

	Para o CentOS 6.2, execute os seguintes comandos:

		sudo yum remove kernel-firmware
		sudo yum --disableexcludes=all install kernel

	Para o CentOS 6.3+, digite o seguinte:

		sudo yum --disableexcludes=all install kernel

14. Modifique a linha de inicialização do kernel no grub para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens de console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

15. Confira se todos os dispositivos SCSI montados no seu kernel incluem um tempo limite de E/S de 300 segundos ou mais.

16.	Em /etc/sudoers, converta a seguinte linha em comentário, se ela existir:

		Defaults targetpw

17.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização

18. Instale o Azure Linux Agent executando o seguinte comando

		yum install WALinuxAgent

19.	Não crie espaço de permuta no disco de SO

	O Azure Linux Agent pode configurar o espaço de permuta automaticamente usando o disco do recurso local anexado à VM após o provisionamento no Azure.  Depois de instalar o agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros no /etc/waagent.conf adequadamente:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: ajuste-o conforme necessário.

20.	Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para o provisionamento no Azure:

		waagent -force -deprovision
		export HISTSIZE=0
		logout

21. Clique em **Desligar** no Gerenciador do Hyper-V.


### Preparar o sistema operacional Ubuntu 12.04+ ###

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela para a máquina virtual.

3.	Substitua os repositórios atuais na sua imagem para usar os repositórios do Azure que contêm o kernel e o pacote do agente necessários para atualizar a VM.  As etapas variam um pouco dependendo da versão do Ubuntu.

	Before editing /etc/apt/sources.list, it is recommended to make a backup
		sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

	Ubuntu 12.04:

		sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
		sudo apt-get update

	Ubuntu 12.10:

		sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
		sudo apt-get update

	Ubuntu 13.04+:

		sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		sudo apt-get update

4. Atualize o sistema operacional para o kernel mais recente executando os seguintes comandos: 

	Ubuntu 12.04:

		sudo apt-get update
		sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
		(recommended) sudo apt-get dist-upgrade
		sudo reboot

	Ubuntu 12.10:

		sudo apt-get update
		sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
		(recommended) sudo apt-get dist-upgrade
		sudo reboot
	
	Ubuntu 13.04 e 13.10:

		sudo apt-get update
		sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade
		sudo reboot

5.	O Ubuntu aguardará a entrada do usuário no prompt do Grub após uma falha de sistema. Para evitar isso, conclua as seguintes etapas:

	a) Abra o arquivo /etc/grub.d/00_header.

	b) Na função **make_timeout()**, pesquise **if ["\${recordfail}" = 1 ]; e**

	c) Altere a instrução abaixo desta linha para **set timeout=5**.

	d) Execute o update-grub.

6. Modifique a linha de inicialização do kernel no Grub ou Grub2 para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens de console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

7.	Em /etc/sudoers, converta a seguinte linha em comentário, se ela existir:

		Defaults targetpw

8.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização

9.	 Instale o agente executando os seguintes comandos com o sudo:

		apt-get update
		apt-get install walinuxagent 

10.	Não crie espaço de permuta no disco de SO

	O Azure Linux Agent pode configurar o espaço de permuta automaticamente usando o disco do recurso local anexado à VM após o provisionamento no Azure.  Depois de instalar o agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros no /etc/waagent.conf adequadamente:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: ajuste-o conforme necessário.

11.	Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para o provisionamento no Azure:

		waagent -force -deprovision
		export HISTSIZE=0
		logout

12. Clique em **Desligar** no Gerenciador do Hyper-V.


### Preparar o sistema operacional SUSE Linux Enterprise Server 11 SP2 & SP3 ###

**OBSERVAÇÃO: o** [SUSE Studio](http://www.susestudio.com) pode criar e gerenciar facilmente suas imagens SLES/opeSUSE para Azure e Hyper-V. Além disso, as seguintes imagens oficiais no SUSE Studio Gallery podem ser baixadas ou clonadas em sua própria conta SUSE Studio para personalização fácil:

> - [SLES 11 SP2 para Azure no SUSE Studio Gallery](http://susestudio.com/a/02kbT4/sles-11-sp2-for-windows-azure)
> - [SLES 11 SP3 para Azure no SUSE Studio Gallery](http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure)

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela para a máquina virtual.

3. Adicione o repositório que contém o kernel mais recente e o Azure Linux Agent. Execute o comando `zypper lr`. Por exemplo, com o SLES 11 SP3, a saída deve ser semelhante ao seguinte:

		# | Alias                        | Name               | Enabled | Refresh
		--+------------------------------+--------------------+---------+--------
		1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
		2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
		3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
		4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
		5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
		6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

 	Caso o comando retorne uma mensagem de erro semelhante a esta:

		"No repositories defined. Use the 'zypper addrepo' command to add one or more repositories."

	talvez seja necessário reabilitar os repositórios ou registrar o sistema.  Isso pode ser feito por meio do utilitário suse_register.  Para obter mais informações, consulte a [documentação SLES](https://www.suse.com/documentation/sles11/).

   Se um dos repositórios de atualização relevantes não estiver habilitado, habilite-o com o seguinte comando:

		zypper mr -e [REPOSITORY NUMBER]

   No caso acima, o comando adequado seria

		zypper mr -e 1 2 3 4

4. Atualize o kernel para obter a versão mais recente disponível:

		zypper up kernel-default

5. Instale o Azure Linux Agent:

		zypper up WALinuxAgent
		
	Você deve ver uma mensagem semelhante à seguinte:

		"There is an update candidate for 'WALinuxAgent', but it is from different vendor.
		Use 'zypper install WALinuxAgent-1.2-1.1.noarch' to install this candidate."

	Como o fornecedor do pacote foi alterado de "Microsoft Corporation" para "SUSE LINUX Products GmbH, Nuernberg, Germany", será necessário instalar o pacote especificamente conforme mencionado na mensagem.

	Observação: A versão do pacote WALinuxAgent pode ser um pouco diferente.

6. Modifique a linha de inicialização no Grub para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens de console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

7.	É recomendável ajustar o /etc/sysconfig/network/dhcp ou equivalente de DHCLIENT_SET_HOSTNAME="yes" para DHCLIENT_SET_HOSTNAME="no"

8.	Em /etc/sudoers, converta a seguinte linha em comentário, se ela existir:

		Defaults targetpw

9.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização

10.	Não crie espaço de permuta no disco de SO

	O Azure Linux Agent pode configurar o espaço de permuta automaticamente usando o disco do recurso local anexado à VM após o provisionamento no Azure.  Depois de instalar o agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros no /etc/waagent.conf adequadamente:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: ajuste-o conforme necessário.

11.	Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para o provisionamento no Azure:

		waagent -force -deprovision
		export HISTSIZE=0
		logout

12. Clique em **Desligar** no Gerenciador do Hyper-V.


### Prepare o sistema operacional openSUSE 12.3 ###

**OBSERVAÇÃO: o** [SUSE Studio](http://www.susestudio.com) pode criar e gerenciar facilmente suas imagens SLES/opeSUSE para Azure e Hyper-V. Além disso, as seguintes imagens oficiais no SUSE Studio Gallery podem ser baixadas ou clonadas em sua própria conta SUSE Studio para personalização fácil:

> - [openSUSE 12.3 para Azure no SUSE Studio Gallery](http://susestudio.com/a/02kbT4/opensuse-12-3-for-windows-azure)

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela para a máquina virtual.

3. Atualize o sistema operacional para obter o kernel e os patches mais recentes disponíveis

4. No shell, execute o comando '`zypper lr`'. Se esse comando retornar

		# | Alias                     | Name                      | Enabled | Refresh
		--+---------------------------+---------------------------+---------+--------
		1 | Cloud:Tools_openSUSE_12.3 | Cloud:Tools_openSUSE_12.3 | Yes     | Yes
		2 | openSUSE_12.3_OSS         | openSUSE_12.3_OSS         | Yes     | Yes
		3 | openSUSE_12.3_Updates     | openSUSE_12.3_Updates     | Yes     | Yes

   então os repositórios foram configurados conforme o esperado; nenhum ajuste será necessário.

   Caso o comando retorne "Nenhum repositório definido. Use o comando 'zypper addrepo' para adicionar um 
   ou mais repositórios." será necessário reabilitar os repositórios:

		zypper ar -f http://download.opensuse.org/distribution/12.3/repo/oss openSUSE_12.3_OSS
		zypper ar -f http://download.opensuse.org/update/12.3 openSUSE_12.3_Updates

   Verifique se os seus repositórios foram adicionados chamando 'zypper lr' novamente.

   Se um dos repositórios de atualização relevantes não estiver habilitado, habilite-o com o seguinte comando:

		zypper mr -e [NUMBER OF REPOSITORY]

5.	Desabilite a investigação automática de DVD ROM.

6.	Instale o Azure Linux Agent:

	Primeiro, adicione o repositório que contém o novo WALinuxAgent:

		zypper ar -f -r http://download.opensuse.org/repositories/Cloud:/Tools/openSUSE_12.3/Cloud:Tools.repo

	Em seguida, execute o seguinte comando:
 
		zypper up WALinuxAgent

	Depois de executar esse comando, será exibida uma mensagem semelhante a esta:

		"There is an update candidate for 'WALinuxAgent', but it is from different vendor. 
		Use 'zypper install WALinuxAgent' to install this candidate."

	Essa mensagem é esperada. Como o fornecedor do pacote foi alterado de "Microsoft Corporation" para "obs://build.opensuse.org/Cloud", será necessário instalar o pacote especificamente conforme mencionado na mensagem.

7.	Modifique a linha de inicialização no Grub para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens de console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

 	E em /boot/grub/menu.lst, remova os parâmetros a seguir da linha de comando do kernel, se eles existirem:

		libata.atapi_enabled=0 reserve=0x1f0,0x8

9.	É recomendável ajustar o /etc/sysconfig/network/dhcp ou equivalente de DHCLIENT_SET_HOSTNAME="yes" para DHCLIENT_SET_HOSTNAME="no"

10.	Em /etc/sudoers, converta a seguinte linha em comentário, se ela existir:

		Defaults targetpw

11.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização

12.	Não crie espaço de permuta no disco de SO

	O Azure Linux Agent pode configurar o espaço de permuta automaticamente usando o disco do recurso local anexado à VM após o provisionamento no Azure.  Depois de instalar o agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros no /etc/waagent.conf adequadamente:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: ajuste-o conforme necessário.

13.	Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para o provisionamento no Azure:

		waagent -force -deprovision
		export HISTSIZE=0
		logout

14. Confira se o Azure Linux Agent é executado durante a inicialização:

		systemctl enable waagent.service

14. Clique em **Desligar** no Gerenciador do Hyper-V.


## <a id="createstorage"> </a>Etapa 2: criar uma conta de armazenamento no Azure ##

A conta de armazenamento representa o mais alto nível do namespace para acessar os serviços de armazenamento e está associada à assinatura do Azure. É necessário ter uma conta de armazenamento no Azure para carregar um arquivo .vhd para o Azure que possa ser usado para criar uma máquina virtual. Você pode criar uma conta de armazenamento usando o Portal de Gerenciamento do Azure.

1. Entre no Portal de Gerenciamento do Azure.

2. Na barra de comandos, clique em **Novo**.

	![Criar conta de armazenamento](./media/virtual-machines-linux-how-to-prepare/create.png)

3. Clique em **Conta de Armazenamento** e em **Criação Rápida**.

	![Criação rápida de uma conta de armazenamento](./media/virtual-machines-linux-how-to-prepare/storage-quick-create.png)

4. Preencha os campos da seguinte forma:

	![Insira os detalhes da conta de armazenamento](./media/virtual-machines-linux-how-to-prepare/storage-create-account.png)

- Em **URL**, digite um nome de subdomínio para usar no URL para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse nome se torna o nome do host na URL que é usada para lidar com os recursos Blob, Fila ou Tabela da assinatura.
	
- Escolha o local ou o grupo de afinidade da conta de armazenamento. Especificando um grupo de afinidade, você pode localizar seus serviços de nuvem no mesmo data center com seu armazenamento.
 
- Escolha entre usar a replicação geográfica ou não para a conta de armazenamento. A replicação geográfica é ativada por padrão. Essa opção replica os dados para um local secundário, sem nenhum custo para você, para que seja feito o failover para um local secundário em caso de uma falha grave que não possa ser tratada no local primário. O local secundário é atribuído automaticamente e não pode ser alterado. Se os requisitos legais ou a política organizacional exigir um controle mais restrito do local de seu armazenamento baseado em nuvem, você poderá desativar a replicação geográfica. No entanto, saiba que ao ativar a replicação geográfica posteriormente, você deverá pagar uma taxa de transferência de uma única vez para replicar seus dados existentes para o local secundário. Serviços de armazenamento sem replicação geográfica são oferecidos com desconto.

5. Clique em **Criar Conta de Armazenamento**.

	Agora, a conta está listada em **Contas de Armazenamento**.

	![Conta de armazenamento criada com êxito](./media/virtual-machines-linux-how-to-prepare/Storagenewaccount.png)


## <a id="#connect"> </a>Etapa 3: preparar a conexão com o Azure ##

Antes de carregar um arquivo .vhd, será necessário estabelecer uma conexão segura entre o computador e a sua assinatura no Azure. 

1. Abra uma janela do Azure PowerShell.

2. Tipo: 

	`Get-AzurePublishSettingsFile`

	Esse comando abre uma janela do navegador e baixa automaticamente um arquivo .publishsettings que contém informações e um certificado para a sua assinatura do Azure. 

3. Salve o arquivo .publishsettings. 

4. Tipo:

	`Import-AzurePublishSettingsFile <PathToFile>`

	Onde `<PathToFile>` é o caminho completo para o arquivo .publishsettings. 

	Para obter mais informações, consulte [Introdução aos cmdlets do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj554332.aspx) 


## <a id="upload"> </a>Etapa 4: carregar a imagem no Azure ##

Quando você carrega o arquivo .vhd, é possível colocar o arquivo .vhd em qualquer lugar no seu armazenamento de blob. Nos exemplos de comando a seguir, **BlobStorageURL** é o URL da conta de armazenamento que você criou na etapa 2, **YourImagesFolder** é o recipiente de armazenamento de blob onde você deseja armazenar as imagens. **VHDName** é o rótulo que aparece no Portal de Gerenciamento para identificar o disco rígido virtual. **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd. 

Faça uma das opções a seguir:

- Na janela Azure PowerShell que você usou na etapa anterior, digite:

	`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	Para obter mais informações, consulte [Add-AzureVhd](http://msdn.microsoft.com/pt-br/library/windowsazure/dn205185.aspx).

- Use a ferramenta de linha de comando do Linux para carregar a imagem. Você pode carregar uma imagem usando o seguinte comando:

		Azure vm image create <image name> --location <Location of the data center> --OS Linux <Sourcepath to the vhd>

## <a id="nonendorsed"> </a>Informações para as distribuições não endossadas ##
Essencialmente todas as distribuições em execução no Azure precisam atender aos seguintes pré-requisitos para ter a chance de serem executadas corretamente na plataforma. 

Esta lista não é conclusiva, já que cada distribuição é diferente e, por isso, é bem possível que mesmo que você atenda a todos os critérios abaixo, ainda será preciso ajustar significativamente sua imagem para garantir que ela seja corretamente executada no topo da plataforma.

É por isso que recomendamos que você inicie com uma das nossas [imagens endossadas de parceiros](https://www.windowsazure.com/pt-br/manage/linux/other-resources/endorsed-distributions/).

A lista a seguir substitui a etapa 1 do processo para criar seu próprio VHD:

1.	Você deverá garantir que está executando um kernel que incorpora os drivers LIS mais recentes para o Hyper-V ou que você conseguiu compilá-los (os códigos deles foram abertos). Os drivers podem ser encontrados [neste local](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)

2.	Seu kernel também deve incluir a versão mais recente do driver ATA PiiX usada para provisionar as imagens e possui as correções pertinentes ao kernel com confirmação cd006086fa5d91414d8ff9ff2b78fbb593878e3c Data:   Sexta, 4 de maio 22:15:11 2012 +0100   ata_piix: adiar disco dos drivers do Hyper-V por padrão

3.	O seu intird compactado deve ser menor que 40 MB (* estamos trabalhando para aumentar esse número, que pode já estar desatualizado neste momento)

4.	Modifique a linha de inicialização do kernel no Grub ou Grub2 para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens de console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

5.	É recomendável ajustar o /etc/sysconfig/network/dhcp ou equivalente de DHCLIENT_SET_HOSTNAME="yes" para DHCLIENT_SET_HOSTNAME="no"

6.	É necessário conferir se todos os dispositivos SCSI montados no seu kernel incluem um tempo limite de E/S de 300 segundos ou mais.

7.	Será necessário instalar o Azure Linux Agent seguindo as etapas no [Guia do Linux Agent](https://www.windowsazure.com/pt-br/manage/linux/how-to-guides/linux-agent-guide/). O agente foi lançado sob a licença Apache 2 e você pode obter os bits mais recentes no [local do agente GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998&clcid=0x409)

8.	Em /etc/sudoers, converta a seguinte linha em comentário, se ela existir:

		Defaults targetpw

9.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização

10.	Não crie espaço de permuta no disco de SO

	O Azure Linux Agent pode configurar o espaço de permuta automaticamente usando o disco do recurso local anexado à VM após o provisionamento no Azure.  Depois de instalar o agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros no /etc/waagent.conf adequadamente:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## OBSERVAÇÃO: ajuste-o conforme necessário.

11.	Será necessário executar os comandos a seguir para desprovisionar a máquina virtual:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12.	Em seguida, você deverá desligar a máquina virtual e continuar com o carregamento.


[Etapa 1: preparar a imagem a ser carregada]: #prepimage
[Etapa 2: criar uma conta de armazenamento no Azure]: #createstorage
[Etapa 3: preparar a conexão com o Azure]: #connect
[Etapa 4: carregar a imagem no Azure]: #upload
[informações para distribuições não endossadas]: #nonendorsed

