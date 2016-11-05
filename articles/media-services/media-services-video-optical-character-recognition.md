---
title: Usar a Análise de Mídia do Azure para converter o conteúdo de texto em arquivos de vídeo em texto digital | Microsoft Docs
description: O OCR (reconhecimento óptico de caracteres) da Análise de Mídia do Azure permite que você converta o conteúdo de texto de arquivos de vídeo em texto digital editável e pesquisável.  Isso permite que você automatize a extração de metadados significativos do sinal de vídeo de sua mídia.
services: media-services
documentationcenter: ''
author: juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako

---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Usar a Análise de Mídia do Azure para converter o conteúdo de texto em arquivos de vídeo em texto digital
## <a name="overview"></a>Visão geral
Se for necessário extrair o conteúdo de texto de seus arquivos de vídeo e gerar um texto digital editável e pesquisável, você deverá usar o OCR (reconhecimento óptico de caracteres) da Análise de Mídia do Azure. Esse Processador de Mídia do Azure detecta o conteúdo de texto em seus arquivos de vídeo e gera arquivos de texto para seu uso. O OCR permite que você automatize a extração de metadados significativos do sinal de vídeo de sua mídia.

Quando usado em conjunto com um mecanismo de pesquisa, você pode facilmente indexar sua mídia por texto e melhorar a capacidade de descoberta do seu conteúdo. Isso é extremamente útil em um vídeo altamente textual, como uma gravação de vídeo ou uma captura de tela de uma apresentação de slides. O processador de mídia de OCR do Azure é otimizado para texto digital.

O processador de mídia de **OCR de Mídia do Azure** atualmente está em Preview.

Este tópico fornece detalhes sobre o **OCR de Mídia do Azure** e mostra como usá-lo com o SDK dos Serviços de Mídia para .NET. Para obter informações e exemplos adicionais, consulte [este blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Arquivos de entrada de OCR
Arquivos de vídeo. Atualmente, há suporte para os seguintes formatos: MP4, MOV e WMV.

## <a name="task-configuration"></a>Configuração de tarefa
Configuração de tarefa (predefinição). Ao criar uma tarefa com o **OCR de Mídia do Azure**, é necessário especificar uma predefinição de configuração usando JSON ou XML. 

### <a name="attribute-descriptions"></a>Descrições de atributos
| Nome do atributo | Descrição |
| --- | --- |
| Linguagem |(opcional) descreve o idioma do texto a ser procurado. Um dos seguintes: AutoDetect (padrão), Arabic, ChineseSimplified, ChineseTraditional, Czech Danish, Dutch, English, Finnish, French, German, Greek, Hungarian, Italian, Japanese, Korean, Norwegian, Polish, Portuguese, Romanian, Russian, SerbianCyrillic, SerbianLatin, Slovak, Spanish, Swedish, Turkish. |
| TextOrientation |(opcional) descreve a orientação do texto a ser procurado.  "Left" significa que a parte superior de todas as letras apontam para a esquerda.  O texto padrão (como aquele que pode ser encontrado em um livro), tem a orientação “Up”.  Um dos seguintes: AutoDetect (padrão), Up, Right, Down, Left. |
| TimeInterval |(opcional) descreve a taxa de amostragem.  O padrão é a cada 1/2 segundo.<br/>Formato JSON – HH:mm:ss.SSS (padrão 00:00:00.500)<br/>Formato XML: duração primitiva do W3C XSD (padrão PT0.5) |
| DetectRegions |(opcional) Uma matriz de objetos DetectRegion especificando regiões dentro do quadro de vídeo para detectar o texto.<br/>Um objeto DetectRegion é composto pelos quatro seguintes valores inteiros:<br/>Left: pixels a partir da margem esquerda<br/>Top: pixels a partir da margem superior<br/>Width: altura da região em pixels<br/>Height: altura da região em pixels |

#### <a name="json-preset-example"></a>Exemplo de predefinição JSON
    {
        'Version':'1.0', 
        'Options': 
        {
        'Language':'English', 
            'TimeInterval':'00:00:01.5',
            'DetectRegions': 
             [
                {'Left':'1','Top':'1','Width':'1','Height':'1'},
                {'Left':'2','Top':'2','Width':'2','Height':'2'}
             ],
            'TextOrientation':'Up'
        }
    }

#### <a name="xml-preset-example"></a>Exemplo de predefinição XML
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>1</Left>
                   <Top>1</Top>
                   <Width>1</Width>
                   <Height>1</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>

## <a name="ocr-output-files"></a>Arquivos de saída de OCR
A saída do processador de mídia de OCR é um arquivo JSON.

### <a name="elements-of-the-output-json-file"></a>Elementos do arquivo JSON de saída
A saída de OCR de vídeo fornece dados segmentados por tempo sobre os caracteres encontrados no vídeo.  Você pode usar atributos como idioma ou orientação para se concentrar exatamente nas palavras em que está interessado em analisar. 

A saída contém os seguintes atributos:

| Elemento | Descrição |
| --- | --- |
| Escala de tempo |"Tiques" por segundo do vídeo |
| Deslocamento |diferença de tempo para carimbos de data/hora. Na versão 1.0 das APIs de Vídeo, sempre será 0. |
| Taxa de quadros |Quadros por segundo do vídeo |
| width |largura do vídeo em pixels |
| height |altura do vídeo em pixels |
| Fragmentos |matriz de partes com base em tempo do vídeo nas quais os metadados estão em bloco |
| iniciar |hora de início de um fragmento em "tiques" |
| duration |duração de um fragmento em "tiques" |
| intervalo |intervalo de cada evento dentro do fragmento determinado |
| events |matriz que contém regiões |
| region |objeto representando palavras ou frases detectadas |
| Linguagem |idioma do texto detectado dentro de uma região |
| orientation |orientação do texto detectado dentro de uma região |
| lines |matriz de linhas de texto detectadas em uma região |
| texto |o texto real |

### <a name="json-output-example"></a>Exemplo de saída JSON
O exemplo de saída a seguir contém as informações gerais de vídeo e vários fragmentos de vídeo. Em cada fragmento de vídeo, ele contém todas as regiões que são detectadas pelo pacote de gerenciamento de OCR com o idioma e sua orientação de texto. A região também contém todas as linhas de palavras nessa região com texto da linha, posição da linha e todas as informações de palavra (conteúdo, posição e confiança da palavra) nesta linha. A seguir está um exemplo e coloquei alguns comentários embutidos.

    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }

## <a name="sample-code"></a>Exemplo de código
O programa a seguir mostra como:

1. Criar um ativo e carregar um arquivo de mídia nesse ativo.
2. Criar um trabalho com um arquivo de configuração/predefinição de OCR.
3. Baixar os arquivos JSON de saída. 
   
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
   
        namespace OCR
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
   
                    // Run the OCR job.
                    var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                                @"C:\supportFiles\OCR\config.json");
   
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
                }
   
                static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My OCR Input Asset",
                        AssetCreationOptions.None);
   
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My OCR Job");
   
                    // Get a reference to Azure Media OCR.
                    string MediaProcessorName = "Azure Media OCR";
   
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
   
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
   
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My OCR Task",
                        processor,
                        configuration,
                        TaskOptions.None);
   
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
   
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);
   
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

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Links relacionados
[Visão geral do Azure Media Services Analytics](media-services-analytics-overview.md)

<!--HONumber=Oct16_HO2-->


