<properties
	pageTitle="Capturar uma imagem de uma VM do Windows | Microsoft Azure"
	description="Capture uma imagem de uma máquina virtual do Windows criada com o modelo de implantação clássico."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="cynthn"/>

#Capture uma imagem de uma máquina virtual do Windows criada com o modelo de implantação clássico.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


Esse artigo mostra como capturar uma máquina virtual do Azure executando Windows para que você a use como uma imagem para criar outras máquinas virtuais. Essa imagem inclui o disco do sistema operacional e discos de dados anexados à máquina virtual. Ele não inclui as configurações da rede, então você precisará configurá-la quando criar as outras máquinas virtuais que usam o modelo.

O Azure armazena a imagem em **Minhas Imagens**. Esse é o mesmo local em que as imagens que você carregou são armazenadas. Para obter detalhes sobre imagens, consulte [Sobre as imagens de máquinas virtuais](virtual-machines-images.md).

##Antes de começar##

Essas etapas assumem que você já criou uma máquina virtual do Azure e já configurou o sistema operacional, incluindo os anexos de discos de dados. Se você ainda não fez isso, consulte essas instruções:

- [Criar uma máquina virtual personalizada executando Windows][]
- [Como anexar um disco de dados à máquina virtual][]

> [AZURE.WARNING]Esse processo exclui a máquina virtual original depois que ela for capturada e não se destina como uma maneira de fazer backup de uma máquina virtual. Uma forma possível de fazer isso é o Backup do Azure, que está disponível como prévia em determinadas regiões. Para obter detalhes, consulte [Fazer backup de máquinas virtuais do Azure](../backup/backup-azure-vms.md). Existem outras soluções de parceiros certificados. Para descobrir o que está disponível no momento, pesquise no Azure Marketplace.

##Capturar a máquina virtual##

1. No [Portal do Azure](http://manage.windowsazure.com), **conecte-se** à máquina virtual. Para obter instruções, consulte [Como entrar em uma máquina virtual que executa o Windows Server][].

2.	Abra uma janela de Prompt de comando como administrador.

3.	Altere o diretório para `%windir%\system32\sysprep` e execute sysprep.exe.

4. 	A caixa de diálogo **Ferramenta de Preparação do Sistema** é aberta. Faça o seguinte:

	- Em **Ação de Limpeza do Sistema**, selecione **Entrar no Sistema OOBE** e verifique se a opção **Generalizar** está marcada. Para obter mais informações sobre como usar o Sysprep, consulte [Como usar Sysprep: uma introdução][].

	- Em **Opções de Desligamento**, selecione **Desligar**.

	- Clique em **OK**.

	![Executar o Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	O Sysprep desliga a máquina virtual, alterando o status dela no Portal do Azure para **Parado**.

8.	No portal do Azure, clique em **Máquinas Virtuais** e selecione a máquina virtual que você deseja capturar.

9.	Na barra de comandos, clique em **Capturar**.

	![Capturar a máquina virtual](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	A caixa de diálogo **Capturar máquina virtual** é exibida.

10.	Em **Nome da Imagem**, digite o nome da nova imagem.

11.	Antes de adicionar uma imagem do Windows Server ao seu conjunto de imagens personalizadas, ela deve ser generalizada executando Sysprep conforme as instruções das etapas anteriores. Clique em **Executei o Sysprep na máquina virtual** para indicar que você fez isso.

12.	Clique na marca de verificação para capturar a imagem. A nova imagem agora está disponível em **Imagens**.

 	![Captura de imagem bem-sucedida](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Próximas etapas##
A imagem está pronta para ser usada para criar máquinas virtuais. Para fazer isso, crie uma máquina virtual usando o item de menu **Da Galeria** e selecionando a imagem que você acabou de criar. Para obter instruções, consulte [Criar uma máquina virtual personalizada executando Windows][].

[Criar uma máquina virtual personalizada executando Windows]: virtual-machines-windows-create-custom.md
[Como anexar um disco de dados à máquina virtual]: storage-windows-attach-disk.md
[Como entrar em uma máquina virtual que executa o Windows Server]: virtual-machines-log-on-windows-server.md
[Como usar Sysprep: uma introdução]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=Oct15_HO3-->