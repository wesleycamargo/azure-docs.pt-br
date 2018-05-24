---
title: Versão da API REST do Serviço Azure Search 2016-09-01-Preview | Microsoft Docs
description: A Versão da API REST do Serviço Azure Search 2016-09-01-Preview inclui recursos experimentais como pesquisas do tipo moreLikeThis.
author: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: reference
ms.date: 04/18/2018
ms.author: nateko
ms.openlocfilehash: 8eae54c912711a11c015737903b6898b98fd5159
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181616"
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>API REST do Serviço Azure Search: versão 2016-09-01-Preview
Este artigo é a documentação de referência para a `api-version=2016-09-01-Preview`. Essa versão prévia estende a versão atual disponibilizada para o público geral, [api-version=2016-09-01](https://docs.microsoft.com/rest/api/searchservice), fornecendo os seguintes recursos experimentais:

* [parâmetro de consulta `moreLikeThis`](search-more-like-this.md) para localizar documentos relevantes para um documento específico.

Certifique-se de direcionar a versão prévia da API `api-version=2016-09-01-Preview` para experimentar esses recursos experimentais. O exemplo a seguir ilustra como a versão prévia da API é especificada ao criar uma consulta "mais como esta".

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Os recursos de versão prévia estão disponíveis para teste e experimentação com a meta de coletar comentários e estão sujeitos a alteração. **É altamente recomendável não usar APIs de preview em aplicativos de produção.**

O serviço Azure Search está disponível em várias versões. Consulte [Controle de versão do serviço Search](https://docs.microsoft.com/azure/search/search-api-versions) para obter detalhes.
