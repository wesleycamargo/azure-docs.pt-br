---
title: Conceitos de reconhecimento facial
titleSuffix: Azure Cognitive Services
description: Aprenda os conceitos sobre reconhecimento facial.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 8c0bf001c2bdbedaa041dbd10b5c7edb08eec4c6
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "64415994"
---
# <a name="face-recognition-concepts"></a>Conceitos de reconhecimento facial

Este artigo explica os conceitos de várias operações de reconhecimento facial (verificação, localizar semelhante, o agrupamento de identificação) e as estruturas de dados subjacente. Em larga escala, reconhecimento descreve o trabalho de comparar duas faces diferentes para determinar se elas são semelhantes ou pertencem à mesma pessoa.

## <a name="recognition-related-data-structures"></a>Estruturas de dados relacionados ao reconhecimento

As operações de reconhecimento usam principalmente as seguintes estruturas de dados. Esses objetos são armazenados na nuvem e podem ser referenciados por suas cadeias de caracteres de ID. Cadeias de caracteres de ID, portanto, sempre são exclusivas em uma assinatura, enquanto os campos de nome podem ser duplicado.

|NOME|DESCRIÇÃO|
|:--|:--|
|**DetectedFace**| Essa é uma representação única face recuperada pelo [detecção facial](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md) operação. Sua ID expira 24 horas após sua criação.|
|**PersistedFace**| Quando **DetectedFace** objetos são adicionados a um grupo (como **FaceList** ou **pessoa**), eles se tornam **PersistedFace** objetos, que podem ser [recuperados](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) a qualquer hora e não expiram.|
|**[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)**/**[LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)**| Esta é uma lista variada de **PersistedFace** objetos. Um **FaceList** tem uma ID exclusiva, uma cadeia de caracteres de nome e, opcionalmente, uma cadeia de caracteres de dados do usuário.|
|**[Pessoa](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)**| Esta é uma lista dos **PersistedFace** objetos que pertencem à mesma pessoa. Ele tem uma ID exclusiva, uma cadeia de caracteres de nome e, opcionalmente, uma cadeia de caracteres de dados do usuário.|
|**[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)**/**[LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)**| Esta é uma lista variada de **pessoa** objetos. Ele tem uma ID exclusiva, uma cadeia de caracteres de nome e, opcionalmente, uma cadeia de caracteres de dados do usuário. Um **PersonGroup** deve ser [treinado](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) antes que ele pode ser usado em operações de reconhecimento.|

## <a name="recognition-operations"></a>Operações de reconhecimento

Esta seção fornece detalhes sobre como as quatro operações de reconhecimento usam as estruturas de dados acima. Consulte a [visão geral](../Overview.md) para obter uma descrição de cada operação de reconhecimento ampla.

### <a name="verification"></a>Verificação

O [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) operação leva uma face ID (**DetectedFace** ou **PersistedFace**) e qualquer outro face ID ou um **pessoa** objeto e Determina se eles pertencem à mesma pessoa. Se você passar em um **pessoa**, você poderá opcionalmente passar uma **PersonGroup** nos quais ela **pessoa** pertence para melhorar o desempenho.

### <a name="find-similar"></a>Localizar semelhante

O [encontrar semelhantes](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) operação leva uma face ID (**DetectedFace** ou **PersistedFace**) e um **FaceList** ou uma matriz de outro face IDs. Com uma **FaceList**, ele retorna um menor **FaceList** de faces que são semelhantes à determinada face. Com uma matriz de IDs de face, ela da mesma forma retorna uma matriz de menor.

### <a name="grouping"></a>Agrupamento

O [grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) operação aceita uma matriz de IDs de face variadas (**DetectedFace** ou **PersistedFace**) e retorna as mesmas IDs agrupadas em várias matrizes menores. Cada matriz de "grupos" contém IDs que pareçam semelhantes de face e uma matriz única "messyGroup" IDs de face para que nenhum semelhanças foram encontradas.

### <a name="identification"></a>Identificação

O [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) operação leva a uma ou várias IDs de face (**DetectedFace** ou **PersistedFace**) e um **PersonGroup** e retorna uma lista dos **pessoa** que cada face pode pertencer a objetos. Retornado **pessoa** objetos são encapsulados como **candidato** objetos que têm um valor de confiança de previsão.

## <a name="input-data"></a>Dados de entrada

Use as dicas a seguir para garantir que suas imagens de entrada fornecer os resultados mais precisos do reconhecimento:

* Os formatos de imagem de entrada com suporte são JPEG, PNG, GIF (o primeiro quadro), BMP.
* Tamanho do arquivo de imagem deve ser maior do que 4 MB.
* Ao criar **pessoa** objetos, você deve usar as fotos que apresentam uma variedade de ângulos e iluminação.
* Algumas faces não podem ser reconhecidos devido a desafios técnicos:
  * Imagens com iluminação extreme (por exemplo, grave luz de fundo).
  * Se há obstruções um ou ambos os olhos de bloqueio.
  * Diferenças de cabelo de estilo ou um rosto de cabelo.
  * Alterações na aparência de face devido a idade.
  * Expressões faciais extremas.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com conceitos de reconhecimento de face, saiba como escrever um script simples que identifica as faces em relação a um treinado **PersonGroup**.

* [Como identificar rostos em imagens](../Face-API-How-to-Topics/HowtoIdentifyFacesinImage.md)