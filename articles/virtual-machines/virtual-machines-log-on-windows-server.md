<properties
	pageTitle="Fazer logon em uma VM | Microsoft Azure"
	description="Use o portal clássico do Azure para fazer logon na máquina virtual do Windows criada com o modelo clássico de implantação."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="cynthn"/>


# Faça logon em uma máquina virtual do Windows usando o portal clássico do Azure



No portal clássico do Azure, você usará o botão **Conectar** para iniciar uma sessão de Área de Trabalho Remota e fazer logon em uma VM do Windows.

Deseja conectar-se a uma VM do Linux? Consulte [Como fazer logon em uma máquina virtual que esteja executando o Linux](virtual-machines-linux-how-to-log-on.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


## Passo a passo em vídeo

Veja um passo a passo em vídeo deste tutorial. Ele também aborda os pontos de extremidade e as portas públicas e privadas usadas para se conectar a uma VM do Windows no Azure.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]


## Conectar-se à máquina virtual

1. Entre no portal clássico do Azure.

2. Clique em **Máquinas Virtuais** e selecione a máquina virtual.

3. Na barra de comandos localizada na parte inferior da página, clique em **Conectar**.

	![Faça logon na máquina virtual](./media/virtual-machines-log-on-windows-server/connectwindows.png)
	
> [AZURE.TIP]Se o botão Conectar não estiver disponível, consulte as dicas de solução de problemas ao final deste artigo.

## Faça logon na máquina virtual

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Dicas de solução de problemas

Veja algumas opções:

-	Se o botão **Conectar** estiver inativo, ou se você estiver enfrentando outros problemas com a conexão de Área de Trabalho Remota, tente redefinir a configuração. Usando o painel da máquina virtual, em **Visão Rápida**, clique em **Redefinir a configuração remota**.
-	Se houver problemas com a senha, tente redefini-la. No painel da máquina virtual, em **Visão Rápida**, clique em **Redefinir senha**.

Se essas dicas não funcionarem ou se não forem necessárias, consulte [Solucionar problemas de conexões da Área de Trabalho Remota com uma Máquina Virtual do Azure baseada no Windows](virtual-machines-troubleshoot-remote-desktop-connections.md). Este artigo orienta você no diagnóstico e na solução de problemas comuns.

<!---HONumber=AcomDC_1203_2015-->