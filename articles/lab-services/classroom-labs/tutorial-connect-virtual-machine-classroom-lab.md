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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 5482ea720ea8d21230587dd9216bd006bf4e5a6e
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650641"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Tutorial: Acessar um laboratório de sala de aula no Azure Lab Services
Neste tutorial, você, como aluno, conecta-se a uma máquina virtual (VM) em um laboratório de sala de aula. 

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Usar o link de registro 
> * Conectar-se à máquina virtual

## <a name="use-the-registration-link"></a>Usar o link de registro

1. Navegue até a **URL de registro** que você recebeu do professor/educador. Não é necessário usar a URL de registro após concluir o registro. Em vez disso, use a URL: [https://labs.azure.com](https://labs.azure.com). 
1. Entre no serviço usando sua conta da escola para concluir o registro. 
2. Depois de registrado, confirme que consegue ver a máquina virtual do laboratório ao qual você tem acesso. 
3. Aguarde até que a máquina virtual fique pronta e **inicie** a VM. Esse processo demora um pouco.  

    ![Iniciar a VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

1. Selecione **Conectar** no bloco da máquina virtual do laboratório que você deseja acessar. 

    ![Conectar-se a uma VM](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Siga uma das etapas a seguir: 
    1. Para máquinas virtuais do **Windows**, salve o arquivo **RDP** no disco rígido. Abra o arquivo RDP para conectar a máquina virtual. Use o **nome de usuário** e **senha** obtido do seu professor/educador para entrar no computador. 
    3. Para máquinas virtuais do **Linux**, você pode usar **SSH** ou **RDP** (se habilitado) para se conectar a elas. Para obter mais informações, veja [Habilitar conexão de área de trabalho remota para computadores Linux](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você acessou um laboratório de sala de aula usando o link de registro obtido do seu professor/educador.

Como proprietário de laboratório, convém exibir quem se registrou em seu laboratório e acompanhar o uso das VMs. Vá para o próximo tutorial para aprender a controlar o uso do laboratório:

> [!div class="nextstepaction"]
> [Acompanhar o uso de um laboratório](tutorial-track-usage.md) 
