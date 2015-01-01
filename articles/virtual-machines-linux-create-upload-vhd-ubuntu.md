<properties urlDisplayName="Upload an Ubuntu Linux VHD" pageTitle="Criar e carregar um VHD do Ubuntu Linux no Azure" metaKeywords="Azure VHD, uploading Linux VHD, Ubuntu" description="Learn to create and upload an Azure virtual hard disk (VHD) that contains an Ubuntu Linux operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains an Ubuntu Linux Operating System" authors="szarkos" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="szarkos" />


# Preparar uma máquina virtual do Ubuntu para o Azure

##Pré-requisitos##

Este artigo pressupõe que você já instalou um sistema operacional Ubuntu Linux em um disco rígido virtual. Existem várias ferramentas para criar arquivos .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instalar a função Hyper-V e configurar uma máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx). 

**Notas de instalação do Ubuntu**

- Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco em formato VHD usando o Gerenciador do Hyper-V ou o cmdlet convert-vhd.

- Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso evitará conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas.  LVM ou [RAID](../virtual-machines-linux-configure-raid) pode ser usado em discos de dados, se preferir.

- Não configure uma partição de permuta no disco do SO. O agente Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporários.  Verifique as etapas a seguir para obter mais informações a esse respeito.

- Todos os VHDs devem ter tamanhos que são múltiplos de 1 MB.


## <a id="ubuntu"> </a>Ubuntu 12.04+ ##

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **Conectar** para abrir a janela para a máquina virtual.

3.	Substitua os repositórios atuais na imagem para usar os repositórios do Azure no Ubuntu. As etapas variam um pouco dependendo da versão do Ubuntu.

	Antes de editar o /etc/apt/sources.list, é recomendável fazer um backup

		# sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

	Ubuntu 12.04:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
		# sudo apt-get update

	Ubuntu 12.10:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
		# sudo apt-get update

	Ubuntu 14.04+:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-get update

4. Atualize o sistema operacional para o kernel mais recente, executando os seguintes comandos: 

	Ubuntu 12.04:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

	Ubuntu 12.10:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot
	
	Ubuntu 14.04+:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

5.	Opcional: se o sistema Ubuntu encontrar um erro e for reinicializado, geralmente ele aguardará a ação do usuário no prompt de inicialização do Grub, o que impede a inicialização adequada do sistema. Para evitar isso, conclua as seguintes etapas:

	a) Abra o arquivo /etc/grub.d/00_header.

	b) Na função **make_timeout()**, pesquise por **if ["\${recordfail}" = 1 ]; então**

	c) Altere a instrução abaixo desta linha para **set timeout=5**.

	d) Execute 'sudo update-grub'.

6. Modifique a linha de inicialização para o Grub para incluir parâmetros adicionais de kernel para o Azure. Para fazer isso, abra /etc/default/grub em um editor de texto, localize a variável chamada `GRUB_CMDLINE_LINUX_DEFAULT` (ou adicione-a, se necessário) e edite-a para incluir os seguintes parâmetros:

		GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

	Salve e feche o arquivo e execute '`sudo update-grub`'. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, que pode auxiliar o suporte técnico do Azure com problemas de depuração. 

8.	Confira se o servidor SSH está instalado e configurado para iniciar no tempo de inicialização.  Geralmente, esse é o padrão.

9.	Instale o Agente Linux do Azure:

		# sudo apt-get update
		# sudo apt-get install walinuxagent

	Observe que a instalação do pacote 'walinuxagent' removerá o 'NetworkManager' e os pacotes 'NetworkManager-gnome', se estiverem instalados.

10.	Execute os comandos a seguir para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

11. Clique em **Ação -> Desligar** no Gerenciador do Hyper-V.Agora, seu VHD Linux está pronto para ser carregado no Azure.



<!--HONumber=35_1-->
