<properties
	pageTitle="Criar e carregar um VHD do Ubuntu Linux no Azure"
	description="Saiba como criar e carregar um disco rígido virtual (VHD) do Azure que contém o sistema operacional Ubuntu Linux."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="szark"/>

# Preparar uma máquina virtual do Ubuntu para o Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Imagens de nuvem oficiais do Ubuntu
O Ubuntu agora publica VHDs oficiais do Azure para download em [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/). Se você precisar compilar sua própria imagem do Ubuntu especializada para o Azure, em vez de usar o procedimento manual abaixo, é recomendável começar com esses VHDs de trabalho conhecidos e personalizá-los conforme necessário. As versões mais recentes da imagem sempre podem ser encontradas nos seguintes locais:

 - Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/precise/release/ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
 - Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)


## Pré-requisitos

Este artigo pressupõe que você já instalou um sistema operacional Ubuntu Linux em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do Ubuntu**

- Veja também as [Notas de instalação gerais do Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
- O formato VHDX não tem suporte no Azure, somente o **VHD fixo**. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.
- Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se preferir, você poderá usar [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) nos discos de dados.
- Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários. Verifique as etapas a seguir para obter mais informações a esse respeito.
- Todos os VHDs devem ter tamanhos que sejam múltiplos de 1 MB.


## Etapas manuais

> [AZURE.NOTE] Antes de criar sua própria imagem personalizada do Ubuntu para o Azure, considere usar as imagens em [http://cloud-images.ubuntu.com/](http://cloud-images.ubuntu.com/) como alternativa.


1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela da máquina virtual.

3.	Substitua os repositórios atuais na imagem para usar os repositórios do Azure no Ubuntu. As etapas variam um pouco dependendo da versão do Ubuntu.

	Antes de editar o /etc/apt/sources.list, é recomendável fazer um backup:

		# sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

	Ubuntu 12,04:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-get update

	Ubuntu 14.04:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-get update

4. As imagens do Ubuntu Azure estão seguindo o kernel *HWE* (Habilitação de Hardware). Atualize o sistema operacional para o kernel mais recente, executando os seguintes comandos:

	Ubuntu 12,04:

		# sudo apt-get update
		# sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
		# sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

	Ubuntu 14.04:

		# sudo apt-get update
		# sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
		# sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot


5. Modifique a linha de inicialização para o Grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra "/etc/default/grub" em um editor de texto, localize a variável chamada `GRUB_CMDLINE_LINUX_DEFAULT` (ou adicione-a, se necessário) e edite-a para incluir os seguintes parâmetros:

		GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

	Salve e feche o arquivo e execute '`sudo update-grub`'. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte técnico do Azure com problemas de depuração.

6.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização. Geralmente, esse é o padrão.

7.	Instale o Agente Linux do Azure:

		# sudo apt-get update
		# sudo apt-get install walinuxagent

	Observe que a instalação do pacote `walinuxagent` removerá os pacotes `NetworkManager` e `NetworkManager-gnome`, se estiverem instalados.

8.	Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

9. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.


## Próximas etapas
Agora, você está pronto para usar o disco rígido virtual Ubuntu Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você estiver carregando o arquivo .vhd no Azure, veja as etapas 2 e 3 em [Criando e carregando um disco rígido virtual que contém o sistema operacional Linux](virtual-machines-linux-classic-create-upload-vhd.md).

## Referências ##

Kernel de Habilitação de Hardware do Ubuntu (HWE):

- [http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
- [http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)

<!---HONumber=AcomDC_0831_2016-->