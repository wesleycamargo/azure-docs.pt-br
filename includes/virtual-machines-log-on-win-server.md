<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

4. Clicar em **Conectar** cria e baixa um arquivo .rdp (Protocolo de Área de Trabalho Remota). Clique em **Abrir** para usar esse arquivo.

5. Na janela de Área de Trabalho Remota, clique em **Conectar** para continuar.

	![Continuar com a conexão](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Na janela Segurança do Windows, digite as credenciais da conta administrativa na máquina virtual e clique em **OK**.

 	>[AZURE.TIP]Na maioria dos casos, você usará o nome de usuário e senha que foram especificados quando a máquina virtual foi criada. Verifique o nome de usuário para certificar-se de que ele tem as informações de domínio corretas:
	>
	>- Se a máquina virtual pertencer a um domínio em sua organização, verifique se o nome de usuário inclui o nome desse domínio.
	>- Se a máquina virtual não pertencer a um domínio, remova quaisquer informações de domínio iniciando a linha com '' ou use o nome da VM como o nome de domínio. Por exemplo, `\MyUserName` ou `MyTestVM\MyUserName`.
	>- Se a máquina virtual for um controlador de domínio, digite o nome de usuário e a senha da conta de administrador para esse domínio.

7.	Clique em **Sim** para verificar a identidade da máquina virtual e concluir o logon.

	![Verificar a identidade do computador](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=Sept15_HO3-->