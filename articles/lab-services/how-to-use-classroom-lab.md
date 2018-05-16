---
title: Como acessar um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Neste tutorial, você deve acessar máquinas virtuais em um laboratório de sala de aula configurado por um professor.
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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 3a24c826ffdd3e368b6b16f6e78fbafc50c54b1e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Como acessar um laboratório de sala de aula no Azure Lab Services
Este artigo descreve como acessar um laboratório de sala de aula, conecte-se à VM no laboratório e interrompa a VM. 

## <a name="view-all-the-classroom-labs"></a>Exibir todos os laboratórios de sala de aula

1. Navegue até a **URL de registro** que você recebeu do professor/educador. 
2. Entre no serviço usando sua conta da escola para concluir o registro. 
3. Depois de registrado, confirme que você vê as máquinas virtuais para os laboratórios aos quais você tem acesso. 

    ![Exibir todos os laboratórios](./media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Conecte-se à máquina virtual em um laboratório de sala de aula

1. Selecione **Conectar** no bloco que representa a máquina virtual do laboratório que você deseja acessar.

    ![Exibir todos os laboratórios](./media/how-to-use-classroom-lab/connect-button.png)
2. Pode levar alguns minutos para que a máquina virtual esteja pronta.

    ![Preparando a máquina virtual para que esteja pronta](./media/how-to-use-classroom-lab/getting-virtual-machine-ready.png)
3. Salve o arquivo RDP no disco rígido e abra-o. 
    
    ![Salve o arquivo RDP](./media/how-to-use-classroom-lab/save-rdp-file.png)
4. Use o **nome de usuário** e **senha** obtido do seu professor/educador para fazer logon no computador. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Interrompa a máquina virtual em um laboratório de sala de aula

Selecione **Parar** no bloco que representa o laboratório de sala de aula. Quando a VM for interrompida, o botão **Iniciar** no bloco está habilitado. 

## <a name="next-steps"></a>Próximas etapas
Introdução à configuração de um laboratório usando o Azure Lab Services:

- [Configurar um laboratório de sala de aula](how-to-manage-classroom-labs.md)
- [Configurar um laboratório personalizado](tutorial-create-custom-lab.md)

 