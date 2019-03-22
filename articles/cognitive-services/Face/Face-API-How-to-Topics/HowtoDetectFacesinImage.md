---
title: Detecte rostos em uma imagem - API de detecção facial
titleSuffix: Azure Cognitive Services
description: Saiba como usar vários dados retornados pelo recurso de detecção de face.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: sbowles
ms.openlocfilehash: bf3af8f5d1d2f063199a8275c2f49c70140e8732
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588764"
---
# <a name="get-face-detection-data"></a>Obter dados de detecção facial

Este guia demonstrará como usar a detecção de face para extrair atributos como sexo, idade ou pose de uma determinada imagem. Os trechos de código neste guia são escritos em C# usando a biblioteca de cliente da API de detecção facial, mas a mesma funcionalidade está disponível por meio de [API REST](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

Este guia mostrará como fazer para:

- Para obter os locais e as dimensões de rostos em uma imagem.
- Obter os locais de vários pontos de referência (alunos, nariz, boca e assim por diante) em uma imagem.
- Adivinhe o gênero, idade e emoções e outros atributos de um rosto detectado.

## <a name="setup"></a>Configuração

Este guia pressupõe que você construiu um **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** objeto, chamado `faceClient`, com uma URL de ponto de extremidade e a chave de assinatura Face. A partir daqui, você pode usar o recurso de detecção de face chamando **[DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)** (usado neste guia) ou **[DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)**. Consulte a [detectar Faces quickstart para C# ](../quickstarts/csharp-detect-sdk.md) para obter instruções sobre como configurar isso.

Este guia se concentrará nas particularidades da chamada de detectar&mdash;quais argumentos que você pode passar, e o que você pode fazer com os dados retornados. É recomendável consultar apenas os recursos que necessários, conforme cada operação leva mais tempo para concluir.

## <a name="get-basic-face-data"></a>Obter dados de face básico

Para encontrar rostos e suas localizações em uma imagem, chame o método com o _returnFaceId_ parâmetro definido como **verdadeiro** (padrão).

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

Retornado **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** objetos podem ser consultados por suas IDs exclusivas e um retângulo que fornece as coordenadas de pixel da face.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

Ver **[FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)** para obter informações sobre como analisar a localização e as dimensões da face. Normalmente, esse retângulo contém os olhos, sobrancelhas, nariz e boca; parte superior do cabeçalho, ouvidos e chin não são necessariamente incluídos. Se você pretende usar o retângulo facial para cortar um cabeçalho completo ou retrato captura intermediário (uma foto ID tipo imagem), você talvez queira expandir o retângulo por uma margem certo em cada direção.

## <a name="get-face-landmarks"></a>Obter pontos de referência

Pontos de referência são um conjunto de pontos de fácil de encontrar no rosto, como os alunos ou a dica de nariz. Você pode obter dados de ponto de referência do rosto, definindo o _returnFaceLandmarks_ parâmetro **verdadeiro**.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

Por padrão, há 27 pontos de referência facial predefinidos. A figura a seguir mostra todos os pontos de 27:

![Um diagrama de face com todos os pontos de 27 referência rotulada](../Images/landmarks.1.jpg)

Os pontos retornados estão em unidades de pixels, assim como o quadro de retângulo facial. O código a seguir demonstra como você pode recuperar os locais dos alunos e nariz:

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

Dados de pontos de referência de face também podem ser usados para calcular com precisão a direção da face. Por exemplo, podemos definir a rotação da face como um vetor do centro da boca para o centro dos olhos. O código a seguir calcula esse vetor:

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

Sabendo que a direção da face, você pode girar o quadro de face retangular para alinhá-lo mais corretamente. Se você quiser cortar rostos em uma imagem, você pode programaticamente girar a imagem para que os rostos aparecem sempre na vertical.

## <a name="get-face-attributes"></a>Obter atributos faciais

Além de retângulos faciais e pontos de referência, a API de detecção facial pode analisar vários atributos conceituais de uma face. Estão incluídos:

- Idade
- Gênero
- Intensidade do sorriso
- Pelos faciais
- Óculos
- 3D pose principal
- Emoção

> [!IMPORTANT]
> Esses atributos são previstos com o uso de algoritmos estatísticos e podem não ser precisos. Tenha cuidado ao tomar decisões com base nos dados de atributo.
>

Para analisar atributos faciais, defina as _returnFaceAttributes_ parâmetro a uma lista de **[enumeração FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** valores.

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

Em seguida, obter referências para os dados retornados e fazer mais operações de acordo com suas necessidades.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

Para saber mais sobre cada um dos atributos, consulte o [glossário](../Glossary.md).

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu como usar as várias funcionalidades de detecção facial. Em seguida, consulte a [glossário](../Glossary.md) para examinar os dados de face que você recuperou mais detalhada.

## <a name="related-topics"></a>Tópicos Relacionados

- [Documentação de referência (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Documentação de referência (SDK do .NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/face?view=azure-dotnet)
