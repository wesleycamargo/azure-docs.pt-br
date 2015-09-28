<properties
	pageTitle="Fazer logon em uma máquina virtual executando o Windows Server"
	description="Saiba como usar o portal de visualização do Azure para fazer logon em uma máquina virtual que esteja executando o Windows Server."
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

# Como fazer logon em uma máquina virtual executando o Windows Server#

Use o botão **Conectar** no portal de visualização do Azure para iniciar uma sessão da Área de Trabalho Remota. Primeiro, conecte-se à máquina virtual, em seguida faça logon.

## Conectar-se à máquina virtual

1. Entre no [portal de visualização do Azure](https://portal.azure.com/), caso ainda não tenha feito isso.

2.	No menu Hub, clique em **Procurar**.

3.	Na folha de pesquisa, role a tela para baixo e clique em **Máquinas Virtuais**.

	![Procurar máquinas virtuais](./media/virtual-machines-log-on-windows-server-preview/search-blade-preview-portal.png)

4.	Selecione a máquina virtual na lista.

5. Na folha da máquina virtual, clique em **Conectar**.

	![Conectar-se à máquina virtual](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## Faça logon na máquina virtual

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## Solucionar problemas

Se as dicas sobre logon não ajudarem ou se não forem necessárias, consulte [Solucionar problemas de conexões da Área de Trabalho Remota com uma Máquina Virtual do Azure baseada no Windows](virtual-machines-troubleshoot-remote-desktop-connections.md). Este artigo orienta você no diagnóstico e na solução de problemas comuns.

<!---HONumber=Sept15_HO3-->