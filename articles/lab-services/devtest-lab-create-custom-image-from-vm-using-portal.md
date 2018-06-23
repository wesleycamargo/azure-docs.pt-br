---
title: Criar uma imagem personalizada do Azure DevTest Labs de uma VM | Microsoft Docs
description: Saiba como criar uma imagem personalizada no Azure DevTest Labs de uma VM provisionada usando o Portal do Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 22f1579b2df2acdc736ed4c1d5cee64d096c320a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635909"
---
# <a name="create-a-custom-image-from-a-vm"></a>Criar uma imagem personalizada de uma VM

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Você pode criar uma imagem personalizada de uma VM provisionada e depois usá-la para criar VMs idênticas. As etapas a seguir ilustram como criar uma imagem personalizada com base em uma VM:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selecione **Todos os serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.

1. Na lista de laboratórios, selecione o laboratório desejado.  

1. No painel do laboratório, selecione **Minhas máquinas virtuais**.
 
1. No painel **Minhas máquinas virtuais**, selecione a VM com base na qual você deseja criar a imagem personalizada.

1. No painel da VM, selecione **Criar imagem personalizada (VHD)**.

    ![Criar item de menu de imagem personalizada](./media/devtest-lab-create-template/create-custom-image.png)

1. No painel **Imagem personalizada**, digite um nome e uma descrição para a sua imagem personalizada. Essas informações são exibidas na lista de bases quando você cria uma VM. A imagem personalizada incluirá o disco do sistema operacional e todos os discos de dados anexados à máquina virtual.

    ![Painel Criar imagem personalizada](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Selecione se o sysprep foi executado na VM. Se o sysprep não tiver sido executado na VM, especifique se deseja que o sysprep seja executado quando uma VM for criada usando essa imagem personalizada.

1. Selecione **OK** quando tiver terminado de criar a imagem personalizada.

Depois de alguns minutos, a imagem personalizada é criada e é armazenada dentro da conta de armazenamento do laboratório. Quando um usuário de laboratório quiser criar uma nova VM, a imagem estará disponível na lista de imagens básicas.

![Imagem personalizada disponível na lista de imagens básicas](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas

- [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copiar imagens personalizadas entre Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Próximas etapas

- [Adicionar uma VM ao laboratório](devtest-lab-add-vm.md)
