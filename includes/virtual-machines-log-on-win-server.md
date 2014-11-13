<properties linkid="manage-windows-howto-logon" urlDisplayName="Log on to a VM" pageTitle="Fazer logon em uma m&aacute;quina virtual executando o Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Saiba como fazer logon em uma m&aacute;quina virtual que execute o Windows Server 2008 R2 usando o Portal de Gerenciamento do Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Como fazer logon em uma m&aacute;quina virtual executando o Windows Server" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

> [WACOM.NOTE] Para obter requisitos e dicas para solução de problemas, consulte [Conectar-se a uma máquina virtual do Azure com protocolo RDP ou SSH][Conectar-se a uma máquina virtual do Azure com protocolo RDP ou SSH].

1.  Se você ainda não fez isso, entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual apropriada.

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
