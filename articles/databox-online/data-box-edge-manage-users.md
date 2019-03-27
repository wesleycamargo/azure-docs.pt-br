---
title: Gerenciar usuários do Azure Data Box Edge | Microsoft Docs
description: Descreve como usar o portal do Azure para gerenciar usuários no Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 5642f0c1bcb7d7d8dcb17885aa6d3e9197889685
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57888409"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Usar o portal do Azure para gerenciar usuários no Azure Data Box Edge

Este artigo descreve como gerenciar usuários no Azure Data Box Edge. Você pode gerenciar o Azure Data Box Edge por meio do portal do Azure ou da IU da Web local. Use o portal do Azure para adicionar, modificar ou excluir usuários.

> [!IMPORTANT]
> O Data Box Edge está em versão prévia. Examine os [termos de serviço do Azure para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de solicitar e implantar essa solução.

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

1. No portal do Azure, acesse seu recurso do Data Box Edge e, em seguida, acesse **Visão geral > Usuários**. Selecione **+ Adicionar usuário** na barra de comandos.

    ![Selecionar Adicionar usuário](media/data-box-edge-manage-users/add-user-1.png)

2. Especifique o nome de usuário e senha do usuário que você deseja adicionar. Confirme a senha e selecione **Adicionar**.

    ![Especificar nome de usuário e senha](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Estes usuários são reservados pelo sistema e não devem ser usados: Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. Uma notificação é mostrada quando a criação de usuário é iniciada e concluída. Após a criação do usuário, na barra de comandos, selecione **Atualizar** para exibir a lista atualizada de usuários.


## <a name="modify-user"></a>Modificar usuário

Você pode alterar a senha associada a um usuário após ele ser criado. Selecione na lista de usuários. Insira e confirme a nova senha. Salve as alterações.
 
![Modificar usuário](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Excluir um usuário

Para excluir um usuário, siga estas etapas no portal do Azure.


1. No portal do Azure, acesse seu recurso do Data Box Edge e, em seguida, acesse **Visão geral > Usuários**.

    ![Selecionar o usuário a ser excluído](media/data-box-edge-manage-users/delete-user-1.png)

2. Selecione um usuário na lista de usuários e, em seguida, selecione **Excluir**.  

   ![Selecione Excluir](media/data-box-edge-manage-users/delete-user-2.png)

3. Quando receber a solicitação, confirme a exclusão. 

   ![Confirmar exclusão](media/data-box-edge-manage-users/delete-user-3.png)

A lista de usuários é atualizada para refletir o usuário excluído.

![Lista atualizada de usuários](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba como [Gerenciar a largura de banda](data-box-edge-manage-bandwidth-schedules.md).
