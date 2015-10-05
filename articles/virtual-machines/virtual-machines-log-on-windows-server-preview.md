<properties
	pageTitle="Faça logon em uma VM do Windows Server | Microsoft Azure"
	description="Saiba como fazer logon em uma VM do Windows Server usando o portal de visualização do Azure e o modelo de implantação do Gerenciador de Recursos."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="cynthn"/>

# Como fazer logon em uma máquina virtual executando o Windows Server 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda o logon em uma VM do Windows usando o modelo de implantação do Gerenciador de Recursos. Você também pode criar um recurso com o [modelo de implantação clássico](virtual-machines-log-on-windows-server.md).

Você usará o botão **Conectar** no portal de visualização do Azure para iniciar uma sessão de área de trabalho remota. Primeiro, conecte-se à máquina virtual, em seguida faça logon.

## Conectar-se à máquina virtual

1. Se você ainda não o fez, entre no [portal de visualização do Azure](https://portal.azure.com/).

2.	No menu Hub, clique em **Procurar**.

3.	Na folha de pesquisa, role para baixo e clique em **Máquinas virtuais**.

	![Procurar máquinas virtuais](./media/virtual-machines-log-on-windows-server-preview/search-blade-preview-portal.png)

4.	Selecione a máquina virtual na lista.

5. Na folha da máquina virtual, clique em **Conectar**.

	![Conectar-se à máquina virtual](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## Faça logon na máquina virtual

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Solucionar problemas

Se as dicas sobre como fazer logon não ajudarem ou não forem o que você precisa, consulte [Solucionar problemas de conexões de área de trabalho remota com uma máquina virtual do Azure baseada no Windows](virtual-machines-troubleshoot-remote-desktop-connections.md). Este artigo orienta você no diagnóstico e na solução de problemas comuns.

<!---HONumber=Sept15_HO4-->