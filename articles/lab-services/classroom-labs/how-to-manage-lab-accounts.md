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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 6f283ce007e96547e01a01a3753ddcb60574bfc3
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412795"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Gerenciar contas de laboratório no Azure Lab Services 
Nos serviços de laboratório do Azure, uma conta de laboratório é um contêiner para tipos de laboratório gerenciado, como laboratórios de sala de aula. Um administrador configura uma conta de laboratório no Azure Lab Services e fornece acesso aos proprietários de laboratório que podem criar laboratórios na conta. Este artigo descreve como criar uma conta de laboratório, exibir todas as contas de laboratório ou excluir uma conta de laboratório.

## <a name="create-a-lab-account"></a>Criar uma conta de laboratório
As etapas a seguir ilustram como usar o portal do Azure para criar uma conta de laboratório no Azure Lab Services. 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** no menu esquerdo. Selecione **Contas de laboratório** na seção **DEVOPS**. Se você selecionar a estrela (`*`) ao lado de **Contas de laboratório**, ela será adicionada à seção **FAVORITOS** no menu esquerdo. Na próxima vez em diante, selecione **Contas de laboratório** em **FAVORITOS**.

    ![Todos os serviços -> Contas de laboratório](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Na página **Contas de laboratório**, selecione **Adicionar** na barra de ferramentas. 

    ![Selecione Adicionar na página Contas de laboratório](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Na página **Conta de laboratório**, execute as seguintes ações: 
    1. Para **Nome da conta de laboratório**, insira um nome. 
    2. Selecione a **Assinatura do Azure** na qual você quer criar a conta de laboratório.
    3. Para **Grupo de recursos**, selecione **Criar novo** e digite um nome para o grupo de recursos.
    4. Para **Local**, selecione um local/região em que você deseja que a conta de laboratório seja criada. 
    5. Selecione uma existente **Galeria de imagens compartilhadas** ou criar um. Você pode salvar o modelo de VM na Galeria de imagens de compartilhado para que ele seja reutilizado por outros. Para obter informações detalhadas sobre galerias de imagens compartilhadas, consulte [usa uma galeria de imagens compartilhadas no Azure Lab Services](how-to-use-shared-image-gallery.md).
    6. Para **Emparelhar rede virtual**, selecione uma VNet (rede virtual) de emparelhamento para a rede de laboratório. Os laboratórios criados nesta conta são conectados à VNet selecionada e têm acesso aos recursos na rede virtual selecionada. 
    7. Especifique um **intervalo de endereços** para VMs do laboratório. O intervalo de endereços deve estar na notação CIDR (CIDR) (exemplo: 10.20.0.0/23). Máquinas virtuais no laboratório serão criadas nesse intervalo de endereço. Para obter mais informações, consulte [especificar um intervalo de endereços para VMs no laboratório](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).    
    8. Para o campo **Permitir que o criador de laboratório escolha a localização do laboratório**, especifique se deseja que os criadores de laboratório possam selecionar uma localização para o laboratório. Por padrão, a opção está desabilitada. Quando ela estiver desabilitada, os criadores de laboratório não poderão especificar uma localização para o laboratório que estão criando. Os laboratórios são criados na localização geográfica mais próxima da conta de laboratório. Quando ela estiver habilitada, um criador de laboratório poderá selecionar uma localização no momento da criação de um laboratório.      
    9. Selecione **Criar**. 

        ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Selecione o **ícone de sino** na barra de ferramentas (**Notificações**), confirme se a implantação foi bem-sucedida e, em seguida, selecione **Ir para o recurso**. 

    Como alternativa, selecione **Atualizar** na página **Contas de laboratório** e, em seguida, selecione a conta de laboratório que você criou. 

    ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Você verá a seguinte página de **conta de laboratório**:

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Exibir contas de laboratório
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** no menu. 
3. Selecione **Contas de Laboratório** para o **tipo**. 
    Você também pode filtrar por assinatura, grupo de recursos, locais e marcas. 

    ![Todos os recursos -> Contas de laboratório](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Visualize e gerencie laboratórios na conta do laboratório

1. Na página **Conta de laboratório**, selecione **Labs** no menu à esquerda.

    ![Laboratórios na conta](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Você vê uma **lista de laboratórios** na conta com as seguintes informações: 
    1. Nome do laboratório.
    2. A data em que o laboratório foi criado. 
    3. Endereço de e-mail do usuário que criou o laboratório. 
    4. Número máximo de usuários permitidos no laboratório. 
    5. Status do laboratório. 

## <a name="delete-a-lab-in-the-lab-account"></a>Excluir um laboratório na conta do laboratório
Siga as instruções na seção anterior para ver uma lista dos laboratórios na conta do laboratório.

1. Selecione **... (reticências)** e selecione **excluir**. 

    ![Excluir um laboratório - botão](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Selecione **Sim** na mensagem de aviso. 

    ![Confirmar exclusão do laboratório](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Excluir uma conta de laboratório
Siga as instruções da seção anterior que exibe as contas de laboratório em uma lista. Use as instruções a seguir para excluir uma conta de laboratório: 

1. Selecione a **conta de laboratório** que você deseja excluir. 
2. Selecione **Excluir** na barra de ferramentas. 

    ![Contas de laboratório -> botão Excluir](../media/how-to-manage-lab-accounts/delete-button.png)
1. Digite **Sim** para confirmar.
1. Selecione **Excluir**. 

    ![Excluir conta de laboratório – confirmação](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)


## <a name="next-steps"></a>Próximas etapas
Consulte o artigo a seguir: [Como configurar contas de laboratório](how-to-configure-lab-accounts.md).