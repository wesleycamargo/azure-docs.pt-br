---
title: Configurar uma conta de laboratório com o Azure Lab Services | Microsoft Docs
description: Neste tutorial, você configura uma conta de laboratório com o Azure Lab Services.
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
ms.date: 11/15/2018
ms.author: spelluru
ms.openlocfilehash: 4b8b5502090e1b115829b65834532dd6ce15a934
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52835906"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Tutorial: configurar uma conta de laboratório com o Azure Lab Services
No Azure Lab Services, uma conta de laboratório serve como a conta central na qual os laboratórios da sua organização são gerenciados. Em sua conta de laboratório, conceda permissão para outras pessoas criarem laboratórios e definir políticas que se aplicam a todos os laboratórios na conta do laboratório. Neste tutorial, saiba como criar uma conta de laboratório como um administrador de laboratório. 

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Criar uma conta de laboratório
> * Adicionar um usuário à função de criador de laboratório
> * Especificar as imagens do Marketplace disponíveis para proprietários de laboratório

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-lab-account"></a>Criar uma conta de laboratório
As etapas a seguir ilustram como usar o portal do Azure para criar uma conta de laboratório no Azure Lab Services. 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu à esquerda, selecione **Criar um recurso**.
3. Procure **Serviços de Laboratório** no Azure Marketplace e selecione **Serviços de Laboratório** na lista suspensa. 
4. Selecione **Serviços de Laboratório (Versão prévia)** na lista filtrada de serviços. 
1. Na janela **Criar uma conta de laboratório**, selecione **Criar**.
2. Na janela **Conta de laboratório**, execute as seguintes ações: 
    1. Para **Nome da conta de laboratório**, insira um nome. 
    2. Selecione a **Assinatura do Azure** na qual você quer criar a conta de laboratório.
    3. Para **Grupo de recursos**, selecione **Criar novo** e digite um nome para o grupo de recursos.
    4. Para **Local**, selecione um local/região em que você deseja que a conta de laboratório seja criada. 
    5. Selecione **Criar**. 

        ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Se você não vir a página da conta de laboratório, selecione o botão **notificações** e, em seguida, clique no botão **Ir para o recurso** nas notificações. 

    ![Janela Criar uma conta de laboratório](../media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. Você verá a seguinte página de **conta de laboratório**:

    ![Página da conta de laboratório](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Adicionar um usuário à função de criador de laboratório
Para configurar um laboratório de curso em uma conta de laboratório, o usuário deve ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta usada para criar a conta de laboratório é automaticamente adicionada a essa função. Se você planeja usar a mesma conta de usuário para criar um laboratório de curso, poderá pular esta etapa. Para usar outra conta de usuário para criar um laboratório de sala de aula, execute as seguintes etapas: 

Para fornecer aos educadores a permissão para criar laboratórios para suas classes, adicione-os à função de **Criador de Laboratório**:

1. Na página **Conta de Laboratório**, selecione **Controle de acesso (IAM)** e clique em **+ Adicionar atribuição de função** na barra de ferramentas. 
2. Na página **Adicionar permissões**, selecione **Criador de Laboratório** em **Função**, selecione o usuário que você deseja adicionar à função de Criadores de Laboratório e selecione **Salvar**. 

## <a name="specify-marketplace-images-available-to-lab-owners"></a>Especificar as imagens do Marketplace disponíveis para proprietários de laboratório
Como proprietário da conta de laboratório, você pode especificar as imagens do Marketplace que os criadores do laboratório podem usar para criar laboratórios na conta de laboratório. 

1. Selecione **Imagens do Marketplace** no menu esquerdo. Por padrão, você deve ver a lista completa de imagens (habilitadas e desabilitadas). É possível filtrar a lista para ver apenas as imagens habilitadas/desabilitadas, selecionando a opção **Somente habilitadas**/**Somente desabilitadas** na lista suspensa na parte superior. 
    
    ![Página Imagens do Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    As imagens do Marketplace exibidas na lista são apenas aquelas que atendem as seguintes condições:
        
    - Cria uma única VM
    - Usa o Azure Resource Manager para provisionar VMs
    - Não requer a compra de um plano de licenciamento extra
2. Para **desabilitar** uma imagem do Marketplace que tenha sido habilitada, execute uma das seguintes ações: 
    1. Selecione **... (reticências)**  na última coluna e selecione **Desabilitar imagem**. 

        ![Desabilitar uma imagem](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Selecione uma ou mais imagens na lista, marcando as caixas de seleção antes dos nomes de imagem na lista e selecione **Desabilitar imagens selecionadas**. 

        ![Desabilitar várias imagens](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. De modo semelhante, para **habilitar** uma imagem do Marketplace execute uma das seguintes ações: 
    1. Selecione **... (reticências)**  na última coluna e selecione **Habilitar imagem**. 
    2. Selecione uma ou mais imagens na lista, marcando as caixas de seleção antes dos nomes de imagem na lista e selecione **Habilitar imagens selecionadas**. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou uma conta de laboratório. Para saber mais sobre como criar um laboratório de sala de aula como profissão, vá para o próximo tutorial:

> [!div class="nextstepaction"]
> [Configurar um laboratório de sala de aula](tutorial-setup-classroom-lab.md)

