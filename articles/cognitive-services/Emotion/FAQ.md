---
title: Perguntas frequentes – API de Detecção de Emoções
titlesuffix: Azure Cognitive Services
description: Obtenha respostas às perguntas frequentes sobre a API de Detecção de Emoções.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 6c1c4b8e5c2701f3c419a58bc3fdc33f7e629bbd
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238499"
---
# <a name="emotion-api-frequently-asked-questions"></a>Perguntas frequentes da API de Detecção de Emoções

> [!IMPORTANT]
> A API de Detecção de Emoções será preterida em 15 de fevereiro de 2019. A funcionalidade de Detecção de Emoções agora está disponível como parte da [API de Detecção Facial](https://docs.microsoft.com/azure/cognitive-services/face/).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Se você não conseguir encontrar respostas para suas perguntas nesta FAQ, tente perguntar à comunidade da API de Detecção de Emoções em [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou entre em contato com Ajuda e Suporte em [UserVoice](https://cognitive.uservoice.com/).  

-----

**Pergunta**: *quais tipos de imagens de obtêm os melhores resultados da API de Detecção de Emoções?*

**Resposta**: use imagens de faciais frontais desobstruídas, completas para obter melhores resultados. A confiabilidade diminui com faces frontais parciais, e a API de Detecção de Emoções pode não reconhecer emoções nas imagens em que a face for girada em mais 45 graus.

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

**Pergunta**: *estou passando os dados de imagem binária, mas é exibido o seguinte: "imagem facial inválida".**

**Resposta**: essa mensagem implica que o algoritmo teve um problema ao analisar a imagem.  
* Os formatos de imagem de entrada com suporte incluem JPEG, PNG, GIF (o primeiro quadro), BMP
* O tamanho do arquivo de imagem não deve ser maior do que 4 MB
* O intervalo de tamanhos de face detectáveis é de 36 x 36 a 4096 x 4096 pixels. As faces fora desse intervalo não serão detectadas
* Talvez algumas faces não sejam detectadas devido a desafios técnicos; por exemplo, grandes ângulos de face (foto de rosto) ou oclusão grande. As faces frontais e quase frontais têm os melhores resultados

-----
