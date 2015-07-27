<properties
	pageTitle="Capturar imagens de máquinas virtuais que executam Windows Server"
	description="Saiba como capturar imagem de máquina virtual do Azure que executam Windows Server 2008 R2."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/13/2015"
	ms.author="kathydav"/>

#Como capturar uma máquina virtual do Windows para usá-la como modelo#

Esse artigo mostra como capturar a máquina virtual do Azure executando Windows para que você a use como modelo para criar outras máquinas virtuais. Esse modelo inclui o disco do sistema operacional e discos de dados anexados à máquina virtual. Ele não inclui as configurações da rede, então você precisará configurá-la quando criar as outras máquinas virtuais que usam o modelo.

O Azure trata esse modelo como uma imagem e o armazena em **Minhas Imagens**. Esse também é o local em que quaisquer imagens carregadas são armazenadas. Para saber mais sobre imagens, consulte [Sobre imagens da máquina virtual no Azure][].

##Antes de começar##

Essas etapas assumem que você já criou uma máquina virtual do Azure e já configurou o sistema operacional, incluindo os anexos de discos de dados. Se você ainda não fez isso, consulte essas instruções:

- [Como criar uma máquina virtual personalizada][]
- [Como anexar um disco de dados à máquina virtual][]

##Capturar a máquina virtual##

1. Conectar-se à máquina virtual clicando em **Conecte-se** na barra de comando. Para obter detalhes, consulte [Como entrar em uma máquina virtual que esteja executando o Windows Server][].

2.	Abra uma janela de Prompt de comando como administrador.


3.	Altere o diretório para `%windir%\system32\sysprep` e execute sysprep.exe.


4. 	A caixa de diálogo **Ferramenta de Preparação do Sistema** é aberta. Faça o seguinte:


	- Em **Ação de Limpeza do Sistema**, selecione **Entrar no Sistema OOBE** e verifique se a opção **Generalizar** está marcada. Para obter mais informações sobre como usar o Sysprep, consulte [Como usar Sysprep: uma introdução][].

	- Em **Opções de Desligamento**, selecione **Desligar**.

	- Clique em **OK**.

	![Executar o Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	O Sysprep desliga a máquina virtual, alterando o status dela no [Portal do Azure](http://manage.windowsazure.com) para **Parado**.


8.	Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual que você deseja capturar.

9.	Na barra de comandos, clique em **Capturar**.

	![Capturar a máquina virtual](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	A caixa de diálogo **Capturar máquina virtual** é exibida.

10.	Em **Nome da Imagem**, digite o nome da nova imagem.

11.	Antes de adicionar uma imagem do Windows Server ao seu conjunto de imagens personalizadas, ela deve ser generalizada executando Sysprep conforme as instruções das etapas anteriores. Clique em **Executei o Sysprep na máquina virtual** para indicar que você fez isso.

12.	Clique na marca de verificação para capturar a imagem.

  **OBSERVAÇÃO: quando você captura uma imagem de uma máquina virtual genérica, a máquina virtual é excluída..**

 A nova imagem agora está disponível em **Imagens**. ![Captura de imagem bem-sucedida](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Próximas etapas##
A imagem está pronta para ser usada como modelo para criar uma nova máquina virtual. Para fazer isso, crie uma máquina virtual personalizada usando o item de menu **Da Galeria** e selecionando a imagem que você acabou de criar. Para obter instruções, consulte [Como criar uma máquina virtual personalizada][].


[Sobre imagens da máquina virtual no Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[Como criar uma máquina virtual personalizada]: virtual-machines-create-custom.md
[Como anexar um disco de dados à máquina virtual]: storage-windows-attach-disk.md
[Como entrar em uma máquina virtual que esteja executando o Windows Server]: http://www.windowsazure.com/manage/windows/how-to-guides/log-on-a-windows-vm/
[Como usar Sysprep: uma introdução]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=July15_HO3-->