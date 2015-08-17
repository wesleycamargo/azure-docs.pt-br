<properties
	pageTitle="Fazer logon em uma máquina virtual executando o Windows Server"
	description="Saiba como usar o portal do Azure para fazer logon em uma máquina virtual executando o Windows Server."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/12/2015"
	ms.author="kathydav"/>


# Como fazer logon em uma máquina virtual executando o Windows Server#

Você usará o botão **Conectar** no portal do Azure para iniciar uma sessão de Área de Trabalho Remota. (Para VMs do Linux, consulte [Como fazer logon em uma máquina virtual executando Linux](virtual-machines-linux-how-to-log-on.md).)

## Como fazer logon

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Dicas de solução de problemas

Aqui estão algumas opções a tentar rapidamente:

Se houver problemas com a conexão de área de trabalho remota, tente redefinir a configuração do portal. Usando o painel da máquina virtual, em **Visão Rápida**, clique em **Redefinir a configuração remota**.

Se houver problemas com sua senha, tente redefini-la no portal. No painel da máquina virtual, em **Visão Rápida**, clique em **Redefinir senha**.

Se isso não funcionar, você precisará fazer uma solução de problemas mais ampla. Para obter instruções, consulte [Solucionar problemas de conexões de Área de Trabalho Remota para uma Máquina Virtual do Azure baseada no Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).

<!---HONumber=August15_HO6-->