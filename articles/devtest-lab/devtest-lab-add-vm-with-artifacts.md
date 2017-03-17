---
title: Gerenciar artefatos de VM no Azure DevTest Labs | Microsoft Docs
description: Saiba como gerenciar artefatos de VM no Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 576509ce-6a33-4c26-87c7-de8b40271efa
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2d298c210fa46cc4473fc7e8809e55bfc102481d
ms.openlocfilehash: c970bca8f6ab9e97741370f74efc5ee66e592468
ms.lasthandoff: 02/27/2017


---
# <a name="manage-vm-artifacts-in-azure-devtest-labs"></a>Gerenciar artefatos de VM no Azure DevTest Labs
Os *artefatos* do Azure DevTest Labs permitem que você especifique as *ações* que serão executadas quando a VM for provisionada. 

As ações de artefato podem executar procedimentos, como execução de scripts do Windows PowerShell, execução de comandos Bash e instalação de software. 

Os *parâmetros* de artefato permitem que você personalize o artefato para seu cenário específico.

Este artigo mostra como gerenciar os artefatos para uma VM em seu laboratório.

## <a name="add-an-existing-artifact-to-a-vm"></a>Adicionar um artefato existente a uma VM
Durante a criação de uma VM, você pode adicionar artefatos existentes. Cada laboratório inclui artefatos do Repositório Público de Artefatos de Laboratórios de Desenvolvimento/Teste, bem como artefatos criados e adicionados por você a seu próprio Repositório de Artefatos.
Para saber como criar artefatos, confira o artigo [Aprenda a criar seus próprios artefatos para uso com Laboratórios de Desenvolvimento/Teste](devtest-lab-artifact-author.md).

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório contendo a VM com a qual você quer trabalhar.  
1. Selecione **Minhas máquinas virtuais**.
1. Selecione a VM desejada.
1. Selecione **Artefatos**. 
1. Selecione **Aplicar artefatos**.
1. Na folha **Aplicar artefatos**, selecione o artefato que você deseja adicionar à VM.
1. Na folha **Adicionar artefatos**, insira os valores de parâmetro necessários e quaisquer parâmetros opcionais dos quais você precise.  
1. Selecione **Adicionar** para adicionar o artefato e retornar à folha **Aplicar artefatos**.
1. Continue adicionando artefatos à sua VM conforme o necessário.
1. Após adicionar os artefatos, você pode [alterar a ordem em que eles são executados](#change-the-order-in-which-artifacts-are-run). Você pode também voltar e [exibir ou modificar um artefato](#view-or-modify-an-artifact).
1. Quando você terminar de adicionar artefatos, selecione **Aplicar**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Alterar a ordem de execução dos artefatos
Por padrão, as ações dos artefatos são executadas na ordem em que eles são adicionados à VM. As etapas a seguir ilustram como alterar a ordem de execução dos artefatos.

1. Na parte superior da folha **Aplicar artefatos**, selecione o link que indica o número de artefatos adicionados à VM.
   
    ![Número de artefatos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na folha **Artefatos selecionados**, arraste e solte os artefatos na ordem desejada. **Observação:** se tiver problemas ao arrastar o artefato, verifique se está arrastando do lado esquerdo do artefato. 
1. Selecione **OK** quando tiver concluído.  

## <a name="view-or-modify-an-artifact"></a>exibir ou modificar um artefato
As etapas a seguir ilustram como exibir ou modificar os parâmetros de um artefato:

1. Na parte superior da folha **Aplicar artefatos**, selecione o link que indica o número de artefatos adicionados à VM.
   
    ![Número de artefatos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Na folha **Artefatos selecionados**, selecione o artefato que você deseja exibir ou editar.  
1. Na folha **Adicionar artefato**, faça as alterações necessárias e selecione **OK** para fechar a folha **Adicionar artefato**.
1. Selecione **OK** para fechar a folha **Artefatos selecionados**.

## <a name="save-azure-resource-manager-template"></a>Salvar modelo do Azure Resource Manager
Um modelo do Azure Resource Manager é uma forma declarativa de definir uma implantação repetível. As etapas a seguir explicam como salvar o modelo do Azure Resource Manager para a VM que está sendo criada.
Depois de salvo, você pode usar o modelo do Azure Resource Manager para [implantar novas VMs com o Azure PowerShell](../azure-resource-manager/resource-group-overview.md#template-deployment).

1. Na folha **Máquina virtual**, selecione **Exibir Modelo de ARM**.
2. Na **folha Exibir o modelo do Azure Resource Manager**, selecione o texto do modelo.
3. Copie o texto selecionado para a área de transferência.
4. Selecione **OK** para fechar a **folha Exibir Modelo do Azure Resource Manager**.
5. Abra um editor de texto.
6. Cole o texto do modelo da área de transferência.
7. Salve o arquivo para uso posterior.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
* Saiba como [criar artefatos personalizados para sua VM do DevTest Labs](devtest-lab-artifact-author.md).

