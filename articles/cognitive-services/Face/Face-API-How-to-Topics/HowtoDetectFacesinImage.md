---
title: 'Exemplo: detectar faces em imagens – API de Detecção Facial'
titleSuffix: Azure Cognitive Services
description: Use a API de Detecção Facial para detectar faces em imagens.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: sample
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: a4c74ff70a4426abf97562bf997479a91afbf17a
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124041"
---
# <a name="example-how-to-detect-faces-in-image"></a>Exemplo: como detectar faces em imagens

Este guia demonstrará como detectar faces de uma imagem, com atributos faciais como sexo, idade ou pose extraídos. As amostras são gravadas em C# usando a biblioteca de clientes da API de Detecção Facial. 

## <a name="concepts"></a>Conceitos

Se você não estiver familiarizado com nenhum dos conceitos a seguir apresentados neste guia, consulte as definições no [Glossário](../Glossary.md), a qualquer momento: 

- Detecção facial
- Pontos de referência facial
- Pose de cabeça
- Atributos faciais

## <a name="preparation"></a>Preparação

Neste exemplo, demonstraremos os recursos a seguir: 

- Detectar faces de uma imagem e marcá-las usando o enquadramento retangular
- Analisar os locais das pupilas, nariz ou boca e, em seguida, marcá-los na imagem
- Analisar a pose de cabeça, gênero e idade da face

Para executar esses recursos, será necessário preparar uma imagem com pelo menos uma face limpa. 

## <a name="step-1-authorize-the-api-call"></a>Etapa 1: autorizar a chamada à API

Cada chamada à API de Detecção Facial exige uma chave de assinatura. Essa chave precisa ser passada por um parâmetro de cadeia de caracteres de consulta ou especificada no cabeçalho da solicitação. Para passar a chave de assinatura através da cadeia de caracteres de consulta, consulte a URL de solicitação para a [Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como um exemplo:

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

Como alternativa, a chave de assinatura também pode ser especificada no cabeçalho da solicitação HTTP: **ocp-apim-subscription-key: &lt;Chave de Assinatura&gt;** Ao usar uma biblioteca de clientes, a chave de assinatura é passada por meio do construtor da classe FaceServiceClient. Por exemplo: 
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step-2-upload-an-image-to-the-service-and-execute-face-detection"></a>Etapa 2: Carregar uma imagem para o serviço e executar a detecção facial

A maneira mais básica de realizar a detecção facial é carregar uma imagem diretamente. Isso é feito, enviando uma solicitação "POST" com o tipo de conteúdo application/octet-stream, com os dados lidos de uma imagem JPEG. O tamanho máximo da imagem é de 4 MB.

Usando a biblioteca de clientes, a detecção facial por meio de upload é feita passando em um objeto de Fluxo. Veja o exemplo abaixo:

```CSharp
using (Stream s = File.OpenRead(@"D:\MyPictures\image1.jpg"))
{
    var faces = await faceServiceClient.DetectAsync(s, true, true);
 
    foreach (var face in faces)
    {
        var rect = face.FaceRectangle;
        var landmarks = face.FaceLandmarks;
    }
}
```

Observe que o método DetectAsync do FaceServiceClient é assíncrono. O método de chamada também deverá ser marcado como assíncrono para usar a cláusula await.
Se a imagem já estiver na Web e tiver uma URL, a detecção facial poderá ser executada, fornecendo também a URL. Neste exemplo, o corpo da solicitação será uma cadeia de caracteres JSON, que contém a URL.
Usando a biblioteca de clientes, a detecção facial por meio de uma URL pode ser executada facilmente usando outra sobrecarga do método DetectAsync.

```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

A propriedade FaceRectangle que é retornada com faces detectadas é essencialmente locais na face em pixels. Normalmente, esse retângulo contém os olhos, as sobrancelhas, o nariz e a boca – a parte superior da cabeça, as orelhas e o queixo não estão incluídos. Se você recortar um retrato completo da cabeça ou plano intermediário (uma imagem tipo foto de ID), poderá expandir a área do enquadramento de face retangular, pois a área da face pode ser muito pequena para alguns aplicativos. Localizar uma face com mais precisão, usando pontos de referência facial (localizar recursos faciais ou mecanismos de direção facial) descrito na próxima seção, será útil.

## <a name="step-3-understanding-and-using-face-landmarks"></a>Etapa 3: Compreender e usar pontos de referência facial

Pontos de referência facial são uma série de pontos detalhados em uma face, normalmente pontos de componentes faciais como as pupilas, canto do olho ou nariz. Os pontos de referência facial são atributos opcionais que podem ser analisados durante a detecção facial. É possível passar 'true' como um valor booliano para o parâmetro de consulta returnFaceLandmarks, ao chamar a [Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), ou usar o parâmetro opcional returnFaceLandmarks para o método DetectAsync, da classe FaceServiceClient, para incluir os pontos de referência facial nos resultados da detecção.

Por padrão, há 27 pontos de referência facial predefinidos. A figura a seguir mostra como todos os 27 pontos são definidos:

![HowToDetectFace](../Images/landmarks.1.jpg)

Os pontos retornados estão em unidades de pixels, assim como o enquadramento retangular de face. Portanto, é mais fácil marcar pontos de interesse específicos na imagem. O código a seguir demonstra a recuperação dos locais do nariz e pupilas:

```CSharp
var faces = await faceServiceClient.DetectAsync(imageUrl, returnFaceLandmarks:true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
 
    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;
 
    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;
 
    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
``` 

Além de marcar recursos faciais em uma imagem, os pontos de referência facial também podem ser usados para calcular com precisão a direção facial. Por exemplo, é possível definir a direção facial como um vetor do centro da boca até o centro dos olhos. O código abaixo explica isso em detalhes:

```CSharp
var landmarks = face.FaceLandmarks;
 
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

Ao conhecer a direção em que a face está, será possível girar o enquadramento de face retangular para alinhá-lo com a face. É evidente usar pontos de referência facial pode fornecer mais detalhes e utilidade.

## <a name="step-4-using-other-face-attributes"></a>Etapa 4: Usar outros atributos faciais

Além dos pontos de referência facial, a API de Detecção Facial também pode analisar vários outros atributos em uma face. Esses atributos incluem:

- Idade
- Gênero
- Intensidade do sorriso
- Pelos faciais
- Uma pose de cabeça tridimensional

Esses atributos são previstos usando algoritmos estatísticos e nem sempre podem ser 100% precisos. No entanto, ainda são úteis quando você quer classificar faces por esses atributos. Para obter mais informações sobre cada um dos atributos, consulte o [Glossário](../Glossary.md).

Abaixo está um exemplo simples de extração de atributos faciais durante a detecção facial:

```CSharp
var requiredFaceAttributes = new FaceAttributeType[] {
                FaceAttributeType.Age,
                FaceAttributeType.Gender,
                FaceAttributeType.Smile,
                FaceAttributeType.FacialHair,
                FaceAttributeType.HeadPose,
                FaceAttributeType.Glasses
            };
var faces = await faceServiceClient.DetectAsync(imageUrl,
    returnFaceLandmarks: true,
    returnFaceAttributes: requiredFaceAttributes);

foreach (var face in faces)
{
    var id = face.FaceId;
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
}
``` 

## <a name="summary"></a>Resumo

Neste guia você aprendeu sobre as funcionalidades de API de [Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), como é possível detectar faces para imagens carregadas locais ou URLs de imagens na Web, como é possível detectar faces, retornando enquadramentos de face retangulares, bem como analisar pontos de referência facial, poses de cabeça tridimensionais e outros atributos faciais.

Para obter mais informações sobre detalhes de API, consulte o guia de referência de API para [Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related-topics"></a>Tópicos Relacionados

[Como identificar faces em imagem](HowtoIdentifyFacesinImage.md)
