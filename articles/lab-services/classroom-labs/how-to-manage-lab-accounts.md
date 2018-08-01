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
ms.openlocfilehash: ff2968f8e2fa9a705817b020f2daa6582d78029c
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225295"
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
Para configurar um laboratório de curso em uma conta de laboratório, o usuário deve ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta usada para criar a conta de laboratório é automaticamente adicionada a essa função. Se você planeja usar a mesma conta de usuário para criar um laboratório de curso, poderá pular esta etapa. Para usar outra conta de usuário e criar um laboratório de curso, siga estas etapas: 

1. Na página **Conta de Laboratório**, selecione **Controle de acesso (IAM)** e clique em **+ Adicionar** na barra de ferramentas. 

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/access-control.png)
2. Na página **Adicionar permissões**, selecione **Criador de Laboratório** em **Função**, selecione o usuário que você deseja adicionar à função de Criadores de Laboratório e selecione **Salvar**. 

    ![Adicionar usuário à função de Criador de Laboratório](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>Especificar as imagens do Marketplace disponíveis para proprietários de laboratório
Nesta seção, você deve especificar imagens do Marketplace que os proprietários de laboratório podem usar para criar laboratórios de curso. 

1. Selecione **Imagens do Marketplace** no menu esquerdo. Por padrão, você deve ver a lista completa de imagens (habilitadas e desabilitadas). É possível filtrar a lista para ver apenas as imagens habilitadas/desabilitadas, selecionando a opção **Somente habilitadas**/**Somente desabilitadas** na lista suspensa na parte superior. 

    ![Página Imagens do Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)
2. Para **desabilitar** uma imagem do Marketplace que tenha sido habilitada, execute uma das seguintes ações: 
    1. Selecione **... (reticências)**  na última coluna e selecione **Desabilitar imagem**. 

        ![Desabilitar uma imagem](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Selecione uma ou mais imagens na lista, marcando as caixas de seleção antes dos nomes de imagem na lista e selecione **Desabilitar imagens selecionadas**. 

        ![Desabilitar várias imagens](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. De modo semelhante, para **habilitar** uma imagem do Marketplace execute uma das seguintes ações: 
    1. Selecione **... (reticências)**  na última coluna e selecione **Habilitar imagem**. 
    2. Selecione uma ou mais imagens na lista, marcando as caixas de seleção antes dos nomes de imagem na lista e selecione **Habilitar imagens selecionadas**. 

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
