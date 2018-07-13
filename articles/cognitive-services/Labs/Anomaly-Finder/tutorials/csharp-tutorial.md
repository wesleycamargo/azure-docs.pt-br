---
title: Aplicativo C# de Detecção de Anomalias - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Explore um aplicativo C# que usa a API de Detecção de Anomalias nos Serviços Cognitivos da Microsoft. Envie os pontos de dados originais para a API e obtenha os pontos de anomalias e o valor esperado.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 2e4100fd7d8e85a6b103c31000176aaaeb3d7151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364011"
---
# <a name="anomaly-detection-c-application"></a>Aplicativo em C# de Detecção de Anomalias

Explore um aplicativo básico do Windows que usa a API de Detecção de Anomalias para detectar anomalias da entrada. O exemplo envia os dados de série temporal para a API de Detecção de Anomalias com sua chave de assinatura, em seguida, obtém todos os pontos de anomalias e o valor esperado para cada ponto de dados da API.

## <a name="prerequisites"></a>pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

O exemplo foi desenvolvido para o .NET Framework usando o [Visual Studio 2017, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Inscrever-se na detecção de anomalias e obter uma chave de assinatura 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Obter e usar o exemplo

Você pode clonar o aplicativo de exemplo de Detecção de Anomalias em seu computador do [Github](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Instalar o exemplo

Na Área de Trabalho do GitHub, abra Sample\AnomalyDetectionSample.sln.

<a name="Step2"></a>
### <a name="build-the-example"></a>Criar o exemplo

Pressione Ctrl+Shift+B, ou clique em Compilar no menu de faixa de opções, então selecione Compilar Solução.

<a name="Step3"></a>
### <a name="run-the-example"></a>Executar o exemplo

1. Após a compilação, pressione **F5** ou clique em **Iniciar** no menu de faixa de opções para executar o exemplo.
2. Localize a janela de interface de usuário de Detecção de Anomalias com a caixa de edição de texto com "{sua_chave_de_assinatura}".
3. Substitua o arquivo request.json, que contém os dados de exemplo, com seus próprios dados e então clique em "Enviar". A Microsoft recebe os dados carregados e os utiliza para detectar todos os pontos de anomalia entre eles. Os dados atualizados não serão persistentes no servidor da Microsoft. Para detectar o ponto de anomalias novamente, você precisa carregar os dados novamente.
4. Se os dados forem bons, você encontrará o resultado da detecção de anomalias no campo "Resposta". Se ocorrer algum erro, as informações de erro também serão mostradas no campo Resposta.

<a name="Review"></a>
### <a name="read-the-result"></a>Ler o resultado

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Analisar e aprender

Agora que você tem um aplicativo pronto para execução, vamos analisar como o aplicativo de exemplo se integra com a tecnologia de Serviços Cognitivos. Esta etapa facilitará continuar aproveitando este aplicativo ou desenvolver seu próprio aplicativo usando a Detecção de Anomalias da Microsoft.

Este aplicativo de exemplo utiliza o ponto de extremidade da API Restful de Detecção de Anomalias.

Analisar como a API Restful é usada no aplicativo de exemplo, vamos examinar um trecho de código de **AnomalyDetectionClient.cs**. O arquivo contém comentários de código indicando "O CÓDIGO DE EXEMPLO DE CHAVE COMEÇA AQUI" e "O CÓDIGO DE EXEMPLO DE CHAVE TERMINA AQUI" para ajudá-lo a localizar os trechos de código reproduzidos abaixo.

```csharp
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

```
**Request(...)**  A seguir, o trecho de código mostra como usar o HttlClient para enviar sua assinatura chave e pontos de dados para o ponto de extremidade da API de Detecção de Anomalias.

```csharp
    public async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
    {
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
        {
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Build the request content
            // ---------------------------------------------------------------------- 
            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Send the request content with POST method.
            // ---------------------------------------------------------------------- 
            var res = await client.PostAsync(endpoint, content);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------
            if (res.IsSuccessStatusCode)
            {
                return await res.Content.ReadAsStringAsync();
            }
            else
            {
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
