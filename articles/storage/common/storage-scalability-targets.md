---
title: Destinos armazenamento do Azure escalabilidade e desempenho - contas de armazenamento
description: Saiba mais sobre as metas de escalabilidade e desempenho, incluindo a capacidade, taxa de solicitação e largura de banda de entrada e saída, para contas de armazenamento do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: e3e0e9ae4a1939aad9ab2ae42a1b51b1b00e2462
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521709"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Azure metas de desempenho e escalabilidade do armazenamento para contas de armazenamento

Este artigo fornece detalhes sobre as metas de desempenho e escalabilidade para contas de armazenamento do Azure. As metas de escalabilidade e desempenho listadas aqui são metas avançadas, mas podem ser alcançadas. Em todos os casos, a taxa de solicitação e a largura de banda obtidas por sua conta armazenamento dependem do tamanho dos objetos armazenados, dos padrões de acesso utilizados e do tipo de carga de trabalho executado por seu aplicativo.

Teste o serviço para determinar se o desempenho atende às suas necessidades. Se possível, evite picos repentinos na taxa de tráfego e certifique-se de que o tráfego esteja bem distribuído entre as partições.

Quando seu aplicativo atinge o limite de processamento de uma partição para sua carga de trabalho, o Armazenamento do Azure começa a retornar respostas com o código de erro 503 (Servidor Ocupado) ou 500 (Tempo Limite da Operação). Se 503 erros estiverem ocorrendo, considere modificar seu aplicativo para usar uma política de backoff exponencial para novas tentativas. A retirada exponencial permite que a carga na partição diminua e afasta os picos de tráfego nessa partição.

## <a name="storage-account-scale-limits"></a>Limites de escala de conta de armazenamento

[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

## <a name="premium-performance-storage-account-scale-limits"></a>Limites de escala de conta de armazenamento de desempenho Premium

[!INCLUDE [azure-premium-limits](../../../includes/azure-storage-limits-premium.md)]

## <a name="storage-resource-provider-scale-limits"></a>Limites de escala de provedor de recursos de armazenamento

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Destinos de escala de Armazenamento de Blobs do Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Destinos de escala de Arquivos do Azure

Para saber mais sobre as metas de escala e desempenho para Arquivos do Azure e da Sincronização de Arquivos do Azure, consulte [Metas de escala e de desempenho de Arquivos do Azure](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="premium-files-scale-targets"></a>Arquivos Premium dimensionar destinos

Há três categorias de limitações a serem consideradas para arquivos premium: contas de armazenamento, compartilhamentos e arquivos.

Por exemplo:  Um único compartilhamento pode alcançar a 100.000 IOPS e um único arquivo pode ser dimensionada até 5.000 IOPS. Assim, por exemplo, se você tiver três arquivos em um compartilhamento, o IOPs máximo que você pode obter a partir desse compartilhamento é 15.000.

#### <a name="premium-file-share-limits"></a>Limites de compartilhamento de arquivo do Premium

> [!IMPORTANT]
> Limites da conta de armazenamento se aplicam a todos os compartilhamentos. Expandindo para o máximo de contas de armazenamento só é possível se houver apenas um compartilhamento por conta de armazenamento.

|Área  |Destino  |
|---------|---------|
|Tamanho mínimo provisionado                        |100 GiB      |
|Max provisionado tamanho                        |100 TiB      |
|Aumentar/diminuir de tamanho mínimo    |1 GiB      |
|IOPS de linha de base    |1 IOPS por GiB até 100.000|
|IOPS de intermitência    |3 x IOPS por GiB até 100.000|
|Taxa de saída         |60 MiB/s + 0,06 * provisionado GiB        |
|Taxa de entrada| 40 MiB/s + 0,04 * provisionado GiB |
|Número máximo de instantâneos        |200       |

#### <a name="premium-file-limits"></a>Limites de arquivo do Premium

|Área  |Destino  |
|---------|---------|
|Tamanho                  |1 TiB         |
|IOPS máxima por arquivo     |5.000         |
|Identificadores simultâneos    |2.000         |

### <a name="azure-file-sync-scale-targets"></a>Destinos de escala de Sincronização de Arquivos do Azure

A sincronização de Arquivos do Azure foi projetado com o objetivo de uso ilimitado, mas o uso ilimitado nem sempre é possível. A tabela a seguir indica os limites do teste da Microsoft e também indica quais destinos são limites rígidos:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Destinos de escala de Armazenamento de Fila do Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Destinos de escala de Armazenamento de Tabela do Azure

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Consulte também

- [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/)
- [Assinatura do Azure e limites de serviços, cotas e restrições](../../azure-subscription-service-limits.md)
- [Replicação de armazenamento do Azure](../storage-redundancy.md)
- [Lista de verificação de desempenho e escalabilidade do Armazenamento do Microsoft Azure](../storage-performance-checklist.md)