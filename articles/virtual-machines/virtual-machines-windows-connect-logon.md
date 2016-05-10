<properties
	pageTitle="Conectar uma VM do Windows Server | Microsoft Azure"
	description="Saiba como se conectar e fazer logon em uma VM do Windows Server usando o portal do Azure e o modelo de implantação do Gerenciador de Recursos."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/28/2016"
	ms.author="cynthn"/>

# Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows Server 


Você usará o botão **Conectar** no portal do Azure para iniciar uma sessão da Área de Trabalho Remota (RDP). Primeiro, conecte-se à máquina virtual, em seguida, faça logon.

## Conectar-se à máquina virtual

1. Se ainda não tiver feito isso, entre no [portal do Azure](https://portal.azure.com/).

2.	No menu Hub, clique em **Máquinas Virtuais**.

3.	Selecione a máquina virtual na lista.

4. Na folha da máquina virtual, clique em **Conectar**.

	![Captura de tela do portal do Azure mostrando como conectar sua VM.](./media/virtual-machines-windows-connect-logon/connect.png)

## Faça logon na máquina virtual

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]


## Próximas etapas

Se você tiver problemas ao tentar se conectar, consulte [Solucionar Problemas de conexões da Área de Trabalho Remota para uma Máquina Virtual do Azure baseada no Windows](virtual-machines-windows-troubleshoot-rdp-connection.md). Este artigo orienta você no diagnóstico e na solução de problemas comuns.

<!---HONumber=AcomDC_0504_2016-->