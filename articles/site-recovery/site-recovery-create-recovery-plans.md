---
title: "Criar e personalizar um plano de recuperação para failover e recuperação no Azure Site Recovery | Microsoft Docs"
description: "Saiba como criar e personalizar planos de recuperação no Azure Site Recovery. Este artigo descreve como fazer failover e recuperar VMs e servidores físicos."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/26/2018
ms.author: raynew
ms.openlocfilehash: 6ad82a8a2f8e16ab794ba90c109904bd45cb6b89
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-recovery-plan-by-using-site-recovery"></a>Criar um plano de recuperação usando o Site Recovery

Este artigo descreve como criar e personalizar um plano de recuperação no [Azure Site Recovery](site-recovery-overview.md).

Criar um plano de recuperação para fazer o seguinte:

* Defina grupos de computadores que fazem failover juntos e, em seguida, inicie-os juntos.
* Modelam dependências entre computadores agrupando-os em um grupo de planos de recuperação. Por exemplo, para fazer failover e exibir um aplicativo específico, inclua todas as VMs para esse aplicativo no mesmo grupo do plano de recuperação.
* Executar um failover. Você pode executar um failover de teste planejado ou não planejado em um plano de recuperação.

## <a name="why-use-a-recovery-plan"></a>Por que usar um plano de recuperação

Um plano de recuperação pode ajudá-lo a preparar-se para um processo de recuperação sistemática criando pequenas unidades independentes que podem ser gerenciadas. As unidades geralmente representam um aplicativo em seu ambiente. Um plano de recuperação pode ajudá-lo a definir a sequência em que as VMs iniciam. Além disso, é possível usar um plano de recuperação para automatizar tarefas comuns durante a recuperação.

> [!TIP]
> Uma maneira de verificar se está preparado para migração na nuvem ou recuperação de desastre é garantir que cada um de seus aplicativos faz parte de um plano de recuperação. Além disso, assegure-se de que cada plano de recuperação seja testado para recuperação no Azure. Com essa preparação, será possível migrar com confiança ou fazer failover do datacenter completo para o Microsoft Azure.
 
Um plano de recuperação tem três principais proposições de valor:

* Modelar um aplicativo para capturar dependências.
* Automatizar a maioria das tarefas de recuperação para reduzir o RTO.
* Testar o failover para estar pronto para um desastre.

### <a name="model-an-application-to-capture-dependencies"></a>Modelar um aplicativo para capturar as dependências

Um plano de recuperação é um grupo de máquinas virtuais que geralmente compõem um aplicativo e que fazem failover em conjunto. Usar constructo de plano de recuperação, você poderá aprimorar um grupo de planos de recuperação para capturar as propriedades específicas do aplicativo. 

Neste artigo, usamos um aplicativo típico de três camadas que pode ter um back-end SQL, middleware e um front-end da Web. Você pode personalizar o plano de recuperação para ajudar a garantir que as máquinas virtuais iniciem na ordem correta após um failover. O back-end SQL deverá iniciar primeiro, o middleware iniciar em seguida e o front-end da Web deverá ser o último a iniciar. Essa ordem garante que o aplicativo esteja funcionando no momento em que a última máquina virtual iniciar. 

Por exemplo, quando o middleware inicia, ele tenta conectar-se à camada SQL. O plano de recuperação garante que a camada SQL já esteja em execução. Fazer com que o servidor front-end inicie por último também ajuda a garantir que os usuários finais não conectem-se à URL do aplicativo antes que todos os componentes estejam em execução e o aplicativo pronto para aceitar solicitações. Para compilar essas dependências, personalize o plano de recuperação para adicionar grupos e, em seguida, selecione uma máquina virtual. Para mover uma máquina virtual entre grupos, altere o grupo da máquina virtual.

![Captura de tela de um plano de recuperação de exemplo no Site Recovery](./media/site-recovery-create-recovery-plans/rp.png)

Após concluir a personalização, será possível visualizar as etapas exatas da recuperação. Aqui está a ordem das etapas que são executadas durante o failover de um plano de recuperação:

1. Uma etapa de desligamento tenta desativar as máquinas virtuais locais. (Exceto em um failover de teste, durante o qual o site primário precisa continuar a execução.)
2. A tentativa de desligamento dispara o failover de todas as máquinas virtuais no plano de recuperação em paralelo. A etapa de failover prepara discos de máquina virtual usando dados replicados.
3. Os grupos de inicialização executam em sua ordem e iniciam as máquinas virtuais em cada grupo. Primeiro, o Grupo 1 executa, o Grupo 2 executa e, finalmente, o Grupo 3 executa. Se houver mais de uma máquina virtual em qualquer grupo (por exemplo, front-end da Web com balanceamento de carga), todas as máquinas virtuais iniciam em paralelo.

> [!TIP]
> O sequenciamento entre grupos garante que as dependências entre as várias camadas de aplicativos sejam respeitadas. O paralelismo, quando apropriado usar, melhora o RTO da recuperação do aplicativo.

   > [!NOTE]
   > As máquinas que fazem parte de um único grupo fazem failover em paralelo. As máquinas que fazem parte de diferentes grupos fazem failover na ordem dos grupos. As máquinas do Grupo 2 iniciam o failover somente depois que todas as máquinas do Grupo 1 fizerem failover e iniciarem.

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>Automatizar a maioria das tarefas de recuperação para reduzir o RTO

Recuperar aplicativos grandes pode ser uma tarefa complexa. Ter muitas etapas manuais para lembrar em tempos de caos é difícil e torna o processo propenso a erros. Além disso, pode ser alguém que não conhece as complexidades do aplicativo que realmente dispara o failover. 

É possível usar um plano de recuperação para automatizar as ações necessárias e que precisam ser tomadas em cada etapa. Você pode configurar as ações necessárias usando runbooks de Automação do Azure. Com runbooks, é possível automatizar tarefas comuns de recuperação, como as tarefas nos exemplos a seguir. Para tarefas que não podem ser automatizadas, você pode inserir ações manuais nos planos de recuperação.

* **Tarefas na máquina virtual do Azure pós-failover**: essas tarefas normalmente são necessárias para poder conectar à máquina virtual. Exemplos:
    * Crie um endereço IP público na máquina virtual pós-failover.
    * Atribua um grupo de segurança de rede à NIC na máquina virtual com failover.
    * Adicione um balanceador de carga a um conjunto de disponibilidade.
* **Tarefas dentro da máquina virtual pós-failover**: essas tarefas reconfiguram o aplicativo para que ele continue funcionando corretamente no novo ambiente. Exemplos:
    * Modifique a cadeia de conexão do banco de dados dentro da máquina virtual.
    * Altere as regras ou a configuração do servidor Web.

> [!TIP]
> Obtenha um failover em um clique e otimize o RTO, criando um plano de recuperação completo que automatiza tarefas pós-recuperação usando runbooks de Automação.

### <a name="test-failover-to-be-ready-for-a-disaster"></a>Teste o failover de modo a estar pronto para um desastre

É possível usar um plano de recuperação para disparar um failover ou um failover de teste. Sempre conclua um failover de teste no aplicativo antes de fazer um failover. Os failover de teste ajuda a verificar se o aplicativo aparece no site de recuperação. Caso tenha perdido algo na configuração, você poderá disparar facilmente a limpeza e, em seguida, refazer o failover da teste. Faça o failover de teste várias vezes até certificar-se de que o aplicativo recupera-se sem problemas.

![Captura de tela de um exemplo de plano de recuperação de teste no Site Recovery](./media/site-recovery-create-recovery-plans/rptest.png)

> [!TIP]
> Como cada aplicação é exclusivo, será necessário compilar planos de recuperação personalizados para cada aplicativo. 
>
> Além disso, na dinâmica atual, o ambiente focalizado em datacenter, os aplicativos e suas dependências alteram com frequência. Para garantir que o plano de recuperação seja atual, teste o failover nos aplicativos a cada trimestre.

## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

1. Selecione **Planos de Recuperação** > **Criar Plano de Recuperação**.
   Especifique um nome para o plano de recuperação, bem como uma origem e um destino. O local de origem deve ter máquinas virtuais habilitadas para failover e recuperação. Escolha uma origem e destino, com base nas máquinas virtuais que você deseja que façam parte do plano de recuperação. 

   |Cenário                   |Fonte               |Destino           |
   |---------------------------|---------------------|-----------------|
   |Azure para o Azure             |Região do Azure         |Região do Azure     |
   |VMware no Azure            |Servidor de configuração |As tabelas            |
   |VMM (Virtual Machine Manager) para Azure               |Nome de exibição do VMM    |As tabelas            |
   |Site do Hyper-V para Azure      |Nome do site Hyper-V    |As tabelas            |
   |Computadores físicos para o Azure |Servidor de configuração |As tabelas            |
   |VMM no VMM                 |Nome amigável de VMM    |Nome de exibição do VMM|

   > [!NOTE]
   > Um plano de recuperação pode conter máquinas virtuais que tenham a mesma origem e destino. As máquinas virtuais do System Center Virtual Machine Manager (VMM) e VMware não podem estar no mesmo plano de recuperação. No entanto, é possível adicionar máquinas virtuais VMware e máquinas físicas no mesmo plano de recuperação. Nesse caso, a origem para ambas as máquinas é um servidor de configuração.

2. Em **Selecionar máquinas virtuais**, selecione as máquinas virtuais (ou o grupo de replicação) que deseja adicionar ao grupo padrão (Grupo 1) no plano de recuperação. Você somente poderá selecionar máquinas virtuais protegidas na origem (como selecionado no plano de recuperação) e que estejam protegidas no destino (como selecionado no plano de recuperação).

## <a name="customize-and-extend-recovery-plans"></a>Personalizar e estender os planos de recuperação

Para personalizar e estender os planos de recuperação, acesse o painel de recurso do plano de recuperação do Site Recovery. Selecione a guia **Personalizar**. É possível personalizar e estender os planos de recuperação, usando as seguintes opções:

- **Adicionar novos grupos**: é possível adicionar até sete grupos do plano de recuperação adicionais ao grupo padrão. Em seguida, você poderá adicionar mais máquinas ou grupos de replicação a esses grupos do plano de recuperação. Os grupos são numerados na ordem em que você os adiciona. É possível incluir uma máquina virtual ou grupo de replicação em apenas um grupo do plano de recuperação.
- **Adicionar uma ação manual**: é possível adicionar ações manuais que executam ou depois de um grupo do plano de recuperação. Quando o plano de recuperação é executado, ele parar no ponto em que você inseriu a ação manual. Uma caixa de diálogo solicita que você especificar que a ação manual foi concluída.
- **Adicionar um script** : é possível adicionar scripts que executam antes ou depois de um grupo do plano de recuperação. Quando você adiciona um script, ele adiciona um novo conjunto de ações para o grupo. Por exemplo, um conjunto de pré-etapas para o Grupo 1 é criado com o nome *Grupo 1: pré-etapas*. Todas as pré-etapas são listadas dentro desse conjunto. Você poderá adicionar um script no site primário, somente se tiver um servidor VMM implantado. Para obter mais informações, consulte [Adicionar um script VMM a um plano de recuperação](site-recovery-how-to-add-vmmscript.md).
- **Adicionar runbooks do Azure**: você pode estender planos de recuperação usando runbooks do Azure. Por exemplo, você pode usar um runbook para automatizar tarefas ou para criar uma única etapa de recuperação. Para obter mais informações, consulte [Adicionar runbooks de Automação do Azure ao plano de recuperação](site-recovery-runbook-automation.md).


## <a name="add-a-script-runbook-or-manual-action-to-a-plan"></a>Adicionar um script, runbook ou uma ação manual a um plano

Após adicionar máquinas virtuais ou grupos de replicação a um grupo do plano de recuperação padrão, você poderá adicionar um script ou ação manual ao grupo do plano de recuperação.

1. Abra o plano de recuperação.
2. Na lista**Etapa**, selecione um item. Em seguida, selecione **Script** ou **Ação Manual**.
3. Especifique se deseja adicionar o script ou a ação antes ou depois do item selecionado. Para mover a posição do script para cima ou para baixo, selecione os botões **Mover para cima** ou **Mover para baixo**.
4. Se você adicionar um script do VMM, selecione **Failover no script do VMM**. Em **Caminho do Script**, insira o caminho relativo para o compartilhamento. Por exemplo, **\RPScripts\RPScript.PS1**.
5. Se você adicionar uma runbook de Automação, especifique a conta de Automação na qual o diretório está localizado. Em seguida, selecione o script de runbook do Azure.
6. Para garantir que o script funcione conforme o esperado, faça um failover do plano de recuperação.

As opções de runbook ou script estão disponíveis somente nos cenários a seguir e durante um failover ou failback. Uma ação manual está disponível tanto para failover quanto para failback.


|Cenário               |Failover |Failback |
|-----------------------|---------|---------|
|Azure para o Azure         |Runbook |Runbook  |
|VMware no Azure        |Runbook |ND       | 
|VMM para Azure           |Runbook |Script   |
|Site do Hyper-V para Azure  |Runbook |ND       |
|VMM no VMM             |Script   |Script   |


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [execução de failovers](site-recovery-failover.md).  
* Para ver o plano de recuperação em ação, assista este vídeo:
    
    > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]
