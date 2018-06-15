---
title: moreLikeThis no Azure Search (versão prévia) | Microsoft Docs
description: Documentação preliminar para o recurso moreLikeThis (versão prévia), exposto na API REST do Azure Search.
authors: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 10/27/2016
ms.author: nateko
ms.openlocfilehash: 29d9a478ca2e91e658d7d0f52e7a193ba694bc16
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790722"
---
# <a name="morelikethis-in-azure-search-preview"></a>moreLikeThis no Azure Search (versão prévia)

`moreLikeThis=[key]` é um parâmetro de consulta na [API do Search](https://docs.microsoft.com/rest/api/searchservice/search-documents). Ao especificar o parâmetro `moreLikeThis` em uma consulta de pesquisa, é possível encontrar documentos semelhantes ao especificado pela chave do documento. Quando uma solicitação de pesquisa é feita com `moreLikeThis`, uma consulta é gerada com termos de pesquisa extraídos do documento fornecido que descrevem melhor o documento. A consulta gerada é usada para fazer a solicitação de pesquisa. Por padrão, o conteúdo de todos os campos `searchable` é considerado, a menos que o parâmetro `searchFields` seja usado para restringir os campos. O parâmetro `moreLikeThis` não pode ser usado com o parâmetro de pesquisa, `search=[string]`.

## <a name="examples"></a>Exemplos 

Veja abaixo um exemplo de uma consulta moreLikeThis. A consulta localiza os documentos cujos campos de descrição são mais parecidos com o campo do documento de origem, conforme especificado pelo parâmetro `moreLikeThis`.

```  
Get /indexes/hotels/docs?moreLikeThis=1002&searchFields=description&api-version=2016-09-01-Preview
```  

```  
POST /indexes/hotels/docs/search?api-version=2016-09-01-Preview
    {  
      "moreLikeThis": "1002",  
      "searchFields": "description"  
    }  
```  

## <a name="feature-availability"></a>Disponibilidade de recursos

No momento, o recurso moreLikeThis está em versão prévia e é compatível apenas com as versões prévias de api, `2015-02-28-Preview` e `2016-09-01-Preview`. Como a versão da API é especificada na solicitação, é possível combinar APIs de versão prévia e disponibilidade geral (GA) no mesmo aplicativo. No entanto, as APIs de versão prévia não estão no SLA e os recursos podem mudar, portanto, não recomendamos usá-las em aplicativos de produção.