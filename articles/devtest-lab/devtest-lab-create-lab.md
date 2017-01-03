---
title: "Criar um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Crie um laboratório no Azure DevTest Labs para máquinas virtuais"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 29c4c2a2818468a2fa8360eebd4b653bdcbbde19
ms.openlocfilehash: 0749d371466226343227c79db544a8e3dca0cca8


---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Criar Laboratórios de Desenvolvimento/Teste do Azure
## <a name="prerequisites"></a>Pré-requisitos
Para criar um laboratório, você precisa de:

* Uma assinatura do Azure. Para saber mais sobre as opções de compra do Azure, consulte [Como comprar o Azure](https://azure.microsoft.com/pricing/purchase-options/) ou [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/). Você deve ser o proprietário da assinatura para criar o laboratório.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Etapas para criar um laboratório n o Azure DevTest Labs
As etapas a seguir ilustram como usar o portal do Azure para criar um laboratório no Azure DevTest Labs. 

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Mais serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na folha **Laboratórios de Desenvolvimento/Teste**, selecione **Adicionar**.
   
    ![Adicionar um laboratório](./media/devtest-lab-create-lab/add-lab-button.png)
4. Na folha **Criar um Laboratório de Desenvolvimento/Teste** :
   
   1. Insira um **Nome do Laboratório** para o novo laboratório.
   2. Selecione a **Assinatura** para associar ao laboratório.
   3. Selecione um **Local** no qual o laboratório será armazenado.
   4. Selecione **Desligamento Automático** para especificar se você deseja ativar e definir os parâmetros de desligamento automático de todas as VMs do laboratório. 
   5. Selecione **Criar**.
      
      ![Criar uma folha de laboratório](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Próximas etapas
Depois de criar seu laboratório, aqui estão algumas das próximas etapas a serem consideradas:

* [Proteger o acesso a um laboratório](devtest-lab-add-devtest-user.md).
* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md).
* [Criar um modelo de laboratório](devtest-lab-create-template.md).
* [Criar artefatos personalizados para suas VMs](devtest-lab-artifact-author.md).
* [Adicionar uma VM com artefatos a um laboratório](devtest-lab-add-vm-with-artifacts.md).




<!--HONumber=Dec16_HO3-->


