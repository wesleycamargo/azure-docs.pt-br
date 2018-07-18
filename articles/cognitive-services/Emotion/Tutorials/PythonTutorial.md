---
title: Tutorial da API de Detecção de Emoções no Python | Microsoft Docs
description: Use um Jupyter Notebook para saber como usar a API de Detecção de Emoções dos Serviços Cognitivos com o Python. Visualize os resultados usando bibliotecas populares.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 70c8ca48c651601f3d7cbb3717c32bfe112176fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363690"
---
# <a name="emotion-api-using-python-tutorial"></a>Tutorial da API de Detecção de Emoções com o Python

> [!IMPORTANT]
> A versão prévia da API de vídeo terminará em 30 de outubro de 2017. Experimente a nova [versão prévia da API do Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) para extrair facilmente as informações dos vídeos e aprimorar as experiências de descoberta de conteúdo, tais como resultados da pesquisa através da detecção de palavras faladas, faces, personagens e emoções. [Saiba mais](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Para facilitar a introdução à API de Detecção de Emoções, o Jupyter Notebook vinculado abaixo mostra como usar a API no Python e como visualizar os resultados usando algumas bibliotecas populares. 

[Link para o bloco de anotações no GitHub](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>Usando o Bloco de Anotações do Jupyter

Para usar o bloco de anotações interativamente, você precisará cloná-lo e executá-lo no Jupyter. Para saber como começar a usar blocos de anotações interativos do Jupyter, siga as instruções descritas em http://jupyter.readthedocs.org/en/latest/install.html. 

Para usar esse bloco de anotações, você precisará de uma chave de assinatura para a API de Detecção de Emoções. Visite a [página Assinatura](https://azure.microsoft.com/try/cognitive-services/) para se inscrever. Na página “Entre”, use sua conta Microsoft para entrar e você poderá assinar e obter chaves gratuitas. Depois de concluir o processo de inscrição, cole a chave na seção de variáveis mostrada abaixo. Tanto a chave primária quanto a secundária funcionam.

```
Python Example 

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
