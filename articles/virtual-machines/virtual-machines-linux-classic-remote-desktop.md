---
title: "Área de Trabalho Remota para uma VM do Linux | Microsoft Docs"
description: "Saiba como instalar e configurar a Área de Trabalho Remota em uma VM do Linux no Microsoft Azure."
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 98b2f3d9108c3f7a4179f5756d56fce9c5acd915


---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Usando a Área de Trabalho Remota para se conectar a uma VM do Linux do Microsoft Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="overview"></a>Visão geral
O protocolo RDP é um protocolo proprietário usado para o Windows. Como podemos usar o RDP para se conectar remotamente a uma VM (máquina virtual) do Linux?

Essa orientação fornecerá a resposta! Ela ajudará você com a instalação e configuração de xrdp em sua VM Linux do Microsoft Azure, e você poderá se conectar com a Área de Trabalho Remota de uma máquina com Windows. Usaremos a VM do Linux executando o Ubuntu ou OpenSUSE como o exemplo neste guia.

Xrdp é um servidor RDP do código aberto que permite a conexão do servidor Linux com a Área de Trabalho Remota de uma máquina com Windows. Ele é executado com muito mais qualidade do que a VNC (Computação de Rede Virtual). A VNC possui uma qualidade "JPEG" e comportamento lento, enquanto o RDP é rápido e claro.

> [!NOTE]
> Você já deve ter uma VM do Microsoft Azure executando o Linux. Para criar e configurar uma VM do Linux, veja o [Tutorial de VM do Linux do Azure](virtual-machines-linux-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).
> 
> 

## <a name="create-endpoint-for-remote-desktop"></a>Criar ponto de extremidade para a Área de Trabalho Remota
Usaremos o ponto de extremidade padrão 3389 para a Área de Trabalho Remota neste documento. Portanto, configure o ponto de extremidade 3389 como a Área de Trabalho Remota para sua VM Linux, como abaixo:

![imagem](./media/virtual-machines-linux-classic-remote-desktop/no1.png)

se você não sabe como configurar o ponto de extremidade para a sua VM, veja as [diretrizes](virtual-machines-linux-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="install-gnome-desktop"></a>Instalar o Gnome Desktop
Conecte-se à sua VM do Linux por meio do PuTTY e instale o `Gnome Desktop`.

Para o Ubuntu, use:

    #sudo apt-get update
    #sudo apt-get install ubuntu-desktop


Para OpenSUSE, use:

    #sudo zypper install gnome-session

## <a name="install-xrdp"></a>Instalar o xrdp
Para o Ubuntu, use:

    #sudo apt-get install xrdp

Para OpenSUSE, use:

> [!NOTE]
> Atualize a versão do OpenSUSE com a versão que você está usando com o comando abaixo. Veja abaixo um exemplo de comando para o `OpenSUSE 13.2`.
> 
> 

    #sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Inicie o xrdp e defina o serviço xdrp na inicialização
Para OpenSUSE, use:

    #sudo systemctl start xrdp
    #sudo systemctl enable xrdp

Para Ubuntu, o xrdp será iniciado e habilitado automaticamente na inicialização após a instalação.

## <a name="using-xfce-if-you-are-using-ubuntu-version-later-than-ubuntu-1204lts"></a>Usando xfce se você estiver usando a versão do Ubuntu posterior ao Ubuntu 12.04LTS
Como o xrdp atual não dava suporte ao Gnome Desktop da versão do Ubuntu posterior ao Ubuntu 12.04LTS, usaremos o `xfce` Desktop em vez disso.

Instale o `xfce`, use:

    #sudo apt-get install xubuntu-desktop

Em seguida, habilite `xfce`, use:

    #echo xfce4-session >~/.xsession

Edite o arquivo de configuração `/etc/xrdp/startwm.sh`, use:

    #sudo vi /etc/xrdp/startwm.sh   

Adicione a linha `xfce4-session` antes da linha `/etc/X11/Xsession`.

Reinicie o serviço xrdp, use:

    #sudo service xrdp restart


## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Conectar-se à VM Linux de uma máquina com Windows
Em um computador com Windows, inicie o cliente de área de trabalho remota, insira o nome DNS da VM do Linux ou acesse `Dashboard` da VM no portal clássico do Azure e clique em `Connect` para se conectar à VM do Linux. Você verá a janela de logon abaixo:

![imagem](./media/virtual-machines-linux-classic-remote-desktop/no2.png)

Faça logon com o `user` & `password` de sua VM do Linux e aproveite agora mesmo a Área de Trabalho Remota de sua VM do Linux no Microsoft Azure!

## <a name="next"></a>Avançar
Para obter mais informações sobre como usar o xrdp, acesse [aqui](http://www.xrdp.org/).




<!--HONumber=Nov16_HO3-->


