---
title: Conceitos de atributos e de detecção facial
titleSuffix: Azure Cognitive Services
description: Aprenda os conceitos sobre a detecção de face e atributos faciais.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 3293067190386738efbfeb433bd38453c9e5699f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64572540"
---
# <a name="face-detection-and-attributes"></a>Detecção facial e atributos

Este artigo explica os conceitos de detecção facial e de dados de atributo de face. Detecção facial é a ação de localização rostos humanos em uma imagem e, opcionalmente, retornando uma variedade de dados relacionados a face.

Você usa o [enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) operação detectar faces em uma imagem. No mínimo, cada detectada face corresponderá a um **faceRectangle** campo na resposta. Este é um conjunto de coordenadas de pixel (esquerda, superior, largura, altura) marcando a face localizada. Usando essas coordenadas, você pode obter o local de face, bem como seu tamanho. A resposta da API, faces são listadas na ordem de tamanho do maior ao menor.

## <a name="face-id"></a>Face ID

A identificação de face é simplesmente uma cadeia de caracteres de identificador exclusivo para cada face detectada em uma imagem. Você pode solicitar uma face ID em sua [enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) chamada à API.

## <a name="face-landmarks"></a>Pontos de referência facial

Pontos de referência são um conjunto de pontos de fácil de encontrar no rosto, como os alunos ou a dica de nariz. Por padrão, há 27 pontos de referência facial predefinidos. A figura a seguir mostra todos os pontos de 27:

![Um diagrama de face com todos os pontos de 27 referência rotulado](../Images/landmarks.1.jpg)

As coordenadas dos pontos são retornadas em unidades de pixels.

## <a name="attributes"></a>Atributos

Os atributos são um conjunto de recursos de face adicional que opcionalmente podem ser detectados pelo [enfrentam - detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API. Estes são os atributos que podem ser detectados:

* **Idade** a idade estimada, nos anos de uma determinada face.
* **Desfoque** pouco de desfoque da face na imagem. Isso retorna um valor entre zero e um, bem como uma classificação informal ("baixa", "Médio", "alta").
* **Exposição** a exposição da face na imagem. Isso retorna um valor entre zero e um, bem como uma classificação informal ("Subexposição", "goodExposure", "Superexposição").
* **Emoções** uma lista de emoções com confiança suas detecção para determinada face. Pontuações de confiança são normalizadas: adicionará as pontuações em todas as emoções até um. As emoções retornadas são felicidade, tristeza, neutral, raiva, desdém, aversão, surpresa e medo.
* **Pelos faciais** a presença pelos faciais estimado e o comprimento da face determinado.
* **Gênero** estimado, o sexo de determinada face. Os valores possíveis são "male", "feminino" e "genderless".
* **Óculos** se determinada face tem óculos. Os valores possíveis são "NoGlasses", "ReadingGlasses", "Óculos de sol" e "Swimming óculos".
* **Fios** o estilo de cabelo da face. Isso mostra se os cabelos está visível, se baldness é detectado e quais cores de cabelo são detectadas.
* **Head apresentam** orientação da superfície no espaço 3D. Isso é descrito pelo pitch, roll e yaw ângulos em graus. Os intervalos de valores são [-90, 90] [-180, 180] e [-90, 90] graus, respectivamente. Consulte o diagrama a seguir para mapeamentos de ângulo:

    ![Um cabeçalho com o tom, roll e yaw eixos rotulados](../Images/headpose.1.jpg)
* **Composição** se a face tem composição. Isso retorna um valor booliano para "eyeMakeup" e "lipMakeup".
* **Ruído** poluição visual detectada na imagem de face. Isso retorna um valor entre zero e um, bem como uma classificação informal ("baixa", "Médio", "alta").
* **Oclusão** se há objetos partes da face de bloqueio. Isso retorna um valor booliano para "eyeOccluded", "foreheadOccluded" e "mouthOccluded".
* **Smiley** a expressão de Smiley da face determinado. Este é um valor entre zero (sem comentários positivos) e outro (sorriso não criptografado).

> [!IMPORTANT]
> Atributos faciais foram previstos com o uso de algoritmos estatísticos e podem não ser precisos. Tenha cuidado ao tomar decisões com base nos dados de atributo.

## <a name="input-data"></a>Dados de entrada

Use as dicas a seguir para garantir que suas imagens de entrada fornecer os resultados mais precisos de detecção:

* Os formatos de imagem de entrada com suporte são JPEG, PNG, GIF (o primeiro quadro), BMP.
* Tamanho do arquivo de imagem deve ser maior do que 4 MB.
* O intervalo de tamanho de face detectável é de 36x36 a 4096x4096 pixels. Faces fora desse intervalo não serão detectados.
* Talvez algumas faces não sejam detectadas devido a desafios técnicos. Extremos ângulos de face (principal) ou oclusão face (objetos como óculos de sol ou parte da face de bloqueio de mãos) pode afetar a detecção. Faces frontais e quase frontais fornecem os melhores resultados.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com conceitos de detecção de face, saiba como escrever um script simples que detecta rostos em uma determinada imagem.

* [Como detectar faces em uma imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)