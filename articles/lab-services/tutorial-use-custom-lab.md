---
title: Acessar um laboratório no Azure DevTest Labs | Microsoft Docs
description: Neste tutorial, você acessa o laboratório criado usando o Azure DevTest Labs, solicita máquinas virtuais, usa-as e, em seguida, cancela-as.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: ab52206230c4dfe2d92c97f1e291ee00a086c570
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470856"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Tutorial: Acessar um laboratório no Azure DevTest Labs
Neste tutorial, você usa o laboratório que foi criado no [Tutorial: Criar um laboratório no Azure DevTest Labs](tutorial-create-custom-lab.md).

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Solicitar uma VM (máquina virtual) no laboratório
> * Conectar-se à VM
> * Cancelar a VM

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="access-the-lab"></a>Acessar o laboratório

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** no menu à esquerda. 
3. Selecione **DevTest Labs** para tipo de recurso. 
4. Selecione o laboratório. 

    ![Selecionar o laboratório](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Solicitar uma VM

1. Na lista de **máquinas de virtuais solicitáveis**, selecione **...**  (reticências) e selecione **Solicitar máquina**.

    ![Solicitar máquina virtual](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Confirme se você vê a VM na lista **Minhas máquinas virtuais**.

    ![Minha máquina virtual](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Conectar-se à VM

1. Selecione sua VM na lista. Você verá a **página de máquina virtual** para sua VM. Selecione **Conectar** na barra de ferramentas.

    ![Conectar-se à máquina virtual](./media/tutorial-use-custom-lab/connect-button.png)
2. Salvar o arquivo **RDP** baixado no seu disco rígido e use-o para se conectar à máquina virtual. Especifique o nome de usuário e a senha que você mencionou quando a VM foi criada na seção anterior. 

    > [!NOTE] 
    > Para se conectar a uma VM Linux, o acesso SSH e/ou RDP deve ser habilitado para a VM. Para obter os passos para se conectar a uma VM Linux via RDP, consulte [Instalar e configurar a Área de Trabalho Remota para conectar-se a uma VM Linux no Azure](../virtual-machines/linux/use-remote-desktop.md). 


## <a name="unclaim-the-vm"></a>Cancelar a VM
Depois que você usar a VM, cancelar a VM seguindo estas etapas: 

1. Na página de máquina virtual, selecione **Cancelar** na barra de ferramentas. 

    ![Cancelar VM](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. A VM é desligada antes que seja cancelada. 

    ![Cancelar status](./media/tutorial-use-custom-lab/unclaim-status.png) 
1. Depois da operação de cancelamento, você verá a VM na lista de **Máquinas de virtuais solicitáveis** na parte inferior. 
    
## <a name="next-steps"></a>Próximas etapas
Este tutorial mostrou como acessar e usar um laboratório que foi criado usando o Azure DevTest Labs. Para obter mais informações sobre como acessar e usar máquinas virtuais em um laboratório, consulte 

> [!div class="nextstepaction"]
> [Como usar VMs em um laboratório](devtest-lab-add-vm.md)

