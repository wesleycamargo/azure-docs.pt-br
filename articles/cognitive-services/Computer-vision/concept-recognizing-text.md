---
title: Reconhecer texto impresso e manuscrito - Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados ao reconhecimento de texto impresso e manuscrito em imagens usando a API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48ce15a11c3e3282535420f3e1bb1915276d70f5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313170"
---
# <a name="recognizing-printed-and-handwritten-text"></a>Reconhecer texto impresso e manuscrito

A Pesquisa Visual Computacional pode detectar e extrair texto impresso e manuscrito das imagens de vários objetos com diferentes superfícies e fundos, como recibos, cartazes, cartões de visita, cartas e quadros de comunicações.

O recurso de reconhecimento de texto é muito semelhante ao [reconhecimento óptico de caracteres (OCR)](concept-extracting-text-ocr.md), mas, ao contrário do OCR, ele é executado de forma assíncrona e usa modelos de reconhecimento atualizados.

> [!NOTE]
> Essa tecnologia atualmente está em versão prévia e disponível somente para texto em inglês.

## <a name="text-recognition-requirements"></a>Requisitos de reconhecimento de texto

A Pesquisa Visual Computacional pode reconhecer texto impresso e manuscrito em imagens que atendem aos seguintes requisitos:

- A imagem deve ser apresentada em formato JPEG, PNG ou BMP
- O tamanho do arquivo da imagem deve ser menor que 4 MB (megabytes)
- As dimensões da imagem devem estar entre 50 x 50 e 4200 x 4200 pixels

## <a name="next-steps"></a>Próximas etapas

Consulte a [Documentação de referência sobre reconhecimento de texto](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) para saber mais.