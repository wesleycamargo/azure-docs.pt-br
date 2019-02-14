---
title: Trabalhos de moderação e revisões humanas no loop – Content Moderator
titlesuffix: Azure Cognitive Services
description: Combine moderação assistida por computador com recursos com humanos no circuito usando a API de Análise do Content Moderator do Azure para obter os melhores resultados para a sua empresa.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 21d71110853c5f18b0b5f0b51d30110eb45ff54a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55862693"
---
# <a name="content-moderation-jobs-and-reviews"></a>Revisões e trabalhos de moderação de conteúdo

Combine moderação assistida por computador com recursos human-in-the-loop usando a [API de Análise](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) do Content Moderator do Azure para obter os melhores resultados para a sua empresa.

A API de Análise oferece as seguintes maneiras de incluir a supervisão humana no processo de moderação de conteúdo:

* As operações `Job` são usadas para iniciar a moderação assistida por computador e a criação de análise humana como uma etapa.
* As operações `Review` são usadas para criação de análise humana, fora da etapa de moderação.
* As operações `Workflow` são usadas para gerenciar fluxos de trabalho que automatizam a verificação com limites para a criação de análise.

As operações `Job` e `Review` aceitam seus pontos de extremidade de retorno de chamada para receber o status e os resultados.

Este artigo aborda as operações `Job` e `Review`. Leia a [Visão geral de fluxos de trabalho](workflow-api.md) para obter informações sobre como criar, editar e obter definições de fluxo de trabalho.

## <a name="job-operations"></a>Operações de trabalho

### <a name="start-a-job"></a>Iniciar um trabalho
Use a operação `Job.Create` para iniciar um trabalho de criação de revisão humana e moderação. O Content Moderator examina o conteúdo e avalia o fluxo de trabalho designado. De acordo com os resultados do fluxo de trabalho, ele cria revisões ou ignora a etapa. Ele também envia as marcas de pós-moderação e pós-revisão para seu ponto de extremidade de retorno de chamada.

A entrada inclui as seguintes informações:

- A ID da equipe de análise.
- O conteúdo a ser moderado.
- O nome do fluxo de trabalho. (O padrão é o fluxo de trabalho “padrão”.)
- Seu ponto de seu retorno de chamada de API para notificações.
 
A resposta a seguir mostra o identificador do trabalho que foi iniciado. Você pode usar o identificador de trabalho para obter o status do trabalho e receber informações detalhadas.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>Obter status do trabalho

Use a operação `Job.Get` e o identificador de trabalho para obter os detalhes de um trabalho em execução ou concluído. A operação retornará imediatamente, enquanto o trabalho de moderação é executado de forma assíncrona. Os resultados são retornados por meio do ponto de extremidade de retorno de chamada.

Sua entrada inclui as seguintes informações:

- A ID da equipe de revisão: o identificador do trabalho retornado pela operação anterior

A resposta inclui as informações a seguir:

- O identificador da análise criada. (Use essa ID para obter os resultados de análise final.)
- O status do trabalho (completo ou em andamento): As marcas de moderação atribuídas (pares chave-valor).
- O relatório de execução do trabalho.
 
 
        {
            "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
            "TeamName": "some team name",
            "Status": "Complete",
            "WorkflowId": "OCR",
            "Type": "Image",
            "CallBackEndpoint": "",
            "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
            "ResultMetaData":[
            {
            "Key": "hasText",
            "Value": "True"
            },
            {
            "Key": "ocrText",
            "Value": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
            }
            ],
            "JobExecutionReport": [
            {
                "Ts": "2018-01-07T00:38:29.3238715",
                "Msg": "Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
                },
                {
                "Ts": "2018-01-07T00:38:29.2928416",
                "Msg": "Job marked completed and job content has been removed"
                },
                {
                "Ts": "2018-01-07T00:38:29.0856472",
                "Msg": "Execution Complete"
                },
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
 
![Revisão de imagem para moderadores humanos](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>Operações de análise

### <a name="create-reviews"></a>Criar análises

Use a operação `Review.Create` para criar as revisões humanas. Você pode moderá-los em outro local ou usar lógica personalizada para atribuir as marcas de moderação.

Suas entradas para esta operação incluem:

- O conteúdo a ser analisado.
- As marcas atribuídas (pares chave-valor) para análise por moderadores humanos.

A resposta a seguir mostra o identificador da análise:

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>Obter status da análise
Use a operação `Review.Get` para obter os resultados após a conclusão de uma análise humana da imagem moderada. Você é notificado por meio de seu ponto de extremidade de retorno de chamada fornecido. 

A operação retorna dois conjuntos de marcas: 

* As marcas atribuídas pelo serviço de moderação
* As marcas depois que a revisão humana foi concluída

Suas entradas incluem no mínimo:

- O nome da equipe de análise
- O identificador de análise retornado pela operação anterior

A resposta inclui as informações a seguir:

- O status da análise
- As marcas (pares chave-valor) confirmadas pelo revisor humano
- As marcas (pares chave-valor) atribuídas pelo serviço de moderação

Consulte ambas as marcas atribuídas ao revisor (**reviewerResultTags**) e as marcas iniciais (**metadados**) na resposta de exemplo a seguir:

    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="next-steps"></a>Próximas etapas

* Teste o [Console de API de Trabalho](try-review-api-job.md) e use os exemplos de código de API REST. Se você estiver familiarizado com Visual Studio e C#, consulte também o [Início rápido do .NET de Trabalhos](moderation-jobs-quickstart-dotnet.md). 
* Para revisões, comece com o [console de API de revisão](try-review-api-review.md) e use os exemplos de código da API REST. Em seguida, consulte o [início rápido do .NET de Análises](moderation-reviews-quickstart-dotnet.md).
* Para análises de vídeos, use o [Início rápido de análise de vídeo](video-reviews-quickstart-dotnet.md)e saiba como [adicionar transcrições para a análise de vídeo](video-transcript-reviews-quickstart-dotnet.md).
