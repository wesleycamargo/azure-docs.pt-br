---
title: Executar trabalhos de moderação de conteúdo com o console de API – Content Moderator
titlesuffix: Azure Cognitive Services
description: Use as operações de trabalho da API de Análise para iniciar trabalhos de moderação de conteúdo de ponta a ponta para conteúdo de imagem ou texto no Content Moderator do Azure.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 8ad504051980356737dbc9af9263bc8370421cdb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228212"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>Iniciar um trabalho de moderação no console de API

Use as [operações de trabalho](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) da API de Análise para iniciar trabalhos de moderação de conteúdo de ponta a ponta para conteúdo de imagem ou texto no Content Moderator do Azure. 

O trabalho de moderação examina o conteúdo usando a API de Moderação de Imagem do Content Moderator ou a API de Moderação de Texto. Em seguida, o trabalho de moderação usa fluxos de trabalho (definidos na ferramenta de análise) para gerar análises na ferramenta de Análise. 

Depois que um moderador humano revisa as marcas atribuídas automaticamente e os dados de previsão e envia uma decisão de moderação final, a API de Análise envia todas as informações para o ponto de extremidade de API.

## <a name="prerequisites"></a>Pré-requisitos

Navegue até a [ferramenta de análise](https://contentmoderator.cognitive.microsoft.com/). Inscreva-se, caso ainda não tenha feito isso. Na ferramenta de análise, [defina um fluxo de trabalho personalizado](Review-Tool-User-Guide/Workflows.md) para usar nessa operação `Job`.

## <a name="use-the-api-console"></a>Usar o console de API
Para testar a API usando o console online, você precisa de alguns valores para inserir no console:
    
- `teamName`: use o campo `Id` na tela de credenciais da ferramenta de análise. 
- `ContentId`: essa cadeia de caracteres é passada para a API e retornada pelo retorno de chamada. **ContentId** é útil para a associação de identificadores internos ou metadados com os resultados de um trabalho de moderação.- `Workflowname`: o nome do [fluxo de trabalho criado](Review-Tool-User-Guide/Workflows.md) na seção anterior.
- `Ocp-Apim-Subscription-Key`: localizada na guia **Configurações**. Para obter mais informações, consulte [Visão Geral](overview.md).

O acesso ao console de API é a partir da janela **Credenciais**.

### <a name="navigate-to-the-api-reference"></a>Navegar até a referência de API
Na janela **Credenciais**, selecione [Referência de API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5).

  A página `Job.Create` abre.

### <a name="select-your-region"></a>Selecione um região
Para **Abrir o console de teste da API**, selecione a região que melhor descreve seu local.
  ![Trabalho - Criar uma seleção de região de página](images/test-drive-job-1.png)

  O console de `Job.Create` API abre. 

### <a name="enter-parameters"></a>Inserir parâmetros

Insira valores para os parâmetros de consulta necessários e a chave de assinatura. Na caixa **Corpo da solicitação**, especifique o local das informações que você quer verificar. Para esse exemplo, vamos usar esta [imagem de exemplo](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png).

  ![Trabalho - Criar parâmetros de consulta de console, cabeçalhos e caixa do corpo da solicitação](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>Enviar a solicitação
Selecione **Enviar**. Uma ID de trabalho é criada. Copie esta opção para usar nas próximas etapas.

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>Abrir a página de detalhes Obter Trabalho
Selecione **Obter**e, em seguida, abra a API selecionando o botão que corresponde à sua região.

  ![Trabalho - Criar console Obter resultados](images/test-drive-job-4.png)

### <a name="review-the-response"></a>Analisar a resposta

Insira valores para **teamName** e **JobID**. Insira a chave de assinatura e selecione **Enviar**. A resposta a seguir mostra detalhes e status do Trabalho de exemplo.

```
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": 
        [
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
```

## <a name="navigate-to-the-review-tool"></a>Navegar até a ferramenta de análise
No painel do Content Moderator, selecione **Análise** > **Imagem**. A imagem que você examinou aparecerá, pronta para análise humana.

  ![Imagem da ferramenta de análise de três ciclistas](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>Próximas etapas

Use a API REST em seu código ou inicie com o [Início rápido do .NET de Trabalhos](moderation-jobs-quickstart-dotnet.md) para integrar o aplicativo.
