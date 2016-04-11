<properties
	pageTitle="Preparar o VHD do Linux Debian| Microsoft Azure"
	description="Aprenda a criar arquivos VHD no Debian 7 e 8 para implantação no Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/25/2016"
	ms.author="szark"/>




# Preparar um VHD do Debian para o Azure

## Pré-requisitos
Esta seção pressupõe que você já instalou um sistema operacional Linux Debian a partir de um arquivo .iso baixado do [site do Debian](https://www.debian.org/distrib/) para um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd; Hyper-V é apenas um exemplo. Para obter instruções sobre como usar a Hyper-V, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).


## Notas de instalação

- Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet **convert-vhd**.
- Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se você preferir, é possível usar LVM ou RAID em discos de dados.
- Não configure uma partição de permuta no disco do SO. O agente Linux do Azure pode ser configurado para criar um arquivo de permuta no disco de recursos temporários. Verifique as etapas a seguir para obter mais informações a esse respeito.
- Todos os VHDs devem ter tamanhos que sejam múltiplos de 1 MB.


## Usar o gerenciamento do Azure para criar VHDs Debian

Existem ferramentas disponíveis para gerar VHDs Debian para o Azure, como os scripts [azure-manage](https://gitlab.credativ.com/de/azure-manage) do [credativ](http://www.credativ.com/). Essa é a abordagem recomendada em vez de criar uma imagem do zero. Por exemplo, para criar um VHD no Debian 8, execute os seguintes comandos para baixar o gerenciamento do Azure (e dependências) e execute o script azure\_build\_image:

	# sudo apt-get update
	# sudo apt-get install git qemu-utils mbr kpartx debootstrap

	# sudo apt-get install python3-pip
	# sudo pip3 install azure-storage azure-servicemanagement-legacy pytest pyyaml
	# git clone https://gitlab.credativ.com/de/azure-manage.git
	# cd azure-manage
	# sudo pip3 install .

	# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## Preparar manualmente um VHD do Debian

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela do console para a máquina virtual.

3. Comente a linha para **deb cdrom** na `/etc/apt/source.list` se você configurar a VM relacionada a um arquivo ISO.

4. Edite o arquivo `/etc/default/grub` e modifique o parâmetro **GRUB\_CMDLINE\_LINUX** da seguinte maneira para incluir parâmetros adicionais de kernel para o Azure.

        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"

5. Recompile o grub e execute:

        # sudo update-grub

6. Adicione repositórios do Azure do Debian a /etc/apt/sources.list para Debian 6 ou 7:

	**Debian 6.x "Wheezy"**

		deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
		deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
		deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
		deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main


	**Debian 7.x "Jessie"**

		deb http://debian-archive.trafficmanager.net/debian jessie-backports main
		deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
		deb http://debian-archive.trafficmanager.net/debian-azure jessie main
		deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


7. Instale o Agente Linux do Azure:

		# sudo apt-get update
		# sudo apt-get install waagent

8. Para o Debian 7, é necessário executar o kernel baseado no 3.16 do repositório de backports do wheezy. Primeiro, crie um arquivo chamado /etc/apt/preferences.d/linux.pref com o seguinte conteúdo:

		Package: linux-image-amd64 initramfs-tools
		Pin: release n=wheezy-backports
		Pin-Priority: 500

	Em seguida, execute "sudo apt-get install linux-image-amd64" para instalar o novo kernel.

8. Desprovisione a máquina virtual, prepare-a para provisionamento no Azure e execute:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. Clique em **Ação** -> Desligar no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.


## Próximas etapas

Agora, você está pronto para usar o disco rígido virtual Debian para criar novas máquinas virtuais no Azure. Se esta é a primeira vez que você está carregando o arquivo .vhd no Azure, veja as etapas 2 e 3 em [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux](virtual-machines-linux-classic-create-upload-vhd.md).

<!---HONumber=AcomDC_0330_2016-->