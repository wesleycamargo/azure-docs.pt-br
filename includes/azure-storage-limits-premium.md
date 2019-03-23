---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e3d2466f05028a963256dbcc052c46650857836d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372706"
---
### <a name="premium-performance-block-blob-storage"></a>Armazenamento de blob de bloco de desempenho Premium

Uma conta de armazenamento de blob de bloco do premium desempenho é otimizada para aplicativos que usam o intervalo de kilobyte, objetos menores. Ele é ideal para aplicativos que exigem muito altas taxas de transação ou armazenamento de baixa latência consistente. O armazenamento de blob de bloco de desempenho Premium foi projetado para dimensionar com seus aplicativos. Se você planeja implantar os aplicativos que exigem a centenas de milhares de solicitações por segundo ou petabytes de capacidade de armazenamento, entre em contato conosco enviando uma solicitação de suporte nas [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="premium-performance-page-blob-storage"></a>Armazenamento de blob de página de desempenho Premium

Desempenho Premium, o uso geral v1 ou v2 contas de armazenamento têm as seguintes metas de escalabilidade:

| Capacidade total da conta                            | Largura de banda total para uma conta de armazenamento com redundância local                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Capacidade do disco: 35 TB <br>Capacidade do instantâneo: 10 TB | Até 50 gigabits para entrada<sup>1</sup> + saída<sup>2</sup> |

<sup>1</sup> Todos os dados (solicitações) que são enviados para uma conta de armazenamento

<sup>2</sup> Todos os dados (respostas) que são recebidos de uma conta de armazenamento

Se você estiver usando contas de armazenamento de desempenho premium para discos não gerenciados e seu aplicativo ultrapassa as metas de escalabilidade de uma única conta de armazenamento, você talvez queira migrar para discos gerenciados. Se você não deseja migrar discos gerenciados, crie seu aplicativo para usar várias contas de armazenamento. Em seguida, particione os dados nessas contas de armazenamento. Por exemplo, se você quiser anexar discos de 51 TB em várias VMs, separe-as entre duas contas de armazenamento. O limite para uma conta de armazenamento único premium é de 35 TB. Certifique-se de que uma conta de armazenamento único premium desempenho nunca tem mais de 35 TB de discos provisionados.