---
title: Acessar um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
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
ms.date: 11/14/2018
ms.author: spelluru
ms.openlocfilehash: b9d140eedf69ed730985412ba1dcdca2d626b191
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706697"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Tutorial: Acessar um laboratório de sala de aula no Azure Lab Services
Neste tutorial, você, como aluno, conecta-se a uma máquina virtual (VM) em um laboratório de sala de aula. 

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Usar o link de registro 
> * Conectar-se à máquina virtual

## <a name="use-the-registration-link"></a>Usar o link de registro

1. Navegue até a **URL de registro** que você recebeu do professor/educador. 
2. Entre no serviço usando sua conta da escola para concluir o registro. 
3. Depois de registrado, confirme que consegue ver a máquina virtual do laboratório ao qual você tem acesso. 
2. Aguarde até que a máquina virtual fique pronta e **inicie** a VM. Esse processo demora um pouco.  

    ![Iniciar a VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

1. Selecione **Conectar** no bloco da máquina virtual do laboratório que você deseja acessar. 

    ![Conectar-se a uma VM](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Salve o arquivo RDP no disco rígido e abra-o (supondo que se trata de uma VM com Windows)
3. Use o **nome de usuário** e **senha** obtido do seu professor/educador para fazer logon no computador. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você acessou um laboratório de sala de aula usando o link de registro obtido do seu professor/educador.

Como proprietário de laboratório, convém exibir quem se registrou em seu laboratório e acompanhar o uso das VMs. Vá para o próximo tutorial para aprender a controlar o uso do laboratório:

> [!div class="nextstepaction"]
> [Acompanhar o uso de um laboratório](tutorial-track-usage.md) 