<properties linkid="manage-linux-howto-logon-linux-vm" urlDisplayName="Log on to a VM" pageTitle="Log on to a virtual machine running Linux in Azure" metaKeywords="Azure Linux vm, Linux SSH" description="Learn how to log on to an Azure virtual machine running Linux by using a Secure Shell (SSH) client." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Log on to a Virtual Machine Running Linux" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Como fazer logon em uma máquina virtual executando o Linux

Em uma máquina virtual que executa o sistema operacional Linux, use um cliente Secure Shell (SSH) para fazer logon.

Instale um cliente SSH no computador que você deseja usar para fazer logon na máquina virtual. Há muitos programas de cliente SSH que você pode escolher. Estas são algumas opções:

-   Em um computador que executa um sistema operacional Windows, poderá usar um cliente SSH como PuTTY. Para obter mais informações, consulte a [Página de download do PuTTY (em inglês)][].
-   Em um computador que executa um sistema operacional Linux, poderá usar um cliente SSH como OpenSSH. Para obter mais informações, consulte [OpenSSH][].

> [WACOM.NOTE] Para mais dicas de requisitos e solução de problemas, consulte [Conecte-se à uma máquina virtual Azure com RDP ou SSH][].

Este procedimento mostra como usar o programa PuTTY para acessar a máquina virtual.

1.  Encontre o **nome do host** e **informações da porta** no [Portal de Gerenciamento][]. Você pode encontrar as informações que você precisa no painel da máquina virtual. Clique no nome da máquina virtual e procure os **Detalhes de SSH** na seção **Visão Rápida** do painel de controle.

    ![Obter detalhes de SSH][]

2.  Abra o programa PuTTY.

3.  Digite o Nome do Host e as Informações da porta coletadas do painel de controle e, em seguida, clique em **Abrir**.

    ![Abra o PuTTY][]

4.  Faça logon na máquina virtual usando a conta especificada quando a máquina foi criada. O nome de usuário é por padrão azureuser.

    ![Faça logon na máquina virtual][]

> [WACOM.NOTE] A extensão VMAccess pode ajudá-lo a redefinir a chave SSH ou a senha, caso a esqueça. Se você esquecer o nome de usuário, você pode usar a extensão para criar um novo com a autoridade sudo. Para obter instruções, consulte [Usando a extensão VMAccess para redefinir credenciais de logon para VM do Linux][].

Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

<!-- LINKS -->

  [Página de download do PuTTY (em inglês)]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [OpenSSH]: http://www.openssh.org/
  [Conecte-se à uma máquina virtual Azure com RDP ou SSH]: http://go.microsoft.com/fwlink/p/?LinkId=398294
  [Portal de Gerenciamento]: http://manage.windowsazure.com
  [Obter detalhes de SSH]: ./media/virtual-machines-linux-how-to-log-on/sshdetails.png
  [Abra o PuTTY]: ./media/virtual-machines-linux-how-to-log-on/putty.png
  [Faça logon na máquina virtual]: ./media/virtual-machines-linux-how-to-log-on/sshlogin.png
  [Usando a extensão VMAccess para redefinir credenciais de logon para VM do Linux]: http://go.microsoft.com/fwlink/p/?LinkId=512138
