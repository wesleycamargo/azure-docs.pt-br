---
title: Filtragem de conteúdo ofensivo - API de tradução de texto
titlesuffix: Azure Cognitive Services
description: Use a API de tradução de texto de filtragem de conteúdo ofensivo.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: bd7a05f2f597d1882293387e5aac8e4d7367d051
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916624"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Adicionar conteúdo ofensivo filtragem com a API de tradução de texto

Normalmente o serviço de tradução retém a linguagem obscena presente no texto de origem da tradução. O grau de obscenidade e o contexto que torna as palavras impróprias diferem entre culturas. Como resultado, o grau de profanação na língua-alvo pode ser amplificado ou reduzido.

Se você quiser evitar ver palavrões na tradução, mesmo que a profanidade esteja presente no texto de origem, use a opção de filtragem de profanação disponível no método Translate (). Essa opção permite escolher se você deseja ver palavrões excluídos, marcados com as tags apropriadas ou não executar nenhuma ação.

O método Translate () usa o parâmetro “options”, que contém o novo elemento “ProfanityAction”. Os valores aceitos de ProfanityAction são "NoAction", "Marcada" e "Excluído".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valores aceitos de ProfanityAction e exemplos
|Valor de ProfanityAction | Ação | Exemplo: Origem – japonês | Exemplo: Destino – inglês|
| :---|:---|:---|:---|
| NoAction | Padrão. O mesmo que não configurar a opção. A linguagem obscena passa do texto de origem para o texto de destino. | 彼は変態です。 | Ele é um idiota. |
| Marked | Palavras impróprias cercadas por marcas XML \<profanação >... \</profanity >. | 彼は変態です。 | Ele é um \<profanity>jerk\</profanity >. |
| Deleted | Palavras impróprias são removidas da saída sem substituição. | 彼は。 | Ele é um. |

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Aplicar a filtragem por meio da chamada de API de tradução de profanação](reference/v3-0-translate.md)
