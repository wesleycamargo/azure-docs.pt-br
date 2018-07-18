---
title: Usar o Serviço de Visão Personalizada de um aplicativo C# - Serviços Cognitivos do Azure | Microsoft Docs
description: Explore um aplicativo C# básico que usa a API de Visão Personalizada nos Serviços Cognitivos da Microsoft. Crie um projeto, adicione marcas, carregue imagens, treine seu projeto e faça uma previsão usando o ponto de extremidade padrão.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 80cb022808748ed2c60dff7c363d6020cb4043a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363847"
---
# <a name="use-the-custom-vision-service-from-a-c35-application"></a>Usar o Serviço de Visão Personalizada de um aplicativo C&#35;

Saiba como usar o Serviço de Visão Personalizada de um aplicativo C#. Depois de criada, você poderá adicionar marcas, carregar imagens, treinar o projeto, obter a URL de ponto de extremidade de previsão do projeto padrão e usar o ponto de extremidade para testar programaticamente uma imagem. Use este exemplo de código-fonte aberto como um modelo para criar seu próprio aplicativo para o Windows usando a API de Serviço de Visão Personalizada.

## <a name="prerequisites"></a>pré-requisitos

* Qualquer edição do Visual Studio 2015 ou 2017 para Windows.

* O [SDK do Serviço de Visão Personalizada](http://github.com/Microsoft/Cognitive-CustomVision-Windows/). Isso inclui o exemplo e as imagens usadas neste documento.

## <a name="get-the-training-and-prediction-keys"></a>Obter as chaves de treinamento e previsão

Para obter as chaves usadas neste exemplo, acesse a [página da Web de Visão Personalizada](https://customvision.ai) e selecione o __ícone de engrenagem__ no canto superior direito. Na seção __Contas__, copie os valores dos campos __Chave de Treinamento__ e __Chave de Previsão__.

![Imagem da interface do usuário de chaves](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>Compreender o código

No Visual Studio, abra o projeto localizado no diretório `Samples/CustomVision.Sample/` do projeto do SDK.

Este aplicativo usa a chave de treinamento que você recuperou anteriormente para criar um novo projeto denominado __Meu Projeto Novo__. Ele então carrega as imagens para treinar e testar um classificador. O classificador identifica se uma árvore é uma __Cicuta__ ou uma __Cerejeira japonesa__.

Os trechos de código a seguir implementam a funcionalidade principal deste exemplo:

* __Criar um novo projeto de Serviço de Visão Personalizada__:

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __Crie marcas em um projeto__:

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __Carregar e imagens de marca__:

    ```csharp
    // Add some images to the tags
    Console.WriteLine("\tUploading images");
    LoadImagesFromDisk();

    // Images can be uploaded one at a time
    foreach (var image in hemlockImages)
    {
        using (var stream = new MemoryStream(File.ReadAllBytes(image)))
        {
            trainingApi.CreateImagesFromData(project.Id, stream, new List<string>() { hemlockTag.Id.ToString() });
        }
    }

    // Or uploaded in a single batch 
    var imageFiles = japaneseCherryImages.Select(img => new ImageFileCreateEntry(Path.GetFileName(img), File.ReadAllBytes(img))).ToList();
    trainingApi.CreateImagesFromFiles(project.Id, new ImageFileCreateBatch(imageFiles, new List<Guid>() { japaneseCherryTag.Id }));
    ```

* __Treine o classificador__:

    ```csharp
    // Now there are images with tags start training the project
    Console.WriteLine("\tTraining");
    var iteration = trainingApi.TrainProject(project.Id);

    // The returned iteration will be in progress, and can be queried periodically to see when it has completed
    while (iteration.Status == "Completed")
    {
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
    }
    ```

* __Defina uma iteração padrão para o ponto de extremidade de previsão__:

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __Criar um ponto de extremidade de previsão__:
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* __Envie uma imagem para o ponto de extremidade de previsão__:

    ```csharp
    // Make a prediction against the new project
    Console.WriteLine("Making a prediction:");
    var result = endpoint.PredictImage(project.Id, testImage);

    // Loop over each prediction and write out the results
    foreach (var c in result.Predictions)
    {
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
    }
    ```

## <a name="run-the-application"></a>Executar o aplicativo

1. Faça as seguintes alterações para adicionar as chaves de treinamento e previsão ao aplicativo:

    * Adicione sua __chave treinamento__ à linha a seguir:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Adicione sua __chave de previsão__ à linha a seguir:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Execute o aplicativo. À medida que o aplicativo é executado, a saída a seguir é gravada no console:

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. Pressione qualquer tecla para sair do aplicativo.
