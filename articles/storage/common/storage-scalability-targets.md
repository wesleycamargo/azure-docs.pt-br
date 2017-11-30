---
title: Metas de desempenho e escalabilidade do Armazenamento do Azure | Microsoft Docs
description: "Saiba mais sobre as metas de desempenho e escalabilidade para Armazenamento do Azure, incluindo a capacidade, taxa de solicitação e largura de banda de entrada e saída, tanto para contas de armazenamento standard quanto premium. Entender as metas de desempenho para partições em de cada um dos serviços do Armazenamento do Azure."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: be721bd3-159f-40a1-88c1-96418537fe75
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/24/2017
ms.author: tamram
ms.openlocfilehash: f62f2020d40e473886cb679cdfe1c164b95f7114
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="azure-storage-scalability-and-performance-targets"></a>Metas de desempenho e escalabilidade do Armazenamento do Azure
## <a name="overview"></a>Visão geral
Este artigo descreve os tópicos de desempenho e escalabilidade do Armazenamento do Azure. Para obter um resumo de outros limites do Azure, confira [Assinatura do Azure e limites de serviços, cotas e restrições](../../azure-subscription-service-limits.md).

> [!NOTE]
> Todas as contas de armazenamento são executadas na nova topologia de rede simples e dão suporte às metas de escalabilidade e desempenho descritas neste artigo, independentemente de quando foram criadas. Para obter mais informações sobre a arquitetura de rede simples do armazenamento do Azure e sobre escalabilidade, consulte [Armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).
> 

> [!IMPORTANT]
> As metas de escalabilidade e desempenho listadas aqui são metas avançadas, mas podem ser alcançadas. Em todos os casos, a taxa de solicitação e a largura de banda obtidas por sua conta armazenamento dependem do tamanho dos objetos armazenados, dos padrões de acesso utilizados e do tipo de carga de trabalho executado por seu aplicativo. Teste o serviço para determinar se o desempenho atende às suas necessidades. Se possível, evite picos repentinos na taxa de tráfego e certifique-se de que o tráfego esteja bem distribuído entre as partições.
> 
> Quando seu aplicativo atinge o limite de processamento de uma partição para sua carga de trabalho, o Armazenamento do Azure começa a retornar respostas com o código de erro 503 (Servidor Ocupado) ou 500 (Tempo Limite da Operação). Se esses erros ocorrerem, seu aplicativo deverá usar uma política de retirada exponencial para repetições. A retirada exponencial permite que a carga na partição diminua e afasta os picos de tráfego nessa partição.
> 
> 

Se as necessidades do seu aplicativo excederem as metas de escalabilidade de uma conta única de armazenamento, você pode preparar seu aplicativo para usar várias contas de armazenamento. Em seguida, particione seus objetos de dados nessas contas de armazenamento. Consulte [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter informações sobre preço por volume.

## <a name="scalability-targets-for-a-storage-account"></a>Metas de escalabilidade para uma conta de armazenamento
[!INCLUDE [azure-storage-limits](../../../includes/azure-storage-limits.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

## <a name="azure-blob-storage-scale-targets"></a>Destinos de escala de Armazenamento de Blobs do Azure
[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

## <a name="azure-files-scale-targets"></a>Destinos de escala de Arquivos do Azure
Para saber mais sobre as metas de escala e desempenho para Arquivos do Azure e da Sincronização de Arquivos do Azure, consulte [Metas de escala e de desempenho de Arquivos do Azure](../files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-scale-targets"></a>Destinos de escala de Sincronização de Arquivos do Azure
[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

## <a name="azure-queue-storage-scale-targets"></a>Destinos de escala de Armazenamento de Fila do Azure
[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

## <a name="azure-table-storage-scale-targets"></a>Destinos de escala de Armazenamento de Tabela do Azure
[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="see-also"></a>Consulte também
* [Detalhes de preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/)
* [Assinatura do Azure e limites de serviços, cotas e restrições](../../azure-subscription-service-limits.md)
* [Replicação de armazenamento do Azure](../storage-redundancy.md)
* [Lista de verificação de desempenho e escalabilidade do Armazenamento do Microsoft Azure](../storage-performance-checklist.md)
* [Armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

