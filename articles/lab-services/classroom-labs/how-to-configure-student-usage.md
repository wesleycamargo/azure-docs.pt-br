---
title: Definir configurações de uso em laboratórios de sala de aula dos Serviços de Laboratório do Azure | Microsoft Docs
description: Saiba como configurar o número de usuários para o laboratório, registrá-los no laboratório, controlar o número de horas que eles podem usar a VM e muito mais.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2018
ms.author: spelluru
ms.openlocfilehash: a91d5826f52b2beb05f2d9a08f3646bd776b294e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142632"
---
# <a name="configure-usage-settings-and-policies"></a>Configurar políticas e configurações de uso
Este artigo descreve como configurar o número de usuários do laboratório, registrá-los no laboratório, controlar o número de horas que eles podem usar a VM e muito mais. 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>Especifique o número de usuários permitidos no laboratório

1. Selecione **Política de uso**. 
2. Em **Política de uso**, configurações, insira o **número de usuários** permitidos para usar o laboratório.
3. Clique em **Salvar**. 

    ![Política de uso](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-users"></a>Enviar link de registro para usuários

1. Alterne para o modo de exibição **Painel**. 
2. Selecione o bloco **Registro de usuário**.

    ![Link de registro do aluno](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Na caixa de diálogo **Registro de usuário**, selecione o botão **Copiar**. O link é copiado para a área de transferência. Cole-o em um editor de email e envie um email para o aluno. Os alunos usam o link para navegar até a página que os ajuda a se registrar no laboratório. 

    ![Link de registro do aluno](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Na caixa de diálogo **Registro de usuário**, selecione **Fechar**. 

## <a name="view-users-registered-with-the-lab"></a>Exibir usuários registrados no laboratório

Selecione **usuários** no menu à esquerda para ver a lista de usuários registrados com o laboratório. 

![Lista de usuários registrados no laboratório](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>Definir cotas por usuário

1. Selecione **Configurações do usuário** no menu à esquerda.
2. Selecione **Quta por usuário** lado a lado. 
3. Selecione **Limitar o número de horas que um usuário pode usar uma VM**. 
4. Por **Quantas horas deseja dar a cada usuário?**, insira o número de horas e selecione **Salvar**. 

    ![Número de horas por usuário](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Você vê o número de horas no bloco **Cota por usuário** agora. 

    ![Cota por usuário](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="manage-user-vms"></a>Gerenciar VMs do usuário
Após o registro dos alunos no Azure Lab Services usando o link de registro fornecido, você verá as VMs atribuídas aos alunos na guia **Máquinas virtuais**. 

![Máquinas virtuais atribuídas aos alunos](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

É possível executar as seguintes tarefas em uma VM de aluno: 

- Pare uma VM, se ela estiver em execução. 
- Inicie uma VM, se ela estiver parada. 
- Conecte-se à VM. 
- Exclua a VM. 
- Veja o número de horas que os usuários usaram a máquina virtual. 


## <a name="next-steps"></a>Próximas etapas
Introdução à configuração de um laboratório usando o Azure Lab Services:

- [Configurar um laboratório de sala de aula](how-to-manage-classroom-labs.md)
- [Configurar um laboratório](../tutorial-create-custom-lab.md)
