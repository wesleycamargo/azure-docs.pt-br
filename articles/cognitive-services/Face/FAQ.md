---
title: Perguntas frequentes - API de Detecção Facial
titlesuffix: Azure Cognitive Services
description: Aqui estão as respostas para as perguntas mais populares sobre o Serviço de API de Detecção Facial.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 47ce80e1b0cefc01752d2445b751ebe1c2d65d08
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351315"
---
# <a name="face-api-frequently-asked-questions"></a>Perguntas frequentes da API de Detecção Facial

> [!TIP]
> Se você não conseguir encontrar respostas para suas perguntas nesta FAQ, tente perguntar à comunidade da API de Detecção Facial em [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou entre em contato com Ajuda e Suporte em [UserVoice](https://cognitive.uservoice.com/).

-----
**Pergunta**: Quais fatores podem reduzir a precisão da API de detecção facial para reconhecimento, verificação ou localizar Similar?

**Resposta**: Geralmente é os mesmos casos em que as pessoas têm dificuldade identificando alguém incluindo:
* Obstruções bloqueando um ou ambos os olhos
* Iluminação Harsh (por exemplo, grave luz de fundo)
* Alterações no estilo de cabelo ou pelo facial
* Alterações devido à idade
* Expressões faciais extremas (por exemplo, sendo também)

API de detecção facial costuma ser bem-sucedida em um desafio casos com o mostrado acima, mas precisão pode ser reduzido. Para tornar o reconhecimento mais robusto e enfrentar esses desafios, treine seu Pessoas com fotos que incluem uma diversidade de iluminação e ângulos.

-----
**Pergunta**:  Estou passando os dados de imagem binária, mas recebo o erro "Imagem de rosto inválida".

**Resposta**:  Este erro indica que o algoritmo teve um problema ao analisar a imagem. Entre as causas estão:
* Os formatos de imagem de entrada com suporte incluem JPEG, PNG, GIF (o primeiro quadro), BMP.
* O tamanho do arquivo de imagem não deve ser maior do que 4 MB
* O intervalo de tamanho de face detectável é de 36x36 a 4096x4096 pixels. As faces fora desse intervalo não serão detectadas
* Talvez algumas faces não sejam detectadas devido a desafios técnicos, por exemplo, grandes ângulos de face (head), oclusão grande. As faces frontais e quase frontais têm os melhores resultados

-----
