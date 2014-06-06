<properties linkid="manage-windows-howto-capture-an-image" urlDisplayName="Capturar uma imagem" pageTitle="Capturar uma imagem de uma máquina virtual que executa o Windows Server" metaKeywords="captura de imagem de vm do Azure, capturando vm" description="Saiba como capturar uma imagem de uma máquina virtual (VM) do Azure que executa o Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="Como capturar uma imagem de uma máquina virtual executando o Windows Server" authors=""  solutions="" writer="kathydav" manager="jeffreyg" editor="tysonn"  />




#Como capturar uma imagem de uma máquina virtual executando o Windows Server#

Você pode usar imagens da Galeria de Imagens para criar máquinas virtuais facilmente ou capturar e usar suas próprias imagens para criar máquinas virtuais personalizadas. Uma imagem é um arquivo de disco rígido virtual (.vhd) que é usado como modelo para criar uma máquina virtual. Uma imagem é um modelo, pois não tem as configurações específicas de uma máquina virtual configurada, como o nome do computador e as configurações de conta de usuário. Se você quiser criar várias máquinas virtuais configuradas do mesmo modo, poderá capturar uma imagem de uma máquina virtual configurada e usar essa imagem como um modelo.


1. Conecte-se à máquina virtual executando as etapas listadas em [Como fazer logon em uma máquina virtual que executa o Windows Server][].

2.	Abra uma janela de Prompt de Comando como administrador.


3.	Altere o diretório para `%windir%\system32\sysprep` e, em seguida, execute sysprep.exe.


4. 	A caixa de diálogo **Ferramenta de Preparação do Sistema** é exibida.


	Em **Ação de Limpeza do Sistema**, selecione **Entrar no Sistema OOBE** e verifique se a opção **Generalizar** está marcada. Para obter mais informações sobre como usar o Sysprep, consulte [Como usar Sysprep: uma introdução (a página pode estar em inglês)][].

5.	Em **Opções de Desligamento**, selecione **Desligar**.

6.	Clique em **OK**.

7.	O Sysprep desliga a máquina virtual, que altera o status da máquina no [Portal de Gerenciamento](http://manage.windowsazure.com) para **Parado**.


8.	Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual que você deseja capturar.

9.	Na barra de comandos, clique em **Capturar**.

	A caixa de diálogo **Criar uma Imagem de uma Máquina Virtual** é exibida.

10.	Em **Nome da Imagem**, digite o nome da nova imagem.

11.	Antes de adicionar uma imagem do Windows Server ao seu conjunto de imagens personalizadas, ela deve ser generalizada executando Sysprep conforme as instruções das etapas anteriores. Clique em **Executei o Sysprep na máquina virtual** para indicar que você fez isso.

12.	Clique na marca de seleção para capturar a imagem. Quando você captura uma imagem de uma máquina virtual, a máquina é excluída.

	A nova imagem agora está disponível em **Imagens**.

	Ao criar uma máquina virtual usando o método **From Gallery**, você poderá usar a imagem capturada, clicando em **Minhas imagens** na página **Escolha uma Imagem**.

	

[Como fazer logon em uma máquina virtual que executa o Windows Server]:http://www.windowsazure.com/pt-br/manage/windows/how-to-guides/log-on-a-windows-vm/
[Como usar o Sysprep: uma introdução]:http://technet.microsoft.com/pt-br/library/bb457073.aspx
[Executar o Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Inserir as opções de Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[A máquina virtual é parada]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capturar uma imagem da máquina virtual]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Digitar o nome da imagem]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Captura de imagem bem-sucedida]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Usar a imagem capturada]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

