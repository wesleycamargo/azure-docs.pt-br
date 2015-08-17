<properties title="Prepare an Oracle Linux Virtual Machine for Azure" pageTitle="Preparar uma máquina virtual Oracle Linux para o Azure" description="Percorra a configuração de uma máquina virtual do Oracle com Linux no Microsoft Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Preparar uma máquina virtual Oracle Linux para o Azure
-   [Preparar uma máquina virtual do Oracle Linux 6.4 e versões posteriores para o Azure](virtual-machines-linux-create-upload-vhd-oracle.md)

-   [Preparar uma máquina virtual do Oracle Linux 7.0 e versões posteriores para o Azure](virtual-machines-linux-create-upload-vhd-oracle.md)

##Pré-requisitos
Este artigo pressupõe que você já instalou um sistema operacional Oracle Linux em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do Oracle Linux**

- O kernel da Oracle, compatível com Red Hat, e seu UEK3 (Unbreakable Enterprise Kernel) têm suporte no Hyper-V e no Azure. Para obter os melhores resultados, atualize o kernel durante a preparação do VHD Oracle Linux.

- O UEK2 da Oracle não é compatível com o Hyper-V nem com o Azure, pois não contém os drivers necessários.

- Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.

- Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se você preferir, é possível usar LVM ou [RAID](virtual-machines-linux-configure-raid.md) em discos de dados.

- Não há suporte para NUMA para tamanhos de máquinas virtuais maiores devido a um bug nas versões do kernel Linux abaixo de 2.6.37. Esse problema afeta principalmente distribuições que usam o kernel upstream do Red Hat 2.6.32. A instalação manual do agente Linux do Azure (waagent) desabilita a NUMA automaticamente na configuração do GRUB para o kernel do Linux. Verifique as etapas a seguir para obter mais informações a esse respeito.

- Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários. Verifique as etapas a seguir para obter mais informações a esse respeito.

- Todos os VHDs devem ter tamanhos que sejam múltiplos de 1 MB.

##Oracle Linux 6.4+
Você deve concluir as etapas de configuração específicas do sistema operacional para que a Máquina virtual seja executada no Azure.

1. No painel central do Gerenciador do Hyper-V, selecione a Máquina virtual.

2. Clique em **Conectar** para abrir a janela da Máquina virtual.

3. Desinstale o NetworkManager executando o seguinte comando:

		# sudo rpm -e --nodeps NetworkManager

	>[AZURE.NOTE]Se o pacote ainda não foi instalado, esse comando falhará com uma mensagem de erro. Isso é esperado.

4. Crie um arquivo chamado **network** no diretório /etc/sysconfig/ que contenha o seguinte texto:

	`NETWORKING=yes` `HOSTNAME=localhost.localdomain`

5.  Crie um arquivo chamado **ifcfg-eth0** no diretório /etc/sysconfig/network-scripts/ que contenha o seguinte texto:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
			TYPE=Ethernet
			USERCTL=no
			PEERDNS=yes
		IPV6INIT=no

6.  Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet. Essas regras provocam problemas ao clonar uma Máquina virtual no Azure ou no Hyper-V:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

7.  Certifique-se de que o serviço de rede será iniciado na inicialização executando o seguinte comando:

		# chkconfig network on

8.  Instale o python-pyasn1 executando o seguinte comando:

		# sudo yum install python-pyasn1

9.  Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/boot/grub/menu.lst" em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração. Essa ação desabilita a NUMA devido a um bug no kernel da Oracle compatível com o Red Hat.

	Além disso, recomendamos que você *remova* os seguintes parâmetros:

		rhgb quiet crashkernel=auto

	As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.

	Você pode deixar configurada a opção `crashkernel`, mas esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

10.  Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Geralmente, esse é o padrão.

11.  Instale o Agente Linux do Azure executando o seguinte comando:

		# sudo yum install WALinuxAgent

	Observe que a instalação do pacote WALinuxAgent removerá o NetworkManager e os pacotes NetworkManager-gnome se eles já não tiverem sido removidos conforme descrito na etapa 2.

12.  Não crie espaço de permuta no disco do SO.

	O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

		ResourceDisk.Format=y
		
		ResourceDisk.Filesystem=ext4
		
		ResourceDisk.MountPoint=/mnt/resource
		
		ResourceDisk.EnableSwap=y
		
		ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.

13.  Execute os comandos a seguir para desprovisionar a Máquina virtual e prepará-la para provisionamento no Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

14.  Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

##Oracle Linux 7.0 ou posterior
**Alterações no Oracle Linux 7**

A preparação de uma Máquina virtual Oracle Linux 7 para o Azure é muito parecida com a preparação das máquinas virtuais Oracle Linux 6, mas há diversas diferenças que merecem atenção:

-   O kernel compatível com Red Hat e o UEK3 da Oracle são compatíveis com o Azure. Recomenda-se usar o kernel UEK3.

-   O pacote do NetworkManager não entra mais em conflito com o agente Linux do Azure. Esse pacote é instalado por padrão e recomendamos que você não o remova.

-   O GRUB2 agora é usado como carregador de inicialização padrão. Com isso, o procedimento de edição de parâmetros do kernel mudou (confira abaixo).

-   O XFS agora é o sistema de arquivos padrão. Ainda é possível usar o sistema de arquivos ext4 se você preferir.

**Etapas da configuração**

1.  No Gerenciador do Hyper-V, selecione a Máquina virtual.

2.  Clique em **Conectar** para abrir a janela do console para a Máquina virtual.

3.  Crie um arquivo chamado **network** no diretório /etc/sysconfig/ que contenha o seguinte texto:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

4.  Crie um arquivo chamado **ifcfg-eth0** no diretório /etc/sysconfig/network-scripts/ que contenha o seguinte texto:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
			PEERDNS=yes
		IPV6INIT=no

5.  Mova (ou remova) as regras de udev para evitar a geração de regras estáticas da interface Ethernet. Essas regras provocam problemas ao clonar uma Máquina virtual no Microsoft Azure ou no Hyper-V:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6.  Certifique-se de que o serviço de rede será iniciado na inicialização executando o seguinte comando:

		# sudo chkconfig network on

7.  Execute o comando a seguir para instalar o pacote python-pyasn1:

		# sudo yum install python-pyasn1

8.  Execute o comando a seguir para limpar os metadados atuais do yum e instalar atualizações:

		# sudo yum clean all
		# sudo yum -y update

9.  Modifique a linha de inicialização do kernel em sua configuração de grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/etc/default/grub" em um editor de texto e edite o parâmetro GRUB\_CMDLINE\_LINUX. Por exemplo:

		GRUB\_CMDLINE\_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

	Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte do Azure com problemas de depuração. Além disso, recomendamos que você *remova* os seguintes parâmetros:

		rhgb quiet crashkernel=auto

	As inicializações gráfica e silenciosa não são úteis em ambientes de rede, quando queremos que todos os logs sejam enviados para a porta serial.

	Você pode deixar configurada a opção `crashkernel`, mas esse parâmetro reduz a memória disponível na máquina virtual em 128 MB ou mais, o que pode ser um problema em máquinas virtuais menores.

10.  Depois de editar "/etc/default/grub" como mostramos acimo, execute o comando a seguir para recompilar a configuração do grub:

		# sudo grub2-mkconfig -o /boot/grub2/grub.cfg

11.  Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Geralmente, esse é o padrão.

12.  Instale o Agente Linux do Azure executando o seguinte comando:

		# sudo yum install WALinuxAgent

13.  Não crie espaço swap no disco do sistema operacional

	O Agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o Agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros em /etc/waagent.conf de maneira apropriada:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.

14.  Execute os comandos a seguir para desprovisionar a Máquina virtual e prepará-la para provisionamento no Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

15.  Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

<!---HONumber=August15_HO6-->