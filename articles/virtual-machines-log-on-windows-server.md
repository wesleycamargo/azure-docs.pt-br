<properties linkid="manage-windows-howto-logon" urlDisplayName="Fazer logon em uma VM" pageTitle="Fazer logon em uma máquina virtual que executa o Windows Server" metaKeywords="Azure log na vm, vm portal" description="Saiba como fazer logon em uma máquina virtual que executa o Windows Server 2008 R2 usando o Portal de Gerenciamento do Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Como fazer logon em uma máquina Virtual que executa o Windows Server" authors=""  solutions="" writer="kathydav" manager="jeffreyg" editor="tysonn"  />




#Como fazer logon em uma máquina virtual executando o Windows Server#

Em uma máquina virtual que executa o sistema operacional Windows Server, use o botão Conectar no Portal de Gerenciamento para iniciar uma Conexão de Área de Trabalho Remota. 

1. Se você ainda não tiver feito isso, entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual apropriada.

3. Na barra de comandos, clique em **Conectar**.

	![Faça logon na máquina virtual](./media/virtual-machines-log-on-windows-server/connectwindows.png)

4. Clique em **Abrir** para usar o arquivo de protocolo de área de trabalho remoto que foi criado automaticamente para a máquina virtual.
	
5. Clique em **Conectar** para prosseguir com o processo de conexão.

	![Continuar com a conexão](./media/virtual-machines-log-on-windows-server/connectpublisher.png)

6. Digite o nome do usuário e a senha da conta administrativa na máquina virtual e, em seguida, clique em **OK**
	
	
7. Clique em **Sim** para verificar a identidade da máquina virtual.

	![Verificar a identidade do computador](./media/virtual-machines-log-on-windows-server/connectverify.png)

	Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.



