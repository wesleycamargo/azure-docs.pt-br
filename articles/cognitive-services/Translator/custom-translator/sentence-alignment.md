---
title: Pareamento e alinhamento de frases - Custom Translator
titleSuffix: Azure Cognitive Services
description: Durante a execução do treinamento, as sentenças presentes em documentos paralelos são emparelhadas ou alinhadas. Tradutor personalizado aprende traduções uma frase de cada vez, lendo uma frase, a tradução desta frase. Em seguida, alinha palavras e frases nessas duas frases entre si.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 557cd8d3af0c774d4dd0558d5d25dba8eec07268
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626640"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Emparelhamento de frases e alinhamento em documentos paralelos

Durante o treinamento, as frases presentes em documentos paralelos são emparelhadas ou alinhadas. O Custom Translator relata o número de sentenças que conseguiu emparelhar como as Sentenças Alinhadas em cada um dos conjuntos de dados.

## <a name="pairing-and-alignment-process"></a>Processo de emparelhamento e alinhamento

O Custom Translator aprende traduções de sentenças uma sentença de cada vez. Ele lê uma sentença da fonte e, em seguida, a tradução desta frase do alvo. Em seguida, alinha palavras e frases nessas duas frases entre si. Este processo permite criar um mapa das palavras e frases em uma frase para as palavras e frases equivalentes na tradução desta frase. O alinhamento tenta garantir que o sistema treine em sentenças que são traduções uma da outra.

## <a name="pre-aligned-documents"></a>Documentos previamente alinhados

Se você souber que possui documentos paralelos, poderá substituir o alinhamento de sentenças fornecendo arquivos de texto pré-alinhados. Você pode extrair todas as sentenças de ambos os documentos em um arquivo de texto, organizar uma sentença por linha e fazer o upload com uma extensão `.align`. A extensão `.align` sinaliza ao Custom Translator que deve pular o alinhamento das frases.

Para obter melhores resultados, verifique se você tem uma frase por linha em seus arquivos. Não tem caracteres de nova linha dentro de uma frase, pois isso causará alinhamentos ruins.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Número mínimo sugerido de sentenças extraídas e alinhadas

Para que um treinamento seja bem-sucedido, a tabela abaixo mostra o número mínimo de sentenças extraídas e frases alinhadas necessárias em cada conjunto de dados. O número mínimo sugerido de sentenças extraídas é muito maior do que o número mínimo sugerido de sentenças alinhadas para levar em conta o fato de que o alinhamento da frase pode não conseguir alinhar todas as sentenças extraídas com êxito.

| Conjunto de dados   | Sugerido contagem mínima de sentença extraídos | Sugerido contagem mínima de sentença alinhado | Contagem máxima de sentença alinhado |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Treinamento   | 10.000                                     | 2.000                                    | Nenhum limite superior                 |
| Ajuste     | 2.000                                      | 500                                      | 2.500                          |
| Testando    | 2.000                                      | 500                                      | 2.500                          |
| Dicionário | 0                                          | 0                                        | Nenhum limite superior                 |

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar um [dicionário](what-is-dictionary.md) no tradutor personalizado.
