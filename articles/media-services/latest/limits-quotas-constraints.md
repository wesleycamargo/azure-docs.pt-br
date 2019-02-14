---
title: Cotas e limitações dos Serviços de Mídia do Azure v3 | Microsoft Docs
description: Este tópico descreve as cotas e as limitações nos Serviços de Mídia do Azure v3
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 1bada751ffac45b36faad6e978567233901adc7d
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700308"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Cotas e limitações dos Serviços de Mídia do Azure v3

Este artigo descreve cotas e limitações nos Serviços de Mídia do Azure v3.

| Recurso | Limite padrão | 
| --- | --- | 
| Ativos por conta de Serviços de Mídia do Azure | 1.000.000|
| Filtros de Manifesto Dinâmico|100|
| JobInputs por trabalho | 50  (fixo)|
| JobOutputs por Trabalho/TransformOutputs em uma Transformação | 20 (fixo) |
| Arquivos por JobInput|10 (fixo)|
| Tamanho do arquivo| Em alguns cenários, há um limite no tamanho máximo de arquivo com suporte para o processamento nos Serviços de Mídia. <sup>(1)</sup> |
| Trabalhos por conta dos Serviços de Mídia | 500.000 <sup>(2)</sup> (fixo)|
| Listar Transformações|Paginar a resposta, com 1000 Transformações por página|
| Listar Trabalhos|Paginar a resposta, com 500 Trabalhos por página|
| Eventos ao vivo por conta dos Serviços de Mídia |5|
| Contas de Serviços de Mídia em uma única assinatura | 25 (fixo) |
| Saídas ao vivo em estado de execução por LiveEvent |3|
| Contas de armazenamento | 100<sup>(4)</sup> (fixo) |
| Pontos de extremidade de streaming no estado “executando” por conta dos Serviços de Mídia|2|
| Políticas de Streaming | 100 <sup>(3)</sup> |
| Transformações por conta dos Serviços de Mídia | 100  (fixo)|
| Localizadores de streaming exclusivos associados a um Ativo ao mesmo tempo | 100<sup>(5)</sup> (fixado) |

<sup>1</sup> O tamanho máximo com suporte para um único blob atualmente é de até 5 TB no Azure Blob Storage. No entanto, os limites adicionais aplicam-se nos Serviços de Mídia do Azure com base nos tamanhos de VM usados pelo serviço. Se o arquivo de origem for maior que 260 GB, o trabalho provavelmente falhará. Se você tiver um conteúdo de 4K maior do que o limite de 260 GB, entre em contato conosco em amshelp@microsoft.com para se informar sobre atenuantes possíveis que darão suporte ao seu cenário.

<sup>2</sup> Esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. 

Qualquer registro de Trabalho em sua conta com mais de 90 dias será excluído automaticamente, mesmo que o número total de registros esteja abaixo da cota máxima. 

<sup>3</sup> Ao usar uma [Política de Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) personalizada, você deve criar um conjunto limitado de políticas para a conta de Serviço de Mídia e reutilizá-los para os Localizadores de Streaming sempre que as mesmas opções e protocolos de criptografia forem necessários. Você não deve criar uma nova Política de Streaming para cada Localizador de Streaming.

<sup>4</sup> As contas de armazenamento devem ser da mesma assinatura do Azure.

<sup>5</sup> Os Localizadores de Streaming não foram desenvolvidos para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM).

## <a name="support-ticket"></a>Tíquete de suporte

Para obter recursos que não são fixos, você pode solicitar que as cotas sejam geradas, abrindo um [tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Inclua informações detalhadas na solicitação sobre as alterações de cota desejadas, cenários de casos de uso e regiões necessárias. <br/>**Não** crie contas adicionais dos Serviços de Mídia do Azure em uma tentativa de obter limites mais altos.

## <a name="next-steps"></a>Próximas etapas

[Visão geral](media-services-overview.md)
