---
title: Solução de problemas do Azure Site Recovery para problemas de replicação do Azure para Azure em andamento | Microsoft Docs
description: Solucionando erros e problemas durante a replicação de máquinas virtuais do Azure para recuperação de desastre
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9ff756270c368d39b7ef78d7c1046f7c91169668
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103739"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Solucionar problemas em andamento na replicação de VM do Azure para Azure

Este artigo descreve problemas comuns no Azure Site Recovery quando você está replicando e recuperando máquinas virtuais do Azure de uma região para outra. Ele também explica como solucioná-los. Para obter mais informações sobre configurações com suporte, consulte a [matriz de suporte para replicar máquinas virtuais do Azure](site-recovery-support-matrix-azure-to-azure.md).

O Azure Site Recovery replica consistentemente os dados da região de origem para a região de recuperação de desastre e cria um ponto de recuperação consistente de falha a cada 5 minutos. Se o Site Recovery não puder criar pontos de recuperação durante 60 minutos, ele alertará você com estas informações:

Mensagem de erro: “Nenhum ponto de recuperação com falha consistente disponível para a VM nos últimos 60 minutos.”</br>
ID do erro: 153007 </br>

As seções a seguir descrevem as causas e soluções.

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>Alta taxa de alteração de dados na máquina virtual de origem
O Azure Site Recovery aciona um evento se a taxa de alteração de dados na máquina virtual de origem for maior que os limites com suporte. Para verificar se o problema é devido à alta rotatividade, vá para **Itens replicados** > **VM** > **Eventos – Últimas 72 horas**.
Você deve ver o evento “Taxa de alteração de dados além dos limites com suporte”:

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

Se você selecionar o evento, deverá ver as informações exatas do disco:

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Limites da Azure Site Recovery
A tabela a seguir fornece os limites do Azure Site Recovery. Esses limites baseiam-se nos nossos testes, mas não podem abranger todas as combinações possíveis de E/S de aplicativos. Os resultados reais podem variar dependendo da combinação de E/S do aplicativo. 

Há dois limites a serem considerados, rotatividade dos dados por disco e rotatividade dos dados por máquina virtual. Por exemplo, vamos examinar o disco Premium P20 na tabela a seguir. O Site Recovery pode lidar com 5 MB/s de rotatividade por disco com, no máximo, cinco desses discos por VM, devido ao limite de 25 MB/s de rotatividade total por VM.

**Destino de armazenamento de replicação** | **Tamanho médio da E/S do disco de origem** |**Rotatividade média dos dados para o disco de origem** | **Rotatividade total dos dados por dia para o disco de dados de origem**
---|---|---|---
Armazenamento Standard | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 ou P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 ou P15 | 32 KB ou maior | 8 MB/s | 672 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20 ou P30 ou P40 ou P50 | 16 KB ou maior |10 MB/s | 842 GB por disco

### <a name="solution"></a>Solução
O Azure Site Recovery tem limites de taxa de alteração de dados com base no tipo de disco. Para saber se esse problema é recorrente ou momentâneo, localize a taxa de alteração de dados da máquina virtual afetada. Acesse a máquina virtual de origem, localize as métricas em **Monitoramento** e adicione-as conforme mostrado nesta captura de tela:

![Processo de três etapas para localizar a taxa de alteração de dados](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. Selecione **Adicionar métrica** e adicione **Bytes de gravação de disco do sistema operacional/s** e **Bytes de gravação de disco de dados/s**.
2. Monitore o pico, conforme mostrado na captura de tela.
3. Exiba as operações de gravação totais que estão acontecendo nos discos do sistema operacional e em todos os discos de dados combinados. Essas métricas talvez não forneçam informações por disco, mas elas indicam o padrão total da rotatividade de dados.

Se um pico for decorrente de uma intermitência de dados ocasional e a taxa de alteração de dados for superior a 10 MB/s (para Premium) e 2 MB/s (para Standard) por algum tempo e cair, a replicação será alcançada. No entanto, se a rotatividade estiver além do limite com suporte na maioria das vezes, considere uma das opções abaixo se possível:

* **Exclua o disco que está causando uma alta taxa de alteração de dados**: é possível excluir o disco usando o [PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine).
* **Altere a camada do disco de armazenamento da recuperação de desastre**: essa opção será possível apenas se a rotatividade do disco de dados for menor que 10 MB/s. Digamos que uma VM com um disco P10 está tendo uma rotatividade de dados superior a 8 MB/s, mas inferior a 10 MB/s. Se o cliente puder usar um disco P30 para o armazenamento de destino durante a proteção, o problema poderá ser resolvido.

## <a name="Network-connectivity-problem"></a>Problemas de conectividade de rede

### <a name="network-latency-to-a-cache-storage-account"></a>Latência de rede para uma conta de armazenamento em cache
O Site Recovery envia dados replicados à conta de armazenamento em cache. Talvez você veja a latência de rede se o carregamento dos dados de uma máquina virtual para a conta de armazenamento em cache for mais lento do que 4 MB em 3 segundos. 

Para verificar se há um problema relacionado à latência, use [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) para carregar os dados da máquina virtual para a conta de armazenamento em cache. Se a latência for alta, verifique se você está usando um NVA (dispositivo virtual de rede) para controlar o tráfego de rede de saída das VMs. O appliance pode ser acelerado se todo o tráfego de replicação passar pelo NVA. 

Recomendamos criar um ponto de extremidade de serviço de rede em sua rede virtual para "Armazenamento", para que o tráfego da replicação não acesse a NVA. Para saber mais, confira [Network virtual appliance configuration](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration) (Configuração da solução de virtualização de rede).

### <a name="network-connectivity"></a>Conectividade de rede
Para replicação de recuperação de Site para o trabalho, conectividade de saída para intervalos específicos de IP ou URLs é necessária da VM. Se a VM estiver atrás de um firewall ou usar regras NSG (grupo de segurança de rede) para controlar a conectividade de saída, você poderá enfrentar um desses problemas. Para garantir que todas as URLs estão conectadas, confira [Conectividade de saída para URLs do Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges). 