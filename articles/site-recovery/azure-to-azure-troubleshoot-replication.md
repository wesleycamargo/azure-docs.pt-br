---
title: Solução de problemas do Azure Site Recovery para erros e assuntos de replicação do Azure para Azure | Microsoft Docs
description: Solucionando problemas de erros e problemas durante a replicação de máquinas virtuais do Azure para recuperação de desastres
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/30/2018
ms.author: asgang
ms.openlocfilehash: 0ac90d8ef29d4293a5eeb5f932687788320c218e
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615789"
---
# <a name="troubleshoot-azure-to-azure-vm-ongoing-replication-issues"></a>Solucionar problemas de replicação em andamento do VM do Azure para o Azure

Este artigo descreve os problemas comuns no Azure Site Recovery na replicação e na recuperação de máquinas virtuais do Azure de uma região para outra região e explica como solucioná-los. Para obter mais informações sobre configurações com suporte, consulte a [matriz de suporte para replicar máquinas virtuais do Azure](site-recovery-support-matrix-azure-to-azure.md).


## <a name="recovery-points-not-getting-generated"></a>Pontos de recuperação não sendo gerados

MENSAGEM DE ERRO: Nenhum ponto de recuperação consistente de falha disponível para a VM nos últimos 60 minutos.</br>
ID DO ERRO: 153007 </br>

O Azure Site Recovery replica consistentemente os dados da região de origem para a região de recuperação de desastre e cria um ponto consistente de falha a cada 5 minutos. Se o Site Recovery não conseguir criar pontos de recuperação por 60 minutos, ele alertará o usuário. Abaixo estão as causas que podem resultar neste erro:

**Causa 1:[ Alta taxa de alteração de dados na máquina virtual de origem](#high-data-change-rate-on-the-source-virtal-machine)**    
**Causa 2: [problema de conectividade de rede ](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Alta taxa de alteração de dados na máquina virtual de origem
O Azure Site Recovery aciona um evento se a taxa de alteração de dados na máquina virtual de origem for maior que os limites com suporte. Para verificar se o problema ocorre devido à alta rotatividade, vá para Itens replicados> VM> clique em "Eventos - após 72 horas".
Você deve ver o evento "Taxa de alteração de dados além dos limites suportados", conforme mostrado na captura de tela abaixo

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Se você clicar no evento, deverá ver as informações exatas do disco, conforme mostrado na captura de tela abaixo

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


#### <a name="azure-site-recovery-limits"></a>Limites da Azure Site Recovery
A tabela a seguir fornece os limites do Azure Site Recovery. Esses limites são baseados em nossos testes, mas eles não podem abranger todas as combinações possíveis de E/S de aplicativos. Os resultados reais podem variar dependendo da combinação de E/S do aplicativo. Também devemos observar que há dois limites a considerar, a rotatividade de dados por disco e a rotatividade de dados da máquina virtual.
Por exemplo, se olharmos para o disco Premium P20 na tabela abaixo, o Site Recovery pode manipular 5 MB/s de rotatividade por disco com um máximo de cinco desses discos por VM, devido ao limite de 25 MB/s de rotatividade total por VM.

**Destino de armazenamento de replicação** | **Tamanho de E/S de disco de origem médio** |**Variação nos dados média do disco de origem** | **Total de variação de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior |10 MB/s | 842 GB por disco

### <a name="solution"></a>Solução
Devemos reconhecer que o Azure Site Recovery possui limites de taxa de alteração de dados com base no tipo de disco. Para saber se esse problema é recorrente ou momentâneo, é importante encontrar o padrão de taxa de alteração de dados da máquina virtual afetada.
Para encontrar a taxa de alteração de dados da máquina virtual afetada. Acesse a métrica virtual de origem> métricas em Monitoramento e adicione as métricas conforme mostrado abaixo.

![high_data_change_rate](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Clique em "Adicionar métrica" e adicione "Bytes de gravação de disco do sistema operacional/s" e "Bytes de gravação de disco de dados/s".
2. Monitore o pico, conforme mostrado na captura de tela.
3. Ele mostrará a operação total de gravações que ocorre no disco do sistema operacional e em todos os discos de dados combinados. Agora, essas métricas podem não informar as informações por nível de disco, mas são um bom indicador do padrão total de perda de dados.

Em casos como o acima, se houver uma intermitência de dados ocasional e a taxa de alteração de dados for maior que 10 MBps (para Premium) e 2 MBps (para Standard) por algum tempo e for reduzida, a replicação será recuperada. No entanto, se a rotatividade estiver além do limite suportado na maioria das vezes, você deve considerar uma das opções abaixo, se possível:

**Opção 1:** Exclua o disco, que está causando alta taxa de alteração de dados: </br>
Você atualmente pode excluir o disco usando [Powershell da recuperação de Site](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine)

**Opção 2:** Altere a camada do disco de armazenamento de recuperação de desastre: </br>
Essa opção só é possível se a variação de dados do disco é menor que 10 MB/s. Digamos que uma VM com disco P10 esteja com uma perda de dados maior que 8 MB/s, mas menor que 10 MB/s. Se o cliente pode usar o disco P30 de armazenamento de destino durante a proteção, o problema pode ser resolvido.

### <a name="Network-connectivity-issue"></a>Problema de conectividade de rede

Para replicação de recuperação de Site para o trabalho, conectividade de saída para intervalos específicos de IP ou URLs é necessária da VM. Se a VM estiver atrás de um firewall ou usa regras de grupo de segurança de rede (NSG) para controlar a conectividade de saída, você poderá enfrentar um desses problemas.</br>
Consulte [conectividade de saída para URLs do Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-troubleshoot-errors?#outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072)
