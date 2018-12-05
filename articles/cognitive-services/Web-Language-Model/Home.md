---
title: Visão geral da API de Modelo de Linguagem da Web
titleSuffix: Azure Cognitive Services
description: A API de Modelo de Linguagem da Web nos Serviços Cognitivos da Microsoft fornece ferramentas de última geração para processamento de linguagem natural.
services: cognitive-services
author: piyushbehre
manager: cgronlun
ms.service: cognitive-services
ms.component: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: pibehre
ROBOTS: NOINDEX
ms.openlocfilehash: 99d48e2abdc166fe6472195c86d890c3c1060cfa
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863781"
---
# <a name="what-is-the-web-language-model-api-preview"></a>O que é a API de Modelo de Linguagem da Web?  (Visualização)

> [!IMPORTANT]
> A versão prévia do Modelo de Linguagem da Web foi encerrada em 9 de agosto de 2018. Recomendamos usar os [módulos de Análise de Texto do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) para análise e processamento de texto.

A API de Modelo de Linguagem da Web da Microsoft é um serviço de nuvem baseado em REST que fornece ferramentas de última geração para processamento de linguagem natural. Com essa API, seu aplicativo pode aproveitar o poder do Big Data por meio de modelos de linguagem treinados com corpora de escala da Web coletados pelo Bing no mercado en-US.

Esses modelos de linguagem de n-grama de retirada suavizados, com suporte a cadeias de Markov até a quinta ordem, são treinados com os corpora a seguir:

- Texto de corpo de página da Web
- Texto de título de página da Web
- Texto de ancoragem de página da Web
- Texto de consulta da pesquisa na Web

A API de Modelo de Linguagem da Web dá suporte a quatro operações de pesquisa:

1. Probabilidade de união (log10) de uma sequência de palavras.
2. Probabilidade condicional (log10) de uma palavra, dada uma sequência de palavras que a precedem.
3. Lista de palavras (preenchimentos) que têm maior probabilidade de seguir uma dada sequência de palavras.
4. Quebra de palavras de cadeias de caracteres que não contêm espaços.

## <a name="getting-started"></a>Introdução

1. Assine o serviço.
2. Baixe o [SDK](https://www.github.com/microsoft/cognitive-weblm-windows).
3. Execute o código de exemplo do SDK.
4. Consulte a [Referência da API](http://web.archive.org/web/20170503191852/ https://westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104/operations/55de9ca4e597ed19b0de8a51) para obter detalhes completos dos pontos de extremidade, incluindo trechos de código em uma variedade de linguagens.

## <a name="underlying-technology"></a>A tecnologia subjacente

O artigo a seguir fornece detalhes sobre o desenvolvimento desses modelos de linguagem e deve ser citado em publicações de pesquisa que usem este serviço:

- [An Overview of Microsoft Web N-gram Corpus and Applications](https://research.microsoft.com/apps/pubs/default.aspx?id=130762) (Visão geral dos aplicativos e do corpus do Microsoft Web N-gram), NAACL-HLT 2010

Clique [aqui](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0) para obter uma lista atual de artigos que citam esse trabalho.
