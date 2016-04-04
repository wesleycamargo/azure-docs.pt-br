<properties
	pageTitle="Fazer logon em uma VM do Windows | Microsoft Azure"
	description="Use o portal do Azure para fazer logon em uma máquina virtual do Windows criada com o modelo de implantação Gerenciador de Recursos."
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
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="cynthn"/>


# Faça logon em uma máquina virtual do Windows usando o Portal do Azure.


No Portal do Azure, você usará o botão **Conectar** para iniciar uma sessão de Área de Trabalho Remota e fazer logon em uma VM do Windows.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-connect-logon.md).


## Conectar-se à máquina virtual

1. Entre no Portal do Azure.

2. Clique em **Máquinas Virtuais** e selecione a máquina virtual.

3. Na barra de comandos localizada na parte inferior da página, clique em **Conectar**.

	![Faça logon na máquina virtual](./media/arm_log_on_windows_vm/rm_windows_connect.png)
	

4. Clicar em **Conectar** cria e baixa um arquivo .rdp (Protocolo de Área de Trabalho Remota). Clique em **Abrir** para usar esse arquivo.

	![Faça logon na máquina virtual](./media/arm_log_on_windows_vm/rm_connect_rdp_file.png)
	
5. Na janela de Área de Trabalho Remota, clique em **Conectar** para continuar.

	![Continuar com a conexão](./media/arm_log_on_windows_vm/rm_connect_unknown.png)

	
6. Na janela Segurança do Windows, digite as credenciais de uma conta na máquina virtual e clique em **OK**.

 	Na maioria dos casos, as credenciais são o nome de usuário e a senha da conta local especificados por você no momento da criação da máquina virtual. O domínio é o nome da máquina virtual inserido como *nomedavm*&#92;*nomedousuário*.
	
	Se a máquina virtual pertencer a um domínio em sua organização, verifique se o nome de usuário inclui o nome do domínio no formato *Domínio*&#92;*Nomedousuário*. A conta precisa estar no grupo Administradores ou ter privilégios de acesso remoto à VM.
	
	Se a máquina virtual for um controlador de domínio, digite o nome de usuário e a senha da conta de administrador para esse domínio.

7.	Clique em **Sim** para verificar a identidade da máquina virtual e concluir o logon.

	![Verificar a identidade do computador](./media/arm_log_on_windows_vm/rm_connect_cert.png)

## Próximas etapas

Se enfrentar problemas ao tentar se conectar, veja [Solucionar problemas de conexões da Área de Trabalho Remota para uma máquina virtual do Azure baseada em Windows](virtual-machines-windows-troubleshoot-rdp-connection.md)

<!---HONumber=AcomDC_0323_2016-->