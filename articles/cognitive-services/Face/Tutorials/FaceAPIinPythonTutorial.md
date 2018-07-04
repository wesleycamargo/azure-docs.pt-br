---
title: Tutorial de Python da API de Detecção Facial | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Saiba como usar a API de Detecção Facial com o SDK de Python para detectar as faces humanas em uma imagem em Serviços Cognitivos.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 90d74d8df2ed59e6f3313ef7c620284d1022a667
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049104"
---
# <a name="getting-started-with-face-api-in-python-tutorial"></a>Guia de Introdução com a API de Detecção Facial no tutorial de Python

Neste tutorial, você aprenderá a chamar a API de Detecção Facial por meio do SDK do Python para detectar as faces humanas em uma imagem.

## <a name="prerequisites"></a> Pré-requisitos

Para usar o tutorial, você precisará fazer o seguinte:

- Instale o Python 2.7+ ou Python 3.5+.
- Instale o pip.
- Instale o SDK de Python para a API de Detecção Facial da seguinte maneira:

```bash
pip install cognitive_face
```

- Obtenha uma [chave de assinatura](https://azure.microsoft.com/try/cognitive-services/) para Serviços Cognitivos da Microsoft. Você pode usar a chave principal ou secundária neste tutorial. (Observe que, para usar qualquer API de Detecção Facial, você deve ter uma chave de assinatura válida.)

## <a name="sdk-example"></a> Detectar uma Face em uma imagem

```python
import cognitive_face as CF

KEY = '<Subscription Key>'  # Replace with a valid subscription key (keeping the quotes in place).
CF.Key.set(KEY)

BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'  # Replace with your regional Base URL
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

Abaixo está um resultado de exemplo. É um `list` de faces detectadas. Cada item na lista é uma instância `dict` em que `faceId` é uma ID exclusiva para a face detectada e `faceRectangle` descreve a posição da face detectada. Uma ID de face expira em 24 horas.

```python
[{u'faceId': u'68a0f8cf-9dba-4a25-afb3-f9cdf57cca51', u'faceRectangle': {u'width': 89, u'top': 66, u'height': 89, u'left': 446}}]
```

## <a name="draw-rectangles-around-the-faces"></a>Desenhar retângulos em torno de faces

Usando as coordenadas de json que você recebeu do comando anterior, você pode desenhar retângulos na imagem para representar visualmente cada face. Você precisará `pip install Pillow` para usar o módulo de geração de imagens `PIL`.  Na parte superior do arquivo, adicione o seguinte:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Em seguida, após `print(faces)`, inclua o seguinte no seu script:

```python
#Convert width height to a point in a rectangle
def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))

#Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

#For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

#Display the image in the users default image browser.
img.show()
```

## <a name='further'></a> Exploração adicional

Para ajudá-lo a explorar ainda mais a API de Detecção Facial, este tutorial fornece um exemplo de interface gráfica do usuário. Para executá-lo, primeiro instale [wxPython](https://wxpython.org/pages/downloads/) , em seguida, execute os comandos a seguir.

```bash
git clone https://github.com/Microsoft/Cognitive-Face-Python.git
cd Cognitive-Face-Python
python sample
```

## <a name="summary"></a> Resumo

Neste tutorial, você aprendeu o processo básico para usar a API de Detecção Facial por meio de chamar o SDK de Python. Para obter mais informações sobre detalhes de API, consulte as instruções e [referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related"></a> Tópicos relacionados

- [Guia de Introdução com a API de Detecção Facial no CSharp](FaceAPIinCSharpTutorial.md)
- [Guia de Introdução com a API de Detecção Facial no Java para Android](FaceAPIinJavaForAndroidTutorial.md)
