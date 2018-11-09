---
title: Gerenciar um modelo de um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Aprenda a criar e gerenciar um modelo de laboratório de sala de aula nos Serviços de Laboratório do Azure.
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
ms.date: 10/25/2018
ms.author: spelluru
ms.openlocfilehash: 3ecbef3b3063ceb413b852f8000b44a85d28d08e
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142643"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Criar e gerenciar um modelo de sala de aula no Azure Lab Services
Um modelo em um laboratório é uma imagem básica da máquina virtual a partir do qual todas as máquinas virtuais de todos os usuários são criadas. Configure a máquina virtual do modelo para que ela seja configurada exatamente com o que você deseja fornecer aos usuários de laboratório. Você pode fornecer um nome e uma descrição do modelo exibido para os usuários do laboratório. Em seguida, publique o modelo para tornar as instâncias do modelo de VM disponíveis para os usuários do laboratório. Quando você publicar um modelo, o Azure Lab Services criará VMs no laboratório usando o modelo. O número de VMs criadas neste processo é o mesmo que o número máximo de usuários permitidos em um laboratório, que você pode definir na política de uso do laboratório. Todas as máquinas virtuais têm a mesma configuração que o modelo.

Este artigo descreve como criar e gerenciar uma máquina virtual de modelo em um laboratório de sala de aula dos Serviços de Laboratório do Azure. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Publicar um modelo ao criar um laboratório de sala de aula
Primeiro, você pode configurar e publicar um modelo ao criar um laboratório de sala de aula.

1. Navegue até [Site do Azure Lab Services](https://labs.azure.com). 
2. Selecione **Iniciar sessão** e insira suas credenciais. O Azure Lab Services oferece suporte a contas organizacionais e contas Microsoft. 
3. Na janela **Novo laboratório**, execute as seguintes ações: 
    1. Especifique um **nome** para o laboratório. 
    2. Especifique o **número de usuários** máximo permitido no laboratório. 
    6. Clique em **Salvar**.

        ![Criar um laboratório de sala de aula](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na página **Selecionar especificações da máquina virtual**, siga estas etapas:
    1. Selecione um **tamanho** para as VMs (máquinas virtuais) criadas no laboratório. 
    2. Selecione a **região** na qual você deseja criar as VMs. 
    3. Selecione a **imagem da VM** a ser usada para criar as VMs no laboratório. 
    4. Selecione **Avançar**.

        ![Definir as especificações da VM](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na página **Definir credenciais**, especifique as credenciais padrão para todas as VMs no laboratório. 
    1. Especifique o **nome do usuário** para todas as VMs no laboratório.
    2. Especifique a **senha** do usuário. 

        > [!IMPORTANT]
        > Anote o nome de usuário e a senha. Eles não serão mostrados novamente.
    3. Selecione **Criar**. 

        ![Definir as credenciais](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na página **Configurar modelo**, você vê o status do processo de criação do laboratório. A criação do modelo no laboratório leva até 20 minutos. 

    ![Configurar o modelo](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Após a configuração do modelo ser concluída, você verá a seguinte página: 

    ![Página de configuração do modelo após a conclusão](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. As seguintes etapas do tutorial são opcionais: 
    1. Inicie a VM modelo selecionando **Iniciar**.
    2. Conecte-se à VM modelo selecionando **Conectar**. 
    3. Instale e configure software em sua VM modelo. 
    4. **Pare** a VM.  
    5. Insira uma **descrição** do modelo

        !["Avançar" na página de configuração do modelo](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Selecione **Avançar** na página do modelo. 
10. Na página **Publicar o modelo**, execute as seguintes ações. 
    1. Para publicar o modelo imediatamente, marque a caixa de seleção *Eu entendo que não posso modificar o modelo depois da publicação. Esse processo só pode ser feito uma vez e pode levar até uma hora* e selecione **Publicar**.  

        > [!WARNING]
        > Depois de publicar, você não pode cancelar a publicação. 
    2. Para publicar mais tarde, selecione **Salvar para mais tarde**. É possível publicar a VM modelo após a conclusão do assistente. Para obter detalhes sobre como configurar e publicar após a conclusão do assistente, consulte a seção [Publicar o modelo](#publish-the-template) no artigo [Como gerenciar laboratórios de curso](how-to-manage-classroom-labs.md).

        ![Publicar modelo](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Você vê o **andamento da publicação** do modelo. Esse processo pode levar até uma hora. 

    ![Publicar modelo – andamento](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Você verá a página a seguir quando o modelo for publicado com êxito. Selecione **Concluído**.

    ![Publicar modelo – êxito](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Você verá o **painel** do laboratório. 
    
    ![Painel de laboratório de sala de aula](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="set-up-a-template-after-creating-a-lab"></a>Configurar um modelo depois de criar um laboratório 
Você também pode configurar um modelo depois de criar o laboratório.   

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


## <a name="publish-the-template"></a>Publicar o modelo 
Se você não publique o modelo ao criar o laboratório, você pode publicá-lo mais tarde. Antes de publicar, você talvez queira se conectar ao modelo de VM e atualizá-lo com qualquer software. Quando você publicar um modelo, o Azure Lab Services criará VMs no laboratório usando o modelo. O número de VMs criadas neste processo é o mesmo que o número máximo de usuários permitidos em um laboratório, que você pode definir na política de uso do laboratório. Todas as máquinas virtuais têm a mesma configuração que o modelo. 

1. Selecione **Publicar** na seção **Modelo**. 

    ![Publicar a VM modelo](../media/tutorial-setup-classroom-lab/public-access.png)
1. Sobre o **publicar o modelo** caixa de mensagem, examine a mensagem e selecione **publicar**. Esse processo pode levar alguns tempo, dependendo de quantas VMs estiverem sendo criadas, que é igual ao número de usuários permitidos no laboratório.
    
    > [!IMPORTANT]
    > Depois que um modelo é publicado, ele não pode ser cancelado. No entanto você poderá republicar o modelo. 
4. Alterne para a página **Máquinas virtuais** e confirme que você vê cinco máquinas virtuais no estado **Não Atribuído**. Essas máquinas virtuais ainda não foram atribuídas aos alunos. 

    ![Máquinas virtuais](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Aguarde até que as máquinas virtuais sejam criadas. Elas devem estar no estado **Parado**. Você pode iniciar a VM de um aluno, conectar-se à VM, parar a VM e excluir a VM nesta página. Você pode iniciá-los nesta página ou permitir que os alunos iniciem as máquinas virtuais. 

    ![Máquinas virtuais no estado parado](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="republish-the-template"></a>Republicar o modelo 
Depois de publicar um modelo, pode ainda se conectar ao modelo de VM, atualizá-lo e, em seguida, republicá-lo. Quando você republicar um modelo de VM, todas as VMs for desconectadas de usuário e eles são recriados com base no modelo atualizado. 

1. Na página do painel do seu laboratório, selecione **republicar** na seção de modelo. 

    ![Republish button](../media/how-to-create-manage-template/republish-button.png)
2. Na caixa de mensagem **Republicar o modelo**, revise o texto e selecione **Republicar** para continuar. Caso contrário, selecione **Cancelar**. 

    ![Republicar a mensagem do modelo](../media/how-to-create-manage-template/republish-template-message.png)
3. Você vê o status da republicação no bloco na seção **Modelo**.

    ![Status de republicação](../media/how-to-create-manage-template/republish-status-publishing.png)
4. Depois que o modelo for publicado, o status é definido como **publicado**. 

    ![Êxito ao republicar](../media/how-to-create-manage-template/republish-success.png)

## <a name="next-steps"></a>Próximas etapas
Introdução à configuração de um laboratório usando o Azure Lab Services:

- [Configurar um laboratório de sala de aula](how-to-manage-classroom-labs.md)
- [Configurar um laboratório](../tutorial-create-custom-lab.md)
