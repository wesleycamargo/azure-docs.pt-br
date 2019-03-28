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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 8e20f612bc54433091036377d51a7e59e3abec51
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402143"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Como acessar um laboratório de sala de aula no Azure Lab Services
Este artigo descreve como acessar um laboratório de sala de aula, conecte-se à VM no laboratório e interrompa a VM. 

## <a name="register-to-a-lab"></a>Registrar-se em um laboratório
1. Navegue até a **URL de registro** que você recebeu do professor/educador. 
2. Entre no serviço usando sua conta da escola para concluir o registro. 
3. Depois de registrado, confirme que consegue ver a máquina virtual do laboratório ao qual você tem acesso. 
2. Aguarde até que a máquina virtual fique pronta e **inicie** a VM. Esse processo demora um pouco.  

    ![Iniciar a VM](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>Exibir todos os laboratórios de sala de aula
Depois de se registrar nos laboratórios, você pode exibir todos os laboratórios de sala de aula por meio das seguintes etapas: 

1. Navegue até [https://labs.azure.com](https://labs.azure.com). 
2. Entre no serviço usando a conta de usuário usada para se registrar no laboratório. 
3. Confirme que você vê todos os laboratórios aos quais tem acesso. 

    ![Exibir todos os laboratórios](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Conecte-se à máquina virtual em um laboratório de sala de aula

1. Inicie a VM caso ainda não o tenha feito; selecione **Iniciar** no bloco. 
2. Selecione **Conectar** no bloco que representa a máquina virtual do laboratório que você deseja acessar. 
3. Siga uma das etapas a seguir: 
   1. Para máquinas virtuais do **Windows**, salve o arquivo **RDP** no disco rígido. Abra o arquivo RDP para conectar a máquina virtual. Use o **nome de usuário** e **senha** obtido do seu professor/educador para fazer logon no computador. 
   3. Para máquinas virtuais do **Linux**, copie e salve a cadeia de conexão SSH na caixa de diálogo **Conecte-se à sua máquina virtual**. Use essa cadeia de conexão de um terminal SSH (como [Putty](https://www.putty.org/)) para conectar a máquina virtual.

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Interrompa a máquina virtual em um laboratório de sala de aula

Para parar a VM, selecione **Parar** no bloco. Quando a VM for interrompida, o botão **Iniciar** no bloco está habilitado. 

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e publique modelos](how-to-create-manage-template.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
 