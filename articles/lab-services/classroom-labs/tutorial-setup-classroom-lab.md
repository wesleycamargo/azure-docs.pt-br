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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 39683c89db57dbeefd190a51415c783d012785e0
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36303768"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutorial: Configurar um laboratório de sala de aula 
Neste tutorial, você configura um laboratório de sala de aula com máquinas virtuais que são usadas por alunos na sala de aula.  

Neste tutorial, você executa as seguintes ações:

> [!div class="checklist"]
> * Criar um laboratório de sala de aula
> * Configurar o laboratório de sala de aula
> * Enviar link de registro para estudantes

## <a name="prerequisites"></a>pré-requisitos
Para configurar um laboratório de sala de aula em uma conta de laboratório, você deve ser um membro da função **Criador de Laboratório** na conta de laboratório. Um proprietário de laboratório pode adicionar um usuário à função Criador de Laboratório usando as etapas deste artigo: [Adicionar um usuário à função Criador de Laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com).
2. Selecione **Iniciar sessão** e insira suas credenciais. 
3. Na janela **Novo laboratório**, execute as seguintes ações: 
    1. Especifique um **nome** para o laboratório de sala de aula. 
    2. Selecione o **tamanho** da máquina virtual que você planeja usar na sala de aula.
    3. Selecione a **imagem** a ser usada para criar a máquina virtual.
    4. Especifique as **credenciais padrão** para fazer logon em máquinas virtuais no laboratório. 
    7. Clique em **Salvar**.

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)
1. Você verá o **painel** do laboratório. 
    
    ![Painel de laboratório de sala de aula](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Configurar a política de uso

1. Selecione **Política de uso**. 
2. Em **Política de uso**, configurações, insira o **número de usuários** permitidos para usar o laboratório.
3. Clique em **Salvar**. 

    ![Política de uso](../media/tutorial-setup-classroom-lab/usage-policy-settings.png)


## <a name="set-up-the-template"></a>Configurar o modelo 
Um modelo em um laboratório é uma imagem básica da máquina virtual a partir do qual todas as máquinas virtuais de todos os usuários são criadas. Configure a máquina virtual do modelo para que ela seja configurada exatamente com o que você deseja fornecer aos usuários de laboratório. Você pode fornecer um nome e uma descrição do modelo exibido para os usuários do laboratório. Publique o modelo para pública para disponibilizar as instâncias do modelo de VM para os usuários do laboratório. 

### <a name="set-title-and-description"></a>Definir título e descrição
1. Na seção **Modelo**, selecione **Editar** (ícone de lápis) para o modelo. 
2. Na janela **Exibição do usuário**, insira um **Título** para o modelo.
3. Insira a **Descrição** para o modelo.
4. Clique em **Salvar**.

    ![Descrição do laboratório de sala de aula](../media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="set-up-the-template-vm"></a>Configurar a VM de modelo
 Você se conecta à VM de modelo e instala todo o software necessário antes de disponibilizá-la para os alunos. 

1. Aguarde até que a máquina virtual do modelo esteja pronta. Quando estiver pronto, o botão **Iniciar** deverá ser habilitado. Para iniciar a máquina virtual, selecione **Iniciar**.

    ![Iniciar a VM de modelo](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Para se conectar à máquina virtual, selecione **Conectar**e siga as instruções. 

    ![Conectar à VM de modelo](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. Instale o software necessário para que os alunos façam o laboratório (por exemplo, Visual Studio, Azure Storage Explorer etc.). 
2. Desconecte (feche sua sessão de área de trabalho remota) da VM de modelo. 
3. **Pare** a VM de modelo selecionando **Parar**. 

    ![Parar a VM de modelo](../media/tutorial-setup-classroom-lab/stop-template-vm.png)

### <a name="publish-the-template"></a>Publicar o modelo 
Quando você publicar um modelo, o Azure Lab Services criará VMs no laboratório usando o modelo. O número de VMs criadas neste processo é o mesmo que o número máximo de usuários permitidos em um laboratório, que você pode definir na política de uso do laboratório. Todas as máquinas virtuais têm a mesma configuração que o modelo. 

1. Selecione **Publicar** na seção **Modelo**. 

    ![Publicar a VM de modelo](../media/tutorial-setup-classroom-lab/public-access.png)
1. Na janela **Publicar**, selecione a opção **Publicado**. 
2. Agora, selecione o botão **Publicar**. Esse processo pode levar alguns tempo, dependendo de quantas VMs estiverem sendo criadas, que é igual ao número de usuários permitidos no laboratório.
    
    > [!IMPORTANT]
    > Depois que um modelo está disponível publicamente, o acesso não pode ser alterado para privado. 
4. Alterne para a página **máquinas virtuais** e confirme que você vê cinco máquinas virtuais no estado **Não Atribuído**. Essas máquinas virtuais ainda não foram atribuídas aos alunos. 

    ![Máquinas virtuais](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Aguarde até que as máquinas virtuais sejam criadas. Elas devem estar no estado **Parado**. Você pode iniciar a VM de um aluno, conectar-se à VM, parar a VM e excluir a VM nesta página. Você pode iniciá-los nesta página ou permitir que os alunos iniciem as máquinas virtuais. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="send-registration-link-to-students"></a>Enviar link de registro para estudantes

1. Alterne para o modo de exibição **Painel**. 
2. Selecione o bloco **Registro de usuário**.

    ![Link de registro do aluno](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. Na caixa de diálogo **Registro de usuário**, selecione o botão **Copiar**. O link é copiado para a área de transferência. Cole-o em um editor de email e envie um email para o aluno. 

    ![Link de registro do aluno](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Na caixa de diálogo **Registro de usuário**, selecione **Fechar**. 


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um laboratório de sala de aula e configurou o laboratório. Para saber como um aluno pode acessar uma VM no laboratório usando o link de registro, vá para o seguinte tutorial:

> [!div class="nextstepaction"]
> [Conectar a uma VM no laboratório de sala de aula](tutorial-connect-virtual-machine-classroom-lab.md)

