---
title: Failover em Site Recovery | Microsoft Docs
description: "O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre o failover no Azure ou em um datacenter secundário."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/15/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: b818d5083f1436035185b1b0d7990b5a36716da4
ms.openlocfilehash: 1fca09ad0c9e1bc72109910cd0dcaf186d6a7c3d
ms.lasthandoff: 02/23/2017


---
# <a name="failover-in-site-recovery"></a>Failover na Recuperação de Site
Este artigo descreve como executar o failover de máquinas virtuais e servidores físicos protegidos pelo Site Recovery. 

## <a name="prerequisites"></a>Pré-requisitos
1. Antes de realizar um failover, faça um [failover de teste](site-recovery-test-failover-to-azure.md) para verificar se tudo está funcionando conforme o esperado. 
1. [Prepare a rede](site-recovery-network-design.md) no local de destino antes de realizar um failover.  


## <a name="run-a-failover"></a>Executar um failover
Este procedimento descreve como executar um failover para um [plano de recuperação](site-recovery-create-recovery-plans.md). Como alternativa, você pode executar o failover de uma única máquina virtual ou servidor físico na página **Itens replicados**


![Failover](./media/site-recovery-failover/Failover.png)

1. Selecione **Planos de Recuperação** > *recoveryplan_name*. Clique em **Failover**. 
2. Na tela de **Failover**, selecione um **Ponto de Recuperação** para o qual fazer o failover. Você pode usar uma das seguintes opções:
    1.    **Mais recente** (padrão): Essa opção primeiro processa todos os dados que foram enviados ao serviço de Site Recovery para criar um ponto de recuperação para cada máquina virtual antes de fazer o failover para ele. Essa opção oferece o menor RPO (Objetivo de Ponto de Recuperação), pois a máquina virtual criada após o failover terá todos os dados que tiverem sido replicados para o serviço de Site Recovery quando o failover for disparado. 
    1.  **Mais recentes processados**: Essa opção faz failover em todas as máquinas virtuais do plano de recuperação para o último ponto de recuperação que já foi processado pelo serviço de Site Recovery. Quando você estiver realizando o failover de teste de uma máquina virtual, o carimbo de data/hora do último ponto de recuperação processado também é mostrado. Se estiver fazendo o failover de um plano de recuperação, você pode ir para a máquina virtual individual e visualizar o bloco **Últimos Pontos de Recuperação** para obter essas informações. Como nenhum tempo é gasto para processar os dados não processados, esta opção fornece uma opção de failover de baixo RTO (objetivo de tempo de recuperação). 
    1.    **Consistente de aplicativo mais recente**: Essa opção falhar em todas as máquinas virtuais do plano de recuperação para o último ponto de recuperação consistente de aplicativo que já foi processado pelo serviço de recuperação de site. Quando você estiver realizando failover de teste de uma máquina virtual, o carimbo de data/hora do ponto mais recente recuperação consistentes com o aplicativo também é mostrado. Se estiver fazendo o failover de um plano de recuperação, você pode ir para a máquina virtual individual e visualizar o bloco **Últimos Pontos de Recuperação** para obter essas informações. 
    1.    **Personalizado**: se você estiver realizando teste de failover de uma máquina virtual, você pode usar essa opção de failover para um determinado ponto de recuperação.

    > [!NOTE]
    > A opção de escolher um ponto de recuperação só está disponível ao fazer failover no Azure. 
    >
    > 


1. Se algumas das máquinas virtuais no plano de recuperação sofrerem failover em uma execução anterior e as máquinas virtuais estiverem ativas nos locais de origem e destino, você pode usar a opção **Alterar direção** para escolher a direção na qual o failover deve ocorrer.
1. Se estiver fazendo failover no Azure e a criptografia de dados estiver habilitada para a nuvem (só se aplica quando você tiver máquinas virtuais Hyper-v protegidas em um Servidor VMM), em **Chave de Criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a configuração no servidor VMM.
1. Selecione **Desligar computador antes de iniciar o failover** se quiser que o Site Recovery tente realizar um desligamento das máquinas virtuais de origem antes de disparar o failover. O failover continuará mesmo o desligamento falhar.  
          
    > [!NOTE]
    > No caso de máquinas virtuais Hyper-v, essa opção também tenta sincronizar os dados locais que ainda não foram enviados para o serviço antes de disparar o failover. 
    >
    > 
     
1. Você pode acompanhar o progresso do failover na página **Trabalhos** . Mesmo que ocorram erros durante um failover não planejado, o plano de recuperação será executado até que seja concluído.
1. Após o failover, valide a máquina virtual ao efetuar logon nela. Se desejar ir a outro ponto de recuperação para a máquina virtual, você pode usar a opção **Alterar ponto de recuperação**.
1. Quando estiver satisfeito com a máquina virtual que passou por failover, você pode **Confirmar** o failover. Isso exclui todos os pontos de recuperação disponíveis no serviço e a opção **Alterar ponto de recuperação** não estará mais disponível.

## <a name="planned-failover"></a>Failover planejado
Além do failover, as máquinas virtuais Hyper-V protegidas usando o Site Recovery também suportam **Failover planejado**. Essa é uma opção de failover com perda de dados zero. Quando um failover planejado é disparado, as máquinas virtuais de origem são desligadas, os dados com sincronização pendente são sincronizados e, então, um failover é disparado. 

> [!NOTE]
> Ao executar o failover de máquinas virtuais Hyper-v de um site local para outro site local, antes de voltar ao site local principal, você precisa fazer a **replicação inversa** da máquina virtual para o site principal e, depois, disparar um failover. Se a máquina virtual principal não estiver disponível, você vai precisar restaurar a máquina virtual de um backup antes de iniciar a **replicação inversa**.   
>
> 

## <a name="failover-job"></a>Trabalho de failover

![Failover](./media/site-recovery-failover/FailoverJob.png)

Quando é disparado, um failover envolve as seguintes etapas:

1. Verificação de pré-requisitos: Essa etapa garante que todas as condições necessárias para o failover sejam atendidas
1. Failover: Essa etapa processa os dados, deixando-os prontos para que uma máquina virtual do Azure seja criada a partir deles. Se você tiver escolhido o ponto de recuperação **Mais recente**, essa etapa cria um ponto de recuperação dos dados que foram enviados para o serviço.
1. Início: Essa etapa cria uma máquina virtual do Azure usando os dados processados na etapa anterior.

> [!WARNING]
> **Não cancele um failover em andamento**: Antes de iniciar o failover, a replicação para a máquina virtual é interrompida. Se você **Cancelar** um trabalho em andamento, o failover é interrompido, mas a máquina virtual não começa a replicação. A replicação não pode ser iniciada novamente. 
>
> 



## <a name="using-scripts-in-failover"></a>Utilizar scripts no Failover
Você talvez queira automatizar determinadas ações durante um failover. É possível usar scripts ou [runbooks de Automação do Azure](site-recovery-runbook-automation.md) nos [planos de recuperação](site-recovery-create-recovery-plans.md) para fazer isso.

## <a name="other-considerations"></a>Outras considerações
* **Letra da unidade** — Para manter a letra da unidade em máquinas virtuais depois do failover, você pode definir a **Política SAN** para a máquina virtual como **OnlineAll**. [Leia mais](https://support.microsoft.com/en-us/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).



## <a name="next-steps"></a>Próximas etapas
Após o failover das máquinas virtuais, quando o datacenter local estiver disponível, você deve [ **Proteger novamente** ](site-recovery-how-to-reprotect.md) as máquinas virtuais VMware no datacenter local.

Use a opção [ **Failover planejado** ](site-recovery-failback-from-azure-to-hyper-v.md) para fazer o **Failback** das máquinas virtuais Hyper-v para o local com o Azure.

Se fizer o failover de uma máquina virtual Hyper-v para outro datacenter local gerenciado por um servidor VMM e o datacenter principal estiver disponível, use a opção **Replicação inversa** para iniciar a replicação para o datacenter principal. 


