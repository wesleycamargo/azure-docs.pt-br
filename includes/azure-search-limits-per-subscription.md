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
ms.openlocfilehash: 8a4f794c8ef24a90498954629c131904621c5b43
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
ms.locfileid: "30844056"
---
Você pode criar vários serviços dentro de uma assinatura, cada um provisionado em uma camada específica, limitada somente pelo número de serviços permitidos em cada camada. Por exemplo, você pode criar até 12 serviços na camada Básico e outros 12 serviços na camada S1 dentro da mesma assinatura. Para obter mais informações sobre as camadas, confira [Escolher uma camada ou SKU para o Azure Search](../articles/search/search-sku-tier.md).

Os limites de serviço máximos podem ser aumentados mediante solicitação. Se você precisar de mais serviços na mesma assinatura, entre em contato com o Suporte do Azure.

| Recurso            | Gratuito&nbsp;<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| Quantidade máxima de serviços    |1     | 12    | 12  | 6  | 6  | 6     |
| Escala máxima em SU&nbsp;<sup>2</sup> |N/D |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> Gratuito é baseado em recursos compartilhados, não dedicados. Não há suporte para escalar verticalmente nos recursos compartilhados.

<sup>2</sup> As SU (unidades de pesquisa) são unidades faturáveis, alocadas como uma *réplica* ou como uma *partição*. Você precisa de ambos de recursos para as operações de armazenamento, indexação e consulta. Para saber mais sobre computação de SU, consulte [Escalar os níveis de recursos para cargas de trabalho de índice e consulta](../articles/search/search-capacity-planning.md). 