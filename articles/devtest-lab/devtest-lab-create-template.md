    <properties
	pageTitle="Create VM templates | Microsoft Azure"
	description="Learn how to create VM templates from VHD images"
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
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# Criar modelos de VM

## Visão geral

Depois que você tiver [criado um laboratório](devtest-lab-create-lab.md), é possível [adicionar VMs a esse laboratório](devtest-lab-add-vm-with-artifacts.md) a partir de uma lista de modelos de VM. Neste artigo, você aprenderá como carregar e configurar um arquivo de imagem de Disco Rígido Virtual (VHD) como um modelo do qual criar suas VMs. Caso não esteja familiarizado com imagens de VHD, consulte o artigo [Criar e carregar um VHD do Windows Server para o Azure](https://azure.microsoft.com/pt-BR/documentation/articles/virtual-machines-create-upload-vhd-windows-server/) para saber como criar uma imagem de VHD. Depois de criar ou obter acesso a uma imagem de VHD, este artigo o orientará você em relação a como carregá-la e criar um modelo a partir dela.

## Criar um modelo de VM

1. Entre no [Portal de visualização do Azure](http://portal.azure.com).

1. Toque em **Procurar** e em **Laboratórios de Desenvolvimento/Testes** na lista.

1. Na lista de laboratórios, toque no laboratório desejado.

1. Na folha de laboratório, toque **Configurações**.

    ![Configurações do laboratório](./media/devtest-lab-create-template/lab-blade-settings.png)

1. Na folha **Configurações**, toque em **Modelos**.

    ![Opção de modelos](./media/devtest-lab-create-template/lab-blade-settings-templates.png)

1. Na folha **Modelos**, toque em **+ Modelo**.

    ![Adicionar modelo](./media/devtest-lab-create-template/add-template.png)

1. Na folha **Adicionar Modelo**:

	1. Digite o nome do modelo. Esse nome é exibido na lista de modelos ao criar uma nova VM.

	1. Insira a descrição do modelo. Essa descrição é exibida na lista de modelos ao criar uma nova VM.

	1. Toque em **Imagem**.

	1. Se a imagem desejada não estiver listada e você quiser adicioná-la, vá para a seção [Adicionar uma nova imagem de modelo](#add-a-new-template-image) e volte quando terminar.

	1. Selecione o a imagem desejada.

	1. Toque em **OK** para fechar a folha **Adicionar Modelo**.

1. Toque em **Configuração do SO**.

1. Na guia **Configuração do SO**, selecione **Windows** ou **Linux**.

1. Se **Windows** for selecionado, especifique por meio da caixa de seleção se o *Sysprep* foi executado na máquina.

1. Insira um **Nome de usuário** para a máquina.

1. Insira uma **Senha** para a máquina. **Observação:** a senha será exibida em texto não criptografado.

1. Toque em **OK** para fechar a folha **Configuração do SO**.

1. Especifique o **Local**.

1. Toque em **OK** para criar o modelo.

##Adicionar uma nova imagem do modelo

Para adicionar uma nova imagem de modelo, você precisará ter acesso a um arquivo de imagem VHD.

1. Na folha **Adicionar Imagem de Modelo**, toque em **Carregar uma imagem usando o PowerShell**.

    ![Carregar imagem](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. A próxima folha exibirá instruções para modificar e executar um script do PowerShell que carrega um arquivo de imagem VHD para sua assinatura do Azure. **Observação:** esse processo pode ser demorado, dependendo do tamanho do arquivo de imagem e da velocidade de conexão.

##Próximas etapas

Depois de adicionar um modelo de VM para uso durante a criação de uma VM, a próxima etapa é [adicionar uma VM ao seu Laboratório de Desenvolvimento/Teste](devtest-lab-add-vm-with-artifacts).

<!---HONumber=Nov15_HO4-->