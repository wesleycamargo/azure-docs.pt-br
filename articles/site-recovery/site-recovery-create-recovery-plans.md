---
title: "Criar e personalizar planos de recuperação para failover e recuperação no Azure Site Recovery | Microsoft Docs"
description: "Descreve como criar e personalizar planos de recuperação no Azure Site Recovery para failover e recuperação de VMs e servidores físicos"
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
ms.openlocfilehash: 9839a989246b28c1a194b8d1f0e99c1bd80ac2e5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="create-recovery-plans"></a>Criar planos de recuperação


Este artigo fornece informações sobre como criar e personalizar planos de recuperação em [Azure Site Recovery](site-recovery-overview.md).

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

 Criar planos de recuperação têm a seguinte função:

* Defina grupos de computadores que fazem failover juntos e então inicialize-os juntos.
* Modele as dependências entre os computadores juntando-os em um grupo de planos de recuperação. Por exemplo, para failover e exibir um aplicativo específico, você agrupar todas as VMs para o aplicativo no mesmo grupo de plano de recuperação.
* Executar um failover. Você pode executar um failover de teste planejado ou não planejado em um plano de recuperação.

## <a name="why-use-recovery-plans"></a>Por que usar os planos de recuperação?

Os planos de recuperação ajudarão você a se planejar para um processo de recuperação sistemático criando pequenas unidades independentes que você pode gerenciar. Essas unidades, normalmente, representam um aplicativo em seu ambiente. O plano de recuperação não só permite que você defina a sequência na qual as máquinas virtuais se inicializam, mas também ajuda a automatizar tarefas comuns durante a recuperação.


**Essencialmente, uma maneira de verificar que você está preparado para a migração para a nuvem ou recuperação de desastres é garantindo que todos os seus aplicativos sejam parte de um plano de recuperação e que cada um dos planos de recuperação sejam testados para recuperação do Microsoft Azure. Com esta preparação, você pode migrar com confiança ou fazer failover de sua central de dados completa para o Microsoft Azure.**
 
A seguir estão as três principais propostas de valor de um plano de recuperação:

### <a name="model-an-application-to-capture-dependencies"></a>Modelar um aplicativo para capturar as dependências

Um plano de recuperação é um grupo de máquinas virtuais que geralmente inclui um aplicativo que faz failover junto. Usar o plano de recuperação constrói, você pode aprimorar esse grupo para capturar as propriedades específicas do aplicativo.
 
Vamos usar o exemplo de um aplicativo típico de três camadas com

* Um back-end de SQL
* um middleware
* um front-end de Web

O plano de recuperação pode ser personalizado para garantir que as máquinas virtuais surjam na ordem certa após um failover. O back-end do SQL deve vir primeiro, o middleware deve ser exibido em seguida e o front-end da web deve vir por último. Essa ordem garante que o aplicativo esteja funcionando no momento em que a última máquina virtual é exibida. Por exemplo, quando o middleware aparece, ele tentará se conectar à camada SQL, e o plano de recuperação garantiu que a camada SQL já esteja em execução. Os servidores de front-end que virem por último também garantem que os usuários finais não se conectem ao URL do aplicativo por engano, até que todos os componentes estejam em execução e o aplicativo esteja pronto para aceitar solicitações. Para criar essas dependências, você pode personalizar o plano de recuperação para adicionar grupos. Em seguida, selecione uma máquina virtual e altere seu grupo para movê-la entre grupos.

![Amostragem do plano de recuperação](./media/site-recovery-create-recovery-plans/rp.png)

Depois de concluir a personalização, você pode visualizar as etapas exatas da recuperação. Aqui está a ordem das etapas executadas durante o failover de um plano de recuperação:

* Primeiro, há uma etapa de desligamento que tenta desligar as máquinas virtuais locais (exceto no failover de teste onde o site primário precisa continuar em execução)
* Em seguida, ele aciona o failover de todas as máquinas virtuais do plano de recuperação em paralelo. A etapa de failover prepara os discos de máquinas virtuais de dados replicados.
* Por fim, os grupos de inicialização executam em sua ordem, iniciando as máquinas virtuais em cada grupo - Grupo 1 primeiro, em seguida, o Grupo 2 e, finalmente, o Grupo 3. Se houver mais de uma máquina virtual em qualquer grupo (por exemplo, um front-end de Web com balanceamento de carga), todos eles são inicializados em paralelo.

**O sequenciamento entre grupos garante que as dependências entre várias camadas de aplicativo sejam respeitadas e o paralelismo, onde apropriado, melhore o RTO de recuperação de aplicativo.**

   > [!NOTE]
   > Os computadores que fazem parte de um único grupo farão o failover em paralelo. Os computadores que fizerem parte de grupos diferentes farão failover na ordem dos grupos. Somente após todas as máquinas do Grupo 1 terem falhado e inicializado, as máquinas do Grupo 2 iniciarão o failover.

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>Automatizar a maioria das tarefas de recuperação para reduzir o RTO

Recuperar aplicativos grandes pode ser uma tarefa complexa. Também é difícil lembrar as etapas de personalização exatas após o failover ou migração. Às vezes, não é você, mas alguém que não reconhece a complexidade do aplicativo, que deve disparar o failover. Lembrar de muitas etapas manuais em tempos de caos é difícil e o torna propenso a erros. Um plano de recuperação fornece uma maneira de automatizar as ações necessárias que você precisa tomar em todas as etapas, usando runbooks de Automação do Microsoft Azure. Com runbooks, você pode automatizar tarefas comuns de recuperação, como os exemplos fornecidos abaixo. Para as tarefas que não podem ser automatizadas, os planos de recuperação também fornecem a capacidade de inserir ações manuais.

* Tarefas na máquina virtual do Azure após o failover – normalmente, são necessárias para que você possa se conectar à máquina virtual, por exemplo:
    * Crie um IP público na máquina virtual após o failover
    * Atribua um NSG ao NIC da máquina virtual com failover
    * Adicione um balanceador de carga para um conjunto de disponibilidade
* Tarefas na máquina virtual após o failover – elas reconfiguram o aplicativo para que ele continue a funcionar corretamente no novo ambiente, por exemplo:
    * Modifique a cadeia de caracteres de conexão de banco de dados dentro da máquina virtual
    * Altere a configuração de servidor web/regras

**Com um plano de recuperação completo que automatiza as tarefas pós-recuperação usando runbooks de automação, você pode obter um failover de um clique e otimizar o RTO.**

### <a name="test-failover-to-be-ready-for-a-disaster"></a>Teste o failover de modo a estar pronto para um desastre

Um plano de recuperação pode ser usado para disparar um failover ou um failover de teste. Você deve sempre concluir um failover de teste no aplicativo antes de realizar um failover. O failover de teste ajuda a verificar se o aplicativo aparecerá no site de recuperação.  Se você tiver perdido algo, você pode disparar facilmente a limpeza e refazer o failover de teste. Realize o failover de teste várias vezes até que você saiba com certeza que o aplicativo se recuperou sem problemas.

![Testar plano de recuperação](./media/site-recovery-create-recovery-plans/rptest.png)

**Cada aplicativo é diferente e você precisa compilar planos de recuperação personalizados para cada um. Além disso, neste mundo de central de dados dinâmico, os aplicativos e suas dependências sempre mudam. Teste o failover de seus aplicativos uma vez por trimestre para verificar se o plano de recuperação está atualizado.**

## <a name="how-to-create-a-recovery-plan"></a>Como criar um plano de recuperação

1. Clique em **Planos de Recuperação** > **Criar Plano de Recuperação**.
   Especifique um nome para o plano de recuperação, bem como uma origem e um destino. O local de origem deve ter máquinas virtuais habilitadas para failover e recuperação. Escolha uma origem e destino, com base nas máquinas virtuais que você deseja que façam parte do plano de recuperação. 

   |Cenário                   |Fonte               |Destino           |
   |---------------------------|---------------------|-----------------|
   |Azure para o Azure             |Região do Azure         |Região do Azure     |
   |VMware no Azure            |Servidor de configuração |As tabelas            |
   |VMM para Azure               |Nome amigável de VMM    |As tabelas            |
   |Site do Hyper-V para Azure      |Nome do site Hyper-V    |As tabelas            |
   |Computadores físicos para o Azure |Servidor de configuração |As tabelas            |
   |VMM no VMM                 |Nome amigável de VMM    |Nome amigável de VMM|

   > [!NOTE]
   > Um plano de recuperação pode conter máquinas virtuais que tenham a mesma origem e destino. Máquinas virtuais do VMware e VMM não podem fazer parte do mesmo plano de recuperação. No entanto, máquinas virtuais VMware e máquinas físicas podem ser adicionadas ao mesmo plano, já que a origem para ambas é um servidor de configuração.

2. Em **Selecionar máquinas virtuais**, selecione as máquinas virtuais (ou o grupo de replicação) que deseja adicionar ao grupo padrão (Grupo 1) no plano de recuperação. Somente as máquinas virtuais protegidas na Origem (conforme selecionado no plano de recuperação) e protegidas para o Destino (conforme selecionado no plano de recuperação) serão permitidas para seleção.

## <a name="how-to-customize-and-extend-recovery-plans"></a>Como personalizar e estender os planos de recuperação

Você pode personalizar e estender os planos de recuperação acessando a folha de recursos de plano de recuperação do Site Recovery e clicando na guia Personalizar.

Você pode personalizar e estender os planos de recuperação:

- **Adicionar novos grupos**— adicionar grupos de plano de recuperação adicionais (até sete) ao grupo padrão e, em seguida, adicionar mais computadores ou grupos de replicação a esses grupos de plano de recuperação. Os grupos são numerados na ordem em que você os adiciona. Uma máquina virtual ou um grupo de replicação pode ser incluído apenas em um grupo de planos de recuperação.
- **Adicionar uma ação manual**: você pode adicionar ações manuais que são executadas antes ou depois de um grupo de planos de recuperação. Quando o plano de recuperação é executado, ele parar no ponto em que você inseriu a ação manual. Uma caixa de diálogo solicita que você especificar que a ação manual foi concluída.
- **Adicionar um script** — você pode adicionar scripts executados antes ou depois de um grupo de planos de recuperação. Quando você adiciona um script, ele adiciona um novo conjunto de ações para o grupo. Por exemplo, um conjunto de pré-etapas do Grupo 1 será criado com o nome: Grupo 1: pré-etapas. Todas as pré-etapas são listadas dentro desse conjunto. Só será possível adicionar um script no site primário se você tiver um servidor VMM implantado. [Saiba mais](site-recovery-how-to-add-vmmscript.md).
- **Adicionar runbooks do Azure**— você pode estender os planos de recuperação com runbooks do Azure. Por exemplo, para automatizar tarefas ou criar recuperação de etapa única. [Saiba mais](site-recovery-runbook-automation.md).


## <a name="how-to-add-a-script-runbook-or-manual-action-to-a-plan"></a>Como adicionar uma ação de script, runbook ou manual a um plano

Você pode adicionar uma ação de script ou manual no grupo padrão do plano de recuperação depois de ter adicionado VMs ou grupos de replicação a ele, e criado o plano.

1. Abra o plano de recuperação.
2. Clique em um item de **etapa** lista e, em seguida, clique em **Script** ou **ação Manual**.
3. Especifique se deseja adicionar o script ou a ação antes ou depois do item selecionado. Para mover a posição do script para cima ou para baixo, use os botões **Mover para cima** e **Mover para baixo**.
4. Se você adicionar um script do VMM, selecione **Failover no script do VMM**. Em **caminho do Script de**, digite o caminho relativo para o compartilhamento. No exemplo do VMM abaixo, você pode especificar o caminho: **\rpscripts\rpscript.ps1**.
5. Se você adicionar um runboook da automação do Azure, especifique a Conta de Automação do Azure na qual o runbook está localizado e selecione o script de runbook do Azure apropriado.
6. Para ter certeza que o script funciona conforme o esperado, faça um failover do plano de recuperação.

As opções de script ou runbook estão disponíveis apenas nos seguintes cenários ao fazer um failover ou o failback. Uma ação manual está disponível para failover e failback.


|Cenário               |Failover |Failback |
|-----------------------|---------|---------|
|Azure para o Azure         |Runbooks |Runbook  |
|VMware no Azure        |Runbooks |ND       | 
|VMM para Azure           |Runbooks |Script   |
|Site do Hyper-V para Azure  |Runbooks |ND       |
|VMM no VMM             |Script   |Script   |


## <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-failover.md) sobre a execução de failovers.

Assista a este vídeo para ver o plano de recuperação em ação.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]
