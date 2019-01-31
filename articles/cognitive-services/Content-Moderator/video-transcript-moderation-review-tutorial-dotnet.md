---
title: 'Tutorial: moderar vídeos e transcrições no .NET – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Este tutorial ajuda a entender como criar uma solução completa de moderação de vídeo e transcrição com moderação assistida por computador e criação de análise com interação humana.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 601c282e6b3339537c3ad419098f55116a40955f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208867"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Tutorial: moderação de vídeos e transcrições

As APIs de vídeo do Content Moderator permitem moderar vídeos e criar análises de vídeos na ferramenta de análise humana. 

Este tutorial ajuda a entender como criar uma solução completa de moderação de vídeo e transcrição com moderação assistida por computador e criação de análise com interação humana.

Baixe o [aplicativo de console C#](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) para este tutorial. O aplicativo de console usa o SDK e os pacotes relacionados para executar as seguintes tarefas:

- Compactar o(s) vídeo(s) de entrada para processamento mais rápido
- Moderar o vídeo para obter instantâneos e quadros com insights
- Usar os carimbos de data/hora do quadro para criar miniaturas (imagens)
- Enviar carimbos de data/hora e miniaturas para criar análises de vídeos
- Converter a conversação de fala em texto (transcrição) do vídeo com a API do Indexador de Mídia
- Moderar a transcrição com o serviço de moderação de texto
- Adicionar a transcrição moderada à análise de vídeo

## <a name="sample-program-outputs"></a>Saídas do programa de exemplo

Antes de prosseguir, veremos as seguintes saídas de exemplo do programa:

- [Saída do console](#program-output)
- [Análise de vídeo](#video-review-default-view)
- [Exibição de transcrição](#video-review-transcript-view)

## <a name="prerequisites"></a>Pré-requisitos

1. Inscreva-se no site da [ferramenta de análise do Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e [crie marcas personalizadas](Review-Tool-User-Guide/tags.md) que o aplicativo de console C# atribui no código. A tela a seguir mostra as marcas personalizadas.

  ![Marcas personalizadas de moderação de vídeo](images/video-tutorial-custom-tags.png)

1. Para executar o aplicativo de exemplo, você precisa de uma conta do Azure e uma conta dos Serviços de Mídia do Azure. Além disso, é necessário acessar a versão prévia privada do Content Moderator. Por fim, serão necessárias as credenciais de autenticação do Azure Active Directory. Para mais detalhes sobre como obter essas informações, consulte o [início rápido da API de Moderação de Vídeo](video-moderation-api.md).

1. Edite o arquivo `App.config` e adicione o nome do locatário do Active Directory, os ponto de extremidade de serviço e as chaves de assinatura indicados por `#####`. As seguintes informações são necessárias:

|Chave|DESCRIÇÃO|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Ponto de extremidade de API do AMS (Serviços de Mídia do Azure)|
|`ClientSecret`|Chave de assinatura para Serviços de Mídia do Azure|
|`ClientId`|ID do cliente para Serviços de Mídia do Azure|
|`AzureAdTenantName`|Nome do locatário do Active Directory que representa a organização|
|`ContentModeratorReviewApiSubscriptionKey`|Chave de assinatura para a API de análise do Content Moderator|
|`ContentModeratorApiEndpoint`|Ponto de extremidade para a API do Content Moderator|
|`ContentModeratorTeamId`|ID da equipe do Content Moderator|

## <a name="getting-started"></a>Introdução

A classe `Program` em `Program.cs` é o principal ponto de entrada do aplicativo de moderação de vídeo.

### <a name="methods-of-class-program"></a>Métodos de classe do Programa

|Método|DESCRIÇÃO|
|-|-|
|`Main`|Analisa a linha de comando, reúne a entrada do usuário e inicia o processamento.|
|`ProcessVideo`|Comprime, carrega, modera e cria análises de vídeo.|
|`CreateVideoStreamingRequest`|Cria um fluxo para carregar um vídeo|
|`GetUserInputs`|Coleta entrada do usuário: usado quando não há opções de linha de comando presentes|
|`Initialize`|Inicializa objetos necessários para o processo de moderação|

### <a name="the-main-method"></a>O método Principal

`Main()` é onde a execução inicia, então, é o local para começar o reconhecimento do processo de moderação de vídeo.

    static void Main(string[] args)
    {
        if (args.Length == 0)
        {
            string videoPath = string.Empty;
            GetUserInputs(out videoPath);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(Path.GetDirectoryName(videoPath), "log.txt");
            try
            {
                ProcessVideo(videoPath).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
        else
        {
            DirectoryInfo directoryInfo = new DirectoryInfo(args[0]);
            if (args.Length == 2)
                bool.TryParse(args[1], out generateVtt);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(args[0], "log.txt");
            var files = directoryInfo.GetFiles("*.mp4", SearchOption.AllDirectories);
            foreach (var file in files)
            {
                try
                {
                    ProcessVideo(file.FullName).Wait();
                }
                catch (Exception ex)
                {
                    Console.WriteLine(ex.Message);
                }
            }
        }
    }

`Main()` trata dos seguintes argumentos de linha de comando:

- O caminho para um diretório contendo arquivos de vídeo MPEG-4 a serem enviados para moderação. Todos os arquivos `*.mp4` neste diretório e os subdiretórios são submetidos para moderação.
- Opcionalmente, um sinalizador booliano (verdadeiro/falso) indicando se as transcrições de texto devem ser geradas com a finalidade de moderar o áudio.

Se nenhum argumento de linha de comando estiver presente, `Main()` chama `GetUserInputs()`. Esse método solicita que o usuário insira o caminho para um único arquivo de vídeo e especifique se uma transcrição de texto deve ser gerada.

> [!NOTE]
> O aplicativo de console usa a [API do Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) para gerar transcrições da faixa de áudio do vídeo carregado. Os resultados são fornecidos no formato WebVTT. Para obter mais informações sobre esse formato, consulte [Formato de faixas de texto de vídeo na Web](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Métodos Inicializar e Processar Vídeo

Independentemente das opções do programa serem provenientes da linha de comando ou da entrada interativa do usuário, o próximo `Main()` chama `Initialize()` para criar as instâncias a seguir:

|Classe|DESCRIÇÃO|
|-|-|
|`AMSComponent`|Compacta os arquivos de vídeo antes de enviá-los para moderação.|
|`AMSconfigurations`|Realiza interface com os dados de configuração do aplicativo, localizados em `App.config`.|
|`VideoModerator`| Upload, codificação, criptografia e moderação usando SDK do AMS|
|`VideoReviewApi`|Gerencia análises de vídeo no serviço do Content Moderator|

Essas classes (além de `AMSConfigurations`, que são simples) são abordadas com mais detalhes nas próximas seções deste tutorial.

Finalmente, os arquivos de vídeo são processados um de cada vez chamando `ProcessVideo()` para cada um.

    private static async Task ProcessVideo(string videoPath)
    {
        Stopwatch sw = new Stopwatch();
        sw.Start();
        Console.ForegroundColor = ConsoleColor.White;
        Console.WriteLine("\nVideo compression process started...");

        var compressedVideoPath = amsComponent.CompressVideo(videoPath);
        if (string.IsNullOrWhiteSpace(compressedVideoPath))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Compression failed.");
        }

        Console.WriteLine("\nVideo compression process completed...");

        UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
        UploadAssetResult uploadResult = new UploadAssetResult();

        if (generateVtt)
        {
            uploadResult.GenerateVTT = generateVtt;
        }
        Console.WriteLine("\nVideo moderation process started...");

        if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Review process failed.");
        }

        Console.WriteLine("\nVideo moderation process completed...");
        Console.WriteLine("\nVideo review process started...");
        string reviewId = await videoReviewApi.CreateVideoReviewInContentModerator(uploadResult);
        Console.WriteLine("\nVideo review successfully completed...");
        sw.Stop();
        Console.WriteLine("\nTotal Elapsed Time: {0}", sw.Elapsed);
        using (var stw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            stw.WriteLine("Video File Name: " + Path.GetFileName(videoPath));
            stw.WriteLine($"ReviewId: {reviewId}");
            stw.WriteLine("Total Elapsed Time: {0}", sw.Elapsed);
        }
    }


O método `ProcessVideo()` é bastante simples. Ele executa as seguintes operações na ordem:

- Compacta o vídeo
- Carrega o vídeo para um ativo dos Serviços de Mídia do Azure
- Cria um trabalho do AMS para moderar o vídeo
- Cria uma análise de vídeo no Content Moderator

As seções a seguir consideram em mais detalhes alguns dos processos individuais invocados por `ProcessVideo()`. 

## <a name="compressing-the-video"></a>Compactação de vídeo

Para minimizar o tráfego, o aplicativo converte arquivos de vídeo para o formato H.264 (MPEG-4 AVC) e os dimensiona para uma largura máxima de 640 pixels. O codec H.264 é recomendado devido à alta eficiência (taxa de compressão). A compactação é feita usando a ferramenta de linha de comando `ffmpeg` gratuita, incluída na pasta `Lib` da solução do Visual Studio. Os arquivos de entrada podem ser de qualquer formato com suporte de `ffmpeg`, incluindo codecs e formatos de arquivos de vídeo normalmente mais utilizados.

> [!NOTE]
> Ao iniciar o programa usando as opções de linha de comando, você especifica um diretório contendo os arquivos de vídeo a serem enviados para moderação. Todos os arquivos nesse diretório com a extensão de nome do arquivo `.mp4` são processados. Para processar outras extensões de nome de arquivo, atualize o `Main()` método em `Program.cs` para incluir as extensões desejadas.

O código que compacta um único arquivo de vídeo é a `AmsComponent` classe em `AMSComponent.cs`. O método responsável por essa funcionalidade é `CompressVideo()`, mostrado aqui.

    public string CompressVideo(string videoPath)
    {
        string ffmpegBlobUrl;
        if (!ValidatePreRequisites())
        {
            Console.WriteLine("Configurations check failed. Please cross check the configurations!");
            throw new Exception();
        }

        if (File.Exists(_configObj.FfmpegExecutablePath))
        {
            ffmpegBlobUrl = this._configObj.FfmpegExecutablePath;
        }
        else
        {
            Console.WriteLine("ffmpeg.exe is missing. Please check the Lib folder");
            throw new Exception();
        }

        string videoFilePathCom = videoPath.Split('.')[0] + "_c.mp4";
        ProcessStartInfo processStartInfo = new ProcessStartInfo();
        processStartInfo.WindowStyle = ProcessWindowStyle.Hidden;
        processStartInfo.FileName = ffmpegBlobUrl;
        processStartInfo.Arguments = "-i \"" + videoPath + "\" -vcodec libx264 -n -crf 32 -preset veryfast -vf scale=640:-1 -c:a aac -aq 1 -ac 2 -threads 0 \"" + videoFilePathCom + "\"";
        var process = Process.Start(processStartInfo);
        process.WaitForExit();
        process.Close();
        return videoFilePathCom;
    }

O código executa as etapas a seguir:

- Verifica para certificar-se de que a configuração em `App.config` contém todos os dados necessários
- Verifica para certificar-se de que o binário `ffmpeg` está presente
- Compila o nome do arquivo de saída acrescentando `_c.mp4` ao nome de base do arquivo (como `Example.mp4` -> `Example_c.mp4`)
- Cria uma cadeia de caracteres da linha de comando para executar a conversão
- Inicia um processo `ffmpeg` usando a linha de comando
- Aguarda que o vídeo seja processado

> [!NOTE]
> Se você souber que os vídeos já estão compactados usando o H.264 e têm dimensões apropriadas, será possível gravar novamente `CompressVideo()` para ignorar a compactação.

O método retorna o nome do arquivo de saída compactado.

## <a name="uploading-and-moderating-the-video"></a>Carregar e moderar o vídeo

O vídeo deve ser armazenado nos Serviços de Mídia do Azure antes que possa ser processado pelo serviço do Content Moderation. A classe `Program` em `Program.cs` tem um método curto `CreateVideoStreamingRequest()` que retorna um objeto representando a solicitação de streaming usada para enviar o vídeo.

    private static UploadVideoStreamRequest CreateVideoStreamingRequest(string compressedVideoFilePath)
    {
        return
            new UploadVideoStreamRequest
            {
                VideoStream = File.ReadAllBytes(compressedVideoFilePath),
                VideoName = Path.GetFileName(compressedVideoFilePath),
                EncodingRequest = new EncodingRequest()
                {
                    EncodingBitrate = AmsEncoding.AdaptiveStreaming
                },
                VideoFilePath = compressedVideoFilePath
            };
    }

O objeto `UploadVideoStreamRequest` resultante é definido em `UploadVideoStreamRequest.cs` (e o pai, `UploadVideoRequest`, em `UploadVideoRequest.cs`). Essas classes não são mostradas aqui; elas são curtas e servem apenas para manter os dados de vídeo compactados e as respectivas informações do vídeo. Outra classe somente dados, `UploadAssetResult` (`UploadAssetResult.cs`) é usada para manter os resultados do processo de upload. Agora, é possível reconhecer essas linhas em `ProcessVideo()`:

    UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
    UploadAssetResult uploadResult = new UploadAssetResult();

    if (generateVtt)
    {
        uploadResult.GenerateVTT = generateVtt;
    }
    Console.WriteLine("\nVideo moderation process started...");

    if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine("Video Review process failed.");
    }

Essas linhas executam as seguintes tarefas:

- Criar um `UploadVideoStreamRequest` para carregar o vídeo compactado
- Definir o sinalizador `GenerateVTT` da solicitação se o usuário solicitou uma transcrição de texto
- Chamar `CreateAzureMediaServicesJobToModerateVideo()` para executar o upload e receber o resultado

## <a name="deep-dive-into-video-moderation"></a>Aprofundar-se na moderação de vídeo

O método `CreateAzureMediaServicesJobToModerateVideo()` está em `VideoModerator.cs`, que contém a maior parte do código que interage com os Serviços de Mídia do Azure. O código-fonte do método é mostrado na seguinte extração.

    public bool CreateAzureMediaServicesJobToModerateVideo(UploadVideoStreamRequest uploadVideoRequest, UploadAssetResult uploadResult)
    {
        asset = CreateAsset(uploadVideoRequest);
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        // Encoding the asset , Moderating the asset, Generating transcript in parallel
        IAsset encodedAsset = null;
        //Creates the job for the tasks.
        IJob job = this._mediaContext.Jobs.Create("AMS Review Job");

        //Adding encoding task to job.
        ConfigureEncodeAssetTask(uploadVideoRequest.EncodingRequest, job);

        ConfigureContentModerationTask(job);

        //adding transcript task to job.
        if (uploadResult.GenerateVTT)
        {
            ConfigureTranscriptTask(job);
        }

        Stopwatch timer = new Stopwatch();
        timer.Start();
        //submit and execute job.
        job.Submit();
        job.GetExecutionProgressTask(new CancellationTokenSource().Token).Wait();
        timer.Stop();
        using (var sw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            sw.WriteLine("AMS Job Elapsed Time: {0}", timer.Elapsed);
        }

        if (job.State == JobState.Error)
        {
            throw new Exception("Video moderation has failed due to AMS Job error.");
        }

        UploadAssetResult result = uploadResult;
        encodedAsset = job.OutputMediaAssets[0];
        result.ModeratedJson = GetCmDetail(job.OutputMediaAssets[1]);
        // Check for valid Moderated JSON
        var jsonModerateObject = JsonConvert.DeserializeObject<VideoModerationResult>(result.ModeratedJson);

        if (jsonModerateObject == null)
        {
            return false;
        }
        if (uploadResult.GenerateVTT)
        {
            GenerateTranscript(job.OutputMediaAssets.Last());
        }

        uploadResult.StreamingUrlDetails = PublishAsset(encodedAsset);
        string downloadUrl = GenerateDownloadUrl(asset, uploadVideoRequest.VideoName);
        uploadResult.StreamingUrlDetails.DownloadUri = downloadUrl;
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        uploadResult.VideoFilePath = uploadVideoRequest.VideoFilePath;
        return true;
    }

Esse código executa as seguintes tarefas:

- Cria um trabalho do AMS para que o processamento seja feito
- Adiciona tarefas para codificar o arquivo de vídeo, moderando-o e gerando uma transcrição de texto
- Envia o trabalho, carregando o arquivo e iniciando o processamento
- Recupera os resultados de moderação, a transcrição do texto (se solicitado) e outras informações

## <a name="sample-video-moderation-response"></a>Exemplo de resposta de moderação de vídeo

O resultado do trabalho de moderação de vídeo (consulte [início rápido de moderação de vídeo](video-moderation-api.md) é uma estrutura de dados JSON que contém os resultados de moderação. Esses resultados incluem uma divisão dos fragmentos (instantâneos) no vídeo, cada um contendo eventos (recortes) com quadros chave que foram sinalizados para revisão. Cada quadro chave é pontuado pela probabilidade de conter conteúdo para adulto. O exemplo a seguir mostra uma resposta JSON:

    {
        "version": 2,
        "timescale": 90000,
        "offset": 0,
        "framerate": 50,
        "width": 1280,
        "height": 720,
        "totalDuration": 18696321,
        "fragments": [
        {
            "start": 0,
            "duration": 18000
        },
        {
            "start": 18000,
            "duration": 3600,
            "interval": 3600,
            "events": [
            [
            {
                "reviewRecommended": false,
                "adultScore": 0.00001,
                "racyScore": 0.03077,
                "index": 5,
                "timestamp": 18000,
                "shotIndex": 0
            }
            ]
        ]
        },
        {
            "start": 18386372,
            "duration": 119149,
            "interval": 119149,
            "events": [
            [
            {
                "reviewRecommended": true,
                "adultScore": 0.00000,
                "racyScore": 0.91902,
                "index": 5085,
                "timestamp": 18386372,
                "shotIndex": 62
            }
        ]
        ]
        }
    ]
    }

Uma transcrição do áudio do vídeo também é produzida quando o sinalizador `GenerateVTT` é definido.

> [!NOTE]
> O aplicativo de console usa a [API do Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) para gerar transcrições da faixa de áudio do vídeo carregado. Os resultados são fornecidos no formato WebVTT. Para obter mais informações sobre esse formato, consulte [Formato de faixas de texto de vídeo na Web](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).


## <a name="creating-the-human-in-the-loop-review"></a>Criar análise por interação humana

O processo de moderação retorna uma lista de quadros chave do vídeo, juntamente com uma transcrição das faixas de áudio. A próxima etapa é criar uma análise na ferramenta de análise do Content Moderator para moderadores humanos. Voltando ao método `ProcessVideo()` em `Program.cs`, você verá a chamada para o método `CreateVideoReviewInContentModerator()`. Este método está na classe `videoReviewApi`, que está em `VideoReviewAPI.cs`, e é mostrado aqui.

    public async Task<string> CreateVideoReviewInContentModerator(UploadAssetResult uploadAssetResult)
    {
    
        string reviewId = string.Empty;
        List<ProcessedFrameDetails> frameEntityList = framegenerator.CreateVideoFrames(uploadAssetResult);
        string path = uploadAssetResult.GenerateVTT == true ? this._amsConfig.FfmpegFramesOutputPath + Path.GetFileNameWithoutExtension (uploadAssetResult.VideoName) + "_aud_SpReco.vtt" : "";
        TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult();
        
    if (File.Exists(path))
        {
            screenTextResult = await GenerateTextScreenProfanity(reviewId, path, frameEntityList);
            uploadAssetResult.Category1TextScore = screenTextResult.Category1Score;
            uploadAssetResult.Category2TextScore = screenTextResult.Category2Score;
            uploadAssetResult.Category3TextScore = screenTextResult.Category3Score;
            uploadAssetResult.Category1TextTag = screenTextResult.Category1Tag;
            uploadAssetResult.Category2TextTag = screenTextResult.Category2Tag;
            uploadAssetResult.Category3TextTag = screenTextResult.Category3Tag;
        }
        
        var reviewVideoRequestJson = CreateReviewRequestObject(uploadAssetResult, frameEntityList);
        if (string.IsNullOrWhiteSpace(reviewVideoRequestJson))
        {
            throw new Exception("Video review process failed in CreateVideoReviewInContentModerator");
        }
        
        reviewId = JsonConvert.DeserializeObject<List<string>>(ExecuteCreateReviewApi(reviewVideoRequestJson).Result).FirstOrDefault();
        frameEntityList = framegenerator.GenerateFrameImages(frameEntityList, uploadAssetResult, reviewId);
        await CreateAndPublishReviewInContentModerator(uploadAssetResult, frameEntityList, reviewId, path, screenTextResult);
        return reviewId;
    
    }

`CreateVideoReviewInContentModerator()` chama vários outros métodos para executar as seguintes tarefas:

> [!NOTE]
> O aplicativo de console usa a biblioteca [FFmpeg](https://ffmpeg.org/) para gerar miniaturas. Essas miniaturas (imagens) correspondem aos carimbos de data/hora do quadro na [saída de moderação de vídeo](#sample-video-moderation-response).

|Tarefa|Métodos|Arquivo|
|-|-|-|
|Extrair os quadros chave do vídeo e criar miniaturas|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Examine a transcrição de texto, se disponível, para localizar áudio com conteúdo para adulto|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Preparar e enviar uma solicitação de análise de vídeo para inspeção humana|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Exibição padrão de análise de vídeo

A tela a seguir mostra os resultados das etapas anteriores.

![Exibição padrão de análise de vídeo](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Geração de transcrição

Até agora, o código apresentado neste tutorial concentrou-se no conteúdo visual. A análise do conteúdo de fala é um processo separado e opcional que, conforme mencionado, usa uma transcrição gerada a partir do áudio. Agora, é o momento de saber como as transcrições de texto são criadas e usadas no processo de análise. A tarefa de geração de transcrição compete ao serviço do [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content).

O aplicativo executa as seguintes tarefas:

|Tarefa|Métodos|Arquivo|
|-|-|-|
|Determina se as transcrições de texto devem ser geradas|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Em caso afirmativo, envia um trabalho de transcrição como parte da moderação|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Obtém uma cópia local da transcrição|`GenerateTranscript()`|`VideoModerator.cs`|
|Sinaliza quadros do vídeo que contêm áudio inapropriado|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Adiciona os resultados à análise|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Configuração de tarefa

Vamos direto à etapa de envio do trabalho de transcrição. `CreateAzureMediaServicesJobToModerateVideo()` chama (já descrito) `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

A configuração da tarefa de transcrição é lida a partir do arquivo `MediaIndexerConfig.json` na pasta `Lib` da solução. Os ativos do AMS são criados para o arquivo de configuração e para a saída do processo de transcrição. Quando o trabalho do AMS é executado, essa tarefa cria uma transcrição de texto da faixa de áudio do arquivo de vídeo.

> [!NOTE]
> O aplicativo de exemplo reconhece a fala apenas em inglês dos EUA.

### <a name="transcript-generation"></a>Geração de transcrição

A transcrição é publicada como um ativo do AMS. Para examinar a transcrição de conteúdo censurável, o aplicativo baixa o ativo dos Serviços de Mídia do Azure. `CreateAzureMediaServicesJobToModerateVideo()` chama `GenerateTranscript()`, mostrado aqui, para recuperar o arquivo.

    public bool GenerateTranscript(IAsset asset)
    {
        try
        {
            var outputFolder = this._amsConfigurations.FfmpegFramesOutputPath;
            IAsset outputAsset = asset;
            IAccessPolicy policy = null;
            ILocator locator = null;
            policy = _mediaContext.AccessPolicies.Create("My 30 days readonly policy", TimeSpan.FromDays(360), AccessPermissions.Read);
            locator = _mediaContext.Locators.CreateLocator(LocatorType.Sas, outputAsset, policy, DateTime.UtcNow.AddMinutes(-5));
            DownloadAssetToLocal(outputAsset, outputFolder);
            locator.Delete();
            return true;
        }
        catch
        {   //TODO:  Logging
            Console.WriteLine("Exception occurred while generating index for video.");
            throw;
        }
    }

Após algumas configurações necessárias do AMS, o download real é executado chamando `DownloadAssetToLocal()`, uma função genérica que copia um ativo do AMS para um arquivo local.

## <a name="transcript-moderation"></a>Moderação de transcrição

Com a transcrição em mãos, ela é examinada e utilizada na análise. A criação da análise é competência de `CreateVideoReviewInContentModerator()`, que chama `GenerateTextScreenProfanity()` para realizar o trabalho. Por sua vez, esse método chama `TextScreen()`, que contém a maior parte da funcionalidade. 

`TextScreen()` realiza as seguintes tarefas:

- Analisa a transcrição para legendas e carimbos de data/hora
- Envia cada legenda para moderação de texto
- Sinaliza quaisquer quadros que possam ter conteúdo de fala censurável

Examinaremos cada uma dessas tarefas detalhadamente:

### <a name="initialize-the-code"></a>Inicializar o código

Primeiro, inicialize todas as variáveis e coleções.

    private async Task<TranscriptScreenTextResult> TextScreen(string filepath, List<ProcessedFrameDetails> frameEntityList)
    {
        List<TranscriptProfanity> profanityList = new List<TranscriptProfanity>();
        string responseContent = string.Empty;
        HttpResponseMessage response;
        bool category1Tag = false;
        bool category2Tag = false;
        bool category3Tag = false;
        double category1Score = 0;
        double category2Score = 0;
        double category3Score = 0;
        List<string> vttLines = File.ReadAllLines(filepath).Where(line => !line.Contains("NOTE Confidence:") && line.Length > 0).ToList();
        StringBuilder sb = new StringBuilder();
        List<CaptionScreentextResult> csrList = new List<CaptionScreentextResult>();
        CaptionScreentextResult captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };

        // Code from the next sections in the tutorial
    

### <a name="parse-the-transcript-for-captions"></a>Analisar a transcrição de legendas

Em seguida, analise a transcrição formatada de VTT para legendas e carimbos de data/hora. A ferramenta de análise exibe essas legendas na Guia Transcrição na tela de análise de vídeo. Os carimbos de data/hora são usados para sincronizar as legendas com os quadros de vídeo correspondentes.

        // Code from the previous section(s) in the tutorial

        //
        // Parse the transcript
        //
        foreach (var line in vttLines.Skip(1))
        {
                if (line.Contains("-->"))
                {
                    if (sb.Length > 0)
                    {
                        captionScreentextResult.Captions.Add(sb.ToString());
                        sb.Clear();
                    }
                    if (captionScreentextResult.Captions.Count > 0)
                    {
                        csrList.Add(captionScreentextResult);
                        captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };
                    }
                    string[] times = line.Split(new string[] { "-->" }, StringSplitOptions.RemoveEmptyEntries);
                    string startTimeString = times[0].Trim();
                    string endTimeString = times[1].Trim();
                    int startTime = (int)TimeSpan.ParseExact(startTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    int endTime = (int)TimeSpan.ParseExact(endTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    captionScreentextResult.StartTime = startTime;
                    captionScreentextResult.EndTime = endTime;
                }
                else
                {
                    sb.Append(line);
                }
                if (sb.Length + line.Length > 1024)
                {
                    captionScreentextResult.Captions.Add(sb.ToString());
                    sb.Clear();
                }
            }
            if (sb.Length > 0)
            {
                captionScreentextResult.Captions.Add(sb.ToString());
            }
            if (captionScreentextResult.Captions.Count > 0)
            {
                csrList.Add(captionScreentextResult);
            }

            // Code from the following section in the quickstart

### <a name="moderate-captions-with-the-text-moderation-service"></a>Moderar legendas com o serviço de moderação de texto

Em seguida, examinaremos as legendas de texto analisadas com a API de Texto do Content Moderator.

> [!NOTE]
> A chave de serviço do Content Moderator possui um limite de taxa de RPS (solicitações por segundo). Se exceder o limite, o SDK lançará uma exceção com um código de erro 429. 
>
> Uma chave de camada gratuita tem um limite de taxa de RPS.

    //
    // Moderate the captions or cues
    //
    int waitTime = 1000;
    foreach (var csr in csrList)
    {
                bool captionAdultTextTag = false;
                bool captionRacyTextTag = false;
                bool captionOffensiveTextTag = false;
                bool retry = true;

                foreach (var caption in csr.Captions)
                {
                    while (retry)
                    {
                        try
                        {
                            System.Threading.Thread.Sleep(waitTime);
                            var lang = await CMClient.TextModeration.DetectLanguageAsync("text/plain", caption);
                            var oRes = await CMClient.TextModeration.ScreenTextWithHttpMessagesAsync(lang.DetectedLanguageProperty, "text/plain", caption, null, null, null, true);
                            response = oRes.Response;
                            responseContent = await response.Content.ReadAsStringAsync();
                            retry = false;
                        }
                        catch (Exception e)
                        {
                            if (e.Message.Contains("429"))
                            {
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                                waitTime = (int)(waitTime * 1.5);
                                Console.WriteLine($"wait time: {waitTime}");
                            }
                            else
                            {
                                retry = false;
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                            }
                        }
                    }
                    var jsonTextScreen = JsonConvert.DeserializeObject<TextScreen>(responseContent);
                    if (jsonTextScreen != null)
                    {
                        TranscriptProfanity transcriptProfanity = new TranscriptProfanity();
                        transcriptProfanity.TimeStamp = "";
                        List<Terms> transcriptTerm = new List<Terms>();
                        if (jsonTextScreen.Terms != null)
                        {
                            foreach (var term in jsonTextScreen.Terms)
                            {
                                var profanityobject = new Terms
                                {
                                    Term = term.Term,
                                    Index = term.Index
                                };
                                transcriptTerm.Add(profanityobject);
                            }
                            transcriptProfanity.Terms = transcriptTerm;
                            profanityList.Add(transcriptProfanity);
                        }
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) captionAdultTextTag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) captionRacyTextTag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) captionOffensiveTextTag = true;
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) category1Tag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) category2Tag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) category3Tag = true;
                        category1Score = jsonTextScreen.Classification.Category1.Score > category1Score ? jsonTextScreen.Classification.Category1.Score : category1Score;
                        category2Score = jsonTextScreen.Classification.Category2.Score > category2Score ? jsonTextScreen.Classification.Category2.Score : category2Score;
                        category3Score = jsonTextScreen.Classification.Category3.Score > category3Score ? jsonTextScreen.Classification.Category3.Score : category3Score;
                    }
                    foreach (var frame in frameEntityList.Where(x => x.TimeStamp >= csr.StartTime && x.TimeStamp <= csr.EndTime))
                    {
                        frame.IsAdultTextContent = captionAdultTextTag;
                        frame.IsRacyTextContent = captionRacyTextTag;
                        frame.IsOffensiveTextContent = captionOffensiveTextTag;
                    }
                }
            }
            TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult()
            {
                TranscriptProfanity = profanityList,
                Category1Tag = category1Tag,
                Category2Tag = category2Tag,
                Category3Tag = category3Tag,
                Category1Score = category1Score,
                Category2Score = category2Score,
                Category3Score = category3Score
            };
            return screenTextResult;
    }

### <a name="breaking-down-the-text-moderation-step"></a>Dividir a etapa de moderação de texto

`TextScreen()` é um método substancial, então, vamos dividi-lo.

1. Primeiro, o método lê o arquivo de transcrição linha por linha. Ele ignora linhas e linhas em branco contendo um `NOTE` com uma pontuação de confiança. Ele extrai os carimbos de data/hora e os itens de texto das *indicações* no arquivo. Uma indicação representa o texto da faixa de áudio e inclui horas de início e término. Uma indicação começa com a linha de carimbo de data/hora com a cadeia de caracteres `-->`. É acompanhada por uma ou mais linhas de texto.

1. Instâncias de `CaptionScreentextResult` (definido em `TranscriptProfanity.cs`) são usadas para manter as informações analisadas de cada indicação.  Quando uma nova linha de carimbo de data/hora é detectada, ou um comprimento máximo de texto de 1024 caracteres é alcançado, um novo `CaptionScreentextResult` é adicionado a `csrList`. 

1. O método seguinte envia cada indicação para a API de Moderação de Texto. Ele chama ambos `ContentModeratorClient.TextModeration.DetectLanguageAsync()` e `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, que são definidos no assembly `Microsoft.Azure.CognitiveServices.ContentModerator`. Para evitar ser limitado por taxa, o método pausará por um segundo antes de enviar cada indicação.

1. Após receber os resultados do serviço de Moderação de Texto, o método os analisa para verificar se atendem aos limites de confiança. Esses valores são estabelecidos em `App.config` como `OffensiveTextThreshold`, `RacyTextThreshold` e `AdultTextThreshold`. Por fim, os próprios termos censuráveis também são armazenados. Todos os quadros dentro do intervalo de tempo da indicação são marcados como contendo texto para adulto, ofensivo e/ou inadequado.

1. `TextScreen()` retorna um `TranscriptScreenTextResult` que contém o resultado de moderação de texto do vídeo como um todo. Esse objeto inclui sinalizadores e pontuações para os vários tipos de conteúdo censurável, junto com uma lista de todos os termos censuráveis. O chamador, `CreateVideoReviewInContentModerator()` chama `UploadScreenTextResult()` para anexar essas informações à análise, de modo que esteja disponível para revisores humanos.
 
## <a name="video-review-transcript-view"></a>Exibição de transcrição de análise de vídeo

A tela a seguir mostra o resultado das etapas de geração e moderação da transcrição.

![Exibição de transcrição de moderação de vídeo](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Saída do programa

A seguinte saída da linha de comando do programa mostra as várias tarefas à medida que são concluídas. Além disso, o resultado de moderação (no formato JSON) e a transcrição de fala estão disponíveis no mesmo diretório dos arquivos de vídeo originais.

    Microsoft.ContentModerator.AMSComponentClient
    Enter the fully qualified local path for Uploading the video :
    "Your File Name.MP4"
    Generate Video Transcript? [y/n] : y
    
    Video compression process started...
    Video compression process completed...
    
    Video moderation process started...
    Video moderation process completed...
    
    Video review process started...
    Video Frames Creation inprogress...
    Frames(83) created successfully.
    Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
    Video review successfully completed...
    
    Total Elapsed Time: 00:05:56.8420355


## <a name="next-steps"></a>Próximas etapas

[Baixar a solução do Visual Studio](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp), os arquivos de exemplo e as bibliotecas necessárias para este tutorial e começar a integração.
