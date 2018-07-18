---
title: Filtragem de linguagem obscena com a API de Tradução de Texto da Microsoft | Microsoft Docs
description: Use a filtragem de linguagem obscena na API de Tradução de Texto da Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a7172e1e8aa336c011fb06e93fc5c4b54d26a3cd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363754"
---
# <a name="how-to-add-profanity-filtering-with-the-microsoft-translator-text-api"></a>Como adicionar a filtragem de linguagem obscena com a API de Tradução de Texto da Microsoft

Normalmente o serviço de tradução retém a linguagem obscena presente no texto de origem da tradução. O grau de obscenidade e o contexto que torna as palavras impróprias diferem entre culturas. Como resultado o grau de obscenidades no idioma de destino pode ser ampliado ou reduzido.

Se você quiser evitar ver linguagem obscena na tradução (independentemente da presença de linguagem obscena no texto de origem), você pode usar a opção de filtragem de linguagem obscena no método Translate(). A opção permite que você escolha se deseja ver a linguagem obscena excluída ou marcada com marcas apropriadas ou se não deseja que nenhuma ação seja tomada.

O método Translate() aceita um parâmetro “options” que contém o novo elemento “ProfanityAction”. Os valores aceitos de ProfanityAction são “NoAction”, “Marked” e “Deleted”.

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valores aceitos de ProfanityAction e exemplos
|Valor de ProfanityAction | Ação | Exemplo: Origem - Japonês | Exemplo: Destino - Inglês|
| :---|:---|:---|:---|
| NoAction | Padrão. O mesmo que não configurar a opção. A linguagem obscena passa do texto de origem para o texto de destino. | 彼は変態です。 | Ele é um idiota. |
| Marked | Palavras impróprias estão entre as marcas XML \<profanity> ... \</profanity>. | 彼は変態です。 | Ele é um \<profanity>jerk\</profanity >. |
| Deleted | Palavras impróprias são removidas da saída sem substituição. | 彼は。 | Ele é um. |

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Aplicar filtragem de linguagem obscena com a chamada à API de Tradução](reference/v3-0-translate.md)

