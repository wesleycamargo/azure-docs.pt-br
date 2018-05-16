---
title: Configurar um laboratório de sala de aula usando o Azure Lab Services | Microsoft Docs
description: Neste tutorial, você configura um laboratório para usar em uma sala de aula.
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
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: 3e9f8d118c4413ec93b7dffcfa41b6ad4381b052
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutorial: Configurar um laboratório de sala de aula 
Neste tutorial, você configura um laboratório de sala de aula com um conjunto de máquinas virtuais que são usados por alunos na sala de aula.  

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Criar um laboratório de sala de aula
> * Configurar o laboratório de sala de aula
> * Enviar link de registro para estudantes

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue até [o site do Azure Lab Services](https://labs.azure.com).
2. Na janela **Novo laboratório**, execute as seguintes ações: 
    1. Especifique um **nome** para o laboratório de sala de aula. 
    2. Selecione o **tamanho** da máquina virtual que você planeja usar na sala de aula.
    3. Selecione a **imagem** a ser usada para criar a máquina virtual.
    4. Especifique as **credenciais padrão** para fazer logon em máquinas virtuais no laboratório. 
    7. Clique em **Salvar**.

        ![Criar um laboratório de sala de aula](./media/tutorial-setup-classroom-lab/new-lab-window.png)
1. Você verá a **home page** do laboratório. 
    
    ![Home page do laboratório de sala de aula](./media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Configurar a política de uso

1. Selecione **Política de uso**. 
2. Em **Política de uso**, configurações, insira o **número de usuários** permitidos para usar o laboratório.
3. Clique em **Salvar**. 

    ![Política de uso](./media/tutorial-setup-classroom-lab/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Configurar o modelo 
Um modelo em um laboratório é de máquinas virtuais de todos os usuários que foram criadas. Configure a máquina virtual de modelo para que ela seja configurada exatamente com o que você deseja fornecer aos usuários de laboratório. Você pode fornecer um nome e uma descrição do modelo que os usuários do laboratório veem e definir a visibilidade como "Público" para disponibilizar as instâncias da VM de modelo para os usuários do laboratório. 

### <a name="set-title-and-description"></a>Definir título e descrição
1. Na seção **Modelo**, selecione **Editar** (ícone de lápis) para o modelo. 
2. Na janela **Exibição usuário**, insira um **Título** para o modelo.
3. Insira a **Descrição** para o modelo.
4. Clique em **Salvar**.

    ![Descrição do laboratório de sala de aula](./media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Torne as instâncias do modelo públicas
Depois de definir a visibilidade de um modelo para **pública**, o Azure Lab Services cria máquinas virtuais no laboratório usando o modelo. O número de máquinas virtuais criadas neste processo é o mesmo que o número máximo de usuários permitidos em um laboratório, você pode definir na política de uso do laboratório. Todas as máquinas virtuais têm a mesma configuração que o modelo. 

1. Selecione **Visibilidade** na seção **Modelo**. 
2. Na página **Disponibilidade**, selecione **Público**.
    
    > [!IMPORTANT]
    > Depois que um modelo está disponível publicamente, o acesso não pode ser alterado para privado. 
3. Clique em **Salvar**.

    ![Disponibilidade](./media/tutorial-setup-classroom-lab/public-access.png)

## <a name="send-registration-link-to-students"></a>Enviar link de registro para estudantes

1. Selecione o bloco **registro de usuário**.
2. Na caixa de diálogo **registro de usuário**, selecione o botão **Copiar**. O link é copiado para a área de transferência. Cole-o em um editor de e-mail e envie um e-mail para o aluno. 

    ![Link de registro do aluno](./media/tutorial-setup-classroom-lab/registration-link.png)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um laboratório de sala de aula e configurou o laboratório. Para saber como um aluno pode acessar uma VM no laboratório usando o link de registro, vá para o seguinte tutorial:

> [!div class="nextstepaction"]
> [Conectar a uma VM no laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)

