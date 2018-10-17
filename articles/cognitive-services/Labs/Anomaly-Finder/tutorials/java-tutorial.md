---
title: 'Tutorial: Detecção de Anomalias, Java'
titlesuffix: Azure Cognitive Services
description: Explore um aplicativo Java que use a API de Detecção de Anomalias. Envie os pontos de dados originais para a API e obtenha os pontos de anomalias e o valor esperado.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 4b544e2e59a40cebf75042c4040b84bceebcecf7
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887693"
---
# <a name="tutorial-anomaly-detection-with-java-application"></a>Tutorial: Detecção de Anomalias com aplicativo Java

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Este artigo demonstra como usar um aplicativo Java simples para invocar a API de Detecção de Anomalias.  
O exemplo envia os dados de série temporal para a API de Detecção de Anomalias com sua chave de assinatura, em seguida, obtém todos os pontos de anomalias e o valor esperado para cada ponto de dados da API.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

Este tutorial foi desenvolvido usando [IntelliJ IDEA](https://www.jetbrains.com/idea). E também é necessário instalar o [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) versão 1.8 + e uma ferramenta de compilação [Maven do Apache](http://maven.apache.org/) atualizada.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Inscrever-se na detecção de anomalias e obter uma chave de assinatura 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Baixar o projeto de tutorial

1. Vá até o [repositório Java](https://github.com/MicrosoftAnomalyDetection/java-sample) MicrosoftAnomalyDetection.
2. Clique no botão Clonar ou baixar.
3. Clique em Baixar ZIP para baixar um arquivo .zip do projeto de tutorial.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Abrir o projeto de tutorial

1. Extraia o arquivo .zip do projeto de tutorial.
2. No IntelliJ IDEIA, clique em **Arquivo > Abrir**, a caixa de diálogo Abrir arquivo ou projeto é exibida.
3. Selecione o caminho raiz do projeto extraído, clique em OK.
4. No painel de Projetos, expanda **src > main > java**.
5. Clique duas vezes em com.microsoft.cognitiveservice.anomalydetection.Main.java para carregar o arquivo no editor.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>Substituir subscriptionKey e região do URI

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>Criar e executar o projeto de tutorial

1. Abra o menu clicando em qualquer lugar com o botão direito na guia do código fonte com.microsoft.cognitiveservice.anomalydetection.Main.java. 
2. Selecione Executar 'Main.main()'
3. O resultado da solicitação de exemplo será retornado e mostrado no terminal.

### <a name="result-of-the-tutorial-project"></a>Resultado do projeto de tutorial

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
