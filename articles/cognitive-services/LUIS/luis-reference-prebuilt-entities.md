---
title: Entidades predefinidas - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém listas das entidades predefinidas incluídas em LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: bc23f2e5d8304400802c74093d4d78a1fcd8cf22
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219471"
---
# <a name="entities-per-culture"></a>Entidades por cultura

O LUIS (Serviço Inteligente de Reconhecimento Vocal) fornece entidades predefinidas. Quando uma entidade predefinida é incluída em seu aplicativo, o LUIS inclui a previsão de entidade correspondente na resposta do ponto de extremidade. Todos os enunciados de exemplo também são rotulados com a entidade. O comportamento das entidades predefinidas **não pode** ser modificado. A menos que indicado de outra forma, entidades predefinidas estão disponíveis em todas as localidades (culturas) do aplicativo de LUIS. A tabela a seguir mostra as entidades predefinidas compatíveis com cada cultura.

Entidade predefinida   |   Inglês (Estados Unidos)<br>```En-us```   |   Francês (França)<br>```fr-FR```   |   Italiano (Itália)<br>```it-IT```   |   Espanhol (Espanha)<br>```es-ES```   |   Chinês<br>```zh-CN```   |   Alemão<br>```de-DE```   |   Português (Brasil)<br>```pt-BR```   |   Japonês (Japão)<br>```ja-JP```   |   Coreano (Coreia do Sul)<br>```ko-kr```   | Francês (Canadá)<br>```fr-CA```   |   Espanhol (México)<br>```es-MX```   |   Holandês (Países Baixos)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Idade](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>dia   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Moeda](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>data<br>daterange<br>tempo real<br>timerange   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Email](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   |   ✔   |   ✔   |   ✔   |   -   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |
[Número](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[Percentual](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   |    -   |    -   |    -   |    ✔   |    -   |    -   |    -   |   -   |   -   |   -   |   -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   ✔   |
[URL](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

Veja as observações sobre [Entidades predefinidas preteridas](luis-reference-prebuilt-deprecated.md)

A frase-chave não está disponível em todas as subculturas de Português (Brasil) - ```pt-BR```.

## <a name="contribute-to-prebuilt-entity-cultures"></a>Contribuir para culturas de entidade predefinida
As entidades predefinidas são desenvolvidas no projeto de software livre Recognizers-Text. [Contribua com](https://github.com/Microsoft/Recognizers-Text) para o projeto. Esse projeto inclui exemplos de moeda por cultura. 

GeographyV2 e PersonName não estão incluídos no projeto reconhecedores de texto. Para problemas com essas entidades predefinidas, abra uma [solicitação de suporte](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [número](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) e [moeda](luis-reference-prebuilt-currency.md). 
