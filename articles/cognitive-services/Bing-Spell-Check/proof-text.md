---
title: O que é a API de Verificação Ortográfica do Bing?
titlesuffix: Azure Cognitive Services
description: A API de Verificação Ortográfica do Bing usa aprendizado de máquina e tradução automática estatística para fazer a verificação ortográfica contextual.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: overview
ms.date: 05/03/2018
ms.author: nolachar
ms.openlocfilehash: 48d9dee014d0759bd339c79811bb7b2fddecfe0b
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214929"
---
# <a name="what-is-bing-spell-check-api"></a>O que é a API de Verificação Ortográfica do Bing?

A API de Verificação Ortográfica do Bing permite fazer verificações contextuais de gramática e ortografia.

Qual é a diferença entre os verificadores ortográficos comuns e o verificador ortográfico do Bing de terceira geração? Os verificadores ortográficos atuais dependem da verificação de ortografia e gramática em conjuntos de regra com base em dicionários, que são atualizados e expandidos periodicamente. Em outras palavras, o verificador ortográfico depende da solidez do dicionário que dá suporte a ele e da equipe editorial que dá suporte ao dicionário. Você conhece esse tipo de verificador ortográfico do Microsoft Word e de outros processadores de texto.

Por outro lado, o Bing desenvolveu um verificador ortográfico baseado na Web que aproveita o aprendizado de máquina e a tradução automática estatística para treinar dinamicamente um algoritmo em constante evolução e altamente contextual. O verificador ortográfico baseia-se em um grande corpus de documentos e pesquisas da Web.

Esse verificador ortográfico pode lidar com qualquer cenário de processamento de texto:

- Reconhece gírias e linguagem informal
- Reconhece erros de nomes comuns no contexto
- Corrige problemas de divisão de palavras com uma única sinalização
- É capaz de corrigir palavras homófonas no contexto e outros erros difíceis de identificar
- Dá suporte a novas marcas, entretenimento digital e expressões comuns conforme vão surgindo
- Palavras que parecem iguais, mas diferem no significado e na ortografia, por exemplo, "acento" e "assento".

## <a name="spell-check-modes"></a>Modos de verificação ortográfica

A API dá suporte a dois modos de revisão de texto, `Proof` e `Spell`.  Experimente exemplos [aqui](https://azure.microsoft.com/services/cognitive-services/spell-check/).
### <a name="proof---for-documents-scenario"></a>Proof: para documentos
O modo padrão é `Proof`. O modo de verificação ortográfica `Proof` faz as verificações mais abrangentes, adicionando capitalização, pontuação básica e outros recursos para auxiliar na criação de documentos. mas está disponível somente nos mercados en-US (Inglês (Estados Unidos)), es-ES (Espanhol), pt-BR (Português (Brasil)) (Observação: somente na versão beta para espanhol e português). Em todos os outros mercados, defina o parâmetro de consulta de modo como Spell. 
<br /><br/>**Observação:** se o comprimento do texto de consulta exceder 4096, ele será truncado para 4096 caracteres, em seguida, será processada. 
### <a name="spell----for-web-searchesqueries-scenario"></a>Spell: para consultas de pesquisa/Web
`Spell` é mais agressiva para retornar melhores resultados de pesquisa. O modo `Spell` localiza a maioria dos erros de ortografia, mas não encontra alguns erros gramaticais que `Proof` encontra, por exemplo, uso de maiúsculas e palavras repetidas.
<br /></br>**Observação:** o tamanho máximo da consulta com suporte está mostrado abaixo. Se a consulta exceder o comprimento máximo, a consulta e seus resultados não serão alterados.
<ul><li>130 caracteres para o código de idioma de en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, ele, zh, ko. </li>
<li>65 caracteres para outros idiomas</li></ul>

## <a name="market-setting"></a>Configuração do mercado
O mercado precisa ser especificado no parâmetro de consulta na URL de solicitação; caso contrário, o verificador ortográfico usará o mercado padrão com base no endereço IP.


## <a name="post-vs-get"></a>POST X GET

A API dá suporte a HTTP POST ou HTTP GET. Qual você vai usar dependerá do tamanho do texto que deve ser revisado. Se as cadeias de caracteres tiverem sempre menos de 1.500 caracteres, o ideal será GET. Mas se você quiser dar suporte a cadeias de até 10 mil caracteres, use POST. A cadeia de caracteres de texto pode incluir qualquer caractere UTF-8 válido.

O exemplo a seguir mostra uma solicitação POST para verificar a ortografia e a gramática de uma cadeia de caracteres de texto. O exemplo inclui o parâmetro de consulta [mode](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#mode) para integridade de consulta (ele poderia ter sido deixado de fora já que o `mode` padrão é Proof). O parâmetro de consulta [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) contém a cadeia de caracteres a ser revisada.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

Se você usar HTTP GET, deverá incluir o parâmetro de consulta `text` na cadeia de caracteres de consulta da URL
  
O exemplo a seguir mostra a resposta à solicitação anterior. A resposta contém um objeto [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#spellcheck). 
  
```  
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
O campo [flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#flaggedtokens) lista os erros de ortografia e gramática que a API encontrou na cadeia de caracteres [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text). O campo `token` contém a palavra a ser substituída. Você usaria o deslocamento de base zero no campo `offset` para localizar o token na cadeia de caracteres `text`. Em seguida, você substituiria a palavra nesse local pela palavra no campo `suggestion`. 

Se o campo `type` for RepeatedToken, você ainda poderia substituir o token por `suggestion`, mas provavelmente também precisaria remover o espaço à direita.

## <a name="throttling-requests"></a>Solicitações de limitação

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Próximas etapas

Para se familiarizar rapidamente com sua primeira solicitação, confira [Fazendo sua primeira solicitação](quickstarts/csharp.md).

Familiarize-se com a [Referência de API de Verificação Ortográfica do Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference). A referência contém a lista de pontos de extremidade, cabeçalhos e parâmetros de consulta que você usaria para solicitar os resultados da pesquisa, além das definições dos objetos de resposta. 

Leia os [Requisitos de exibição e uso do Bing](./useanddisplayrequirements.md) para não violar nenhuma das regras de uso dos resultados da pesquisa.
