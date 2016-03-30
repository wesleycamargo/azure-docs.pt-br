<properties
	pageTitle="Criar uma imagem personalizada do Laboratório de Desenvolvimento/Teste de um arquivo VHD | Microsoft Azure"
	description="Saiba como criar uma imagem personalizada de um arquivo VHD, que possa ser usada para criar VMs em um Laboratório de Desenvolvimento/Teste"
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
	ms.date="03/13/2016"
	ms.author="tarcher"/>

# Criar uma imagem personalizada do Laboratório de Desenvolvimento/Teste de um arquivo VHD

## Visão geral

Depois de [criar um Laboratório de Desenvolvimento/Teste](devtest-lab-create-lab.md), você poderá [adicionar VMs (máquinas virtuais) a esse laboratório](devtest-lab-add-vm-with-artifacts.md). Durante a criação de uma VM, você especifica uma *base*, que pode ser uma *imagem personalizada* ou uma *imagem do Marketplace*. Neste artigo, você verá como criar uma imagem personalizada de um arquivo VHD. Observe que você precisará acessar um arquivo VHD válido para executar todas as etapas deste artigo.

## Criar uma imagem personalizada

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Toque em **Procurar** e toque em **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, toque no laboratório desejado.

1. A folha **Configurações** do laboratório selecionado será exibida.

1. Na folha **Configurações** do laboratório, toque em **Imagens Personalizadas**.

    ![Opção Imagens Personalizadas](./media/devtest-lab-create-template/lab-settings-custom-images.png)

1. Na folha **Imagens personalizadas**, toque em **+ Imagem Personalizada**.

    ![Imagem de Adicionar Personalizado](./media/devtest-lab-create-template/add-custom-image.png)

1. Insira o nome da imagem personalizada. Esse nome é exibido na lista de imagens base durante a criação de uma nova VM.

1. Insira a descrição da imagem personalizada. Essa descrição é exibida na lista de imagens base durante a criação de uma nova VM.

1. Toque em **Arquivo VHD**.

1. Se você tiver acesso a um arquivo VHD que não esteja listado, adicione-o seguindo as instruções da seção [Carregar um arquivo VHD](#upload-a-vhd-file) e volte para cá quando terminar.

1. Selecione o arquivo VHD desejado.

1. Toque em **OK** para fechar a folha **Arquivo VHD**.

1. Toque em **Configuração do SO**.

1. Na guia **Configuração do SO**, selecione **Windows** ou **Linux**.

1. Se **Windows** for selecionado, especifique por meio da caixa de seleção se o *Sysprep* foi executado na máquina.

1. Toque em **OK** para fechar a folha **Configuração do SO**.

1. Toque em **OK** para criar a imagem personalizada.

1. Acesse a seção [Próximas etapas](#next-steps).

##Carregar um arquivo VHD

Para adicionar uma nova imagem personalizada, você precisará ter acesso a um arquivo VHD.

1. Na folha **Arquivo VHD**, toque em **Carregar um arquivo VHD usando o PowerShell**.

    ![Carregar imagem](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. A próxima folha exibirá instruções para modificar e executar um script do PowerShell que carrega um arquivo VHD em sua assinatura do Azure. **Observação:** esse processo pode ser demorado, dependendo do tamanho do arquivo VHD e da velocidade de conexão.

##Próximas etapas

Depois de adicionar uma imagem personalizada para uso durante a criação de uma VM, a próxima etapa será [adicionar uma VM ao seu Laboratório de Desenvolvimento/Teste](./devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=AcomDC_0316_2016-->