---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: adf99b941a775f105d8c65da3ac6c11dc7257120
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416250"
---
O Azure atualiza a plataforma periodicamente para aprimorar a confiabilidade, desempenho e segurança da infraestrutura de host para máquinas virtuais. Essas atualizações vão desde a aplicação de patch de componentes de software no ambiente de hospedagem, a atualização de componentes de rede até o encerramento de hardware. A maioria dessas atualizações não têm impacto nas máquinas virtuais hospedadas. No entanto, há casos em que as atualizações possuem um impacto e o Azure escolhe o método menos impacto de atualizações:

- Se uma atualização não reinicializável for possível, a VM estará em pausa, enquanto o host é atualizado ou quando estiver ativo migrados para um host já atualizado.

- Se a manutenção requer uma reinicialização, você receberá um aviso informando para quando a manutenção está planejada. O Azure fornecerá uma janela de tempo, onde você pode iniciar a manutenção, em um momento mais oportuno para você. Janela de tempo de manutenção de autoatendimento normalmente é 30 dias, a menos que ele seja urgente para realizar a manutenção. Azure também está investindo em tecnologias para reduzir os casos, quando as VMs precisam ser reinicializadas para manutenção planejada de plataforma. 

Esta página descreve como o Microsoft Azure executa os dois tipos de manutenção. Para obter mais informações sobre eventos não planejados (interrupções), consulte Gerenciar a disponibilidade das máquinas virtuais para [Windows](../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).

Você pode obter notificação na VM sobre manutenção futura usando os Eventos Agendados para [Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Para obter instruções sobre como gerenciar a manutenção planejada, consulte “Administrando notificações de manutenção planejada” para [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-not-requiring-a-reboot"></a>Manutenção não exigir uma reinicialização

Como mencionado acima, a maioria das atualizações de plataforma são feitas sem nenhum impacto para máquinas virtuais do cliente. Quando a atualização de impacto zero não é possível Azure escolhe o mecanismo de atualização que é o mínimo de impacto para máquinas virtuais do cliente. A maioria desses manutenção de impacto diferente de zero faz com que menos de 10 segundos pausar para a VM. Em alguns casos, mecanismos de manutenção de preservação de memória são usados, que pausa a VM por até 30 segundos e preserva a memória RAM. A VM é reiniciada e seu relógio é sincronizado automaticamente. Preservação da manutenção da memória funciona para mais de 90%, as VMs do Azure, exceto a série H, M, N e G. Cada vez mais, o Azure está usando tecnologias de migração ao vivo e melhorando o mecanismo de manutenção de preservação de memória para reduzir a duração da pausa.  

Essas operações de manutenção não reiniciáveis são o domínio de falha aplicado pelo domínio de falha e o andamento é interrompido quando algum sinal de aviso de integridade é recebido. 

Alguns aplicativos podem ser afetados por esses tipos de atualizações. No caso da VM estar sendo migrada para um host diferente, algumas cargas de trabalho confidenciais poderão notar uma ligeira degradação do desempenho em alguns minutos que levam a pausar a máquina virtual. Tais aplicativos podem se beneficiar do uso para os Eventos Agendados [Windows](../articles/virtual-machines/windows/scheduled-events.md) ou [Linux](../articles/virtual-machines/linux/scheduled-events.md) para se preparar para manutenção da VM e não têm impacto durante a manutenção do Azure. O Azure também está trabalhando em recursos de controle de manutenção para tais aplicativos extremamente confidenciais. 

### <a name="live-migration"></a>Migração ao vivo

Migração ao vivo é uma operação de não-rebootful que preserva a memória para a VM e resulta em limitada pausa ou congela, normalmente durando não mais de 5 segundos. Atualmente, toda a infraestrutura como uma máquina Virtual de serviço (IaaS), além da série H, M, N e G, são elegíveis para migração ao vivo. Isso equivale a mais de 90% das VMs de IaaS implantado para Azure frota. 

Migração ao vivo é iniciada pela malha do Azure nos seguintes cenários:
- Manutenção Planejada
- Falha de hardware
- Otimizações de alocação

Migração ao vivo é aproveitada em alguns cenários de manutenção planejada, e os eventos agendados podem ser usados para saber de antemão Live ao início de operações de migração.

Migração ao vivo também é usada para mover as máquinas virtuais do hardware com uma falha iminente previsto quando detectado por nossos algoritmos de aprendizado de máquina e otimizar as alocações de máquina Virtual. Para saber mais sobre nossa modelagem preditiva que detecta instâncias de hardware degradado, consulte nossa postagem de blog intitulada [resiliência de máquina Virtual do Azure melhorando com ML preditivo e migração ao vivo](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Os clientes sempre receberá um aviso de migração ao vivo em seu portal do Azure no Monitor / Logs de integridade do serviço, bem como por meio de eventos agendados se elas estão sendo usadas.

## <a name="maintenance-requiring-a-reboot"></a>Manutenção que exige uma reinicialização

Quando as VMs precisarem ser reinicializadas para manutenção planejada, você será notificado com antecedência. A manutenção planejada tem duas fases: uma janela de autoatendimento e uma janela de manutenção agendada.

A **janela de autoatendimento** permite que você inicie a manutenção em suas VMs. Durante esse tempo é normalmente de quatro semanas, você pode consultar cada VM para ver seu status e verificar o resultado da última solicitação de manutenção.

Quando você inicia a manutenção de autoatendimento, sua VM é reimplantada em um nó já atualizado. Como a VM é reiniciada, o disco temporário é perdido e os endereços IP dinâmicos associados ao adaptador de rede virtual são atualizados.

Se você iniciar a manutenção de autoatendimento e ocorrer um erro durante o processo, a operação é interrompida, a VM não é atualizada e você obterá a opção de tentar novamente a manutenção de autoatendimento. 

Quando a janela de autoatendimento tiver passado, a **janela de manutenção agendada** começa. Durante essa janela de tempo você ainda pode consultar a janela de manutenção, mas não iniciará a manutenção por conta própria.

Para obter informações sobre como gerenciar a manutenção que exige reinicialização, consulte “Notificações de manutenção planejada” para [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Considerações sobre disponibilidade durante a manutenção agendada 

Se você decidir aguardar até a janela de manutenção planejada, há algumas coisas a serem consideradas para manter a disponibilidade mais alta de suas VMs. 

#### <a name="paired-regions"></a>Regiões emparelhadas

Cada região do Azure é emparelhada com outra região na mesma área geográfica e formando juntas um par regional. Na fase de manutenção agendada, o Azure atualizará somente as VMs em uma única região de um par de regiões. Por exemplo, ao atualizar as Máquinas Virtuais no Centro-Norte dos EUA, o Azure não atualizará qualquer Máquina Virtual no Centro-Sul dos EUA ao mesmo tempo. No entanto, outras regiões, como o Norte da Europa, podem estar em manutenção simultaneamente com Leste dos EUA. Noções básicas sobre como funcionam os pares de região podem ajudá-lo a melhor distribuir suas VMs entre regiões. Para obter mais informações, consulte [pares de regiões do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidade e conjuntos de dimensionamento

Ao implantar uma carga de trabalho usando VMs do Azure, é possível criar as VMs em um conjunto de disponibilidade para fornecer alta disponibilidade para o aplicativo. Isso garante que, durante uma interrupção ou eventos de manutenção, pelo menos uma máquina virtual estará disponível.

Em um conjunto de disponibilidade, VMs individuais são distribuídas em até 20 domínios de atualização (UDs). Durante a manutenção planejada, somente um único domínio é atualizado em um período específico. A ordem dos domínios de atualização que são afetados não necessariamente acontece de forma sequencial. 

Os conjuntos de dimensionamento de máquinas virtuais são um recurso de computação do Azure que permite implantar e gerenciar um conjunto de VMs idênticas como um único recurso. O conjunto de dimensionamento é implantado automaticamente nos domínios de atualização, como VMs em um conjunto de disponibilidade. Assim como com conjuntos de disponibilidade, com conjuntos de dimensionamento somente um domínio de atualização é afetado em um determinado momento durante a manutenção agendada.

Para obter mais informações sobre como configurar as máquinas virtuais para alta disponibilidade, consulte Gerenciar a disponibilidade de suas máquinas virtuais do [Windows](../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).
