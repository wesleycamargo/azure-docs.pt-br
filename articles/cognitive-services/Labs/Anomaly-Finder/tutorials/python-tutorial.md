---
title: Aplicativo Python de Detecção de Anomalias - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Explore um bloco de anotações de Python que use a API de Detecção de Anomalias nos Serviços Cognitivos da Microsoft. Envie os pontos de dados originais para a API e obtenha os pontos de anomalias e o valor esperado.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: d35f41ddab21aa155376ad52ff4084298dab8fc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364203"
---
# <a name="anomaly-detection-python-application"></a>Aplicativo Python de Detecção de Anomalias

O tutorial mostra como usar a API de Detecção de Anomalias em Python e como visualizar os resultados usando as bibliotecas populares. Use Jupyter para executar o tutorial e tentar seus próprios dados com a sua chave de assinatura. Para saber como começar com blocos de anotações interativos do Jupyter, consulte a [Documentação de Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>pré-requisitos

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Inscrever-se na detecção de anomalias e obter uma chave de assinatura 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Baixar o exemplo de código

1. Navegue até o [tutorial de bloco de anotações no Github](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Clique no botão verde para clonar ou baixar o tutorial. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Abrir o tutorial de bloco de anotações em Jupyter

1. Abra um prompt de comando e vá até a pasta python-sample.
2. Execute o Jupyter Notebook no prompt de comando, o que iniciará Jupyter.
3. Na janela do Jupyter, clique em <em>API de Detecção de Anomalias Example.ipynb</em> para abrir o tutorial de bloco de anotações.   

## <a name="running-the-tutorial"></a>Executar o tutorial

Para usar este bloco de anotações, você precisará de uma chave de assinatura para a API de Detecção de Anomalias. Visite a página Assinatura para se inscrever. Na página de “Entrar”, use sua conta da Microsoft para entrar e você será capaz de se inscrever e obter suas chaves. Depois de concluir o processo de inscrição, cole a chave na seção de variáveis do bloco de anotações (reproduzida abaixo). Tanto a chave primária como a secundária funcionam. Certifique-se de colocar a chave entre aspas para transformá-la em uma cadeia de caracteres.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
