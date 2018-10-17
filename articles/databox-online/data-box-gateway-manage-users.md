---
title: Gerenciar usuários do Gateway do Azure Data Box | Microsoft Docs
description: Descreve como usar o portal do Azure para gerenciar usuários em seu Gateway do Azure Data Box.
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
ms.openlocfilehash: 3bd368cf19edf1370aaeef54d3ba0e4fd26ac239
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49070253"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-gateway"></a>Use o portal do Azure para gerenciar usuários em seu Gateway do Azure Data Box 

Este artigo descreve como gerenciar usuários em seu Gateway do Azure Data Box. Você pode gerenciar o Gateway do Azure Data Box usando o portal do Azure ou a interface do usuário da Web local. Use o portal do Azure para adicionar, modificar ou excluir usuários.

> [!IMPORTANT]
> - O Gateway do Data Box está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de solicitar e implantar essa solução.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Adicionar um usuário
> * Modificar usuário
> * Excluir um usuário 

## <a name="about-users"></a>Sobre os usuários

Os usuários podem ter privilégio completo ou somente leitura. Como o nome indica, usuários somente leitura podem apenas exibir os dados do compartilhamento. Usuários com privilégio completo podem ler dados de compartilhamento, gravar nesses compartilhamentos e modificar ou excluir os dados de compartilhamento. 

 - **Usuário com privilégios completo** – um usuário local com acesso completo. 
 - **Usuário somente leitura** – um usuário local com acesso somente leitura. Esses usuários estão associados a compartilhamentos que permitem operações somente leitura.

As permissões do usuário são definidas quando o usuário é criado durante a criação do compartilhamento. Após as permissões associadas a um usuário serem definidas, elas podem ser modificadas usando o Explorador de Arquivos. 


## <a name="add-a-user"></a>Adicionar um usuário

Para adicionar um usuário, siga estas etapas no portal do Azure.

1. No portal do Azure, vá até seu recurso do Gateway do Data Box e navegue até **Visão geral**. Clique em **+ Adicionar usuário** na barra de comandos.

    ![Clique em adicionar usuário](media/data-box-gateway-manage-users/add-user-1.png)

2. Especifique o nome de usuário e senha do usuário que você deseja adicionar. Confirme a senha e clique em **Adicionar**.

    ![Clique em adicionar usuário](media/data-box-gateway-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Estes usuários são reservados pelo sistema e não devem ser usados: Administrador, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Convidado.  

3. Você será notificado quando a criação do usuário for iniciada e concluída. Após o usuário ser criado, clique em **Atualizar** na barra de comandos para exibir a lista de usuários atualizada.


## <a name="modify-user"></a>Modificar usuário

Você pode alterar a senha associada a um usuário após ele ser criado. Selecione e clique na lista de usuários. Forneça e confirme a nova senha. Salve as alterações.
 
![Modificar usuário](media/data-box-gateway-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Excluir um usuário

Para excluir um usuário, siga estas etapas no portal do Azure.

1. Selecione e clique no usuário na lista de usuários e, em seguida, clique em **Excluir**.  

   ![Excluir um usuário](media/data-box-gateway-manage-users/delete-user-1.png)

2. Quando receber a solicitação, confirme a exclusão. 

   ![Excluir um usuário](media/data-box-gateway-manage-users/delete-user-2.png)

A lista de usuários é atualizada para refletir o usuário excluído.

![Excluir um usuário](media/data-box-gateway-manage-users/delete-user-3.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar a largura de banda](data-box-gateway-manage-bandwidth-schedules.md).
