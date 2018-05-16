---
title: Gerenciar laboratórios de salas de aula no Azure Lab Services | Microsoft Docs
description: Saiba como criar e configurar um laboratório de sala de aula, exibir todos os laboratórios de sala de aula, compartilhar o link de registro com um usuário de laboratório, ou excluir um laboratório.
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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 17544275f921486529518e37eb171cd0b4f26791
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="manage-classroom-labs-in-azure-lab-services-formerly-azure-devtest-labs"></a>Gerenciar laboratórios de sala de aula no Azure Lab Services (anteriormente Azure DevTest Labs)
Este artigo descreve como criar e configurar um laboratório de sala de aula, exibir todos os laboratórios de sala de aula ou excluir um laboratório de sala de aula.

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com).
2. Na janela **Novo laboratório**, execute as seguintes ações: 
    1. Especifique um **nome** para o laboratório de sala de aula. 
    2. Selecione o **tamanho** da máquina virtual que você planeja usar na sala de aula.
    3. Selecione a **imagem** a ser usada para criar a máquina virtual.
    4. Especifique as **credenciais padrão** para usar para fazer logon em máquinas virtuais no laboratório.
    7. Clique em **Salvar**.

        ![Criar um laboratório de sala de aula](./media/how-to-manage-classroom-labs/new-lab-window.png)
1. Você verá a **home page** do laboratório. 
    
    ![Home page do laboratório de sala de aula](./media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Configurar a política de uso

1. Selecione **Política de uso**. 
2. Em **Política de uso**, configurações, insira o **número de usuários** permitidos para usar o laboratório.
3. Clique em **Salvar**. 

    ![Política de uso](./media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Configurar o modelo
Um modelo em um laboratório é de quais máquinas virtuais todos os usuários foram criados. Configure a máquina virtual do modelo para que ela seja configurada exatamente com o que você deseja fornecer aos usuários de laboratório. Você pode fornecer um nome e uma descrição do modelo que os usuários do laboratório veem e definir a visibilidade como "Público" para disponibilizar as instâncias da VM de modelo para os usuários do laboratório.  

## <a name="set-template-title-and-description"></a>Definir título e descrição do modelo
1. Na seção **Modelo**, selecione **Editar** (ícone de lápis) para o modelo. 
2. Na janela **Exibição do usuário**, insira um **Título** para o modelo.
3. Insira a **Descrição** para o modelo.
4. Clique em **Salvar**.

    ![Descrição do laboratório de sala de aula](./media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Torne as instâncias do modelo públicas 
Depois de definir a visibilidade de um modelo para **Oública**, o Azure Lab Services cria VMs no laboratório usando o modelo. O número de VMs criadas neste processo é o mesmo que o número máximo de usuários permitidos em um laboratório, que você pode definir na política de uso do laboratório. Todas as máquinas virtuais têm a mesma configuração que o modelo.  

1. Selecione **Visibilidade** na seção **Modelo**. 
2. Na página **Disponibilidade**, selecione **Público**.
    
    > [!IMPORTANT]
    > Depois que um modelo está disponível publicamente, o acesso não pode ser alterado para privado. 
3. Clique em **Salvar**.

    ![Disponibilidade](./media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>Enviar link de registro para estudantes

1. Selecione o bloco **Registro de usuário**.
2. Na caixa de diálogo **Registro de usuário**, selecione o botão **Copiar**. O link é copiado para a área de transferência. Cole-o em um editor de email e envie um email para o aluno. 

    ![Link de registro do aluno](./media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>Exibir todos os laboratórios de sala de aula
1. Navegue até [Portal do Azure Lab Services](https://labs.azure.com).
2. Confirme se você vê todos os laboratórios na conta de laboratório selecionada. 

    ![Todos os laboratórios](./media/how-to-manage-classroom-labs/all-labs.png)
3. Use a lista suspensa na parte superior para selecionar uma conta de laboratório diferente. Você verá laboratórios na conta de laboratório selecionada. 

## <a name="delete-a-classroom-lab"></a>Excluir um laboratório de sala de aula
1. No bloco para o laboratório, selecione três pontos (...) no canto. 

    ![Selecione o laboratório](./media/how-to-manage-classroom-labs/select-three-dots.png)
2. Selecione **Excluir**. 

    ![Botão Excluir](./media/how-to-manage-classroom-labs/delete-button.png)
3. Na caixa de diálogo **Excluir laboratório**, selecione **Excluir**. 

    ![Caixa de diálogo Excluir](./media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>Próximas etapas
Introdução à configuração de um laboratório usando o Azure Lab Services:

- [Configurar um laboratório de sala de aula](how-to-manage-classroom-labs.md)
- [Configurar um laboratório personalizado](tutorial-create-custom-lab.md)
