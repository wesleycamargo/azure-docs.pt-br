<properties
	pageTitle="Usando a Área de Trabalho Remota para se conectar a uma VM Linux do Microsoft Azure."
	description="Saiba como instalar e configurar a Área de Trabalho Remota em uma VM Linux do Microsoft Azure."
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
	ms.date="09/14/2015"
	ms.author="mingzhan"/>


#Usando a Área de Trabalho Remota para se conectar a uma VM Linux do Microsoft Azure

##Visão geral

RDP (Remote Desktop Protocol) é um protocolo proprietário usado para Windows, como podemos usar o RDP para se conectar remotamente à VM (máquina virtual) Linux?

Essa orientação fornecerá a resposta! Ela ajudará você com a instalação e configuração de xrdp em sua VM Linux do Microsoft Azure, e você poderá se conectar com a Área de Trabalho Remota de uma máquina com Windows. Usaremos a VM do Linux executando o Ubuntu ou OpenSUSE como o exemplo neste guia.

Xrdp é um servidor RDP do código aberto que permite a conexão do servidor Linux com a Área de Trabalho Remota de uma máquina com Windows. Ele é executado com muito mais qualidade do que a VNC (Computação de Rede Virtual). A VNC possui uma qualidade "JPEG" e comportamento lento, enquanto o RDP é rápido e claro.
 

> [AZURE.NOTE]Você já deve ter uma VM do Microsoft Azure executando o Linux. Para criar e configurar uma VM Linux, consulte o [tutorial da VM Linux do Azure](virtual-machines-linux-tutorial.md)


##Criar ponto de extremidade para a Área de Trabalho Remota
Usaremos o ponto de extremidade padrão 3389 para a Área de Trabalho Remota neste documento. Portanto, configure o ponto de extremidade 3389 como a Área de Trabalho Remota para sua VM Linux, como abaixo:


![imagem](./media/virtual-machines-linux-remote-desktop/no1.png)


Se você não sabe como configurar o ponto de extremidade para sua VM, consulte [orientação](virtual-machines-set-up-endpoints.md).


##Instalar o Gnome Desktop

Conecte-se à VM Linux por meio de putty e instale `Gnome Desktop`.

Para o Ubuntu, use:

	#sudo apt-get update
	#sudo apt-get install ubuntu-desktop


Para OpenSUSE, use:

	#sudo zypper install gnome-session

##Instalar o xrdp

Para o Ubuntu, use:

	#sudo apt-get install xrdp

Para OpenSUSE, use:

> [AZURE.NOTE]Atualize a versão de OpenSUSE com a versão que você está usando no comando abaixo. Veja a seguir um exemplo de comando para `OpenSUSE 13.2`.

	#sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


##Inicie o xrdp e defina o serviço xdrp na inicialização

Para OpenSUSE, use:

	#sudo systemctl start xrdp
	#sudo systemctl enable xrdp

Para Ubuntu, o xrdp será iniciado e habilitado automaticamente na inicialização após a instalação.

##Usando xfce se você estiver usando a versão do Ubuntu posterior ao Ubuntu 12.04LTS

Como o xrdp atual não dá suporte ao Gnome Desktop da versão do Ubuntu posterior ao Ubuntu 12.04LTS, usaremos o `xfce` Desktop em vez disso.

Instale `xfce`, use:

    #sudo apt-get install xubuntu-desktop

Em seguida, habilite `xfce`, use:
    
    #echo xfce4-session >~/.xsession

Edite o arquivo de configuração `/etc/xrdp/startwm.sh`, use:

    #sudo vi /etc/xrdp/startwm.sh   

Adicione a linha `xfce4-session` antes da linha `/etc/X11/Xsession`.

Reinicie o serviço xrdp, use:

    #sudo service xrdp restart


##Conectar-se à VM Linux de uma máquina com Windows
Em uma máquina com Windows, inicie o cliente de área de trabalho remoto, insira o nome DNS de sua VM Linux ou acesse `Dashboard` de sua VM no Portal do Azure e clique em `Connect` para se conectar à sua VM Linux. Você verá a janela de logon abaixo:

![imagem](./media/virtual-machines-linux-remote-desktop/no2.png)

Faça logon com o `user` e a `password` de sua VM Linux e aproveite agora mesmo a Área de Trabalho Remota de sua VM Linux do Microsoft Azure!


##Avançar
Para saber mais sobre como usar o xrdp, clique [aqui](http://www.xrdp.org/).





 

<!---HONumber=Sept15_HO3-->