---
title: Gerenciar imagens personalizadas do Azure DevTest Labs para criar VMs | Microsoft Docs
description: Saiba como criar uma imagem personalizada com base em um arquivo VHD ou em uma VM existente no Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 62d64f5a59a003b2ca15d424c8844dee716bf147


---
# <a name="manage-azure-devtest-labs-custom-images-to-create-vms"></a>Gerenciar imagens personalizadas do Azure DevTest Labs para criar VMs
No Azure DevTest Labs, as imagens personalizadas permitem criar VMs rapidamente sem esperar que todos os softwares necessários sejam instalados no computador de destino. Imagens personalizadas permitem pré-instalar todo o software de que você precisa em um arquivo VHD e, depois, usar o arquivo VHD para criar uma VM. Como o software já está instalado, o tempo de criação da VM é muito mais rápido. Além disso, imagens personalizadas são usadas para clonar VMs criando uma imagem personalizada de uma VM e, depois, criando VMs com base nessa imagem personalizada.

Neste artigo, você aprenderá a:

* [Criar uma imagem personalizada de um arquivo VHD](#create-a-custom-image-from-a-vhd-file) para criar uma VM usando essa imagem personalizada. 
* [Criar uma imagem personalizada de uma VM](#create-a-custom-image-from-a-vm) para clonagem rápida da VM.

## <a name="create-a-custom-image-from-a-vhd-file"></a>Criar uma imagem personalizada de um arquivo VHD
Nesta seção, você verá como criar uma imagem personalizada com base em um arquivo VHD.
Você precisará acessar um arquivo VHD válido para executar todas as etapas desta seção.   

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na folha do laboratório, selecione **Configuração**. 
5. Na folha **Configuração** do laboratório, selecione **Imagens personalizadas**.
6. Na folha **Imagens personalizadas**, selecione **+ Imagem personalizada**.
   
    ![Imagem de Adicionar Personalizado](./media/devtest-lab-create-template/add-custom-image.png)
7. Insira o nome da imagem personalizada. Esse nome é exibido na lista de imagens base durante a criação de uma VM.
8. Insira a descrição da imagem personalizada. Essa descrição é exibida na lista de imagens base durante a criação de uma VM.
9. Selecione **Arquivo VHD**.
10. Se você tiver acesso a um arquivo VHD que não esteja listado, adicione-o seguindo as instruções da seção [Carregar um arquivo VHD](#upload-a-vhd-file) e volte para cá quando terminar.
11. Selecione o arquivo VHD desejado.
12. Selecione **OK** para fechar a folha **Arquivo VHD**.
13. Selecione **Configuração do SO**.
14. Na guia **Configuração do SO**, selecione **Windows** ou **Linux**.
15. Se **Windows** for selecionado, especifique por meio da caixa de seleção se o *Sysprep* foi executado na máquina.
16. Selecione **OK** para fechar a folha **Configuração do SO**.
17. Selecione **OK** para criar a imagem personalizada.
18. Acesse a seção [Próximas etapas](#next-steps) .

### <a name="upload-a-vhd-file"></a>Carregar um arquivo VHD
Para adicionar uma imagem personalizada, você precisa ter acesso a um arquivo VHD.

1. Na folha **Arquivo VHD**, selecione **Carregar um arquivo VHD usando o PowerShell**.
   
    ![Carregar imagem](./media/devtest-lab-create-template/upload-image-using-psh.png)
2. A próxima folha exibirá instruções para modificar e executar um script do PowerShell que carrega um arquivo VHD em sua assinatura do Azure. 
   **Observação:** esse processo pode ser demorado, dependendo do tamanho do arquivo VHD e da velocidade de conexão.

## <a name="create-a-custom-image-from-a-vm"></a>Criar uma imagem personalizada de uma VM
Se tiver uma VM já configurada, você poderá criar uma imagem personalizada com base nessa VM e, depois, usar essa imagem personalizada para criar outras VMs idênticas. As etapas a seguir ilustram como criar uma imagem personalizada com base em uma VM:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na folha do laboratório, selecione **Minhas máquinas virtuais**.
5. Na folha **Minhas máquinas virtuais** , selecione a VM com base na qual você deseja criar a imagem personalizada.
6. Na folha da VM, selecione **Criar imagem personalizada (VHD)**.
   
    ![Criar item de menu de imagem personalizada](./media/devtest-lab-create-template/create-custom-image.png)
7. Na folha **Criar imagem** , digite um nome e uma descrição para a sua imagem personalizada. Essas informações são exibidas na lista de bases quando você cria uma VM.
   
    ![Criar folha de imagem personalizada](./media/devtest-lab-create-template/create-custom-image-blade.png)
8. Selecione se o sysprep foi executado na VM. Se o sysprep não tiver sido executado na VM, especifique se deseja que o sysprep seja executado quando uma VM for criada usando essa imagem personalizada.
9. Selecione **OK** quando tiver terminado de criar a imagem personalizada.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
* [Copiar imagens personalizadas entre Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Próximas etapas
Depois de adicionar uma imagem personalizada para uso durante a criação de uma VM, a próxima etapa será [adicionar uma VM ao seu laboratório](devtest-lab-add-vm-with-artifacts.md).




<!--HONumber=Nov16_HO3-->


