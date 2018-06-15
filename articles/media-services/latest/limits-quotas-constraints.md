---
title: Cotas e limitações dos Serviços de Mídia do Azure v3 | Microsoft Docs
description: Este tópico descreve as cotas e as limitações nos Serviços de Mídia do Azure v3
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 21fc80d7cb274197ae75d2fd5524e76e1e6288d9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783085"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Cotas e limitações dos Serviços de Mídia do Azure v3

Este tópico descreve as cotas e as limitações nos Serviços de Mídia do Azure v3.

| Recurso | Limite padrão | 
| --- | --- | 
| Ativos por conta de Serviços de Mídia do Azure | 1.000.000|
| JobInputs por trabalho | 100 |
| JobOutputs por trabalho | 30 (fixo) |
| Tamanho do arquivo| Em alguns cenários, há um limite no tamanho máximo de arquivo com suporte para o processamento nos Serviços de Mídia. <sup>(1)</sup> |
| Trabalhos por conta dos Serviços de Mídia | 50,000<sup>(2)</sup> |
| LiveEvents por conta dos Serviços de Mídia |5|
| Contas de Serviços de Mídia em uma única assinatura | 25 (fixo) |
| StreamingPolicies | 1.000.000<sup>(3)</sup> |
| LiveOutputs em estado de execução por LiveEvent |3|
| LiveOutputs em estado de interrupção por LiveEvent |50|
| Contas de armazenamento | 1.000<sup>(4)</sup> (fixo) |
| Pontos de extremidade de streaming no estado “executando” por conta dos Serviços de Mídia|2|
| Transformações por conta dos Serviços de Mídia | 20 |
| StreamingLocators exclusivos associados a um Ativo simultaneamente | 20<sup>(5)</sup> |
  
<sup>1</sup>O tamanho máximo com suporte de um único blob atualmente é até 5 TB de Armazenamento de Blobs do Azure. No entanto, os limites adicionais aplicam-se nos Serviços de Mídia do Azure com base nos tamanhos de VM usados pelo serviço. Se o arquivo de origem for maior do que 260 GB seu trabalho poderá falhar. Se você tiver um conteúdo de 4K maior do que o limite de 260 GB, entre em contato conosco em amshelp@microsoft.com para se informar sobre atenuantes possíveis que darão suporte ao seu cenário.

<sup>2</sup> Esse número inclui trabalhos em fila, concluídos, ativos e cancelados. Ele não inclui trabalhos excluídos. 

Qualquer registro de Trabalho em sua conta com mais de 90 dias será excluído automaticamente, mesmo que o número total de registros esteja abaixo da cota máxima. 

<sup>3</sup> Há um limite de 1 milhão de entradas de StreamingPolicy para diferentes políticas dos Serviços de Mídia (por exemplo, para política de StreamingLocator ou ContentKeyAuthorizationPolicy). 

>[!NOTE]
> Você deverá usar a mesma ID de política se estiver sempre usando os mesmos dias/permissões de acesso/etc. 

<sup>4</sup> As contas de armazenamento devem ser da mesma assinatura do Azure.

<sup>5</sup> Os StreamingLocators não foram desenvolvidos para gerenciar o controle de acesso por usuário. Para conceder direitos de acesso diferentes para usuários individuais, use as soluções de gerenciamento de direitos digitais (DRM).

## <a name="support-ticket"></a>Tíquete de suporte

Para obter recursos que não são fixos, você pode solicitar que as cotas sejam geradas, abrindo um [tíquete de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Inclua informações detalhadas na solicitação sobre as alterações de cota desejadas, cenários de caso de uso e regiões necessárias. <br/>**Não** crie contas adicionais dos Serviços de Mídia do Azure em uma tentativa de obter limites mais altos.

## <a name="next-steps"></a>Próximas etapas

[Visão geral](media-services-overview.md)
