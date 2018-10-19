---
title: Reimplantar uma VM em um laboratório no Azure DevTest Labs | Microsoft Docs
description: Saiba como reimplantar uma máquina virtual (mover de um nó do Azure para outro) no Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 273b0f1105d8b71b90a06e2627e201b97f12a754
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095388"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Reimplantar uma VM em um laboratório no Azure DevTest Labs
Se você não puder se conectar a uma VM (máquina virtual) em um laboratório por meio de uma conexão de área de trabalho remota, reimplante a VM e tente se conectar novamente. Quando você reimplanta uma VM, o DevTest Labs move a VM do nó no qual ela está em execução para um novo nó da infraestrutura do Azure. Em seguida, ele inicia a VM, mantendo todas as opções de configuração e os recursos associados. Esse recurso economiza o tempo gasto na solução de problemas de conexão de área de trabalho remota ou do acesso de aplicativo a VMs baseadas no Windows no laboratório. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Etapas para reimplantar uma VM em um laboratório 
Para reimplantar uma VM em um laboratório no Azure DevTest Labs, siga estas etapas: 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
3. Na lista de laboratórios, selecione o laboratório que inclui a VM que você deseja reimplantar.  
4. No painel esquerdo, selecione **Minhas Máquinas Virtuais**. 
5. Na lista de VMs, selecione uma VM.
6. Na página Máquina Virtual de sua VM, selecione **Reimplantar**, em **OPERAÇÕES**, no menu esquerdo.

    ![Reimplantar](media/devtest-lab-redeploy-vm/redeploy.png)
7. Leia as informações na página e selecione o botão **Reimplantar**. 9. Verifique o status da operação de reimplantação na janela **Notificações**.

    ![Status da reimplantação](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Próximas etapas
Saiba como redimensionar uma VM no Azure DevTest Labs, confira [Redimensionar uma VM](devtest-lab-resize-vm.md).


