---
title: "Versão da API REST do Serviço Azure Search 2016-09-01-Preview | Microsoft Docs"
description: "A Versão da API REST do Serviço Azure Search 2016-09-01-Preview inclui recursos experimentais como pesquisas de sinônimos e do tipo moreLikeThis."
services: search
documentationcenter: na
author: mhko
manager: jlembicz
editor: 
ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 10/25/2017
ms.author: nateko
ms.openlocfilehash: 082c207f892fcc277d30d66c6165dd9920d3ab27
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2017
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>API REST do Serviço Azure Search: versão 2016-09-01-Preview
Este artigo é a documentação de referência para a `api-version=2016-09-01-Preview`. Essa versão prévia estende a versão atual disponibilizada para o público geral, [api-version=2016-09-01](https://msdn.microsoft.com/library/dn798935.aspx), fornecendo os seguintes recursos experimentais:

* [API de sinônimos](search-synonyms.md) para carregar mapas de sinônimos e pesquisas de expansão.
* [parâmetro de consulta `moreLikeThis`](search-more-like-this.md) para localizar documentos relevantes para um documento específico.

Certifique-se de direcionar a versão prévia da API `api-version=2016-09-01-Preview` para experimentar esses recursos experimentais. O exemplo a seguir ilustra como a versão prévia da API é especificada ao criar uma consulta "mais como esta".

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Os recursos de versão prévia estão disponíveis para teste e experimentação com a meta de coletar comentários e estão sujeitos a alteração. **É altamente recomendável não usar APIs de preview em aplicativos de produção.**

O serviço Azure Search está disponível em várias versões. Consulte [Controle de versão do serviço Search](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes.
