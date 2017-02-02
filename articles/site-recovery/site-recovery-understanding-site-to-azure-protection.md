---
title: "Replicação do Hyper-V com o Azure Site Recovery | Microsoft Docs"
description: "Use este artigo para entender os conceitos técnicos que ajudam você a instalar, configurar e gerenciar com êxito o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: mkjain
editor: 
ms.assetid: 97916915-1379-47df-8369-12ddf022c4da
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/19/2017
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: b49771ff1e29aeb6ec582c21061085504705991b


---
# <a name="hyper-v-replication-with-azure-site-recovery"></a>Replicação do Hyper-V com o Azure Site Recovery
Este artigo descreve os conceitos técnicos que ajudam você a configurar e gerenciar com êxito um site do Hyper-V ou um site do SCVMM (System Center Virtual Machine Manager) para a proteção do Azure usando o Azure Site Recovery.

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>Configurando o ambiente de origem para replicação entre o local e o Azure
Como parte da configuração de recuperação de desastre entre o local e o Azure, certifique-se de baixar e instalar o Provedor do Azure Site Recovery no servidor do VMM. Instale o Agente de Serviços de Recuperação do Azure em cada host Hyper-V.

![Implantação de site do VMM para replicação entre o local e o Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

Configuração do ambiente de origem em uma infraestrutura gerenciada do Hyper-V é semelhante à configuração do ambiente de origem em uma infraestrutura gerenciada do VMM. A única diferença é que o provedor e o agente estão instalados no host Hyper-V propriamente dito. No ambiente do VMM, o provedor é instalado no servidor VMM e o agente é instalado nos hosts Hyper-V (em caso de replicação para o Azure).

## <a name="workflows"></a>Fluxos de trabalho
### <a name="enable-protection"></a>Habilitar proteção
Depois que você proteger uma máquina virtual local ou do portal do Azure, um trabalho do Site Recovery chamado **Habilitar proteção** é iniciado. Você pode monitorá-lo na guia **Trabalhos** .

![Trabalho "Habilitar proteção" na lista de trabalhos](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

O trabalho **Habilitar proteção** verifica os pré-requisitos antes de invocar o método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) . Esse método cria a replicação no Azure usando entradas que são configuradas durante a proteção.

O trabalho **Habilitar proteção** inicia a replicação inicial localmente invocando o método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) . Esse método envia os discos virtuais da máquina virtual para o Azure.

![Detalhes do trabalho "Habilitar proteção"](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>Finalizar proteção na máquina virtual
Um [instantâneo da VM do Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) é tirado quando a replicação inicial é disparada. Os discos rígidos virtuais são processados individualmente, até que todos os discos sejam carregados no Azure. Esse processo normalmente demora um tempo para ser concluído, dependendo do tamanho do disco e da largura de banda. Para otimizar o uso da rede, veja [Como gerenciar o uso de largura de banda de rede da proteção local do Azure](https://support.microsoft.com/kb/3056159).

Após a conclusão da replicação inicial, o trabalho **Finalizar proteção na máquina virtual** define as configurações de rede e de pós-replicação. Enquanto a replicação inicial está em andamento:

* Todas as alterações feitas aos discos são rastreadas.
* O armazenamento em disco adicional é consumido pelos arquivos de instantâneos e de HRL (log de réplica do Hyper-V).

Após a conclusão da replicação inicial, o instantâneo de VM do Hyper-V é excluído. Essa exclusão resulta na mesclagem de alterações de dados após a replicação inicial para o disco pai.

![Trabalho "Finalizar proteção na máquina virtual" na lista de trabalhos](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>Replicação delta
O Rastreador de Replicação de Réplica do Hyper-V, que faz parte do Mecanismo de Replicação de Réplica do Hyper-V, rastreia as alterações para um disco rígido virtual como arquivos de log de réplica do Hyper-V (*.hrl). Os arquivos HRL estão localizados no mesmo diretório que os discos associados.

Cada disco configurado para a replicação tem um arquivo HRL associado. Esses logs são enviados para a conta de armazenamento do cliente após a conclusão da replicação inicial. Quando um log está em trânsito para o Azure, as alterações no arquivo principal são rastreadas em outro arquivo de log no mesmo diretório.

Durante a replicação inicial ou a replicação delta, a integridade da replicação de VM pode ser monitorada no modo de exibição de VM, conforme mencionado em [Monitorar a integridade da replicação de máquina virtual](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="resynchronization"></a>Ressincronização
Uma máquina virtual é marcada para ressincronização quando há uma falha na replicação delta e a replicação inicial completa consome muita largura de banda de rede e muito tempo. Por exemplo, quando o tamanho do arquivo HRL chega a 50% do tamanho total do disco, a máquina virtual é marcada para ressincronização. A ressincronização minimiza a quantidade de dados enviados pela rede, com o cálculo de somas de verificação dos discos da máquina virtual de origem e de destino e com o envio apenas do diferencial.

Após a conclusão da ressincronização, a replicação delta normal deverá ser retomada. Se ocorrer uma interrupção da rede ou outra interrupção qualquer, você poderá retomar a ressincronização.

Por padrão, a Ressincronização automaticamente agendada é configurada para ocorrer fora das horas de trabalho. Se a máquina virtual precisar ser ressincronizada manualmente, escolha a máquina virtual no portal e clique em **Ressincronizar**.

![Ressincronização manual](media/site-recovery-understanding-site-to-azure-protection/image04.png)

A ressincronização usa um algoritmo de agrupamento de bloco fixo em que os arquivos de origem e destino são divididos em partes fixas. As somas de verificação para cada bloco são geradas e comparadas para determinar quais blocos da origem precisam ser aplicados ao destino.

### <a name="retry-logic"></a>Lógica de repetição
Há uma lógica de repetição interna para erros de replicação. Ela pode ser classificada em duas categorias:

| Categoria | Cenários |
| --- | --- |
| Erro não recuperável |Nenhuma nova tentativa é feita. O status de replicação da máquina virtual é **Crítico**e há necessidade de intervenção do administrador. Alguns exemplos incluem:  <ul><li>Cadeia VHD quebrada</li><li>Estado inválido para a máquina virtual de réplica</li><li>Erro de autenticação de rede</li><li>Erro de autorização</li><li>Máquina virtual que não é encontrada, no caso de um servidor autônomo Hyper-V</li></ul> |
| Erro recuperável |Novas tentativas ocorrem a cada intervalo da replicação usando de modo exponencial a retirada, o que aumenta o intervalo de repetição desde o início da primeira tentativa (1, 2, 4, 8 e 10 minutos). Se o erro persistir, repita a operação a cada 30 minutos. Alguns exemplos incluem:  <ul><li>Erro de rede</li><li>Pouco espaço em disco</li><li>Condição de memória insuficiente</li></ul> |

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Ciclo de vida de recuperação e de proteção da máquina virtual do Hyper-V
![Ciclo de vida de recuperação e de proteção da máquina virtual do Hyper-V](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>Outras referências
* [Monitorar e solucionar problemas de proteção para VMware, VMM, Hyper-V e sites físicos](site-recovery-monitoring-and-troubleshooting.md)
* [Contatando o Suporte da Microsoft](site-recovery-monitoring-and-troubleshooting.md#reach-out-for-microsoft-support)
* [Erros comuns do Azure Site Recovery e suas resoluções](site-recovery-monitoring-and-troubleshooting.md#common-azure-site-recovery-errors-and-their-resolutions)



<!--HONumber=Nov16_HO3-->


