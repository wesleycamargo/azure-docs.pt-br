<properties linkid="manage-linux-howto-logon-linux-vm" urlDisplayName="Fazer logon em uma VM" pageTitle="Fazer logon em uma máquina virtual com Linux no Azure" metaKeywords="Linux Azure vm, Linux SSH" description="Saiba como fazer logon em uma máquina virtual do Azure que executa o Linux usando um cliente de Secure Shell (SSH)." metaCanonical="" services="virtual-machines" documentationCenter="" title="Como fazer logon em uma máquina Virtual com Linux" authors=""  solutions="" writer="" manager="" editor=""  />




#Como fazer logon em uma máquina virtual executando o Linux #

Em uma máquina virtual que executa o sistema operacional Linux, use um cliente Secure Shell (SSH) para fazer logon.

Você deve instalar um cliente SSH no computador que você deseja usar para fazer logon na máquina virtual. Há muitos programas de cliente SSH que você pode escolher. Estas são algumas opções:

- Se você estiver usando um computador que executa um sistema operacional Windows, pode usar um cliente SSH como o PuTTY. Para obter mais informações, consulte a [Página de download do PuTTY (em inglês)](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
- Se você estiver usando um computador que executa um sistema operacional Linux, pode usar um cliente SSH como o OpenSSH. Para obter mais informações, consulte [OpenSSH](http://www.openssh.org/).

Este procedimento mostra como usar o programa PuTTY para acessar a máquina virtual.

1. Encontre o **nome do host** e **informações da porta** no [Portal de Gerenciamento](http://manage.windowsazure.com). Você pode encontrar as informações que você precisa no painel da máquina virtual. Clique no nome da máquina virtual e procure os **Detalhes de SSH** na seção **Visão Rápida** do painel de controle.

	![Obter detalhes de SSH](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. Abra o programa PuTTY.

3. Digite o Nome do Host e as Informações da porta coletadas do painel de controle e, em seguida, clique em **Abrir**.

	![Abra o PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. Faça logon na máquina virtual usando a conta especificada quando a máquina foi criada.

	![Faça logon na máquina virtual](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

	Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

