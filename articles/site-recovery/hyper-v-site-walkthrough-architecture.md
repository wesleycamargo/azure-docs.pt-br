---
title: "Rever a arquitetura para replicação do Hyper-V (sem o System Center VMM) no Azure com o Azure Site Recovery | Microsoft Docs"
description: "Este artigo fornece uma visão geral dos componentes e da arquitetura usada ao replicar VMs Hyper-V locais (sem VMM) para o Azure com o serviço Azure Site Recovery."
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
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 044dfe686de36c56703d126db94d0b4382b85886
ms.contentlocale: pt-br
ms.lasthandoff: 06/23/2017

---


<a id="step-1-review-the-architecture-for-hyper-v-replication-to-azure" class="xliff"></a>

# Etapa 1: rever a arquitetura para replicação do Hyper-V no Azure


Este artigo descreve os componentes e processos envolvidos ao replicar máquinas virtuais do Hyper-V locais (que não são gerenciadas pelo System Center VMM) para o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).

Poste comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



<a id="architectural-components" class="xliff"></a>

## Componentes de arquitetura

Há vários componentes envolvidos ao replicar máquinas virtuais do Hyper-V para o Azure sem VMM.

**Componente** | **Localidade** | **Detalhes**
--- | --- | ---
**As tabelas** | No Azure, você precisa de uma conta do Microsoft Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Os dados replicados são armazenados na conta de armazenamento, e VMs do Azure são criadas com os dados replicados quando ocorre failover do site local.<br/><br/> As VMs do Azure se conectam à rede virtual do Azure quando são criadas.
**Hyper-V** | Hosts e clusters Hyper-V são reunidos em sites do Hyper-V. O Provedor do Azure Site Recovery e o agente dos Serviços de Recuperação são instalados em cada host Hyper-V. | O Provedor coordena a replicação com o Site Recovery pela internet. O agente dos Serviços de Recuperação trata da replicação de dados.<br/><br/> As comunicações do provedor e do agente são protegidas e criptografadas. Os dados replicados no armazenamento do Azure também são criptografados.
**VMs Hyper-V** | Você precisa de uma ou mais VMs em execução no servidor de host Hyper-V. | Nada precisa ser explicitamente instalado em VMs.

Saiba mais sobre os pré-requisitos de implantação e os requisitos para cada um desses componentes na [matriz de suporte](site-recovery-support-matrix-to-azure.md).

**Figura 1: Replicação de site do Hyper-V para o Azure**

![Componentes](./media/hyper-v-site-walkthrough-architecture/arch-onprem-azure-hypervsite.png)


<a id="replication-process" class="xliff"></a>

## Processo de replicação

**Figura 2: replicação processos de recuperação e replicação do Hyper-V para o Azure**

![fluxo de trabalho](./media/hyper-v-site-walkthrough-architecture/arch-hyperv-azure-workflow.png)

<a id="enable-protection" class="xliff"></a>

### Habilitar proteção

1. Depois de habilitar a proteção para uma máquina virtual do Hyper-V, no portal do Azure ou no local, a opção **Habilitar proteção** é iniciada.
2. O trabalho verifica se o computador está em conformidade com os pré-requisitos antes de invocar [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) para configurar a replicação com as configurações definidas por você.
3. O trabalho é iniciado com a replicação inicial, invocando o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), para inicializar uma replicação completa de VM e enviar os discos virtuais da VM no Azure.
4. Você pode monitorar o trabalho na guia **Trabalhos** .
 
<a id="replicate-the-initial-data" class="xliff"></a>

### Replicar os dados iniciais

1. Um [Instantâneo da VM do Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) é tirado quando a replicação inicial é disparada.
2. Discos rígidos virtuais são replicadas individualmente até que eles sejam copiados para o Azure. Ele pode demorar um pouco, dependendo do tamanho da VM e largura de banda de rede. Para otimizar o uso da rede, veja [Como gerenciar o uso de largura de banda de rede da proteção local do Azure](https://support.microsoft.com/kb/3056159).
3. Se houver alterações no disco durante a replicação inicial, o Controlador de Replicação de Réplica do Hyper-V mostrará essas alterações em Logs de Replicação do Hyper-V (.hrl). Esses arquivos estão localizados na mesma pasta dos discos. Cada disco tem um arquivo .hrl associado que será enviado ao armazenamento secundário.
4. O instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial.
5. Quando a replicação inicial termina, o instantâneo de VM é excluído. As alterações de disco delta no log são sincronizadas e mescladas para o disco pai.


<a id="finalize-protection" class="xliff"></a>

### Finalizar proteção

1. Após a conclusão da replicação inicial, o trabalho **Finalizar proteção na máquina virtual** definirá outras configurações de rede e pós-replicação para que a máquina virtual fique protegida.
2. Se você estiver replicando no Azure, talvez seja necessário ajustar as configurações para a máquina virtual de modo que ela fique pronta para o failover. Aqui você já pode executar um failover de teste para verificar se tudo está funcionando conforme o esperado.

<a id="replicate-the-delta" class="xliff"></a>

### Replicar o delta

1. Após a replicação inicial, a sincronização delta se inicia, de acordo com as configurações de replicação.
2. O Controlador de Replicação de Réplica do Hyper-V rastreia as alterações em um disco rígido virtual como arquivos. hrl. Cada disco configurado para a replicação tem um arquivo .hrl associado. Esses logs são enviados para a conta de armazenamento do cliente após a conclusão da replicação inicial. Quando um log está em trânsito para o Azure, as alterações no disco primário são rastreadas em outro arquivo de log no mesmo diretório.
3. Durante a replicação inicial e delta, você pode monitorar a VM na exibição da VM. [Saiba mais](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

<a id="synchronize-replication" class="xliff"></a>

### Sincronizar a replicação

1. Se a replicação delta falhar, e uma replicação completa for dispendiosa em termos de largura de banda ou tempo, a VM fica marcada para ressincronização. Por exemplo, se os arquivos .hrl alcançarem 50% do tamanho do disco, a VM será marcada para ressincronização.
2.  A ressincronização minimiza a quantidade de dados enviados, calculando as somas de verificação das máquinas virtuais de origem e de destino e enviando apenas os dados de delta. A ressincronização usa um algoritmo de agrupamento de bloco fixo em que os arquivos de origem e destino são divididos em partes fixas. As somas de verificação para cada bloco são geradas e comparadas para determinar quais blocos da origem precisam ser aplicados ao destino.
3. Após a conclusão da ressincronização, a replicação delta normal deverá ser retomada. Por padrão a ressincronização está agendada para execução automática fora do expediente, mas você pode ressincronizar uma máquina virtual manualmente. Por exemplo, se ocorrer uma interrupção da rede ou outra interrupção qualquer, você poderá retomar a ressincronização. Para fazer isso, selecione a máquina virtual no portal > **Ressincronizar**.

    ![Ressincronização manual](media/hyper-v-site-walkthrough-architecture/image4.png)


<a id="retry-logic" class="xliff"></a>

### Lógica de repetição

Se um erro de replicação ocorrer, haverá uma repetição interna. Ela pode ser classificada em duas categorias:

**Categoria** | **Detalhes**
--- | ---
**Erros não recuperáveis** | Nenhuma nova tentativa é feita. O status da VM será **Crítico** e há necessidade de intervenção do administrador. Exemplos desses erros incluem: cadeia VHD quebrada; Estado inválido para a VM de réplica; Erros de autenticação de rede: erros de autorização; A VM não encontrou erros (para servidores do Hyper-V autônomo)
**Erros recuperáveis** | Novas tentativas ocorrem a cada intervalo da replicação usando de modo exponencial a retirada, o que aumenta o intervalo de repetição desde o início da primeira tentativa por 1, 2, 4, 8 e 10 minutos. Se o erro persistir, repita a operação a cada 30 minutos. Os exemplos incluem: erros da rede; erros de pouco espaço em disco; condições de memória insuficiente |



<a id="failover-and-failback-process" class="xliff"></a>

## Processo de failover e failback

1. Você pode executar um [failover](site-recovery-failover.md) planejado ou não planejado de VMs Hyper-V locais para o Azure. Se você executar um failover planejado, as VMs de origem serão desligadas para evitar a perda de dados.
2. Você pode fazer o failover de um único computador ou criar [planos de recuperação](site-recovery-create-recovery-plans.md) para orquestrar o failover de vários computadores.
4. Após executar o failover, você deve ser capaz de ver as VMs de réplica criadas no Azure. Você pode atribuir um endereço IP público à VM, se necessário.
5. Você confirma então o failover para começar a acessar a carga de trabalho por meio da VM do Azure de réplica.
6. Quando o site primário local estiver disponível novamente, você poderá executar o [failback](site-recovery-failback-from-azure-to-hyper-v.md). Você dispara um failover planejado do Azure para o site primário. Para um failover planejado, você pode selecionar failback para a mesma VM ou um local alternativo e sincronizar alterações entre o Azure e o local, para garantir que não haja perda de dados. Quando as VMs são criadas no local, você confirma o failover.




<a id="next-steps" class="xliff"></a>

## Próximas etapas

Vá para a [Etapa 2: examinar os pré-requisitos de implantação](hyper-v-site-walkthrough-prerequisites.md)

