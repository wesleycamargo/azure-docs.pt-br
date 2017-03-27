---
title: "Gerenciar fórmulas no Azure DevTest Labs para criar VMs | Microsoft Docs"
description: "Saiba como atualizar e remover fórmulas do Azure DevTest Labs"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 841dd95a-657f-4d80-ba26-59a9b5104fe4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: tarcher
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: fd334dc8cd22990456888146f41389054f2e8b29
ms.lasthandoff: 03/15/2017


---
# <a name="manage-azure-devtest-labs-formulas"></a>Gerenciar fórmulas do Azure DevTest Labs

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

O artigo [Criar fórmulas do Azure DevTest Labs](devtest-lab-create-formulas.md#create-a-formula), orienta você pelo processo de criação de uma fórmula no Azure DevTest Labs. Depois que você tiver uma fórmula, este artigo o orientará pelo gerenciamento delas.

## <a name="modify-a-formula"></a>Modificar uma Fórmula
Para modificar uma fórmula, siga estas etapas:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na folha do laboratório, selecione **Fórmulas (bases reutilizáveis)**.
   
    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na folha **Fórmulas do laboratório** , selecione a fórmula que você deseja modificar.
6. Na folha **Atualizar fórmula**, faça as edições desejadas e selecione **Atualizar**.

## <a name="delete-a-formula"></a>Excluir uma fórmula
Para excluir uma fórmula, siga estas etapas:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Na folha **Configurações**, selecione **Fórmulas**.
   
    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Na folha **Fórmulas do laboratório** , clique nas reticências à direita da fórmula que você deseja excluir.
   
    ![Menu Fórmula](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. No menu de contexto da fórmula, selecione **Excluir**.
   
    ![Menu de contexto da Fórmula](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Selecione **Sim** na caixa de diálogo de confirmação de exclusão.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionadas
* [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Próximas etapas
Depois de criar uma fórmula para uso durante a criação de uma VM, a próxima etapa será [adicionar uma VM ao seu laboratório](devtest-lab-add-vm-with-artifacts.md).


