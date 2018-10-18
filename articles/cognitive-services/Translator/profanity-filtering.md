---
title: Filtragem de conteúdo ofensivo - API de tradução de texto
titlesuffix: Azure Cognitive Services
description: Use a API de tradução de texto de filtragem de conteúdo ofensivo.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 87814571e6f1c20b219020651eb798fa49a28deb
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127908"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Adicionar conteúdo ofensivo filtragem com a API de tradução de texto

Normalmente o serviço de tradução retém a linguagem obscena presente no texto de origem da tradução. O grau de obscenidade e o contexto que torna as palavras impróprias diferem entre culturas. Como resultado o grau de obscenidades no idioma de destino pode ser ampliado ou reduzido.

Se você quiser evitar ver profanação na tradução (mesmo que profanidade esteja presente no texto de origem), use a opção de filtragem de profanação no método Translate (). Essa opção permite que você escolha se deseja ver palavrões excluídos ou marcados com as tags apropriadas ou nenhuma ação executada.

O método Translate() aceita um parâmetro “options” que contém o novo elemento “ProfanityAction”. Os valores aceitos de ProfanityAction são "NoAction", "Marcada" e "Excluído".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Valores aceitos de ProfanityAction e exemplos
|Valor de ProfanityAction | Ação | Exemplo: Origem - Japonês | Exemplo: Destino - Inglês|
| :---|:---|:---|:---|
| NoAction | Padrão. O mesmo que não configurar a opção. A linguagem obscena passa do texto de origem para o texto de destino. | 彼は変態です。 | Ele é um idiota. |
| Marked | Palavras impróprias estão entre as marcas XML \<profanity> ... \</profanity>. | 彼は変態です。 | Ele é um \<profanity>jerk\</profanity >. |
| Deleted | Palavras impróprias são removidas da saída sem substituição. | 彼は。 | Ele é um. |

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Aplicar filtragem de linguagem obscena com a chamada à API de Tradução](reference/v3-0-translate.md)

