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
ms.openlocfilehash: ca9c4c959d21f26369600129f3897b7624dd84f2
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371166"
---
# <a name="azure-storage-scalability-and-performance-targets-for-storage-accounts"></a>Azure metas de desempenho e escalabilidade do armazenamento para contas de armazenamento

Este artigo fornece detalhes sobre as metas de desempenho e escalabilidade para contas de armazenamento do Azure. As metas de escalabilidade e desempenho listadas aqui são metas avançadas, mas podem ser alcançadas. Em todos os casos, a taxa de solicitação e a largura de banda obtidas por sua conta armazenamento dependem do tamanho dos objetos armazenados, dos padrões de acesso utilizados e do tipo de carga de trabalho executado por seu aplicativo.

Teste o serviço para determinar se o desempenho atende às suas necessidades. Se possível, evite picos repentinos na taxa de tráfego e certifique-se de que o tráfego esteja bem distribuído entre as partições.

Quando seu aplicativo atinge o limite de processamento de uma partição para sua carga de trabalho, o Armazenamento do Azure começa a retornar respostas com o código de erro 503 (Servidor Ocupado) ou 500 (Tempo Limite da Operação). Se 503 erros estiverem ocorrendo, considere modificar seu aplicativo para usar uma política de backoff exponencial para novas tentativas. A retirada exponencial permite que a carga na partição diminua e afasta os picos de tráfego nessa partição.

## <a name="standard-performance-storage-account-scale-limits"></a>Limites de escala de conta de armazenamento de desempenho padrão

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

### <a name="azure-file-sync-scale-targets"></a>Destinos de escala de Sincronização de Arquivos do Azure

A sincronização de Arquivos do Azure foi projetado com o objetivo de uso ilimitado, mas o uso ilimitado nem sempre é possível. A tabela a seguir indica os limites do teste da Microsoft e também indica quais destinos são limites rígidos:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Destinos de escala de Armazenamento de Fila do Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Destinos de escala de Armazenamento de Tabela do Azure

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Veja também

- [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/)
- [Assinatura do Azure e limites de serviços, cotas e restrições](../../azure-subscription-service-limits.md)
- [Replicação de armazenamento do Azure](../storage-redundancy.md)
- [Lista de verificação de desempenho e escalabilidade do Armazenamento do Microsoft Azure](../storage-performance-checklist.md)