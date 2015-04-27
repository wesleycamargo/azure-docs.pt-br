<properties 
	pageTitle="Criar e carregar um VHD Linux baseado em CentOS no Azure" 
	description="Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contenha um sistema operacional Linux baseado em CentOS." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="szarkos"/>

# Preparar uma máquina virtual baseada em CentOS para o Azure

- [Preparar uma máquina virtual CentOS 6.x para o Azure](#centos6)
- [Preparar uma máquina virtual CentOS 7.0 ou posterior para o Azure](#centos7)

##Pré-requisitos##

Este artigo pressupõe que você já instalou um sistema operacional Linux CentOS (ou derivado similar) em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina virtual](http://technet.microsoft.com/library/hh846766.aspx). 


**Notas de instalação do CentOS**

- Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.

- Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso evitará conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas.  Se você preferir, é possível usar LVM ou [RAID](virtual-machines-linux-configure-raid.md) em discos de dados.

- Não há suporte para NUMA para tamanhos de máquinas virtuais maiores devido a um bug nas versões do kernel Linux abaixo de 2.6.37. Esse problema afeta principalmente distribuições que usam o kernel upstream do Red Hat 2.6.32. A instalação manual do agente Linux do Azure (waagent) desabilita a NUMA automaticamente na configuração do GRUB para o kernel do Linux. Verifique as etapas a seguir para obter mais informações a esse respeito.

- Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários.  Verifique as etapas a seguir para obter mais informações a esse respeito.

- Todos os VHDs devem ter tamanhos que são múltiplos de 1 MB.


## <a id="centos6"> </a>CentOS 6.x ##

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela do console para a máquina virtual.

3. Desinstale o NetworkManager executando o seguinte comando:

		# sudo rpm -e --nodeps NetworkManager

	**Observação:** se o pacote ainda não estiver instalado, esse comando falhará com uma mensagem de erro. Isso é esperado.

4.	Crie um arquivo chamado **network** no diretório `/etc/sysconfig/` que contenha o seguinte texto:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

5.	Crie um arquivo chamado **ifcfg-eth0** no diretório `/etc/sysconfig/network-scripts/` que contenha o seguinte texto:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

6.	Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet. Essas regras provocam problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/


7. Certifique-se de que o serviço de rede será iniciado na inicialização executando o seguinte comando:

		# sudo chkconfig network on


8. **Somente para CentOS 6.3**: Instale os drivers dos Serviços de Integração Linux

	**Importante: Essa etapa só é válida para o CentOS 6.3 e versões anteriores.**  No CentOS 6.4+, os Serviços de Integração do Linux são *already available in the kernel*.

	a) Obtenha o arquivo .iso que contém os drivers dos Serviços de Integração do Linux no [Centro de Download da Microsoft](http://www.microsoft.com/pt-br/download/details.aspx?id=41554).

	b) No Gerenciador do Hyper-V, no painel **Ações**, clique em **Configurações**.

	![Open Hyper-V settings](./media/virtual-machines-linux-create-upload-vhd/settings.png)

	c) No painel **Hardware**, clique em **Controlador IDE 1**.

	![Add DVD drive with install media](./media/virtual-machines-linux-create-upload-vhd/installiso.png)

	d) Na caixa **Controlador IDE**, clique em **Unidade de DVD** e em **Adicionar**.

	e) Selecione o **Arquivo de imagem**, navegue até **Linux IC v3.2.iso** e clique em **Abrir**.

	f) Na página **Configurações**, clique em **OK**.

	g) Clique em **Conectar** para abrir a janela da máquina virtual.

	h) Na janela Prompt de comando, digite os seguintes comandos:

		# sudo mount /dev/cdrom /media
		# sudo /media/install.sh
		# sudo reboot

9. Execute o comando a seguir para instalar o pacote python-pyasn1:

		# sudo yum install python-pyasn1

10. Se quiser usar os espelhos OpenLogic hospedados em datacenters Azure, substitua o arquivo /etc/yum.repos.d/CentOS-Base.repo com os repositórios mencionados a seguir.  Essa ação adiciona o repositório **[[openlogic]]**, que inclui pacotes para o agente Linux do Azure:

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

	**Observação:** O restante deste guia parte do pressuposto de que você está usando, no mínimo, o repositório [openlogic], que será usado a seguir para instalar o agente Linux do Azure.


11.	Adicione a seguinte linha a /etc/yum.conf:

		http_caching=packages

	E **somente para o CentOS 6.3** adicione a seguinte linha:

		exclude=kernel*

12. Desabilite o módulo yum "fastestmirror", editando o arquivo "/etc/yum/pluginconf.d/fastestmirror.conf" e, em `[main]`, digite o seguinte

		set enabled=0

13.	Execute o comando a seguir para limpar os metadados atuais do yum:

		# yum clean all

14. **Somente para o CentOS 6.3**, atualize o kernel usando este comando:

		# sudo yum --disableexcludes=all install kernel

15.	Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/boot/grub/menu.lst" em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração. Essa ação desabilita a NUMA devido a um bug na versão do kernel usada pelo CentOS 6.

	Além disso, recomendamos *remove* os seguintes parâmetros:

		rhgb quiet crashkernel=auto

	As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.

	Você pode deixar configurada a opção  `crashkernel`, mas esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.


16.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.

17. Instale o Agente Linux do Azure executando o seguinte comando:

		# sudo yum install WALinuxAgent

	Observe que a instalação do pacote WALinuxAgent removerá o NetworkManager e os pacotes NetworkManager-gnome se eles já não tiverem sido removidos conforme descrito na etapa 2.

18.	Não crie espaço swap no disco do sistema operacional

	O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporary* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19.	Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

20. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.


----------


## <a id="centos7"> </a>CentOS 7.0+ ##

**Alterações no CentOS 7 (e em derivativos similares)**

A preparação de uma máquina virtual CentOS 7 para o Azure é muito parecida com a preparação das máquinas virtuais CentOS 6, mas há diversas diferenças que merecem atenção:

 - O pacote do NetworkManager não entra mais em conflito com o agente Linux do Azure. Esse pacote é instalado por padrão e recomendamos que você não o remova.
 - O GRUB2 agora é usado como carregador de inicialização padrão. Com isso, o procedimento de edição de parâmetros do kernel mudou (confira abaixo).
 - O XFS agora é o sistema de arquivos padrão. Ainda é possível usar o sistema de arquivos ext4 se você preferir.


**Etapas da configuração**

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela do console para a máquina virtual.

3.	Crie um arquivo chamado **network** no diretório `/etc/sysconfig/` que contenha o seguinte texto:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

4.	Crie um arquivo chamado **ifcfg-eth0** no diretório `/etc/sysconfig/network-scripts/` que contenha o seguinte texto:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

5.	Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet. Essas regras provocam problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6. Certifique-se de que o serviço de rede será iniciado na inicialização executando o seguinte comando:

		# sudo chkconfig network on

7. Execute o comando a seguir para instalar o pacote python-pyasn1:

		# sudo yum install python-pyasn1

8. Se quiser usar os espelhos OpenLogic hospedados em datacenters Azure, substitua o arquivo /etc/yum.repos.d/CentOS-Base.repo com os repositórios mencionados a seguir.  Essa ação adiciona o repositório **[[openlogic]]**, que inclui pacotes para o agente Linux do Azure:

		[openlogic]
		name=CentOS-$releasever - openlogic packages for $basearch
		baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
		enabled=1
		gpgcheck=0
		
		[base]
		name=CentOS-$releasever - Base
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#released updates
		[updates]
		name=CentOS-$releasever - Updates
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#additional packages that may be useful
		[extras]
		name=CentOS-$releasever - Extras
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#additional packages that extend functionality of existing packages
		[centosplus]
		name=CentOS-$releasever - Plus
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#contrib - packages by Centos Users
		[contrib]
		name=CentOS-$releasever - Contrib
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		


	**Observação:** O restante deste guia parte do pressuposto de que você está usando, no mínimo, o repositório [openlogic], que será usado a seguir para instalar o agente Linux do Azure.

9.	Execute o comando a seguir para limpar os metadados atuais do yum e instalar atualizações:

		# sudo yum clean all
		# sudo yum -y update

10.	Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/etc/default/grub" em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:

		GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

	Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração. Além disso, recomendamos *remove* os seguintes parâmetros:

		rhgb quiet crashkernel=auto

	As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.

	Você pode deixar configurada a opção  `crashkernel`, mas esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

11. Depois de editar "/etc/default/grub" como mostramos acimo, execute o comando a seguir para recompilar a configuração do grub:

		# sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.

13. Instale o Agente Linux do Azure executando o seguinte comando:

		# sudo yum install WALinuxAgent

14.	Não crie espaço swap no disco do sistema operacional

	O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporary* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

16. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.



<!--HONumber=45--> 
