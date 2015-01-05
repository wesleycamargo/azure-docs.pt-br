<properties urlDisplayName="Capture an image" pageTitle="Capturar imagens de máquinas virtuais que executam Windows Server" metaKeywords="Azure capture image vm, capturing vm" description="Learn how to capture an image of an Azure virtual machine (VM) running Windows Server 2008 R2. " metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Capture an Image of a Virtual Machine Running Windows Server" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="11/24/2014" ms.author="kathydav" />

#Como capturar a máquina virtual do Windows para usá-la como modelo#

Esse artigo mostra como capturar a máquina virtual do Azure executando Windows para que você a use como modelo para criar outras máquinas virtuais. O modelo inclui o disco do SO e qualquer disco de dados anexados à máquina virtual. Isso não inclui a configuração da rede, então você precisará configurá-la quando você criar as outras máquinas virtuais que usam o modelo.

O Azure trata esse modelo como uma imagem e o armazena em **Minhas Imagens**. Esse também é o local em que quaisquer imagens carregadas são armazenadas.Para mais informações sobre imagens, consulte [Sobre imagens da máquina virtual no Azure] [].

##Antes de começar##

Essas etapas assumem que você já criou uma máquina virtual do Azure e já configurou o sistema operacional, incluindo os anexos de discos de dados. Se você ainda não fez isso, consulte essas instruções:

- [Como criar uma máquina virtual personalizada] []
- [Como anexar um disco de dados à máquina virtual] []

##Capturar a máquina virtual##

1. Conectar-se à máquina virtual clicando em **Conecte-se** na barra de comando. Para mais detalhes, consulte [ Como fazer logon em uma máquina virtual executando o Windows Server][].

2.	Abra uma janela de Prompt de comando como administrador.


3.	Altere o diretório para `%windir%\system32\sysprep` e, a seguir, execute sysprep.exe.


4. 	A caixa de diálogo **Ferramenta de Preparação do Sistema** é exibida.Faça o seguinte:


	- Em **Ação de Limpeza do Sistema**, selecione **Entrar no Sistema OOBE** e verifique se a opção **Generalizar** está marcada. Para obter mais informações sobre como usar o Sysprep, consulte [Como usar o Sysprep: Uma introdução][].

	- Em **Opções de Desligamento**, selecione **Desligar**.

	- Clique em **OK**.

	![Run Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	O Sysprep desliga a máquina virtual, que altera o status da máquina virtual no [Portal de Gerenciamento](http://manage.windowsazure.com) para **Parado**.


8.	Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual que você deseja capturar.

9.	Na barra de comandos, clique em **Capturar**.

	![Capture virtual machine](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	A caixa de diálogo **Capturar máquina virtual** é exibida.

10.	Em **Nome da Imagem**, digite o nome da nova imagem.

11.	Antes de adicionar uma imagem do Windows Server ao seu conjunto de imagens personalizadas, ela deve ser generalizada executando Sysprep conforme as instruções das etapas anteriores. Clique em **Executei o Sysprep na máquina virtual** para indicar que você fez isso.

12.	Clique na marca de verificação para capturar a imagem. Quando você captura uma imagem de uma máquina virtual genérica, a máquina virtual é excluída.

	A nova imagem agora está disponível em **Imagens**.

	![Image capture successful](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Próximas etapas##
A imagem está pronta para ser usada como modelo para criar uma nova máquina virtual. Para fazer isto, crie uma máquina virtual personalizada usando o método **de Galeria** e selecione a imagem que você acabou de criar. Para obter instruções, consulte [Como criar uma máquina virtual personalizada][].

	
[Sobre imagens da máquina virtual no Azure]: http://msdn.microsoft.com/pt-br/library/azure/dn790290.aspx
[Como criar uma máquina virtual personalizada]: ../virtual-machines-create-custom/
[Como anexar um disco de dados à máquina virtual]: ../storage-windows-attach-disk/
[Como fazer logon em uma máquina virtual executando o Windows Server]: http://www.windowsazure.com/pt-br/manage/windows/how-to-guides/log-on-a-windows-vm/
[Como usar o Sysprep: Uma introdução]: http://technet.microsoft.com/pt-br/library/bb457073.aspx
[Executar o Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Inserir as opções de Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[A máquina virtual é parada]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capturar uma imagem da máquina virtual]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Digitar o nome da imagem]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Captura de imagem bem-sucedida]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Usar a imagem capturada]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!--HONumber=35.1-->
