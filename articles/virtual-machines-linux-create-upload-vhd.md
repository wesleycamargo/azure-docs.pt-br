<properties linkid="manage-linux-common-task-upload-vhd" urlDisplayName="Carregar um VHD" pageTitle="Criar e carregar um VHD do Linux no Azure" metaKeywords="VHD do Azure, carregando VHD do Linux" description="Saiba como criar e carregar um disco rígido virtual (VHD) do Azure com o sistema operacional Linux." metaCanonical="" services="virtual-machines" documentationCenter="" title="Criando e carregando um disco rígido virtual que contém o sistema operacional Linux" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />





# Criando e carregando um disco rígido virtual que contém o sistema operacional Linux 

Uma máquina virtual no Azure executa o sistema operacional que você escolhe ao criar a máquina virtual. O Azure armazena o sistema operacional de uma máquina virtual em um disco rígido virtual no formato VHD (um arquivo .vhd). Um VHD de um sistema operacional que foi preparado para duplicação é chamado de uma imagem. Este artigo mostra como criar sua própria imagem, carregando um arquivo .vhd com um sistema operacional que você instalou e generalizou. Para obter mais informações sobre discos e imagens no Azure, consulte [Gerenciar discos e imagens (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/jj672979.aspx)

**Observação**: ao criar uma máquina virtual, você pode personalizar as configurações do sistema operacional para facilitar a execução de seu aplicativo. A configuração que você definir é armazenada em disco nessa máquina virtual. Para obter instruções, consulte [Como criar uma máquina virtual personalizada](/pt-br/manage/windows/how-to-guides/custom-create-a-vm/).

**Importante**: o SLA da plataforma do Azure se aplica a máquinas virtuais com o sistema operacional Linux somente quando uma das distribuições endossadas é usada com os detalhes da configuração especificados [neste artigo](http://support.microsoft.com/kb/2805216). Todas as distribuições do Linux fornecidas na galeria de imagens do Azure são distribuições endossadas com a configuração necessária.


##Pré-requisitos##
Este artigo pressupõe que você tenha os seguintes itens:

- **Um certificado de gerenciamento** -  Você criou um certificado de gerenciamento para a assinatura para a qual você deseja carregar um VHD e exportou o certificado para um arquivo .cer. Para obter mais informações sobre como criar certificados, consulte [Criar um certificado de gerenciamento para o Azure (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx). 

- **Sistema operacional Linux instalado em um arquivo .vhd.**  - Você instalou um sistema operacional Linux com suporte em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd. Você pode usar uma solução de virtualização, como o Hyper-V, para criar o arquivo .vhd e instalar o sistema operacional. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx). 

	**Importante**: não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.

	Para obter uma lista das distribuições endossadas, consulte [Linux em distribuições endossadas do Azure](../linux-endorsed-distributions). Como alternativa, consulte a seção no final deste artigo referente a [informações para distribuições não endossadas][].

- **Ferramenta de linha de comando do Linux Azure.** Se você estiver usando um sistema operacional Linux para criar sua imagem, use essa ferramenta para carregar o arquivo VHD. Para baixar a ferramenta, consulte [Ferramentas de linha de comando do Azure para Linux e Mac](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409).

- **Cmdlet Add-AzureVhd**, que faz parte do módulo do PowerShell do Azure. Para baixar o módulo, consulte [Downloads do Azure](/pt-br/develop/downloads/). Para obter informações de referência, consulte [Add-AzureVhd (a página pode estar em inglês)](http://msdn.microsoft.com/library/windowsazure/dn495173.aspx).

**Em todas as distribuições, observe o seguinte:**

- Ao instalar o sistema Linux é recomendável utilizar partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas.

- O Agente Linux do Azure (waagent) não é compatível com o NetworkManager. A configuração de rede deve ser controlável por meio de scripts de ifup/ifdown.  A maioria dos pacotes RPM/Deb fornecidos pelas distribuições configuram o NetworkManager como um conflito para o pacote do waagent e, portanto, desinstalarão o NetworkManager quando você instalar o pacote do Agente Linux.  O Agente Linux do Azure também exige que o pacote python-pyasn1 esteja instalado.

- NUMA não tem suporte para tamanhos maiores de VM devido a um bug nas versões de kernel do Linux inferiores à 2.6.37. A instalação manual do waagent desabilitará automaticamente o NUMA na configuração do GRUB para o kernel do Linux. Esse problema afeta principalmente distribuições que usam o kernel upstream do Red Hat 2.6.32.

- É recomendável não criar uma partição SWAP no momento da instalação. Você pode configurar o espaço SWAP usando o Agente Linux do Azure. Também não é recomendável usar o kernel base do Linux com uma máquina virtual do Azure sem o patch disponível no [Site da Microsoft](http://go.microsoft.com/fwlink/?LinkID=253692&clcid=0x409) (muitas distribuições/kernels atuais já podem incluir essa correção).

- Todos os VHDs devem ter tamanhos que sejam múltiplos de 1 MB.


Esta tarefa inclui as seguintes etapas:

- [Etapa 1: preparar a imagem a ser carregada] []
- [Etapa 2: Criar uma conta de armazenamento no Azure] []
- [Etapa 3: Preparar a conexão com o Azure] []
- [Etapa 4: Carregar a imagem no Azure] []

## <a id="prepimage"> </a>Etapa 1: preparar a imagem a ser carregada ##

### Preparar o CentOS 6.2+ ###

Você deve concluir as etapas de configuração específicas do sistema operacional para que a máquina virtual seja executada no Azure.

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela para a máquina virtual.

3. Desinstale o NetworkManager executando o seguinte comando:

		rpm -e --nodeps NetworkManager

	**Observação:** se o pacote ainda não estiver instalado, esse comando irá falhar com uma mensagem de erro. Isso é esperado.

4.	Crie um arquivo chamado **network** no diretório `/etc/sysconfig/` que contém o seguinte texto:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

5.	Crie um arquivo chamado **ifcfg-eth0** no diretório `/etc/sysconfig/network-scripts/` que contém o seguinte texto:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

6.	Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet.  Essas regras provocam problemas ao clonar uma máquina virtual no Windows Azure ou no Hyper-V:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7. Certifique-se de que o serviço de rede será iniciado na inicialização executando o seguinte comando:

		# chkconfig network on

8.	**Somente CentOS 6.2 ou 6.3**: instale os drivers dos Serviços de Integração do Linux

	**Observação:** a etapa só é válida para CentOS 6.2 e 6.3. No CentOS 6.4+ os Serviços de Integração do Linux já estão disponíveis no kernel.

	a) Obter o arquivo. ISO que contém os drivers para os serviços de integração do Linux no [Centro de Download](http://www.microsoft.com/pt-br/download/details.aspx?id=34603).

	b) No Gerenciador de Hyper-V, no painel **Ações**, clique em **Configurações**.

	![Abrir configurações do Hyper-V](./media/virtual-machines-linux-create-upload-vhd/settings.png)

	c) No painel **Hardware**, clique em **IDE Controller 1**.

	![Adicionar unidade de DVD com mídia de instalação](./media/virtual-machines-linux-create-upload-vhd/installiso.png)

	d) No campo **IDE Controller**, clique em **DVD Drive** e depois em **Adicionar**.

	e) Selecione o **Arquivo de imagem**, vá para **Linux IC v3.2.iso**e, em seguida, clique em **Abrir**

	f) No painel **Configurações**, clique em **OK**.

	g) Clique em **Conectar** para abrir a janela da máquina virtual.

	h) Na janela Prompt de comando, digite os seguintes comandos:

		mount /dev/cdrom /media
		/media/install.sh
		reboot

9. Instale o python-pyasn1 executando o seguinte comando:

		# sudo yum install python-pyasn1

10. Substitua o arquivo /etc/yum.repos.d/CentOS-Base.repo pelo seguinte texto

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

11.	Adicione a seguinte linha a /etc/yum.conf:

		http_caching=packages

	E no CentOS 6.2 e 6.3 apenas adicione a seguinte linha:

		exclude=kernel*

12. Desabilite o módulo yum "fastestmirror", editando o arquivo "/etc/yum/pluginconf.d/fastestmirror.conf" e, em [main], digite o seguinte

		set enabled=0

13.	Execute o comando a seguir para limpar os metadados atuais do yum:

		yum clean all

14. No CentOS 6.2 e 6.3, atualize o kernel de uma VM em funcionamento executando os seguintes comandos

	Para CentOS 6.2, execute os seguintes comandos:

		sudo yum remove kernel-firmware
		sudo yum --disableexcludes=all install kernel

	Para CentOS 6.3+, digite o seguinte:

		sudo yum --disableexcludes=all install kernel

15.	Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra /boot/grub/menu.lst em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração. Além disso, isso desabilitará o NUMA devido a um erro na versão do kernel usada pelo CentOS 6.

16.	Em /etc/sudoers, comente a linha a seguir, se existente:

		Defaults targetpw

17.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.

18. Instale o Agente Linux do Azure executando o seguinte comando:

		yum install WALinuxAgent

	Observe que a instalação do pacote WALinuxAgent removerá o NetworkManager e os pacotes NetworkManager-gnome se eles já não tiverem sido removidos conforme descrito na etapa 2.

19.	Não crie espaço de permuta no disco do SO.

	O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure.  Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

20.	Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

		waagent -force -deprovision
		export HISTSIZE=0
		logout

21. Clique em **Desligar** no Gerenciador do Hyper-V.


### Preparar o Ubuntu 12.04+ ###

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela para a máquina virtual.

3.	Substitua os repositórios atuais na sua imagem para usar os repositórios do Azure que contêm o kernel e o pacote do agente necessários para atualizar a VM.  As etapas variam um pouco dependendo da versão do Ubuntu.

	Antes de editar o /etc/apt/sources.list, é recomendável fazer um backup
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
	
	Ubuntu 13.04, 13.10 e 14.04:

		sudo apt-get update
		sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade
		sudo reboot

5.	O Ubuntu aguardará a entrada do usuário no prompt do Grub após uma falha de sistema. Para evitar isso, conclua as seguintes etapas:

	a) Abra o arquivo /etc/grub.d/00_header.

	b) Na função **make_timeout()**, pesquise por **if ["\${recordfail}" = 1 ] e**

	c) Altere a instrução abaixo desta linha para **set timeout=5**.

	d) Execute 'sudo update-grub'.

6. Modifique a linha de inicialização para o Grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra /etc/default/grub em um editor de texto, localize a variável chamada "GRUB_CMDLINE_LINUX_DEFAULT" (ou adicione-a, se necessário) e edite-a para incluir os seguintes parâmetros:

		GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

	Salve e feche o arquivo e execute 'sudo update-grub'. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte técnico do Azure com problemas de depuração. 

7.	Em /etc/sudoers, comente a linha a seguir, se existente:

		Defaults targetpw

8.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.

9.	 Instale o agente executando os seguintes comandos com o sudo:

		apt-get update
		apt-get install walinuxagent

	Observe que a instalação do pacote walinuxagent removerá o NetworkManager e os pacotes NetworkManager-gnome, se estiverem instalados.

10.	Não crie espaço de permuta no disco do SO.

	O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure.  Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

		waagent -force -deprovision
		export HISTSIZE=0
		logout

12. Clique em **Desligar** no Gerenciador do Hyper-V.


### Preparar o Oracle Linux 6.4+ ###

Você deve concluir as etapas de configuração específicas do sistema operacional para que a máquina virtual seja executada no Azure.

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela para a máquina virtual.

3. Desinstale o NetworkManager executando o seguinte comando:

		rpm -e --nodeps NetworkManager

	**Observação:** se o pacote ainda não estiver instalado, esse comando irá falhar com uma mensagem de erro. Isso é esperado.

4.	Crie um arquivo chamado **network** no diretório `/etc/sysconfig/` que contém o seguinte texto:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

5.	Crie um arquivo chamado **ifcfg-eth0** no diretório `/etc/sysconfig/network-scripts/` que contém o seguinte texto:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

6.	Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet.  Essas regras provocam problemas ao clonar uma máquina virtual no Windows Azure ou no Hyper-V:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7. Certifique-se de que o serviço de rede será iniciado na inicialização executando o seguinte comando:

		# chkconfig network on

8. Instale o python-pyasn1 executando o seguinte comando:

		# sudo yum install python-pyasn1

9.	Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra /boot/grub/menu.lst em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração. Além disso, isso desabilitará o NUMA devido a um erro na versão do kernel usada pelo CentOS 6.

10.	Em /etc/sudoers, comente a linha a seguir, se existente:

		Defaults targetpw

11.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.

12. Instale o Agente Linux do Azure executando o seguinte comando:

		yum install WALinuxAgent

	Observe que a instalação do pacote WALinuxAgent removerá o NetworkManager e os pacotes NetworkManager-gnome, se estiverem instalados.

13.	Não crie espaço de permuta no disco do SO.

	O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure.  Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14.	Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

		waagent -force -deprovision
		export HISTSIZE=0
		logout

15. Clique em **Desligar** no Gerenciador do Hyper-V.


### Preparar o SUSE Linux Enterprise Server 11 SP2 e SP3 ###

**OBSERVAÇÃO: o** [SUSE Studio](http://www.susestudio.com) pode criar e gerenciar facilmente suas imagens SLES/opeSUSE para Azure e Hyper-V. Além disso, as seguintes imagens oficiais no SUSE Studio Gallery podem ser baixadas ou clonadas em sua própria conta SUSE Studio para personalização fácil:

> - [SLES 11 SP3 para Azure no SUSE Studio Gallery](http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure)

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela para a máquina virtual.

3. Adicione o repositório que contém o kernel mais recente e o Agente Linux do Azure. Execute o comando `zypper lr`. Por exemplo, com o SLES 11 SP3, a saída deve ser semelhante ao seguinte:

		# | Alias                        | Name               | Enabled | Refresh
		--+------------------------------+--------------------+---------+--------
		1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
		2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
		3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
		4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
		5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
		6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

 	Caso o comando retorne uma mensagem de erro como a seguinte:

		"Nenhum repositório definido. Use o comando 'zypper addrepo' para adicionar um ou mais repositórios."

	então os repositórios talvez precisem ser reativados ou registrados no sistema.  Isso pode ser feito por meio do utilitário suse_register.  Para obter mais informações, consulte a [documentação SLES](https://www.suse.com/documentation/sles11/).

   Caso um dos repositórios de atualização relevantes não estiver ativado, ative-o com o comando a seguir:

		zypper mr -e [REPOSITORY NUMBER]

   No caso acima, o comando apropriado seria

		zypper mr -e 1 2 3 4

4. Atualize o kernel para obter a versão mais recente disponível:

		zypper up kernel-default

5. Instale o Agente Linux do Azure:

		zypper up WALinuxAgent
		
	Você deve ver uma mensagem semelhante à seguinte:

		"There is an update candidate for 'WALinuxAgent', but it is from different vendor.
		Use 'zypper install WALinuxAgent-1.2-1.1.noarch' to install this candidate."

	Como o fornecedor do pacote foi alterado de "Microsoft Corporation" para "SUSE LINUX Products GmbH, Nuremberg, Alemanha", é preciso instalar o pacote como mencionado na mensagem.

	Observação: a versão do pacote do WALinuxAgent pode ser um pouco diferente.

6. Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra /boot/grub/menu.lst em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração.

7.	É recomendável que você defina o /etc/sysconfig/network/dhcp ou o equivalente de DHCLIENT_SET_HOSTNAME = "yes" como DHCLIENT_SET_HOSTNAME = "no"

8.	Em /etc/sudoers, comente a linha a seguir, se existente:

		Defaults targetpw

9.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização

10.	Não crie espaço de permuta no disco do SO

	O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure.  Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

		waagent -force -deprovision
		export HISTSIZE=0
		logout

12. Clique em **Desligar** no Gerenciador do Hyper-V.


### Preparar o openSUSE 12.3+ ###

**OBSERVAÇÃO: o** [SUSE Studio](http://www.susestudio.com) pode criar e gerenciar facilmente suas imagens SLES/opeSUSE para Azure e Hyper-V. Além disso, as seguintes imagens oficiais no SUSE Studio Gallery podem ser baixadas ou clonadas em sua própria conta SUSE Studio para personalização fácil:

> - [openSUSE 13.1 para Azure no SUSE Studio Gallery](https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure)

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela para a máquina virtual.

3. Atualize o sistema operacional para obter o kernel e os patches mais recentes disponíveis

4. No shell, execute o comando '`zypper lr`'. Se esse comando retornar uma saída semelhante à seguinte (observe que os números de versão podem variar):

		# | Alias                     | Name                      | Enabled | Refresh
		--+---------------------------+---------------------------+---------+--------
		1 | Cloud:Tools_openSUSE_12.3 | Cloud:Tools_openSUSE_12.3 | Yes     | Yes
		2 | openSUSE_12.3_OSS         | openSUSE_12.3_OSS         | Yes     | Yes
		3 | openSUSE_12.3_Updates     | openSUSE_12.3_Updates     | Yes     | Yes

   , os repositórios estão configurados conforme o esperado e nenhum ajuste é necessário.

   Se o comando retornar "Nenhum repositório definido. Use o comando 'zypper addrepo' para adicionar um 
   ou mais repositórios". Em seguida, os repositórios precisam ser reabilitados:

		zypper ar -f http://download.opensuse.org/distribution/12.3/repo/oss openSUSE_12.3_OSS
		zypper ar -f http://download.opensuse.org/update/12.3 openSUSE_12.3_Updates

   Verifique se seus repositórios foram adicionados chamando 'zypper Ir' novamente.

   Caso um dos repositórios de atualização relevantes não estiver ativado, ative-o com o comando a seguir:

		zypper mr -e [NUMBER OF REPOSITORY]

5.	Desabilite a investigação automática de DVD ROM.

6.	Instale o Agente Linux do Azure:

	Primeiro, adicione o repositório que contém o novo WALinuxAgent:

		zypper ar -f -r http://download.opensuse.org/repositories/Cloud:/Tools/openSUSE_12.3/Cloud:Tools.repo

	Em seguida, execute o seguinte comando:
 
		zypper up WALinuxAgent

	Depois de executar este comando, você pode receber uma mensagem semelhante a seguinte:

		"There is an update candidate for 'WALinuxAgent', but it is from different vendor. 
		Use 'zypper install WALinuxAgent' to install this candidate."

	Essa mensagem é esperada. Como o fornecedor do pacote foi alterado de "Microsoft Corporation" para "obs://build.opensuse.org/Cloud", é necessário instalar o pacote explicitamente conforme mencionado na mensagem.

7.	Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra /boot/grub/menu.lst em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração.  Além disso, remova os seguintes parâmetros da linha de inicialização do kernel, se existirem:

		libata.atapi_enabled=0 reserve=0x1f0,0x8

9.	É recomendável que você defina o /etc/sysconfig/network/dhcp ou o equivalente de DHCLIENT_SET_HOSTNAME="yes" como DHCLIENT_SET_HOSTNAME = "no"

10.	Em /etc/sudoers, comente a linha a seguir, se existente:

		Defaults targetpw

11.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização

12.	Não crie espaço de permuta no disco do SO

	O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure.  Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

		waagent -force -deprovision
		export HISTSIZE=0
		logout

14. Verifique se o Agente Linux do Azure é executado durante a inicialização:

		systemctl enable waagent.service

14. Clique em **Desligar** no Gerenciador do Hyper-V.


## <a id="createstorage"> </a>Etapa 2: Criar uma conta de armazenamento no Azure ##

A conta de armazenamento representa o mais alto nível do namespace para acessar os serviços de armazenamento e está associada à assinatura do Azure. Você precisa de uma conta de armazenamento no Azure para carregar um arquivo .vhd no Azure que pode ser usado para a criação de uma máquina virtual. Você pode criar uma conta de armazenamento usando o Portal de Gerenciamento do Azure.

1. Entre no Portal de Gerenciamento do Azure.

2. Na barra de comandos, clique em **Novo**.

	![Criar conta de armazenamento](./media/virtual-machines-linux-create-upload-vhd/create.png)

3. Clique em **Conta de Armazenamento** e em **Criação Rápida**.

	![Criação rápida de uma conta de armazenamento](./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png)

4. Preencha os campos da seguinte forma:

	![Insira os detalhes da conta de armazenamento](./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png)

- Em **URL**, digite um nome de subdomínio para usar no URL para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse nome se torna o nome do host na URL que é usada para lidar com os recursos Blob, Fila ou Tabela da assinatura.
	
- Escolha o local ou o grupo de afinidade da conta de armazenamento. Especificando um grupo de afinidade, você pode localizar seus serviços de nuvem no mesmo data center com seu armazenamento.
 
- Escolha entre usar a replicação geográfica ou não para a conta de armazenamento. A replicação geográfica é ativada por padrão. Essa opção replica os dados para um local secundário, sem nenhum custo para você, para que seja feito o failover para um local secundário em caso de uma falha grave que não possa ser tratada no local primário. O local secundário é atribuído automaticamente e não pode ser alterado. Se os requisitos legais ou a política organizacional exigir um controle mais restrito do local de seu armazenamento baseado em nuvem, você poderá desativar a replicação geográfica. No entanto, saiba que ao ativar a replicação geográfica posteriormente, você deverá pagar uma taxa de transferência de uma única vez para replicar seus dados existentes para o local secundário. Serviços de armazenamento sem replicação geográfica são oferecidos com desconto.

5. Clique em **Criar Conta de Armazenamento**.

	A conta é listada em **Contas de Armazenamento**.

	![Conta de armazenamento criada com êxito](./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png)


## <a id="#connect"> </a>Etapa 3: Preparar a conexão com o Azure ##

Para poder carregar um arquivo .vhd, você precisa estabelecer uma conexão segura entre seu computador e sua assinatura do Azure. 

1. Abra uma janela do PowerShell do Azure.

2. Tipo: 

	`Get-AzurePublishSettingsFile`

	Esse comando abre uma janela de navegador e baixa automaticamente um arquivo .publishsettings que contém informações e um certificado de sua assinatura do Azure. 

3. Salve o arquivo .publishsettings. 

4. Tipo:

	`Import-AzurePublishSettingsFile <PathToFile>`

	Onde `<PathToFile>` é o caminho completo para o arquivo .publishsettings. 

	Para obter mais informações, consulte [Introdução aos cmdlets do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj554332.aspx) 


## <a id="upload"> </a>Etapa 4: Carregar a imagem no Azure ##

Quando carrega o arquivo .vhd, você pode colocá-lo em qualquer lugar no seu armazenamento de blob. Nos exemplos de comando a seguir, **BlobStorageURL** é a URL da conta de armazenamento que você criou na Etapa 2, **YourImagesFolder** é o contêiner no armazenamento de blob onde você deseja armazenar as imagens. **VHDName** é o rótulo que aparece no Portal de Gerenciamento para identificar o disco rígido virtual. **PathToVHDFile** é o caminho completo e o nome do arquivo .vhd. 

Faça uma das opções a seguir:

- Na janela PowerShell do Azure que você usou na etapa anterior, digite:

	`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	Para obter mais informações, consulte [Add-AzureVhd (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/windowsazure/dn205185.aspx).

- Use a ferramenta de linha de comando do Linux para carregar a imagem. Você pode carregar uma imagem usando o seguinte comando:

		Azure vm image create <image name> --location <Location of the data center> --OS Linux <Sourcepath to the vhd>

## <a id="nonendorsed"> </a>Informações para as distribuições não endossadas ##
Essencialmente, todas as distribuições em execução no Azure precisam atender aos seguintes pré-requisitos para ter a chance de serem executadas corretamente na plataforma. 

Esta lista não é conclusiva, já que cada distribuição é diferente e, por isso, é bem possível que mesmo que você atenda a todos os critérios abaixo, ainda será preciso ajustar significativamente sua imagem para garantir que ela seja corretamente executada no topo da plataforma.

É por isso que recomendamos que você inicie com uma das nossas [imagens endossadas de parceiros](https://www.windowsazure.com/pt-br/manage/linux/other-resources/endorsed-distributions/).

A lista a seguir substitui a etapa 1 do processo para criar seu próprio VHD:

1.	Você precisa garantir que está executando um kernel que incorpora os drivers LIS mais recentes para o Hyper V ou que os compilou com êxito (tornaram-se software livre). Os drivers podem ser encontrados [neste local](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)

2.	O kernel também deve incluir a versão mais recente do driver ATA PiiX que é usado para fazer a provisão de imagens e possui as correções para o kernel com confirmação cd006086fa5d91414d8ff9ff2b78fbb593878e3c Date:   Fri May 4 22:15:11 2012 +0100   ata_piix: adiar discos para as unidades Hyper-V, por padrão

3.	O seu intird compactado deve ser menor que 40 MB (* estamos trabalhando para aumentar esse número, que pode já estar desatualizado neste momento)

4.	Modifique a linha de inicialização do kernel no Grub ou Grub2 para incluir os parâmetros a seguir. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

5.	É recomendável que você defina o /etc/sysconfig/network/dhcp ou o equivalente de DHCLIENT_SET_HOSTNAME="yes" como DHCLIENT_SET_HOSTNAME = "no"

6.	É necessário conferir se todos os dispositivos SCSI montados no seu kernel incluem um tempo limite de E/S de 300 segundos ou mais.

7.	Você precisará instalar o Agente Linux do Azure seguindo as etapas no [Guia do Agente Linux](https://www.windowsazure.com/pt-br/manage/linux/how-to-guides/linux-agent-guide/). O agente foi lançado sob a licença Apache 2 e você pode obter os bits mais recentes no [local do agente GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998&clcid=0x409)

8.	Em /etc/sudoers, comente a linha a seguir, se existente:

		Defaults targetpw

9.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização

10.	Não crie espaço de permuta no disco do SO

	O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure.  Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	Será necessário executar os comandos a seguir para desprovisionar a máquina virtual:

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12.	Em seguida, você deverá desligar a máquina virtual e continuar com o carregamento.


[Etapa 1: preparar a imagem a ser carregada]: #prepimage
[Etapa 2: Criar uma conta de armazenamento no Azure]: #createstorage
[Etapa 3: Preparar a conexão com o Azure]: #connect
[Etapa 4: Carregar a imagem no Azure]: #upload
[informações para distribuições não endossadas]: #nonendorsed
