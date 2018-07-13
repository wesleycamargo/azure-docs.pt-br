---
title: Perguntas frequentes da API de Detecção de Emoções| Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre a API de Detecção de Emoções nos Serviços Cognitivos.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/26/2017
ms.author: anroth
ms.openlocfilehash: 8532d7c00fd8d7b01d84b5e55cb9bbc60241789c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363416"
---
# <a name="emotion-api-frequently-asked-questions"></a>Perguntas frequentes da API de Detecção de Emoções
 
> [!IMPORTANT]
> A versão prévia da API de vídeo terminará em 30 de outubro de 2017. Experimente a nova [versão prévia da API do Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) para extrair facilmente as informações dos vídeos e aprimorar as experiências de descoberta de conteúdo, tais como resultados da pesquisa através da detecção de palavras faladas, faces, personagens e emoções. [Saiba mais](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Se você não conseguir encontrar respostas para suas perguntas nesta FAQ, tente perguntar à comunidade da API de Detecção de Emoções em [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou entre em contato com Ajuda e Suporte em [UserVoice](https://cognitive.uservoice.com/).  

-----

**Pergunta**: *quais tipos de imagens de obtêm os melhores resultados da API de Detecção de Emoções?*

**Resposta**: use imagens de faciais frontais desobstruídas, completas para obter melhores resultados. A confiabilidade diminui com faces frontais parciais e API de Detecção de Emoções pode não reconhecer emoções nas imagens em que a face está girada a 45 graus ou mais.

-----

**Pergunta**: *quantos emoções a API de Detecção de Emoções pode identificar?*

**Resposta**: a API de Detecção de Emoções reconhece oito emoções diferentes universalmente aceitas: 
* Alegria
* Tristeza
* Surpresa
* Raiva
* Medo
* Desprezo
* Nojo 
* Neutro 

-----

**Pergunta**: *existe alguma maneira para passar um fluxo de vídeo ao vivo para a API e obter o resultado simultaneamente?*

**Resposta**: use a API de Detecção de Emoções baseada em imagem e chame-a em cada quadro ou ignore os quadros para desempenho.  Estão disponíveis exemplos de análise de vídeo quadro-a-quadro.

-----

**Pergunta**: *estou passando os dados de imagem binária mas aparece: "imagem facial inválida".**

**Resposta**: isso implica que o algoritmo teve um problema ao analisar a imagem.  
* Os formatos de imagem de entrada com suporte incluem JPEG, PNG, GIF (o primeiro quadro), BMP. 
* O tamanho do arquivo de imagem deve ser maior do que 4MB
* O intervalo de tamanho de face detectável é de 36x36 a 4096x4096 pixels. As faces fora desse intervalo não serão detectadas
* Talvez algumas faces não sejam detectadas por questões técnicas; por exemplo, ângulos de face muito grandes (foto de rosto) e oclusão grande. As faces frontais e quase frontais têm os melhores resultados

-----
