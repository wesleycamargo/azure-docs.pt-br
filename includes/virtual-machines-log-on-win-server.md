<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. Clicar em **Conectar** cria e baixa um arquivo .rdp (Protocolo de Área de Trabalho Remota). Clique em **Abrir** para usar esse arquivo.

5. Na janela de Área de Trabalho Remota, clique em **Conectar** para continuar.

	![Captura de tela de um aviso sobre um Editor Desconhecido.](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Na janela Segurança do Windows, digite as credenciais de uma conta na máquina virtual e clique em **OK**.

 	Em geral, as credenciais são o nome de usuário e a senha da conta local especificados por você no momento da criação da máquina virtual. Nesse caso, o domínio é o nome da máquina virtual e é inserido como *nomedavm*&#92;*nome de usuário*.
	
	Se a máquina virtual pertencer a um domínio em sua organização, verifique se o nome de usuário inclui o nome do domínio no formato *Domínio*&#92;*Nomeusuário*. A conta precisa estar no grupo Administradores ou ter privilégios de acesso remoto concedidos à VM.
	
	Se a máquina virtual for um controlador de domínio, digite o nome de usuário e a senha da conta de administrador para esse domínio.

7.	Clique em **Sim** para verificar a identidade da máquina virtual e concluir o logon.

	![Captura de tela mostrando uma mensagem sobre como verificar a identidade da VM.](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=AcomDC_0420_2016-->