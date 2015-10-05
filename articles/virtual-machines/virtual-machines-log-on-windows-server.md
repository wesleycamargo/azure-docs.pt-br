<properties
	pageTitle="Fazer logon em uma VM | Microsoft Azure"
	description="Use o portal para fazer logon na máquina virtual do Windows criada com o modelo clássico de implantação."
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
	ms.date="09/15/2015"
	ms.author="cynthn"/>


# Efetue logon na máquina virtual do Windows criada com o modelo clássico de implantação

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda o gerenciamento de recursos com o modelo clássico de implantação.

Você usará o botão **Conectar** no portal de visualização do Azure para iniciar uma sessão de área de trabalho remota. Primeiro, conecte-se à máquina virtual, em seguida faça logon.

Deseja conectar-se a uma VM do Linux? Consulte [Como fazer logon em uma máquina virtual que esteja executando o Linux](virtual-machines-linux-how-to-log-on.md).

## Conectar-se à máquina virtual

Veja um passo a passo deste tutorial.

[AZURE.VIDEO logging-on-to-vm-running-windows-server-on-azure]

1. Entre no [portal do Azure](http://manage.windowsazure.com), caso ainda não tenha feito isso.

2. Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual apropriada.

3. Na barra de comandos, clique em **Conectar**.

	![Faça logon na máquina virtual](./media/virtual-machines-log-on-windows-server/connectwindows.png)

## Faça logon na máquina virtual

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Dicas de solução de problemas

Veja algumas opções a serem experimentadas rapidamente no portal:

-	Se houver problemas com a conexão da Área de Trabalho Remota, tente redefinir a configuração. Usando o painel da máquina virtual, em **Visão Rápida**, clique em **Redefinir a configuração remota**.
-	Se houver problemas com a senha, tente redefini-la. No painel da máquina virtual, em **Visão Rápida**, clique em **Redefinir senha**.

Se essas dicas não funcionarem ou se não forem necessárias, consulte [Solucionar problemas de conexões da Área de Trabalho Remota com uma Máquina Virtual do Azure baseada no Windows](virtual-machines-troubleshoot-remote-desktop-connections.md). Este artigo orienta você no diagnóstico e na solução de problemas comuns.

<!---HONumber=Sept15_HO4-->