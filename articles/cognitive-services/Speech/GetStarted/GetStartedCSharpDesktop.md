---
title: Introdução à API de Reconhecimento de Fala do Bing usando a biblioteca de área de trabalho em C# | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Desenvolva aplicativos básicos do Windows que usam a API de Reconhecimento de Fala do Bing para converter áudio de fala em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5f3b70a2dd9816210ed61280be38504a3980d205
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515382"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Início Rápido: Usar a API de Reconhecimento de Fala do Bing em C&#35; para o .NET no Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Esta página mostra como desenvolver um aplicativo básico do Windows que usa a API de Reconhecimento de Fala para converter áudio de fala em texto. O uso da Biblioteca de clientes permite a transmissão em tempo real, o que significa que, quando o aplicativo cliente envia o áudio ao serviço, ele recebe simultaneamente e parcialmente de forma assíncrona os resultados do reconhecimento.

Os desenvolvedores que querem usar o Serviço de Fala de aplicativos executados em qualquer dispositivo podem usar a biblioteca de área de trabalho em C#. Para usar a biblioteca, instale o [pacote NuGet Microsoft.ProjectOxford.SpeechRecognition-x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) para uma plataforma de 32 bits e o [pacote NuGet Microsoft.ProjectOxford.SpeechRecognition-x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) para uma plataforma de 64 bits. Para a referência de API de biblioteca de clientes, consulte [biblioteca de área de trabalho em C# do Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

As seções a seguir descrevem como instalar, compilar e executar o aplicativo de exemplo em C#, usando a biblioteca de área de trabalho em C#.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

O exemplo a seguir foi desenvolvido para Windows 8+ e o .NET Framework 4.5+ usando [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Obter o aplicativo de exemplo

Clone o exemplo do repositório [Exemplo de biblioteca de área de trabalho em C# do Speech](https://github.com/microsoft/cognitive-speech-stt-windows).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Inscreva-se na API de Reconhecimento de Fala e receba uma chave de assinatura de avaliação gratuita

O Speech API é parte dos Serviços Cognitivos (anteriormente Projeto Oxford). É possível obter chaves de assinatura de avaliação gratuita na página de [assinatura dos Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/). Após selecionar o Speech API, selecione **Obter chave de API** para obter a chave. Isso retorna uma chave primária e secundária. Ambas as chaves estão vinculadas à mesma cota, portanto, é possível usar uma das chaves.

> [!IMPORTANT]
> * Obtenha uma chave de assinatura. Antes de usar as bibliotecas de clientes do Speech, será necessário ter uma [chave de assinatura](https://azure.microsoft.com/try/cognitive-services/).
>
> * Use sua chave de assinatura. Com o aplicativo de exemplo de área de trabalho em C# fornecido, cole a chave de inscrição na caixa de texto ao executar o exemplo. Para obter mais informações, consulte [Executar o aplicativo de exemplo](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Etapa 1: Instalar o aplicativo de exemplo

1. Inicie o Visual Studio 2015 e selecione **Arquivo** > **Abrir** > **Projeto/Solução**.

2. Navegue até a pasta onde você salvou os arquivos baixados da API de Reconhecimento de Fala. Selecione **Speech** > **Windows** e, em seguida, selecione a pasta Sample-WP.

3. Clique duas vezes para abrir o arquivo de solução (.sln) do Visual Studio 2015 nomeado SpeechToText-WPF-Samples.sln. A solução é aberta no Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Etapa 2: Compilar o aplicativo de exemplo

1. Se você quiser usar o reconhecimento *com intenção*, primeiro será necessário inscrever-se no [LUIS (Serviço Inteligente de Reconhecimento Vocal)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Em seguida, use a URL de ponto de extremidade do aplicativo LUIS para definir o valor da chave `LuisEndpointUrl` no arquivo app.config na pasta samples/SpeechRecognitionServiceExample. Para obter mais informações sobre a URL do ponto de extremidade do aplicativo LUIS, consulte [Publicar seu aplicativo](../../luis/luis-get-started-create-app.md#publish-your-app).

   > [!TIP]
   > Substitua o caractere `&` na URL do ponto de extremidade de LUIS por `&amp;` para garantir que a URL seja interpretada corretamente pelo analisador XML.

2. Pressione Ctrl+Shift+B ou selecione **Compilar** no menu de faixa de opções. Em seguida, selecione **Compilar Solução**.

## <a name="step-3-run-the-sample-application"></a>Etapa 3: Executar o aplicativo de exemplo

1. Após a conclusão da compilação, pressione F5 ou selecione **Iniciar** no menu de faixa de opções para executar o exemplo.

2. Vá para a janela **Exemplo de Conversão de Fala em Texto do Projeto Oxford**. Cole a chave de assinatura na caixa de texto **Cole a chave de assinatura aqui para iniciar**, conforme mostrado. Para persistir a chave de assinatura no PC ou laptop, selecione **Salvar Chave**. Para excluir a chave de assinatura do sistema, selecione **Excluir Chave** para removê-la do PC ou laptop.

   ![Colar na chave de Reconhecimento de Fala](../Images/SpeechRecog_paste_key.PNG)

3. Em **Fonte de Reconhecimento de Fala**, escolha uma das seis fontes de fala, que enquadram-se em duas categorias de entrada principais:

   * Use o microfone do computador ou um microfone conectado para capturar a fala.
   * Reproduza um arquivo de áudio.

   Cada categoria tem três modos de reconhecimento:

    * **Modo de ShortPhrase**: Um enunciado de até 15 segundos. Conforme os dados são enviados ao servidor, o cliente recebe vários resultados parciais e um resultado final com várias melhores escolhas.
    * **Modo de LongDictation**: Um enunciado de até dois minutos. Como os dados são enviados ao servidor, o cliente recebe vários resultados parciais e vários resultados finais, com base em onde o servidor indica pausas de sentença.
    * **Detecção de intenção**: O servidor retorna informações estruturadas adicionais sobre a entrada de fala. Para usar a detecção de intenção, será necessário primeiro treinar um modelo usando [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Use exemplos de arquivos de áudio com esse aplicativo de exemplo. Localize os arquivos no repositório que você baixou com esse exemplo na pasta samples/SpeechRecognitionServiceExample. Esses arquivos de áudio de exemplo executarão automaticamente, se nenhum outro arquivo for escolhido ao selecionar **Usar arquivo wav para modo Shortphrase** ou **Usar arquivo wav para modo Longdictation** como a entrada de fala. Atualmente, apenas o formato de áudio WAV tem suporte.

![Interface da Conversão de Fala em Texto](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Exemplos explicados

### <a name="recognition-events"></a>Eventos de reconhecimento

* **Eventos de resultados parciais**: Esse evento é chamado sempre que o serviço de fala prevê o que você pode estar dizendo, mesmo antes de terminar de falar (se você usar `MicrophoneRecognitionClient`) ou terminou de enviar dados (se você usar `DataRecognitionClient`).
* **Eventos de erro**: Chamados quando o serviço detecta um erro.
* **Eventos de intenção**: Chamados em clientes "WithIntent" (somente no modo ShortPhrase) após o resultado do reconhecimento final ser analisado em uma intenção JSON estruturada.
* **Eventos de resultado**:
  * No modo `ShortPhrase`, esse evento é chamado e retorna os melhores resultados após parar de falar.
  * No modo `LongDictation`, o manipulador de eventos é chamado várias vezes, com base em onde o serviço identifica pausas de sentença.
  * **Para cada uma das melhores escolhas**, um valor de confiança e algumas formas diferentes do texto reconhecido são retornados. Para obter mais informações, consulte [Formato de saída](../Concepts.md#output-format).

Os manipuladores de eventos já são apontados no código na forma de comentários de código.

## <a name="related-topics"></a>Tópicos relacionados

* [Referência de biblioteca de área de trabalho do Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html)
* [Introdução à API de Reconhecimento de Fala da Microsoft em Java no Android](GetStartedJavaAndroid.md)
* [Introdução à API de Reconhecimento de Fala da Microsoft em Objective-C no iOS](Get-Started-ObjectiveC-iOS.md)
* [Introdução à API de Reconhecimento de Fala da Microsoft em JavaScript](GetStartedJSWebsockets.md)
* [Introdução à API de Reconhecimento de Fala da Microsoft via REST](GetStartedREST.md)
