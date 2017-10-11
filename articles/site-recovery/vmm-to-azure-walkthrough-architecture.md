---
title: "Rever a arquitetura para replicação do Hyper-V (com o System Center VMM) no Azure com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo fornece uma visão geral dos componentes e da arquitetura usada ao replicar VMs Hyper-V locais em nuvens VMM para o Azure com o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.openlocfilehash: df4e227d02901153d3cfcfd4dfd4f11de180763a
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2017
---
# <a name="step-1-review-the-architecture"></a>Etapa 1: revisar a arquitetura


Este artigo descreve os componentes e processos usados ao replicar máquinas virtuais do Hyper-V locais em nuvens do System Center Virtual Machine Manager (VMM) para o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).

Poste comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="architectural-components"></a>Componentes de arquitetura

Há vários componentes envolvidos ao replicar máquinas virtuais do Hyper-V em nuvens do VMM para o Azure.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**As tabelas** | No Azure, você precisa de uma conta do Microsoft Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Os dados replicados são armazenados na conta de armazenamento, e VMs do Azure são criadas com os dados replicados quando ocorre failover do site local.<br/><br/> As VMs do Azure se conectam à rede virtual do Azure quando são criadas.
**Servidor VMM** | O servidor VMM tem uma ou mais nuvens que contém hosts Hyper-V. | No servidor VMM, instale o Provedor do Site Recovery para orquestrar a replicação com o Site Recovery e registre o servidor no cofre dos Serviços de Recuperação.
**Host Hyper-V** | Um ou mais hosts/clusters Hyper-V gerenciados pelo VMM. |  Você pode instalar o agente dos Serviços de Recuperação em cada membro do cluster ou host.
**VMs Hyper-V** | Uma ou mais VMs em execução em um servidor host do Hyper-V. | Nada precisa ser explicitamente instalado em VMs.
**Rede** |Redes lógicas e de VM configuradas no servidor VMM. Uma rede de VM deve ser vinculada a uma rede lógica associada à nuvem. | As redes VM são mapeadas para as redes virtuais do Azure, para que as VMs do Azure fiquem localizadas em uma rede quando são criadas após o failover.

Saiba mais sobre os pré-requisitos de implantação e os requisitos para cada um desses componentes na [matriz de suporte](site-recovery-support-matrix-to-azure.md).


**Figura 1: replicar VMs em hosts Hyper-V nas nuvens do VMM para o Azure**

![Componentes](./media/vmm-to-azure-walkthrough-architecture/arch-onprem-onprem-azure-vmm.png)


## <a name="replication-process"></a>Processo de replicação

**Figura 2: replicação processos de recuperação e replicação do Hyper-V para o Azure**

![fluxo de trabalho](./media/vmm-to-azure-walkthrough-architecture/arch-hyperv-azure-workflow.png)

### <a name="enable-protection"></a>Habilitar proteção

1. Depois de habilitar a proteção para uma máquina virtual do Hyper-V, no portal do Azure ou no local, a opção **Habilitar proteção** é iniciada.
2. O trabalho verifica se o computador está em conformidade com os pré-requisitos antes de invocar [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) para configurar a replicação com as configurações definidas por você.
3. O trabalho é iniciado com a replicação inicial, invocando o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), para inicializar uma replicação completa de VM e enviar os discos virtuais da VM no Azure.
4. Você pode monitorar o trabalho na guia **Trabalhos** .
        ![Lista de trabalhos](media/vmm-to-azure-walkthrough-architecture/image1.png) ![Habilitar busca detalhada da proteção](media/vmm-to-azure-walkthrough-architecture/image2.png)

### <a name="replicate-the-initial-data"></a>Replicar os dados iniciais

1. Um [Instantâneo da VM do Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) é tirado quando a replicação inicial é disparada.
2. Discos rígidos virtuais são replicadas individualmente até que eles sejam copiados para o Azure. Ele pode demorar um pouco, dependendo do tamanho da VM e largura de banda de rede. Para otimizar o uso da rede, veja [Como gerenciar o uso de largura de banda de rede da proteção local do Azure](https://support.microsoft.com/kb/3056159).
3. Se houver alterações no disco durante a replicação inicial, o Controlador de Replicação de Réplica do Hyper-V mostrará essas alterações em Logs de Replicação do Hyper-V (.hrl). Esses arquivos estão localizados na mesma pasta dos discos. Cada disco tem um arquivo .hrl associado que será enviado ao armazenamento secundário.
4. O instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial.
5. Quando a replicação inicial termina, o instantâneo de VM é excluído. As alterações de disco delta no log são sincronizadas e mescladas para o disco pai.


### <a name="finalize-protection"></a>Finalizar proteção

1. Após a conclusão da replicação inicial, o trabalho **Finalizar proteção na máquina virtual** definirá outras configurações de rede e pós-replicação para que a máquina virtual fique protegida.
    ![Finalizar trabalho de proteção](media/vmm-to-azure-walkthrough-architecture/image3.png)
2. Se você estiver replicando no Azure, talvez seja necessário ajustar as configurações para a máquina virtual de modo que ela fique pronta para o failover. Aqui você já pode executar um failover de teste para verificar se tudo está funcionando conforme o esperado.

### <a name="replicate-the-delta"></a>Replicar o delta

1. Após a replicação inicial, a sincronização delta se inicia, de acordo com as configurações de replicação.
2. O Controlador de Replicação de Réplica do Hyper-V rastreia as alterações em um disco rígido virtual como arquivos. hrl. Cada disco configurado para a replicação tem um arquivo .hrl associado. Esses logs são enviados para a conta de armazenamento do cliente após a conclusão da replicação inicial. Quando um log está em trânsito para o Azure, as alterações no disco primário são rastreadas em outro arquivo de log no mesmo diretório.
3. Durante a replicação inicial e delta, você pode monitorar a VM na exibição da VM. [Saiba mais](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="synchronize-replication"></a>Sincronizar a replicação

1. Se a replicação delta falhar, e uma replicação completa for dispendiosa em termos de largura de banda ou tempo, a VM fica marcada para ressincronização. Por exemplo, se os arquivos .hrl alcançarem 50% do tamanho do disco, a VM será marcada para ressincronização.
2.  A ressincronização minimiza a quantidade de dados enviados, calculando as somas de verificação das máquinas virtuais de origem e de destino e enviando apenas os dados de delta. A ressincronização usa um algoritmo de agrupamento de bloco fixo em que os arquivos de origem e destino são divididos em partes fixas. As somas de verificação para cada bloco são geradas e comparadas para determinar quais blocos da origem precisam ser aplicados ao destino.
3. Após a conclusão da ressincronização, a replicação delta normal deverá ser retomada. Por padrão a ressincronização está agendada para execução automática fora do expediente, mas você pode ressincronizar uma máquina virtual manualmente. Por exemplo, se ocorrer uma interrupção da rede ou outra interrupção qualquer, você poderá retomar a ressincronização. Para fazer isso, selecione a máquina virtual no portal > **Ressincronizar**.

    ![Ressincronização manual](media/vmm-to-azure-walkthrough-architecture/image4.png)


### <a name="retry-logic"></a>Lógica de repetição

Se um erro de replicação ocorrer, haverá uma repetição interna. Ela pode ser classificada em duas categorias:

**Categoria** | **Detalhes**
--- | ---
**Erros não recuperáveis** | Nenhuma nova tentativa é feita. O status da VM será **Crítico** e há necessidade de intervenção do administrador. Exemplos desses erros incluem: cadeia VHD quebrada; Estado inválido para a VM de réplica; Erros de autenticação de rede: erros de autorização; A VM não encontrou erros (para servidores do Hyper-V autônomo)
**Erros recuperáveis** | Novas tentativas ocorrem a cada intervalo da replicação usando de modo exponencial a retirada, o que aumenta o intervalo de repetição desde o início da primeira tentativa por 1, 2, 4, 8 e 10 minutos. Se o erro persistir, repita a operação a cada 30 minutos. Os exemplos incluem: erros da rede; erros de pouco espaço em disco; condições de memória insuficiente |



## <a name="failover-and-failback-process"></a>Processo de failover e failback

1. Você pode executar um [failover](site-recovery-failover.md) planejado ou não planejado de VMs Hyper-V locais para o Azure. Se você executar um failover planejado, as VMs de origem serão desligadas para evitar a perda de dados.
2. Você pode fazer o failover de um único computador ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar o failover de vários computadores.
4. Após executar o failover, você deve ser capaz de ver as VMs de réplica criadas no Azure. Você pode atribuir um endereço IP público à VM, se necessário.
5. Você confirma então o failover para começar a acessar a carga de trabalho por meio da VM do Azure de réplica.
6. Quando o site primário local estiver disponível novamente, você poderá executar o [failback](site-recovery-failback-from-azure-to-hyper-v.md). Você dispara um failover planejado do Azure para o site primário. Para um failover planejado, você pode selecionar failback para a mesma VM ou um local alternativo e sincronizar alterações entre o Azure e o local, para garantir que não haja perda de dados. Quando as VMs são criadas no local, você confirma o failover.




## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 2: examinar os pré-requisitos de implantação](vmm-to-azure-walkthrough-prerequisites.md)
