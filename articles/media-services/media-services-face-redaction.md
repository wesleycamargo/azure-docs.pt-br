---
title: "Edição facial com o Azure Media Analytics | Microsoft Docs"
description: "Este tópico demonstra como editar rostos com o Azure Media Analytics."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 5b6d8b8c-5f4d-4fef-b3d6-dc22c6b5a0f5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/12/2016
ms.author: juliako;
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a272384d76baf2d8c22b612d6a736fa1fae377e5


---
# <a name="face-redaction-with-azure-media-analytics"></a>Edição facial com o Azure Media Analytics
## <a name="overview"></a>Visão geral
**Azure Media Redactor** é um MP (processador de mídia) do [Azure Media Analytics](media-services-analytics-overview.md) que oferece edição facial escalonável na nuvem. A edição facial permite que você modifique seu vídeo para desfocar rostos de pessoas selecionadas. Você pode querer usar o serviço de edição facial em cenários de segurança pública e de notícias veiculadas. Alguns minutos de vídeo com vários rostos podem levar horas para serem editados manualmente, mas, com esse serviço, o processo de edição facial exigirá apenas algumas etapas simples. Para saber mais, confira [este](https://azure.microsoft.com/blog/azure-media-redactor/)blog.

Este tópico fornece detalhes sobre o **Azure Media Redactor** e mostra como usá-lo com o SDK dos Serviços de Mídia para .NET.

No momento, o MP do **Azure Media Redactor** está em versão de Visualização.

## <a name="face-redaction-modes"></a>Modos de edição facial
A edição facial trabalha detectando rostos em cada quadro de vídeo e controlando o objeto de rosto para frente e para trás no tempo, para que a mesma pessoa possa ser desfocada também de outros ângulos. O processo de edição automatizada é muito complexo e nem sempre produz 100% do resultado desejado. Portanto, o Media Analytics oferece duas maneiras de modificar o resultado final.

Além de um modo totalmente automatizado, há um fluxo de trabalho de duas etapas que permite a seleção de rostos e a desmarcação de rostos selecionados usando uma lista de IDs. Além disso, para fazer ajustes avulsos por quadro, o MP usa um arquivo de metadados no formato JSON. Esse fluxo de trabalho é dividido nos modos **Analisar** e **Editar**. Você pode combinar os dois modos em uma única passagem que executa ambas as tarefas em um mesmo trabalho. Esse modo é chamado **Combinado**.

### <a name="combined-mode"></a>Modo Combinado
Esse procedimento produzirá um mp4 editado automaticamente sem qualquer entrada manual.

| Estágio | Nome do Arquivo | Observações |
| --- | --- | --- |
| Ativo de entrada |foo.bar |Vídeo em formato WMV, MOV ou MP4 |
| Configuração de entrada |Predefinição de configuração de tarefa |{'version':'1.0', 'options': {'mode':'combined'}} |
| Ativo de saída |foo_redacted.mp4 |Vídeo com desfoque aplicado |

#### <a name="input-example"></a>Exemplo de entrada:
[veja este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Exemplo de saída:
[veja este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Modo Analisar
A etapa **Analisar** do fluxo de trabalho de duas etapas utiliza uma entrada de vídeo e produz um arquivo JSON com a localização dos rostos, e imagens jpg de cada rosto detectado.

| Estágio | Nome do Arquivo | Observações |
| --- | --- | --- |
| Ativo de entrada |foo.bar |Vídeo em formato WMV, MPV ou MP4 |
| Configuração de entrada |Predefinição de configuração de tarefa |{'version':'1.0', 'options': {'mode':'analyze'}} |
| Ativo de saída |foo_annotations.json |Dados de anotação da localização dos rostos no formato JSON. Isso pode ser editado pelo usuário para modificar as caixas delimitadoras de desfoque. Confira o exemplo abaixo. |
| Ativo de saída |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Um jpg cortado de cada rosto detectado, em que o número indica o labelId do rosto |

#### <a name="output-example"></a>Exemplo de saída:
    {
      "version": 1,
      "timescale": 50,
      "offset": 0,
      "framerate": 25.0,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 2,
          "interval": 2,
          "events": [
            [  
              {
                "id": 1,
                "x": 0.306415737,
                "y": 0.03199235,
                "width": 0.15357475,
                "height": 0.322126418
              },
              {
                "id": 2,
                "x": 0.5625317,
                "y": 0.0868245438,
                "width": 0.149155334,
                "height": 0.355517566
              }
            ]
          ]
        },

… truncado

### <a name="redact-mode"></a>Modo Edição
A segunda etapa do fluxo de trabalho tem um grande número de entradas que precisam ser combinadas em um único ativo.

Isso inclui uma lista de IDs a serem desfocados, o vídeo original e o JSON de anotações. Esse modo usa as anotações para aplicar desfoque ao vídeo de entrada.

O resultado da etapa Analisar não inclui o vídeo original. O vídeo precisa ser carregado no ativo de entrada para a tarefa do modo Editar e selecionado como o arquivo primário.

| Estágio | Nome do Arquivo | Observações |
| --- | --- | --- |
| Ativo de entrada |foo.bar |Vídeo em formato WMV, MPV ou MP4. O mesmo vídeo da etapa 1. |
| Ativo de entrada |foo_annotations.json |arquivo de metadados de anotações da fase 1, com modificações opcionais. |
| Ativo de entrada |foo_IDList.txt (opcional) |Nova lista opcional separada por linhas de IDs de rostos para editar. Se deixado em branco, todas as faces são desfocadas. |
| Configuração de entrada |Predefinição de configuração de tarefa |{'version':'1.0', 'options': {'mode':'redact'}} |
| Ativo de saída |foo_redacted.mp4 |Vídeo com desfoque aplicado com base nas anotações |

#### <a name="example-output"></a>Saída de exemplo
É a saída de uma IDList com uma ID selecionada.

[veja este vídeo](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

## <a name="attribute-descriptions"></a>Descrições de atributos
O MP de edição fornece detecção facial e acompanhamento de alta precisão local de até 64 rostos humanos em um quadro de vídeo. Faces frontais fornecem os melhores resultados, enquanto as faces laterais e faces pequenas (menores ou iguais a 24x24 pixels) têm resultados menos precisos.

As faces detectadas e acompanhadas retornam com coordenadas que indicam o local das faces, bem como um número de identificação da face indicando o acompanhamento dessa pessoa. Os números de identificação de face estão sujeitos à redefinição em circunstâncias nas quais a face frontal é perdida ou sobreposta no quadro, resultando na atribuição de várias IDs às mesmas pessoas.

Para obter explicações detalhadas dos atributos, confira o tópico [Detectar rostos e emoções com o Azure Media Analytics](media-services-face-and-emotion-detection.md) .

## <a name="sample-code"></a>Exemplo de código
O programa a seguir mostra como:

1. Criar um ativo e carregar um arquivo de mídia nesse ativo.
2. Criar um trabalho com uma tarefa de edição facial baseada em um arquivo de configuração que contém a predefinição de JSON a seguir. 
   
        {'version':'1.0', 'options': {'mode':'combined'}}
3. Baixe os arquivos JSON de saída. 
   
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
   
        namespace FaceRedaction
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
   
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
   
                static void Main(string[] args)
                {
   
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
   
                    // Run the FaceRedaction job.
                    var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                                                @"C:\supportFiles\FaceRedaction\config.json");
   
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
                }
   
                static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My Face Redaction Input Asset",
                        AssetCreationOptions.None);
   
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My Face Redaction Job");
   
                    // Get a reference to Azure Media Redactor.
                    string MediaProcessorName = "Azure Media Redactor";
   
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
   
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
   
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My Face Redaction Task",
                        processor,
                        configuration,
                        TaskOptions.None);
   
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
   
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);
   
                    // Use the following event handler to check job progress.  
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
   
                    // Launch the job.
                    job.Submit();
   
                    // Check job execution and wait for job to finish.
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
   
                    progressJobTask.Wait();
   
                    // If job state is Error, the event handling
                    // method for job progress should log errors.  Here we check
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                        Console.WriteLine(string.Format("Error: {0}. {1}",
                                                        error.Code,
                                                        error.Message));
                        return null;
                    }
   
                    return job.OutputMediaAssets[0];
                }
   
                static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
                {
                    IAsset asset = _context.Assets.Create(assetName, options);
   
                    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                    assetFile.Upload(filePath);
   
                    return asset;
                }
   
                static void DownloadAsset(IAsset asset, string outputDirectory)
                {
                    foreach (IAssetFile file in asset.AssetFiles)
                    {
                        file.Download(Path.Combine(outputDirectory, file.Name));
                    }
                }
   
                static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors
                        .Where(p => p.Name == mediaProcessorName)
                        .ToList()
                        .OrderBy(p => new Version(p.Version))
                        .LastOrDefault();
   
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor",
                                                                   mediaProcessorName));
   
                    return processor;
                }
   
                static private void StateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
   
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished.");
                            Console.WriteLine();
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:
                            // Cast sender as a job.
                            IJob job = (IJob)sender;
                            // Display or log error details as needed.
                            // LogJobStop(job.Id);
                            break;
                        default:
                            break;
                    }
                }
   
            }
        }

## <a name="next-step"></a>Próxima etapa
Revise os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Links relacionados
[Visão geral do Azure Media Services Analytics](media-services-analytics-overview.md)

[Demonstrações do Azure Media Analytics](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)




<!--HONumber=Nov16_HO3-->


