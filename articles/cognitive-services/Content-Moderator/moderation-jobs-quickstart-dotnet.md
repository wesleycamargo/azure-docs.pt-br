---
title: Content Moderator Azure - Iniciar trabalhos de moderação usando .NET | Microsoft Docs
description: Como iniciar trabalhos de moderação usando o SDK do Content Moderator do Azure para .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/06/2018
ms.author: sajagtap
ms.openlocfilehash: a103875607355993e216ce1ddea02009fc8fa1c4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363455"
---
# <a name="start-moderation-jobs-using-net"></a>Iniciar trabalhos de moderação usando .NET

Este artigo fornece informações e exemplos de código para ajudá-lo a começar a usar o SDK do Content Moderator para .NET para:
 
- Iniciar um trabalho de moderação para verificar e criar análises para moderadores humanos
- Obter o status da análise pendente
- Controlar e obter o status final da revisão
- Enviar o resultado para a Url de retorno de chamada

Este artigo pressupõe que você já está familiarizado com Visual Studio e C#.

## <a name="sign-up-for-content-moderator-services"></a>Inscrever-se nos serviços do Content Moderator

Antes de usar os serviços do Content Moderator por meio da API REST ou o SDK, você precisa de uma chave de assinatura.
Consulte o [Início Rápido](quick-start.md) para saber como você pode obter a chave.

## <a name="define-a-custom-moderation-workflow"></a>Definir um fluxo de trabalho de moderação personalizado

Um trabalho de moderação examina o conteúdo usando as APIs e usa um **fluxo de trabalho** para determinar se deseja criar análises ou não.
Enquanto a ferramenta de análise contém um fluxo de trabalho padrão, vamos [definir um fluxo de trabalho personalizado](Review-Tool-User-Guide/Workflows.md) para este guia de início rápido.

Você pode usar o nome do fluxo de trabalho em seu código que inicia o trabalho de moderação.

## <a name="create-your-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Adicione um novo projeto de **Aplicativo do console (.NET Framework)** à solução.

   No código de exemplo, nomeie o projeto **CreateReviews**.

1. Selecione esse projeto como o único projeto de inicialização para a solução.

1. Adicione uma referência para o assembly de projeto **ModeratorHelper** que você criou no [início rápido do auxiliar de cliente do Content Moderator](content-moderator-helper-quickstart-dotnet.md).

### <a name="install-required-packages"></a>Instalar os pacotes necessários

Instale os seguintes pacotes NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Atualizar as instruções de uso do programa

Modifique as instruções de uso do programa.

    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using ModeratorHelper;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="initialize-application-specific-settings"></a>Inicializar as configurações específicas do aplicativo

Adicione as seguintes constantes e campos estáticos à classe **Programa** em Program.cs.

> [!NOTE]
> Você define a constante TeamName para o nome usado ao criar a assinatura do Content Moderator. Você pode recuperar o TeamName no [site do Content Moderator](https://westus.contentmoderator.cognitive.microsoft.com/).
> Depois de fazer logon, selecione **Credenciais** do menu **Configurações** (engrenagem).
>
> O nome da equipe é o valor do campo **ID** na seção **API**.


    /// <summary>
    /// The moderation job will use this workflow that you defined earlier.
    /// See the quickstart article to learn how to setup custom workflows.
    /// </summary>
    private const string WorkflowName = "OCR";
    
    /// <summary>
    /// The name of the team to assign the job to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Conent Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "***";

    /// <summary>
    /// The URL of the image to create a review job for.
    /// </summary>
    private const string ImageUrl =
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are ralative the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Revies show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Adicionar código para moderar automaticamente, criar uma análise e obter os detalhes do trabalho

> [!Note]
> Na prática, você configura a URL de retorno de chamada **CallbackEndpoint** para a URL que recebe os resultados da análise manual (por meio de uma solicitação HTTP POST).

Comece adicionando o seguinte código ao método **principal**.

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the nameof the workflow defined in the online review tool.
            // See the quickstart article to learn more.
            var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                    TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

            // Record the job ID.
            var jobId = jobResult.Result.Body.JobIdProperty;

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                jobResult.Result.Body, Formatting.Indented));

            Thread.Sleep(2000);
            writer.WriteLine();

            writer.WriteLine("Get review job status.");
            var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                    TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                    jobDetails.Result.Body, Formatting.Indented));

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
            Thread.Sleep(latencyDelay * 1000);

            writer.WriteLine("Get review details.");
            jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
            TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
            jobDetails.Result.Body, Formatting.Indented));
        }
        writer.Flush();
        writer.Close();
    }

> [!NOTE]
> A chave de serviço do Content Moderator possui um limite de taxa de RPS (solicitações por segundo). Se exceder o limite, o SDK lançará uma exceção com um código de erro 429. 
>
> Uma chave de camada gratuita tem um limite de taxa de RPS.

## <a name="run-the-program-and-review-the-output"></a>Executar o programa e analisar a saída

Você vê a saída de exemplo a seguir no console:

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Entre na ferramenta de análise do Content Moderator para ver a análise de imagem pendente.

Use o botão **Avançar** para enviar.

![Análise de imagem para moderadores humanos](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Consulte a saída de exemplo no arquivo de log

> [!NOTE]
> Em seu arquivo de saída, as cadeias de caracteres **TeamName**, **ContentId**, **CallBackEndpoint**, e **WorkflowId** refletem os valores usados anteriormente.

    Create moderation job for an image.
    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

    Get review details.
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": [
        {
            "Ts": "2018-01-07T00:38:26.7714671",
            "Msg": "Successfully got hasText response from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:26.4181346",
            "Msg": "Getting hasText from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:25.5122828",
            "Msg": "Starting Execution - Try 1"
        }
        ]
    }


## <a name="your-callback-url-if-provided-receives-this-response"></a>A URL de retorno de chamada, se fornecida, receberá essa resposta.

Você visualiza uma resposta como o exemplo a seguir:

> [!NOTE]
> Na sua resposta de retorno de chamada, as cadeias de cadeia de caracteres **ContentId** e **WorkflowId** refletem os valores que você usou anteriormente.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
        "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
        "WorkFlowId": "OCR",
        "Status": "Complete",
        "ContentType": "Image",
        "CallBackType": "Job",
        "ContentId": "contentID",
        "Metadata": {
            "hastext": "True",
            "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
            "imagename": "contentID"
        }
    }


## <a name="next-steps"></a>Próximas etapas

[Baixar a solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros guias de início rápido do Content Moderator para .NET e começar a integração.
