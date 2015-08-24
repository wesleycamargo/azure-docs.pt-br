
1. Se você ainda não tiver feito isso, entre no [Portal do Azure](http://manage.windowsazure.com).

2. Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual apropriada.

3. Na barra de comandos, clique em **Conectar**.

	![Faça logon na máquina virtual](./media/virtual-machines-log-on-win-server/connectwindows.png)

4. Clique em **Abrir** para usar o arquivo do protocolo RDP criado automaticamente para a máquina virtual.

5. Clique em **Conectar** para continuar.

	![Continuar com a conexão](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Digite as credenciais da conta administrativa na máquina virtual e, em seguida, clique em **OK**.

 >[AZURE.TIP]Na maioria dos casos, você usará o nome de usuário e senha que foram especificados quando a máquina virtual foi criada. Verifique o nome de usuário para certificar-se de que ele tem as informações de domínio corretas:

>- Se a máquina virtual pertence a um domínio na sua organização, verifique se o nome de usuário inclui o nome daquele domínio.
- Se a VM não pertencer a um domínio, remova quaisquer informações de domínio iniciando a linha com '' ou use o nome da VM como o nome de domínio. Por exemplo, `\MyUserName` ou `MyTestVM\MyUserName`.
- Se a VM for um controlador de domínio, digite o nome de usuário e a senha da conta de administrador para esse domínio.

Clique em **Sim** para verificar a identidade da máquina virtual.

![Verificar a identidade do computador](./media/virtual-machines-log-on-win-server/connectverify.png)

Agora você pode trabalhar remotamente com a máquina virtual.

<!---HONumber=August15_HO7-->