---
title: Criar um ambiente com um cluster do Service Fabric no Azure DevTest Labs | Microsoft Docs
description: Saiba como criar um ambiente com um cluster do Service Fabric independente e iniciar e parar o cluster usando agendamentos.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: EMaher
manager: femila
editor: spelluru
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: enewman
ms.openlocfilehash: 9848f197800c391285c4065685b910685f0ac64b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57319061"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Criar um ambiente com cluster independente do Service Fabric no Azure DevTest Labs
Este artigo fornece informações sobre como criar um ambiente com um cluster do Service Fabric independente no Azure DevTest Labs. 

## <a name="overview"></a>Visão geral
DevTest Labs pode criar ambientes de teste independente, conforme definido pelos modelos de gerenciamento de recursos do Azure. Esses ambientes contêm os dois recursos de IaaS, como máquinas virtuais e recursos de PaaS, como o Service Fabric. DevTest Labs permite que você gerencie máquinas virtuais em um ambiente, fornecendo os comandos para controlar as máquinas virtuais. Esses comandos permitem iniciar ou parar uma máquina virtual em um agendamento. Da mesma forma, DevTest Labs pode também ajudam a gerenciar clusters do Service Fabric em um ambiente. Você pode iniciar ou parar um cluster do Service Fabric em um ambiente manualmente ou por meio de uma agenda.

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric
Clusters do Service Fabric são criados com o uso de ambientes no DevTest Labs. Cada ambiente é definido por um modelo do Azure Resource Manager em um repositório Git. O [repositório público do Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) para o DevTest Labs contém o modelo do Resource Manager para criar um cluster do Service Fabric na [Cluster ServiceFabric](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) pasta. 

1. Primeiro, crie um laboratório no Azure DevTest Labs usando as instruções no artigo a seguir: [Criar um laboratório](devtest-lab-create-lab.md). Observe que o **ambientes públicos** opção é **em** por padrão. 
2. Confirme que o provedor do Service Fabric é registrado para sua assinatura seguindo estas etapas:
    1. Selecione **inscrições** no menu de navegação à esquerda e selecione seu **assinatura**
    2. Sobre o **assinatura** página, selecione **provedores de recursos** no **configurações** seção no menu à esquerda. 
    3. Se **Microsoft.ServiecFabric** não estiver registrado, selecione **registrar**. 
3. Na página **DevTest Lab** para seu laboratório, selecione **+Adicionar** na barra de ferramentas. 
    
    ![Botão Adicionar na barra de ferramentas](./media/create-environment-service-fabric-cluster/add-button.png)
3. Sobre o **escolher uma base** página, selecione **Cluster do Service Fabric laboratório** na lista. 

    ![Selecione o laboratório de Cluster do Service Fabric na lista](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Sobre o **definir as configurações** página, faça as seguintes etapas: 
    1. Especifique um **nome** para seu cluster **ambiente**. Esse é o nome do grupo de recursos no Azure em que o cluster do Service Fabric vai ser criado. 
    2. Selecione o **sistema operacional (SO)** para máquinas virtuais do cluster. O valor padrão é: **Windows**.
    3. Especifique um nome para o **administrador** para o cluster. 
    4. Especifique um **senha** para o administrador. 
    5. Para o **certificado**, insira as informações do certificado como uma cadeia de caracteres codificada em Base64. Para criar um certificado, execute as seguintes etapas:
        1. Baixe o **criar ClusterCertificate.ps1** do arquivo da [repositório Git](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). Como alternativa, clone o repositório em seu computador. 
        2. Inicie o **PowerShell**. 
        3. Execute o **ps1** do arquivo usando o comando `.\Create-ClusterCertificate.ps1`. Você verá um arquivo de texto aberto no bloco de notas com as informações que necessárias para preencher os campos relacionados ao certificado nesta página. . 
    6. Insira o **senha do certificado**.
    7. Especifique o **impressão digital** para seu certificado.
    8. Selecione **Add** sobre o **definir configurações** página. 

        ![Definir configurações de cluster](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Após a criação do cluster, você verá um grupo de recursos com o nome do ambiente que você forneceu na etapa anterior. Quando você expande, você pode ver o cluster do Service Fabric nela. Se o status do grupo de recursos está bloqueado em **Creating**, selecione **atualizar** na barra de ferramentas. O **cluster do Service Fabric** environment cria um cluster de 5 nós 1-nodetype no Linux ou Windows.

    No exemplo a seguir **mysfabricclusterrg** é o nome do grupo de recursos é criado especificamente para o cluster do Service Fabric. É importante observar que os ambientes de laboratório são independentes dentro do grupo de recursos no qual eles são criados. Isso significa que o modelo que define o ambiente, que só pode acessar recursos no grupo de recursos recém-criado ou [redes virtuais configuradas para serem usados pelo laboratório](devtest-lab-configure-vnet.md). Este exemplo acima cria todos os recursos necessários no mesmo grupo de recursos.

    ![Cluster criado](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Iniciar ou parar o cluster
Você pode iniciar ou parar o cluster tanto da página de dev/Test Lab em si ou na página de Cluster do Service Fabric fornecidos pelo DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Na página de dev/Test Lab
Você pode iniciar ou parar o cluster na página de dev/Test Lab para seu laboratório. 

1. Selecione **três pontos (...)**  para o cluster do Service Fabric, conforme mostrado na imagem a seguir: 

    ![Iniciar e interromper os comandos para o cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. Você pode ver dois comandos no menu de contexto para **inicie** e **interromper** o cluster. O comando start inicia todos os nós em um cluster. O comando de parada interrompe todos os nós em um cluster. Depois que um cluster é interrompido, o cluster do Service Fabric em si permanece no estado pronto, mas nenhum nó está disponíveis até que o comando start está atualizando for reemitido no cluster no laboratório.

    Há algumas considerações a serem observadas ao usar um cluster do Service Fabric em um ambiente de teste. Ele pode levar algum tempo para que o cluster do Service Fabric realimentar completamente depois que os nós foram reiniciados. Para reter os dados de desligamento para inicialização, os dados devem ser salvo em um disco gerenciado anexado à máquina virtual. Há implicações de desempenho ao usar um disco gerenciado anexado, portanto, é recomendável para ambientes de teste somente. Se o disco usado para armazenamento de dados não for feito, em seguida, dados serão perdidos, quando o comando Parar é emitido no cluster.

### <a name="from-the-service-fabric-cluster-page"></a>Na página de Cluster do Service Fabric 
Há outra maneira de iniciar ou parar o cluster. 

1. Selecione o cluster do Service Fabric na exibição de árvore na página de dev/Test Lab. 

    ![Selecione o cluster](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Sobre o **Cluster do Service Fabric** página para o cluster, você deve ver comandos na barra de ferramentas para iniciar ou parar o cluster. 

    ![Iniciar ou parar os comandos na página de Cluster do Service Fabric](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Configurar a inicialização automática e a agenda de desligamento automático
Clusters do Service Fabric também podem ser iniciados ou interrompidos em um agendamento. Essa experiência é semelhante à experiência para máquinas virtuais em um laboratório. Para economizar dinheiro, por padrão, todos os clusters criados automaticamente em um laboratório é fechado no momento definido pelo laboratório [política de desligamento](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown). Você pode substituir, especificando se o cluster deve ser desligado ou especificando a hora em que o cluster é desligado. 

![Agendamentos existentes para iniciar automaticamente e o desligamento automático](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Aceitar a agenda de início automático
Para aceitar o agendamento de inicialização, execute as seguintes etapas:

1. Selecione **Auto-start** no menu à esquerda
2. Selecione **na** para **permitem este cluster do service fabric seja agendada para início automático**. Esta página só será habilitada se o proprietário de laboratório permitiu que os usuários para iniciar automaticamente suas máquinas virtuais ou clusters do Service Fabric.
3. Selecione **Salvar** na barra de ferramentas. 

    ![Página de estrela automáticas](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Definir as configurações de desligamento automático 
Para alterar as configurações do desligamento, execute as seguintes etapas:

1. Selecione **desligamento automático** no menu à esquerda. 
2. Nessa página, você pode optar por não desligamento automático, selecionando **Off** para **habilitado**. 
3. Se você tiver selecionado **na** para **habilitado**, siga estas etapas:
    1. Especifique o **tempo** do desligamento.
    2. Especifique o **fuso horário** para o tempo. 
    3. Especifique se deseja que o DevTest Labs para enviar **notificações** antes do desligamento automático. 
    4. Se você selecionou **Yes** para a opção de notificação, especifique a **URL do Webhook** e/ou **endereço de email** para enviar notificações. 
    5. Selecione **Salvar** na barra de ferramentas.

        ![Desligar a página de automático](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Para exibir nós no cluster do Service Fabric
Página do cluster do Service Fabric, que você viu nas etapas anteriores é específica para a página do DevTest Labs. Ele não mostra os nós no cluster. Para obter mais informações sobre o cluster, siga estas etapas:

1. Sobre o **dev/Test Lab** página para seu laboratório, selecione o **grupo de recursos** na exibição de árvore no **minhas máquinas virtuais** seção.

    ![Escolha o grupo de recursos](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Sobre o **grupo de recursos** página, você verá todos os recursos no grupo de recursos em uma lista. Selecione suas **cluster do Service Fabric** na lista. 

    ![Selecione o cluster na lista](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Você vê o **Cluster do Service Fabric** página para seu cluster. Esta é a página que fornece o Service Fabric. Você ver todas as informações sobre os clusters como nós, tipos de nó, etc.

    ![Página inicial de Cluster do Service Fabric](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos para obter detalhes sobre os ambientes: 

- [Crie ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e usar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
