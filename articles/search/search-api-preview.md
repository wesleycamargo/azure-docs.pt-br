---
title: API REST de visualização de pesquisa de 2019-05-06-versão prévia do Azure - Azure Search
description: Azure Search Service REST versão da API de 2019-05-06-Preview inclui recursos experimentais como repositório de dados de conhecimento e as chaves de criptografia gerenciadas pelo cliente.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 91c58507d8758a65772110afba71354deecd3b12
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024279"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Versão da api REST do serviço de Azure Search 2019-05-06-Preview
Este artigo descreve a versão `api-version=2019-05-06-Preview` da API REST do serviço Azure Search, oferecendo recursos experimentais ainda não disponíveis.

> [!NOTE]
> Os recursos de versão prévia estão disponíveis para teste e experimentação com a meta de coletar comentários e estão sujeitos a alteração. É altamente recomendável não usar APIs de versão prévia em aplicativos de produção.


## <a name="new-in-2019-05-06-preview"></a>Novo no 2019-05-06-Preview

[**Armazenamento de dados de Conhecimento** ](knowledge-store-concept-intro.md) é um novo destino de um pipeline com base em IA enriquecimento. Além de um índice, você agora pode manter estruturas de dados preenchida criadas durante a indexação no armazenamento do Azure. Você controla as estruturas físicas de seus dados por meio de elementos em um conjunto de qualificações, incluindo como formatação dos dados, se os dados são armazenados no armazenamento de tabelas ou armazenamento de BLOBs, e se há vários modos de exibição.

[**As chaves de criptografia gerenciadas pelo cliente** ](search-security-manage-encryption-keys.md) para criptografia em repouso do lado do serviço também é um novo recurso de visualização. Além de interno criptografia em repouso gerenciado pela Microsoft, você pode aplicar uma camada adicional de onde você está a única proprietária das chaves de criptografia.

## <a name="other-preview-features"></a>Outros recursos de versão prévia

Os recursos anunciados em versões prévias anteriores ainda estão em versão prévia pública. Se estiver chamando uma API com uma versão prévia anterior, você poderá continuar usando essa versão ou mudar para a `2019-05-06-Preview` sem alterações no comportamento esperado.

+ [O parâmetro de consulta moreLikeThis](search-more-like-this.md) localiza documentos relevantes para um documento específico. Esse recurso é encontrado em versões prévias anteriores. 


## <a name="how-to-call-a-preview-api"></a>Como chamar uma API de versão prévia

As versões prévias mais antigas ainda funcionam, mas se tornam obsoletas ao longo do tempo. Se o código chama `api-version=2016-09-01-Preview` ou `api-version=2017-11-11-Preview`, as chamadas ainda são válidas. No entanto, apenas a versão prévia mais recente é atualizada com melhorias. 

A sintaxe de exemplo a seguir ilustra uma chamada para a versão de visualização da API.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

O serviço Azure Search está disponível em várias versões. Para obter mais informações, confira [Versões de API](search-api-versions.md).

## <a name="next-steps"></a>Próximas etapas

Examine a documentação de referência de API de REST do serviço Azure Search. Se você encontrar problemas, solicitar ajuda no [StackOverflow](https://stackoverflow.com/) ou [contate o suporte](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referência da API REST do serviço de pesquisa](https://docs.microsoft.com/rest/api/searchservice/)