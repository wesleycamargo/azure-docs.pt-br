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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 48056d6e2988dd674351aca83526032175c355b6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214384"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gerenciar laboratórios de sala de aula no Azure Lab Services 
Este artigo descreve como criar e configurar um laboratório de sala de aula, exibir todos os laboratórios de sala de aula ou excluir um laboratório de sala de aula.

## <a name="prerequisites"></a>Pré-requisitos
Para configurar um laboratório de sala de aula em uma conta de laboratório, você deve ser um membro da função **Criador de Laboratório** na conta de laboratório. A conta que você usou para criar uma conta de laboratório é adicionada automaticamente a essa função. Um proprietário de laboratório pode adicionar outros usuários à função de Criador de Laboratório seguindo as etapas neste artigo: [Adicionar um usuário à função de Criador de Laboratório](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Criar um laboratório de sala de aula

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com).
2. Selecione **Iniciar sessão** e insira suas credenciais. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft.
3. Na janela **Novo laboratório**, execute as seguintes ações: 
    1. Especifique um **nome** para o laboratório de sala de aula. 
    2. Selecione o **tamanho** da máquina virtual que você planeja usar na sala de aula.
    3. Selecione a **imagem** a ser usada para criar a máquina virtual.
    4. Especifique as **credenciais padrão** para usar para fazer logon em máquinas virtuais no laboratório.
    7. Clique em **Salvar**.

        ![Criar um laboratório de sala de aula](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. Você verá o **painel** do laboratório. 
    
    ![Painel de laboratório de sala de aula](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Configurar a política de uso

1. Selecione **Política de uso**. 
2. Em **Política de uso**, configurações, insira o **número de usuários** permitidos para usar o laboratório.
3. Clique em **Salvar**. 

    ![Política de uso](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Configurar o modelo
Um modelo em um laboratório é uma imagem básica da máquina virtual a partir do qual todas as máquinas virtuais de todos os usuários são criadas. Configure a máquina virtual do modelo para que ela seja configurada exatamente com o que você deseja fornecer aos usuários de laboratório. Você pode fornecer um nome e uma descrição do modelo exibido para os usuários do laboratório. Publique o modelo para disponibilizar instâncias da VM modelo para os usuários do laboratório.  

### <a name="set-template-title-and-description"></a>Definir título e descrição do modelo
1. Na seção **Modelo**, selecione **Editar** (ícone de lápis) para o modelo. 
2. Na janela **Exibição do usuário**, insira um **Título** para o modelo.
3. Insira a **Descrição** para o modelo.
4. Clique em **Salvar**.

    ![Descrição do laboratório de sala de aula](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="set-up-the-template-vm"></a>Configurar a VM modelo
 Você se conecta à VM modelo e instala todo o software necessário antes de disponibilizá-la para os alunos. 

1. Aguarde até que a máquina virtual do modelo esteja pronta. Quando estiver pronto, o botão **Iniciar** deverá ser habilitado. Para iniciar a máquina virtual, selecione **Iniciar**.

    ![Iniciar a VM modelo](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Para se conectar à máquina virtual, selecione **Conectar** e siga as instruções. 

    ![Conectar à VM modelo](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. Instale o software necessário para que os alunos façam o laboratório (por exemplo, Visual Studio, Gerenciador de Armazenamento do Azure etc.). 
2. Desconecte (feche sua sessão de área de trabalho remota) da VM modelo. 
3. **Pare** a VM modelo selecionando **Parar**. 

    ![Parar a VM modelo](../media/tutorial-setup-classroom-lab/stop-template-vm.png)


### <a name="publish-the-template"></a>Publicar o modelo 
Quando você publicar um modelo, o Azure Lab Services criará VMs no laboratório usando o modelo. O número de VMs criadas neste processo é o mesmo que o número máximo de usuários permitidos em um laboratório, que você pode definir na política de uso do laboratório. Todas as máquinas virtuais têm a mesma configuração que o modelo. 

1. Selecione **Publicar** na seção **Modelo**. 

    ![Publicar a VM modelo](../media/tutorial-setup-classroom-lab/public-access.png)
1. Na janela **Publicar**, selecione a opção **Publicado**. 
2. Agora, selecione o botão **Publicar**. Esse processo pode levar alguns tempo, dependendo de quantas VMs estiverem sendo criadas, que é igual ao número de usuários permitidos no laboratório.
    
    > [!IMPORTANT]
    > Depois que um modelo é publicado, ele não pode ser cancelado. 
4. Alterne para a página **Máquinas virtuais** e confirme que você vê cinco máquinas virtuais no estado **Não Atribuído**. Essas máquinas virtuais ainda não foram atribuídas aos alunos. 

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

## <a name="view-all-classroom-labs"></a>Exibir todos os laboratórios de sala de aula
1. Navegue até [Portal do Azure Lab Services](https://labs.azure.com).
2. Confirme se você vê todos os laboratórios na conta de laboratório selecionada. 

    ![Todos os laboratórios](../media/how-to-manage-classroom-labs/all-labs.png)
3. Use a lista suspensa na parte superior para selecionar uma conta de laboratório diferente. Você verá laboratórios na conta de laboratório selecionada. 

## <a name="delete-a-classroom-lab"></a>Excluir um laboratório de sala de aula
1. No bloco para o laboratório, selecione três pontos (...) no canto. 

    ![Selecione o laboratório](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Selecione **Excluir**. 

    ![Botão Excluir](../media/how-to-manage-classroom-labs/delete-button.png)
3. Na caixa de diálogo **Excluir laboratório**, selecione **Excluir**. 

    ![Caixa de diálogo Excluir](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 
## <a name="manage-student-vms"></a>Gerenciar VMs de aluno
Após o registro dos alunos no Azure Lab Services usando o link de registro fornecido, você verá as VMs atribuídas aos alunos na guia **Máquinas virtuais**. 

![Máquinas virtuais atribuídas aos alunos](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

É possível executar as seguintes tarefas em uma VM de aluno: 

- Pare uma VM, se ela estiver em execução. 
- Inicie uma VM, se ela estiver parada. 
- Conecte-se à VM. 
- Exclua a VM. 

## <a name="next-steps"></a>Próximas etapas
Introdução à configuração de um laboratório usando o Azure Lab Services:

- [Configurar um laboratório de sala de aula](how-to-manage-classroom-labs.md)
- [Configurar um laboratório](../tutorial-create-custom-lab.md)
