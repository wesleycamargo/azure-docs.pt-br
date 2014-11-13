<properties urlDisplayName="Log on to a VM" pageTitle="Fazer logon em uma m&aacute;quina virtual executando o Windows Server" metaKeywords="Azure logging on vm, vm portal" description="Saiba como usar o Portal de Gerenciamento do Azure para fazer logon em uma m&aacute;quina virtual executando o Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="Como fazer logon em uma m&aacute;quina virtual executando o Windows Server" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="08/05/2014" ms.author="kathydav" />

# Como fazer logon em uma máquina virtual executando o Windows Server

Em uma máquina virtual que executa o sistema operacional Windows Server, use o botão Conectar no Portal de Gerenciamento para iniciar uma Conexão de Área de Trabalho Remota.

> [WACOM.NOTE] Se precisar redefinir o nome de usuário ou a senha, ou habilitar o RDP na máquina virtual, use a extensão [VMAccess][VMAccess] para fazer isso. Para mais dicas de requisitos e solução de problemas, consulte [Conecte-se à uma máquina virtual Azure com RDP ou SSH][Conecte-se à uma máquina virtual Azure com RDP ou SSH].

1.  Se você ainda não fez isso, entre no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual apropriada.

3.  Na barra de comandos, clique em **Conectar**.

    ![Faça logon na máquina virtual][Faça logon na máquina virtual]

4.  Clique em **Abrir** para usar o arquivo do protocolo RDP criado automaticamente para a máquina virtual.

5.  Clique em **Conectar** para continuar.

    ![Continuar com a conexão][Continuar com a conexão]

6.  Digite o nome do usuário e a senha da conta administrativa na máquina virtual e, em seguida, clique em **OK** Este é o nome de usuário e a senha que você especificou quando criou a máquina virtual, ao menos que a máquina virtual seja agora um controlador de domínio. Nesse caso, digite o nome de usuário e a senha da conta de administrador do domínio.

7.  Clique em **Sim** para verificar a identidade da máquina virtual.

    ![Verificar a identidade do computador][Verificar a identidade do computador]

    Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

  [VMAccess]: http://go.microsoft.com/fwlink/p/?LinkId=396856
  [Conecte-se à uma máquina virtual Azure com RDP ou SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Faça logon na máquina virtual]: ./media/virtual-machines-log-on-windows-server/connectwindows.png
  [Continuar com a conexão]: ./media/virtual-machines-log-on-windows-server/connectpublisher.png
  [Verificar a identidade do computador]: ./media/virtual-machines-log-on-windows-server/connectverify.png
