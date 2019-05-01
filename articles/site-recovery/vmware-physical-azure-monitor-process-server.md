---
title: Monitore o servidor de processo do Azure Site Recovery
description: Este artigo descreve como monitorar o servidor de processo do Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/24/2019
ms.author: rayne
ms.openlocfilehash: 5fac369f15edb3ef0be31d3dc7d7434104c18dfe
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928161"
---
# <a name="monitor-the-process-server"></a>Monitorar o servidor de processo

Este artigo descreve como monitorar o [recuperação de Site](site-recovery-overview.md) servidor de processo.

- O servidor de processo é usado quando você configurar a recuperação de desastre de VMs do VMware locais e servidores físicos no Azure.
- Por padrão o servidor de processo é executado no servidor de configuração. Ele é instalado por padrão, quando você implanta o servidor de configuração.
- Opcionalmente, a escala e o identificador de um número maior de computadores replicados e maiores volumes de tráfego de replicação, você pode implantar servidores de processo adicional, escalabilidade horizontal.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre a função e a implantação de servidores de processo.

## <a name="monitoring-overview"></a>Visão geral de monitoramento

Como o servidor de processo tem muitas funções, especialmente em cache os dados replicados, compactação e transferência para o Azure, é importante monitorar a integridade do servidor de processo em uma base contínua.

Há várias situações que normalmente afetam o desempenho do servidor de processo. Problemas que afetam o desempenho terá um efeito cascata sobre a integridade da VM, eventualmente, enviar por push o servidor de processo e suas máquinas replicadas em um estado crítico. Situações incluem:

- Grande número de VMs usa um servidor de processo, se aproximando ou exceder as limitações recomendadas.
- Máquinas virtuais usando o servidor de processo tem uma alta taxa de variação.
- Taxa de transferência de rede entre VMs e o servidor de processo não é suficiente para carregar dados de replicação para o servidor de processo.
- Taxa de transferência de rede entre o servidor de processo e o Azure não é suficiente para carregar dados de replicação do servidor de processo para o Azure.

Todos esses problemas podem afetar o objetivo de ponto de recuperação (RPO) de VMs. 

**Por quê?** Como gerar um ponto de recuperação para uma VM requer todos os discos na VM para ter um ponto comum. Se um disco tem uma taxa de rotatividade alta, a replicação está lenta ou o servidor de processo não é ideal, ele afeta a eficiência com pontos de recuperação são criados.

## <a name="monitor-proactively"></a>Monitorar proativamente

Para evitar problemas com o servidor de processo, é importante:

- Entender os requisitos específicos para os servidores de processo usando [capacidade e a orientação de dimensionamento](site-recovery-plan-capacity-vmware.md#capacity-considerations)e certifique-se de servidores de processo são implantados e executar de acordo com as recomendações.
- Monitorar alertas e solucionar problemas quando eles ocorrem, para manter os servidores de processo em execução com eficiência.


## <a name="process-server-alerts"></a>Alertas do servidor de processo

O servidor de processo gera um número de alertas de integridade resumidos na tabela a seguir.

**Tipo de alerta** | **Detalhes**
--- | ---
![Healthy][green] | Servidor de processo está conectado e íntegro.
![Aviso][yellow] | Utilização de CPU > 80% nos últimos 15 minutos
![Aviso][yellow] | Uso de memória > 80% nos últimos 15 minutos
![Aviso][yellow] | Cache de espaço livre de pasta < 30% nos últimos 15 minutos
![Aviso][yellow] | Serviços de servidor de processo não estão em execução nos últimos 15 minutos
![Crítico][red] | Utilização de CPU > 95% nos últimos 15 minutos
![Crítico][red] | Uso de memória > 95% nos últimos 15 minutos
![Crítico][red] | Cache de espaço livre de pasta < 25% nos últimos 15 minutos
![Crítico][red] | Não há pulsação do servidor de processo por 15 minutos.

![Chave de tabela](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> O status de integridade geral do servidor de processo se baseia no pior alerta gerado.



## <a name="monitor-process-server-health"></a>Monitorar integridade de servidor de processo

Você pode monitorar o estado de integridade de seus servidores de processo da seguinte maneira: 

1. Para monitorar a integridade da replicação e o status de um computador replicado e de seu servidor de processo, no cofre > **itens replicados**, clique na máquina que você deseja monitorar.
2. Na **integridade da replicação**, você pode monitorar o status de integridade da VM. Clique no status para fazer drill down para obter detalhes do erro.

    ![Integridade do servidor de processo no painel da VM](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. Na **integridade do servidor de processo**, você pode monitorar o status do servidor de processo. Fazer drill down para obter detalhes.

    ![Detalhes do servidor de processo no painel da VM](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Integridade também pode ser monitorada usando a representação gráfica da página VM.
    - Um servidor de processo de escalonamento horizontal será realçado em laranja se houver avisos associados a ele e vermelho se ele tiver qualquer problema crítico. 
    - Se o servidor de processo está em execução na implantação padrão no servidor de configuração, em seguida, o servidor de configuração será realçado adequadamente.
    - Para fazer uma busca detalhada, clique no servidor de configuração ou no servidor de processo. Observe os problemas e as recomendações de correção.

Você também pode monitorar servidores no cofre em processo **infraestrutura do Site Recovery**. Na **gerenciar sua infraestrutura do Site Recovery**, clique em **servidores de configuração**. Selecione o servidor de configuração associado com o servidor de processo e drill para baixo em detalhes do servidor de processo.


## <a name="next-steps"></a>Próximas etapas

- Se houver problemas de servidores de processo, siga nosso [diretrizes de solução de problemas](vmware-physical-azure-troubleshoot-process-server.md)
- Se precisar de mais ajuda, poste sua pergunta no [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
