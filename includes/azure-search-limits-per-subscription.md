---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: a466ea29fa31c4c628724f3d5138a1612ef7a0f4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553225"
---
Você pode criar vários serviços dentro de uma assinatura. Cada um deles pode ser provisionado em uma camada específica. Você está limitado somente pelo número de serviços permitidos em cada camada. Por exemplo, você pode criar até 12 serviços na camada Básico e outros 12 serviços na camada S1 dentro da mesma assinatura. Para obter mais informações sobre as camadas, consulte [escolher uma camada ou SKU para o Azure Search](../articles/search/search-sku-tier.md).

Os limites de serviço máximos podem ser aumentados mediante solicitação. Se você precisar de mais serviços na mesma assinatura, entre em contato com o suporte do Azure.

| Recurso            | Livre<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Quantidade máxima de serviços    |1     | 12    | 12  | 6  | 6  | 6     |
| Escala máxima em unidades de pesquisa (SU)<sup>2</sup> |N/D |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup>gratuito é baseado nos recursos compartilhados, não dedicados. Não há suporte para escalar verticalmente nos recursos compartilhados.

<sup>2</sup>unidades de pesquisa são unidades faturáveis, alocadas como uma *réplica* ou uma *partição*. Você precisa de ambos de recursos para as operações de armazenamento, indexação e consulta. Para saber mais sobre computação de SU, consulte [Escalar os níveis de recursos para cargas de trabalho de índice e consulta](../articles/search/search-capacity-planning.md). 