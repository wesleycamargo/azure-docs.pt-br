---
title: Gerenciar usuários do Gateway do Azure Data Box | Microsoft Docs
description: Descreve como usar o portal do Azure para gerenciar agendas de largura de banda em seu Gateway do Azure Data Box.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/09/2018
ms.author: alkohli
ms.openlocfilehash: d1049ef66ad02987c6523e4b5ec5dd9959e0fa47
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49070208"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-gateway"></a>Use o portal do Azure para gerenciar agendas de largura de banda em seu Gateway do Azure Data Box  

Este artigo descreve como gerenciar usuários em seu Gateway do Azure Data Box. As agendas de largura de banda permitem configurar o uso da largura de banda da rede em diferentes agendamentos ao longo das horas do dia. Essas agendas podem ser aplicadas às operações de download e upload de seu dispositivo para a nuvem. 

Você pode adicionar, modificar ou excluir as agendas de largura de banda de seu Gateway do Data Box usando o portal do Azure.

> [!IMPORTANT]
> - O Gateway do Data Box está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de solicitar e implantar essa solução.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Adicionar uma agenda
> * modificar uma agenda
> * excluir uma agenda 


## <a name="add-a-schedule"></a>Adicionar uma agenda

Para adicionar um usuário, siga estas etapas no portal do Azure.

1. No portal do Azure de seu recurso do Gateway do Data Box, acesse **Largura de banda**.
2. No painel direito, clique em **+ Adicionar agendamento**.

    ![Clique em adicionar usuário](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-1.png)

3. Em **Adicionar agendamento**: 

    1. Informe o **Dia de início**, o **Dia de término**, a **Hora de início** e a **Hora de término** da agenda. 
    2. Se essa agenda é executada o dia inteiro, você pode marcar a opção **O dia inteiro**. 
    3. **Taxa de largura de banda** é a largura de banda em Megabits por segundo (Mbps) usada pelo dispositivo em operações que envolvem a nuvem (uploads e downloads). Forneça um número entre 1 e 1.000 para esse campo. 
    4. Marque a opção de largura de banda **Ilimitada** se você não quiser restringir o download e o upload de dados. 
    5. Clique em **Adicionar**.

    ![Clique em adicionar usuário](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-2.png)

3. É criada uma agenda com os parâmetros especificados. Em seguida, essa agenda é exibida na lista de agendas de largura de banda no portal.


## <a name="edit-schedule"></a>Editar agenda

Execute as etapas a seguir para editar uma agenda de largura de banda. 

1. No portal do Azure, vá até seu recurso do Gateway do Data Box e acesse Largura de banda. 
2. Na lista de agendas de largura de banda, selecione e clique em uma agenda que você deseja modificar.
    ![Modificar usuário](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-1.png)

3. Faça as alterações desejadas e salve-as.

    ![Modificar usuário](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-2.png)

4. Após a agenda ser modificada, a lista de agendas será atualizada para refletir a agenda modificada.

    ![Modificar usuário](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>excluir uma agenda

Execute as seguintes etapas para excluir uma agenda de largura de banda associada a seu dispositivo de Gateway do Data Box.

1. No portal do Azure, vá até seu recurso do Gateway do Data Box e acesse **Largura de banda**.  

2. Na lista de agendas de largura de banda, selecione uma agenda que você deseja excluir. Clique com o botão direito do mouse para exibir o menu de contexto e clique em **Excluir**. 

   ![Excluir um usuário](media/data-box-gateway-manage-bandwidth-schedules/delete-schedule-1.png)

3.  Após a agenda ser excluída, a lista de agendas será atualizada.



## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar a largura de banda](data-box-gateway-manage-bandwidth-schedules.md).
