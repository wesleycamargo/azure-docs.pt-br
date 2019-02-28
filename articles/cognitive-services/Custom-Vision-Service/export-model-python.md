---
title: 'Tutorial: Executar um modelo TensorFlow em Python – Serviço de Visão Personalizada'
titlesuffix: Azure Cognitive Services
description: Executar um modelo TensorFlow em Python.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 05/17/2018
ms.author: areddish
ms.openlocfilehash: 298279fd67b312b6a7ab3a9939444c344407127f
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806888"
---
# <a name="tutorial-run-tensorflow-model-in-python"></a>Tutorial: Executar o modelo TensorFlow em Python

Depois de ter [exportado seu modelo TensorFlow](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) do Serviço de Visão Personalizada, este guia de início rápido mostrará como usar esse modelo localmente para classificar imagens.

## <a name="install-required-components"></a>Instalar componentes necessários

### <a name="prerequisites"></a>Pré-requisitos

Para usar o tutorial, você precisa do seguinte:

- Instale Python 2.7+ ou o Python 3.5+.
- Instale pip.

Além disso, você precisará instalar os pacotes a seguir:

```
pip install tensorflow
pip install pillow
pip install numpy
pip install opencv-python
```

## <a name="load-your-model-and-tags"></a>Carregar seu modelo e suas marcas

O arquivo zip baixado contém um model.pb e um labels.txt. Esses arquivos representam o modelo treinado e os rótulos de classificação. A primeira etapa é carregar o modelo para o seu projeto.

```Python
import tensorflow as tf
import os

graph_def = tf.GraphDef()
labels = []

# Import the TF graph
with tf.gfile.FastGFile(filename, 'rb') as f:
    graph_def.ParseFromString(f.read())
    tf.import_graph_def(graph_def, name='')

# Create a list of labels.
with open(labels_filename, 'rt') as lf:
    for l in lf:
        labels.append(l.strip())
```

## <a name="prepare-an-image-for-prediction"></a>Preparar uma imagem para previsão

Há algumas etapas para preparar a imagem para que ela tenha a forma certa para previsão. Estas etapas simulam a manipulação de imagem executada durante o treinamento:

### <a name="open-the-file-and-create-an-image-in-the-bgr-color-space"></a>Abrir o arquivo e criar uma imagem no espaço de cores BGR

```Python
from PIL import Image
import numpy as np
import cv2

# Load from a file
imageFile = "<path to your image file>"
image = Image.open(imageFile)

# Update orientation based on EXIF tags, if the file has orientation info.
image = update_orientation(image)

# Convert to OpenCV format
image = convert_to_opencv(image)
```

### <a name="deal-with-images-with-a-dimension-1600"></a>Lidar com imagens com uma dimensão >1.600

```Python
# If the image has either w or h greater than 1600 we resize it down respecting
# aspect ratio such that the largest dimension is 1600
image = resize_down_to_1600_max_dim(image)
```

### <a name="crop-the-largest-center-square"></a>Cortar o quadrado central maior

```Python
# We next get the largest center square
h, w = image.shape[:2]
min_dim = min(w,h)
max_square_image = crop_center(image, min_dim, min_dim)
```

### <a name="resize-down-to-256x256"></a>Redimensionar para 256 x 256

```Python
# Resize that square down to 256x256
augmented_image = resize_to_256_square(max_square_image)
```


### <a name="crop-the-center-for-the-specific-input-size-for-the-model"></a>Corte o centro para o tamanho de entrada específico para o modelo

```Python
# Get the input size of the model
with tf.Session() as sess:
    input_tensor_shape = sess.graph.get_tensor_by_name('Placeholder:0').shape.as_list()
network_input_size = input_tensor_shape[1]

# Crop the center for the specified network_input_Size
augmented_image = crop_center(augmented_image, network_input_size, network_input_size)

```

As etapas acima usam as funções auxiliares a seguir:

```Python
def convert_to_opencv(image):
    # RGB -> BGR conversion is performed as well.
    r,g,b = np.array(image).T
    opencv_image = np.array([b,g,r]).transpose()
    return opencv_image

def crop_center(img,cropx,cropy):
    h, w = img.shape[:2]
    startx = w//2-(cropx//2)
    starty = h//2-(cropy//2)
    return img[starty:starty+cropy, startx:startx+cropx]

def resize_down_to_1600_max_dim(image):
    h, w = image.shape[:2]
    if (h < 1600 and w < 1600):
        return image

    new_size = (1600 * w // h, 1600) if (h > w) else (1600, 1600 * h // w)
    return cv2.resize(image, new_size, interpolation = cv2.INTER_LINEAR)

def resize_to_256_square(image):
    h, w = image.shape[:2]
    return cv2.resize(image, (256, 256), interpolation = cv2.INTER_LINEAR)

def update_orientation(image):
    exif_orientation_tag = 0x0112
    if hasattr(image, '_getexif'):
        exif = image._getexif()
        if (exif != None and exif_orientation_tag in exif):
            orientation = exif.get(exif_orientation_tag, 1)
            # orientation is 1 based, shift to zero based and flip/transpose based on 0-based values
            orientation -= 1
            if orientation >= 4:
                image = image.transpose(Image.TRANSPOSE)
            if orientation == 2 or orientation == 3 or orientation == 6 or orientation == 7:
                image = image.transpose(Image.FLIP_TOP_BOTTOM)
            if orientation == 1 or orientation == 2 or orientation == 5 or orientation == 6:
                image = image.transpose(Image.FLIP_LEFT_RIGHT)
    return image
```

## <a name="predict-an-image"></a>Prever uma imagem

Depois que a imagem estiver preparada como um tensor, poderemos enviá-la por meio do modelo para uma previsão:

```Python

# These names are part of the model and cannot be changed.
output_layer = 'loss:0'
input_node = 'Placeholder:0'

with tf.Session() as sess:
    prob_tensor = sess.graph.get_tensor_by_name(output_layer)
    predictions, = sess.run(prob_tensor, {input_node: [augmented_image] })
```

## <a name="view-the-results"></a>Exibir os resultados

Os resultados da execução do tensor de imagem por meio do modelo precisarão ser mapeados de volta para os rótulos.

```Python
    # Print the highest probability label
    highest_probability_index = np.argmax(predictions)
    print('Classified as: ' + labels[highest_probability_index])
    print()

    # Or you can print out all of the results mapping labels to probabilities.
    label_index = 0
    for p in predictions[0]:
        truncated_probablity = np.float64(np.round(p,8))
        print (labels[label_index], truncated_probablity)
        label_index += 1
```
## <a name="next-steps"></a>Próximas etapas

Você também pode encapsular o modelo em um aplicativo móvel:
* [Usar seu modelo Tensorflow exportado em um aplicativo Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Usar seu modelo CoreML exportado em um aplicativo iOS Swift](https://go.microsoft.com/fwlink/?linkid=857726)
* [Usar seu modelo CoreML exportado em um aplicativo iOS com Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

