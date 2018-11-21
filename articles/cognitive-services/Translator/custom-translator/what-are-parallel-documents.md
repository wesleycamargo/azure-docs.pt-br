---
title: O que são documentos paralelos? - Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Documentos paralelos são pares de documentos em que um é a tradução do outro. Um documento no par contém sentenças no idioma de origem e o outro documento contém essas frases traduzidas no idioma de destino.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: e9214314bab40e3ce402fed84f429f3542519240
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626680"
---
# <a name="what-are-parallel-documents"></a>O que são documentos paralelos?

Documentos paralelos são pares de documentos em que um é a tradução do outro. Um documento no par contém sentenças no idioma de origem e o outro documento contém essas frases traduzidas no idioma de destino.
Não importa qual idioma está marcado como "source" e qual idioma está marcado como "target" - um documento paralelo pode ser usado para treinar um sistema de tradução em qualquer direção.

## <a name="use-of-parallel-documents"></a>Uso de documentos paralelos

Documentos paralelos são usados pelo sistema:

1.  Aprender como palavras, frases e frases são comumente mapeadas entre os dois idiomas.

2.  Aprender a processar o contexto apropriado, dependendo das frases vizinhas. Uma palavra nem sempre pode traduzir para a mesma palavra na outra língua.

Como prática recomendada, verifique se há uma correspondência de sentença 1:1 entre as versões de idioma de origem e de destino dos documentos.

Os documentos enviados são privados para cada área de trabalho e podem ser usados em quantos projetos ou treinamentos você desejar. Frases extraídas de seus documentos são armazenadas separadamente em seu repositório como arquivos de texto Unicode simples e estão disponíveis para exclusão. Não use o Custom Translator como repositório de documentos, você não poderá fazer o download dos documentos que você enviou no formato que você enviou.

## <a name="recommendations"></a>Recomendações

Se o seu projeto é específico de domínio (categoria), seus documentos devem ser consistentes na terminologia dentro dessa categoria. A qualidade do sistema de tradução resultante depende do número de sentenças no conjunto de documentos e da qualidade das sentenças. Quanto mais exemplos seus documentos contiverem usos diversos para uma palavra específica da sua categoria, o melhor trabalho que o sistema pode fazer durante a tradução.

Você precisará de um mínimo de 10.000 sentenças paralelas para treinar um sistema. Como prática recomendada, você pode adicionar continuamente mais conteúdo paralelo e reciclagem, para melhorar a qualidade do seu sistema de tradução.

A Microsoft exige que os documentos carregados para o conversor personalizado não violem as propriedades de direitos autorais ou intelectual de terceiros. Para mais informações, consulte os [Termos de Uso](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
O upload de um documento usando o portal não altera a propriedade da propriedade intelectual no próprio documento.

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar um [dicionário](what-is-dictionary.md) no tradutor personalizado.