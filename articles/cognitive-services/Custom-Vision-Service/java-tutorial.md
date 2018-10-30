---
title: 'Tutorial: Criar um projeto de classificação de imagem com o SDK de Visão Personalizada para Java'
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
ms.openlocfilehash: e302fc580d9c83d269f0deedd051a3ea23bd274e
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957200"
---
# <a name="tutorial-create-an-image-classification-project-with-the-custom-vision-sdk-for-java"></a>Tutorial: Criar um projeto de classificação de imagem com o SDK de Visão Personalizada para Java

Saiba como criar um projeto de classificação de imagem com o Serviço de Visão Personalizada usando o Java. Depois de criado, você poderá adicionar marcas, carregar imagens, treinar o projeto, obter a URL de ponto de extremidade de previsão padrão do projeto e usá-lo para testar programaticamente uma imagem. Use este exemplo de código-fonte aberto como um modelo para compilar seu próprio aplicativo usando a API de Visão Personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- JDK 7 ou 8 instalado.
- Maven instalado.

## <a name="get-the-training-and-prediction-keys"></a>Obter as chaves de treinamento e previsão

Para obter as chaves usadas neste exemplo, acesse a [página da Web de Visão Personalizada](https://customvision.ai) e selecione o __ícone de engrenagem__ no canto superior direito. Na seção __Contas__, copie os valores dos campos __Chave de Treinamento__ e __Chave de Previsão__.

![Imagem da interface do usuário de chaves](./media/python-tutorial/training-prediction-keys.png)

## <a name="install-the-custom-vision-service-sdk"></a>Instalar o SDK do Serviço de Visão Personalizada

Você pode instalar o SDK de Visão Personalizada do repositório central do Maven:
* [SDK de treinamento](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [SDK de previsão](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

## <a name="understand-the-code"></a>Compreender o código

O projeto completo, incluindo imagens, está disponível nos [exemplos da Visão Personalizada do Azure para o repositório do Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master). 

Use seu Java IDE favorito para abrir o projeto `Vision/CustomVision`. 

Este aplicativo usa a chave de treinamento que você recuperou anteriormente para criar um novo projeto denominado __Projeto Java de exemplo__. Ele então carrega as imagens para treinar e testar um classificador. O classificador identifica se uma árvore é uma __Cicuta__ ou uma __Cerejeira japonesa__.

Os snippets de código a seguir implementam a funcionalidade principal deste exemplo:

## <a name="create-a-custom-vision-service-project"></a>Criar um projeto de Serviço de Visão Personalizada

> [!IMPORTANT]
> Defina o `trainingApiKey` para o valor de chave de treinamento que você recuperou anteriormente.

```java
final String trainingApiKey = "insert your training key here";
TrainingApi trainClient = CustomVisionTrainingManager.authenticate(trainingApiKey);

Trainings trainer = trainClient.trainings();

System.out.println("Creating project...");
Project project = trainer.createProject()
            .withName("Sample Java Project")
            .execute();
```

## <a name="add-tags-to-your-project"></a>Adicionar marcas ao seu projeto

```java
// create hemlock tag
Tag hemlockTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Hemlock")
    .execute();

// create cherry tag
Tag cherryTag = trainer.createTag()
    .withProjectId(project.id())
    .withName("Japanese Cherry")
    .execute();
```

## <a name="upload-images-to-the-project"></a>Carregar imagens ao projeto

O exemplo está configurado para incluir as imagens no pacote final. As imagens são lidas da seção de recursos do arquivo jar e carregadas para o serviço.

```java
System.out.println("Adding images...");
for (int i = 1; i <= 10; i++) {
    String fileName = "hemlock_" + i + ".jpg";
    byte[] contents = GetImage("/Hemlock", fileName);
    AddImageToProject(trainer, project, fileName, contents, hemlockTag.id(), null);
}

for (int i = 1; i <= 10; i++) {
    String fileName = "japanese_cherry_" + i + ".jpg";
    byte[] contents = GetImage("/Japanese Cherry", fileName);
    AddImageToProject(trainer, project, fileName, contents, cherryTag.id(), null);
}
```

O snippet anterior faz uso de duas funções auxiliares que recuperam as imagens como fluxos de recurso e as carrega para o serviço.

```java
private static void AddImageToProject(Trainings trainer, Project project, String fileName, byte[] contents, UUID tag)
{
    System.out.println("Adding image: " + fileName);

    ImageFileCreateEntry file = new ImageFileCreateEntry()
        .withName(fileName)
        .withContents(contents);

    ImageFileCreateBatch batch = new ImageFileCreateBatch()
        .withImages(Collections.singletonList(file));

    batch = batch.withTagIds(Collections.singletonList(tag));

    trainer.createImagesFromFiles(project.id(), batch);
}

private static byte[] GetImage(String folder, String fileName)
{
    try {
        return ByteStreams.toByteArray(CustomVisionSamples.class.getResourceAsStream(folder + "/" + fileName));
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    return null;
}
```

## <a name="train-the-project"></a>Treinar o projeto

Isso cria a primeira iteração no projeto e marca essa iteração como a iteração padrão. 

```java
System.out.println("Training...");
Iteration iteration = trainer.trainProject(project.id());

while (iteration.status().equals("Training"))
{
    System.out.println("Training Status: "+ iteration.status());
    Thread.sleep(1000);
    iteration = trainer.getIteration(project.id(), iteration.id());
}

System.out.println("Training Status: "+ iteration.status());
trainer.updateIteration(project.id(), iteration.id(), iteration.withIsDefault(true));
```

## <a name="get-and-use-the-default-prediction-endpoint"></a>Obter e usar o ponto de extremidade de previsão padrão

> [!IMPORTANT]
> Defina o `predictionApiKey` para o valor de chave de previsão que você recuperou anteriormente.

```java
final String predictionApiKey = "insert your prediction key here";
PredictionEndpoint predictClient = CustomVisionPredictionManager.authenticate(predictionApiKey);

// Use below for predictions from a url
// String url = "some url";
// ImagePrediction results = predictor.predictions().predictImage()
//                         .withProjectId(project.id())
//                         .withUrl(url)
//                         .execute();

// load test image
byte[] testImage = GetImage("/Test", "test_image.jpg");

// predict
ImagePrediction results = predictor.predictions().predictImage()
    .withProjectId(project.id())
    .withImageData(testImage)
    .execute();

for (Prediction prediction: results.predictions())
{
    System.out.println(String.format("\t%s: %.2f%%", prediction.tagName(), prediction.probability() * 100.0f));
}
```

## <a name="run-the-example"></a>Executar o exemplo

Como compilar e executar a solução usando o Maven:

```
mvn compile exec:java
```

A saída do aplicativo é semelhante ao seguinte texto:

```
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```