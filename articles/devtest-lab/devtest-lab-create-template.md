<properties
	pageTitle="Gerenciar imagens personalizadas do Azure DevTest Labs para criar VMs | Microsoft Azure"
	description="Saiba como criar uma imagem personalizada com base em um arquivo VHD ou em uma VM existente no Azure DevTest Labs"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2016"
	ms.author="tarcher"/>

# Gerenciar imagens personalizadas do Azure DevTest Labs para criar VMs

Após ter [criado um laboratório](devtest-lab-create-lab.md), você poderá [adicionar VMs (máquinas virtuais) a esse laboratório](devtest-lab-add-vm-with-artifacts.md). Durante a criação de uma VM, você especifica uma *base*, que pode ser uma *imagem personalizada* ou uma *imagem do Marketplace*. Neste artigo, você aprenderá como [criar uma imagem personalizada de um VHD](#create-a-custom-image-from-a-vhd) para que você possa criar uma VM com base nessa imagem personalizada. Além disso, você também pode [criar uma imagem personalizada de uma VM](#create-a-custom-image-from-a-vm) para subsequente criação rápida de VM.

## Criar uma imagem personalizada com base em um arquivo VHD

Nesta seção, você verá como criar uma imagem personalizada com base em um arquivo VHD. Observe que você precisará acessar um arquivo VHD válido para executar todas as etapas desta seção.


1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Procurar** e, em seguida, **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.

1. A folha **Configurações** do laboratório selecionado será exibida.

1. Na folha **Configurações** do laboratório, selecione **Imagens personalizadas**.

    ![Opção Imagens Personalizadas](./media/devtest-lab-create-template/lab-settings-custom-images.png)

1. Na folha **Imagens personalizadas**, selecione **+ Imagem personalizada**.

    ![Imagem de Adicionar Personalizado](./media/devtest-lab-create-template/add-custom-image.png)

1. Insira o nome da imagem personalizada. Esse nome é exibido na lista de imagens base durante a criação de uma nova VM.

1. Insira a descrição da imagem personalizada. Essa descrição é exibida na lista de imagens base durante a criação de uma nova VM.

1. Selecione **Arquivo VHD**.

1. Se você tiver acesso a um arquivo VHD que não esteja listado, adicione-o seguindo as instruções da seção [Carregar um arquivo VHD](#upload-a-vhd-file) e volte para cá quando terminar.

1. Selecione o arquivo VHD desejado.

1. Selecione **OK** para fechar a folha **Arquivo VHD**.

1. Selecione **Configuração do SO**.

1. Na guia **Configuração do SO**, selecione **Windows** ou **Linux**.

1. Se **Windows** for selecionado, especifique por meio da caixa de seleção se o *Sysprep* foi executado na máquina.

1. Selecione **OK** para fechar a folha **Configuração do SO**.

1. Selecione **OK** para criar a imagem personalizada.

1. Acesse a seção [Próximas etapas](#next-steps).

###Carregar um arquivo VHD

Para adicionar uma nova imagem personalizada, você precisará ter acesso a um arquivo VHD.

1. Na folha **Arquivo VHD**, selecione **Carregar um arquivo VHD usando o PowerShell**.

    ![Carregar imagem](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. A próxima folha exibirá instruções para modificar e executar um script do PowerShell que carrega um arquivo VHD em sua assinatura do Azure. **Observação:** esse processo pode ser demorado, dependendo do tamanho do arquivo VHD e da velocidade de conexão.

## Criar uma imagem personalizada com base em uma VM
Se você tiver uma máquina virtual já configurada, poderá criar uma imagem personalizada com base nessa VM e subsequentemente usar essa imagem personalizada para criar outras VMs idênticas. As etapas a seguir ilustram como criar uma imagem personalizada com base em uma VM:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Procurar** e, em seguida, **DevTest Labs** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.

1. Na folha **Visão geral** do laboratório, selecione a VM com base na qual você deseja criar a imagem personalizada.

1. Na folha da VM, selecione **Criar imagem personalizada (VHD)**.

	![Criar item de menu de imagem personalizada](./media/devtest-lab-create-template/create-custom-image.png)

1. Na folha **Criar imagem**, digite um nome e uma descrição para a sua imagem personalizada. Essas informações serão exibidas na lista de bases quando você criar uma VM.

	![Criar folha de imagem personalizada](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Selecione se o sysprep foi executado na VM. Se o sysprep não tiver sido executado na VM, especifique se deseja ou não que sysprep seja executado quando uma VM for criada usando essa imagem personalizada.

1. Selecione **OK** quando tiver terminado de criar a imagem personalizada.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Postagens de blogs relacionadas

- [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar imagens personalizadas entre Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##Próximas etapas

Depois de adicionar uma imagem personalizada para uso durante a criação de uma VM, a próxima etapa será [adicionar uma VM ao seu laboratório](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0831_2016-->