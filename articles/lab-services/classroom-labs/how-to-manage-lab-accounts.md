---
title: Gerenciar contas de laboratório no Azure Lab Services | Microsoft Docs
description: Saiba como criar uma conta de laboratório, exibir todas as contas de laboratório ou excluir uma conta de laboratório em uma assinatura do Azure.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 6039ea482b0968d48fc21ff3dfec82a2ff0db43d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715319"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Gerenciar contas de laboratório no Azure Lab Services 
No Azure Lab Services, uma conta de laboratório é um contêiner para laboratórios gerenciados, como laboratórios de sala de aula. Um administrador configura uma conta de laboratório no Azure Lab Services e fornece acesso aos proprietários de laboratório que podem criar laboratórios na conta. Este artigo descreve como criar uma conta de laboratório, exibir todas as contas de laboratório ou excluir uma conta de laboratório.

## <a name="create-a-lab-account"></a>Criar uma conta de laboratório
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione **Criar um recurso**.
3. Procure **Serviços de Laboratório** no Azure Marketplace e selecione **Serviços de Laboratório** na lista suspensa. 
4. Selecione **Serviços de Laboratório (Versão prévia)** na lista filtrada de serviços. 
5. Na janela **Criar uma conta de laboratório**, selecione **Criar**.
7. Na janela **Conta de laboratório**, execute as seguintes ações: 
    1. Para **Nome da conta de laboratório**, insira um nome. 
    2. Selecione a **Assinatura do Azure** na qual você quer criar a conta de laboratório.
    3. Para **Grupo de recursos**, selecione **Criar novo** e digite um nome para o grupo de recursos.
    4. Para **Local**, selecione um local/região em que você deseja que a conta de laboratório seja criada. 
    5. Selecione **Criar**. 

        ![Janela Criar uma conta de laboratório](../media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Se você não vir a página da conta de laboratório, selecione o botão **notificações** e, em seguida, clique no botão **Ir para o recurso** nas notificações. 

    ![Janela Criar uma conta de laboratório](../media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Você verá a seguinte página de **conta de laboratório**:

    ![Página da conta de laboratório](../media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um usuário à função de criador de laboratório
Para fornecer aos educadores a permissão para criar laboratórios para suas classes, adicione-os à função de Criador de Laboratório:

1. Na página **Conta de Laboratório**, selecione **Controle de acesso (IAM)** e clique em **+ Adicionar** na barra de ferramentas. 

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/access-control.png)
2. Na página **Adicionar permissões**, selecione **Criador de Laboratório** em **Função**, selecione o usuário que você deseja adicionar à função de Criadores de Laboratório e selecione **Salvar**. 

    ![Adicionar usuário à função de Criador de Laboratório](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="view-lab-accounts"></a>Exibir contas de laboratório
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** no menu. 
3. Selecione **Serviços de Laboratório** para o **tipo**. 
    Você também pode filtrar por assinatura, grupo de recursos, locais e marcas. 

## <a name="delete-a-lab-account"></a>Excluir uma conta de laboratório
Siga as instruções da seção anterior que exibe as contas de laboratório em uma lista. Use as instruções a seguir para excluir uma conta de laboratório: 

1. Selecione a **conta de laboratório** que você deseja excluir. 
2. Selecione **Excluir** na barra de ferramentas. 
3. Digite **Sim** para confirmar.
4. Selecione **Excluir**. 

## <a name="next-steps"></a>Próximas etapas
Introdução à configuração de um laboratório usando o Azure Lab Services:

- [Configurar um laboratório de sala de aula](tutorial-setup-classroom-lab.md)
- [Configurar um laboratório](../tutorial-create-custom-lab.md)
