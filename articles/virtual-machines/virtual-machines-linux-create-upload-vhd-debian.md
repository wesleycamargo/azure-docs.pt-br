<properties
	pageTitle="Preparar o VHD do Linux Debian| Microsoft Azure"
	description="Aprenda a criar arquivos VHD no Debian 7 e 8 para implantação no Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="mingzhan"/>




#Preparar VHD do Debian para o Azure

##Pré-requisitos
Esta seção pressupõe que você já instalou um sistema operacional Linux Debian a partir de um arquivo .iso baixado do [site do Debian](https://www.debian.org/distrib/) para um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd; Hyper-V é apenas um exemplo. Para obter instruções sobre como usar a Hyper-V, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).


## Notas de Instalação

- Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet **convert-vhd**.
- Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso irá evitar conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. Se você preferir, é possível usar LVM ou RAID em discos de dados.
- Não configure uma partição de permuta no disco do SO. O agente Linux do Azure pode ser configurado para criar um arquivo de permuta no disco de recursos temporários. Verifique as etapas a seguir para obter mais informações a esse respeito.
- Todos os VHDs devem ter tamanhos que sejam múltiplos de 1 MB.


##Debian 7.x and 8.x

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela do console para a máquina virtual.

3. Comente a linha para **deb cdrom** na `/etc/apt/source.list` se você configurar a VM relacionada a um arquivo ISO.

4. Edite o arquivo `/etc/default/grub` e modifique o parâmetro **GRUB\_CMDLINE\_LINUX** da seguinte maneira para incluir parâmetros adicionais de kernel para o Azure.

        GRUB_CMDLINE_LINUX="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

5. Recompile o grub e execute:

        # sudo update-grub 

6. Instale os pacotes de dependência do Agent Linux do Azure:

        # apt-get install -y git parted

7.	Instale o Agente Linux do Azure do Github usando [diretriz](virtual-machines-linux-update-agent.md) e escolha a versão 2.0.14:

			# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
			# chmod +x waagent
			# cp waagent /usr/sbin
			# /usr/sbin/waagent -install -verbose

8. Desprovisione a máquina virtual, prepare-a para provisionamento no Azure e execute:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
 
9. Clique em **Ação** -> Desligar no Gerenciador do Hyper-V. Agora, seu VHD Linux está pronto para ser carregado no Azure.

##Usando o Script do Credativ para criar o VHD do Debian

Há um script disponível no site do Credativ que poderá ajudá-lo a criar o VHD do Debian automaticamente. Você pode baixá-lo [aqui](https://gitlab.credativ.com/de/azure-manage) e instalá-lo na sua VM do Linux. Para criar um VHD do Debian (por exemplo, Debian 7) execute:

        # azure_build_image --option release=wheezy --option image_prefix=lilidebian7 --option image_size_gb=30 SECTION

Se houver qualquer problema para usar esse script, apenas informe o problema para o Credativ [aqui](https://gitlab.credativ.com/groups/de/issues).

## Próximas etapas

Agora você está pronto para usar o .vhd do Debian para criar novas Máquinas Virtuais do Azure.

<!---HONumber=AcomDC_1203_2015-->