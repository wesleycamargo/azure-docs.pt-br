---
title: Acompanhar o uso de um laboratório no Azure Lab Services | Microsoft Docs
description: Neste tutorial, você, como um criador/proprietário de laboratório, acompanha o uso do laboratório.
services: lab-services
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
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 710d157dcf4c6d060e59bcfbb69455e2ddc91bdd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450123"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Tutorial: Acompanhar o uso de um laboratório no Azure Lab Services
Este tutorial mostra como um criador/proprietário de laboratório pode acompanhar o uso de um laboratório.

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Exibir usuários registrados em seu laboratório
> * Exibir o uso de VMs no laboratório
> * Gerenciar VMs de aluno 


## <a name="view-users-registered-with-the-lab"></a>Exibir usuários registrados no laboratório

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com). 
2. Selecione **Iniciar sessão** e insira suas credenciais. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft.
3. Na página **Meus Laboratórios**, selecione o laboratório do qual você deseja acompanhar o uso. 
4. Selecione a guia **Usuários**. Você verá os alunos que foram registrados no laboratório. Selecione **Link de registro**, copie o link e envie-o a um novo aluno que ainda não tenha sido registrado no laboratório. 

    ![Usuários registrados](../media/tutorial-track-usage/registered-users.png)

## <a name="view-the-usage-of-vms-in-the-lab"></a>Exibir o uso de VMs no laboratório 

1. Selecione **Máquinas Virtuais** no menu à esquerda. 
2. Confirme se você vê o status das máquinas virtuais e o número de horas em que as VMs estão em execução. O tempo gasto em uma VM de estudante não é contado em relação ao tempo de uso mostrado na última coluna. 

    ![Uso de VM](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Gerenciar VMs de aluno 
Conforme você passar o mouse sobre uma linha na lista da máquina virtual, verá os controles para realizar as seguintes tarefas: 

- Como conectar-se a uma VM
- Iniciar uma VM
- Parar uma VM
- Excluir uma VM

![Controles da VM](../media/tutorial-track-usage/vm-controls.png) 



## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a localizar os usuários que foram registrados no laboratório, acompanhar o uso de VMs no laboratório e gerenciar VMs no laboratório.

Para saber mais sobre os laboratórios da sala de aula, consulte os tópicos nos [guias de tutorial](how-to-manage-lab-accounts.md).
