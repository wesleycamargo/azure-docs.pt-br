---
title: 'Tutorial: criar um projeto de classificação de imagem – Serviço de Visão Personalizada, Python'
titlesuffix: Azure Cognitive Services
description: Crie um projeto, adicione marcações, faça upload de imagens, treine seu projeto e faça uma previsão usando o ponto de extremidade padrão.
services: cognitive-services
author: areddish
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: tutorial
ms.date: 08/28/2018
ms.author: areddish
ms.openlocfilehash: 14b805a60637a889698132e169d5a41670a8bce0
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363370"
---
# <a name="tutorial-create-an-image-classification-project-using-the-custom-vision-service-with-python"></a>Tutorial: criar um projeto de classificação de imagem usando o Serviço de Visão Personalizada com Python

Saiba como criar um projeto de classificação de imagem com o Serviço de Visão Personalizada e um script Python básico. Depois de criado, você poderá adicionar marcas, carregar imagens, treinar o projeto, obter a URL de ponto de extremidade de previsão padrão do projeto e usá-lo para testar programaticamente uma imagem. Use este exemplo de código-fonte aberto como um modelo para compilar seu próprio aplicativo usando a API de Visão Personalizada.



## <a name="prerequisites"></a>Pré-requisitos

- Python 2.7+ ou Python 3.5+.
- A ferramenta pip.

## <a name="get-the-training-and-prediction-keys"></a>Obter as chaves de treinamento e previsão

Para obter as chaves usadas neste exemplo, acesse a [página da Web de Visão Personalizada](https://customvision.ai) e selecione o __ícone de engrenagem__ no canto superior direito. Na seção __Contas__, copie os valores dos campos __Chave de Treinamento__ e __Chave de Previsão__.

![Imagem da interface do usuário de chaves](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Instalar o SDK do Serviço de Visão Personalizada

Para instalar o SDK do Serviço de Visão Personalizada, use o seguinte comando:

```
pip install azure-cognitiveservices-vision-customvision
```

## <a name="get-example-images"></a>Obter imagens de exemplo

Este exemplo usa as imagens do diretório `Samples/Images` do projeto [https://github.com/Microsoft/Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows/tree/master/Samples/Images). Clone ou baixe e extraia o projeto em seu ambiente de desenvolvimento.

## <a name="create-a-custom-vision-service-project"></a>Criar um projeto de Serviço de Visão Personalizada

Para criar um novo projeto de Serviço de Visão Personalizada, crie um novo arquivo chamado `sample.py`. Use o código a seguir como o conteúdo do arquivo:

> [!IMPORTANT]
> Defina o `training_key` para o valor de chave de treinamento que você recuperou anteriormente.
>
> Defina o `prediction_key` para o valor de chave de previsão que você recuperou anteriormente.

```python
from azure.cognitiveservices.vision.customvision.training import training_api
from azure.cognitiveservices.vision.customvision.training.models import ImageUrlCreateEntry

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"

trainer = training_api.TrainingApi(training_key)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My Project")
```

## <a name="add-tags-to-your-project"></a>Adicionar marcas ao seu projeto

Para adicionar marcas ao seu projeto, adicione o código a seguir ao final do arquivo `sample.py`:

```python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

## <a name="upload-images-to-the-project"></a>Carregar imagens ao projeto

Para adicionar imagens de exemplo ao projeto, insira o código a seguir após a criação da marca. Esse código carrega a imagem com a marca correspondente:

> [!IMPORTANT]
>
> Altere o caminho para as imagens com base em onde você baixou o projeto Cognitive-CustomVision-Windows anteriormente.

```python
base_image_url = "https://raw.githubusercontent.com/Microsoft/Cognitive-CustomVision-Windows/master/Samples/"

print ("Adding images...")
for image_num in range(1,10):
    image_url = base_image_url + "Images/Hemlock/hemlock_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ hemlock_tag.id ] ) ])

for image_num in range(1,10):
    image_url = base_image_url + "Images/Japanese Cherry/japanese_cherry_{}.jpg".format(image_num)
    trainer.create_images_from_urls(project.id, [ ImageUrlCreateEntry(url=image_url, tag_ids=[ cherry_tag.id ] ) ])


# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following:
#
#import os
#hemlock_dir = "Images\\Hemlock"
#for image in os.listdir(os.fsencode("Images\\Hemlock")):
#    with open(hemlock_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ hemlock_tag.id ])
#
#cherry_dir = "Images\\Japanese Cherry"
#for image in os.listdir(os.fsencode("Images\\Japanese Cherry")):
#    with open(cherry_dir + "\\" + os.fsdecode(image), mode="rb") as img_data: 
#        trainer.create_images_from_data(project.id, img_data, [ cherry_tag.id ])
```

## <a name="train-the-project"></a>Treinar o projeto

Para treinar o classificador, adicione o código a seguir ao final do arquivo `sample.py`:

```python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Make it the default project endpoint
trainer.update_iteration(project.id, iteration.id, is_default=True)
print ("Done!")
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>Obter e usar o ponto de extremidade de previsão padrão

Para enviar uma imagem para o ponto de extremidade de previsão e recuperar a previsão, adicione o seguinte código ao final do arquivo `sample.py`:

```python
from azure.cognitiveservices.vision.customvision.prediction import prediction_endpoint
from azure.cognitiveservices.vision.customvision.prediction.prediction_endpoint import models

# Now there is a trained endpoint that can be used to make a prediction

predictor = prediction_endpoint.PredictionEndpoint(prediction_key)

test_img_url = base_image_url + "Images/Test/test_image.jpg"
results = predictor.predict_image_url(project.id, iteration.id, url=test_img_url)

# Alternatively, if the images were on disk in a folder called Images alongside the sample.py, then
# they can be added by using the following.
#
# Open the sample image and get back the prediction results.
# with open("Images\\test\\test_image.jpg", mode="rb") as test_data:
#     results = predictor.predict_image(project.id, test_data, iteration.id)

# Display the results.
for prediction in results.predictions:
    print ("\t" + prediction.tag_name + ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-example"></a>Executar o exemplo

Execute a solução. Os resultados da previsão são exibidos no console.

```
python sample.py
```

A saída do aplicativo é semelhante ao seguinte texto:

```
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```