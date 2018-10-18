---
title: Perguntas frequentes - API de Detecção Facial
titlesuffix: Azure Cognitive Services
description: Aqui estão as respostas para as perguntas mais populares sobre o Serviço de API de Detecção Facial.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 9b30fa0fbbd655c03800dadb19cc2568d404204d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129549"
---
# <a name="face-api-frequently-asked-questions"></a>Perguntas frequentes da API de Detecção Facial

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Se você não conseguir encontrar respostas para suas perguntas nesta FAQ, tente perguntar à comunidade da API de Detecção Facial em [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou entre em contato com Ajuda e Suporte em [UserVoice](https://cognitive.uservoice.com/).

-----
**Pergunta**: que fatores podem reduzir precisão da API de Detecção Facial para reconhecimento, verificação ou localizar similar?

**Resposta**: geralmente são os mesmo casos em que pessoas têm dificuldade de identificar uma pessoa, incluindo;
* Obstruções bloqueando um ou ambos os olhos
* Iluminação adversa, por exemplo, luz de fundo intensa
* Alterações no estilo de cabelo ou pelo facial
* Alterações devido à idade
* Expressões faciais extremas (por exemplo, gritando)

A API de Detecção Facial costuma ser bem-sucedida em casos difíceis como esses, mas a precisão pode ser reduzida. Para tornar o reconhecimento mais robusto e enfrentar esses desafios, treine seu Pessoas com fotos que incluem uma diversidade de iluminação e ângulos.

-----
**Pergunta**: estou passando os dados de imagem binária mas recebo um erro de "imagem facial inválida".

**Resposta**: isso implica que o algoritmo teve um problema ao analisar a imagem. Entre as causas estão:
* Os formatos de imagem de entrada com suporte incluem JPEG, PNG, GIF (o primeiro quadro), BMP.
* O tamanho do arquivo de imagem deve ser maior do que 4MB
* O intervalo de tamanho de face detectável é de 36x36 a 4096x4096 pixels. As faces fora desse intervalo não serão detectadas
* Talvez algumas faces não sejam detectadas por questões técnicas; por exemplo, ângulos de face muito grandes (foto de rosto) e oclusão grande. As faces frontais e quase frontais têm os melhores resultados

-----
