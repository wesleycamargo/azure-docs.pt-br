<properties linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Learn how to log on to a virtual machine running Windows Server 2008 R2 by using the Azure Management Portal." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

> [WACOM.NOTE] Para obter requisitos e dicas para solução de problemas, consulte [Conectar-se a uma máquina virtual do Azure com protocolo RDP ou SSH][Conectar-se a uma máquina virtual do Azure com protocolo RDP ou SSH].

1.  Se você ainda não fez isso, entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Clique em **Máquinas Virtuaise**, em seguida, selecione a máquina virtual apropriada.

3.  Na barra de comandos, clique em **Conectar**.

    ![Faça logon na máquina virtual][Faça logon na máquina virtual]

4.  Clique em **Abrir** para usar o arquivo do protocolo RDP criado automaticamente para a máquina virtual.

5.  Clique em **Conectar** para prosseguir com o processo de conexão.

    ![Continuar com a conexão][Continuar com a conexão]

6.  Digite o nome do usuário e a senha da conta administrativa na máquina virtual e, em seguida, clique em **OK**

7.  Clique em **Sim** para verificar a identidade da máquina virtual.

    ![Verificar a identidade do computador][Verificar a identidade do computador]

    Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

  [Conectar-se a uma máquina virtual do Azure com protocolo RDP ou SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Faça logon na máquina virtual]: ./media/virtual-machines-log-on-win-server/connectwindows.png
  [Continuar com a conexão]: ./media/virtual-machines-log-on-win-server/connectpublisher.png
  [Verificar a identidade do computador]: ./media/virtual-machines-log-on-win-server/connectverify.png
