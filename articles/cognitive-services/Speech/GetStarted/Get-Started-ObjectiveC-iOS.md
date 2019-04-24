---
title: Introdução à API de Reconhecimento de Fala do Bing em Objective-C no iOS | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use a API de Reconhecimento de Fala do Bing para desenvolver aplicativos iOS que convertem áudio de fala em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 09b7e8961e59bd6fad49408c28e9ee9a4a209cae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515283"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Início Rápido: Usar a API de Reconhecimento de Fala do Bing em Objective-C no iOS

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Com a API de reconhecimento de fala, você pode desenvolver aplicativos do iOS que usam o serviço de fala baseado em nuvem para converter o áudio falado em texto. A API dá suporte a streaming em tempo real, para que seu aplicativo possa simultaneamente e assincronamente receber os resultados de reconhecimento parcial ao mesmo tempo em que ele está enviando áudio para o serviço.

Este artigo usa um aplicativo de exemplo para demonstrar os conceitos básicos de como iniciar a API de reconhecimento de fala para desenvolver um aplicativo iOS. Para obter uma referência completa de API, consulte o [referência da biblioteca cliente SDK de Fala](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

Verifique se o pacote Mac XCode IDE está instalado.

### <a name="get-the-client-library-and-examples"></a>Obtenha a biblioteca de clientes e o exemplo

A biblioteca de cliente de fala e exemplos para iOS estão disponíveis no [SDK de cliente fala para iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Inscreva-se na API de Reconhecimento de Fala e receba uma chave de assinatura de avaliação gratuita

O Speech API é parte dos Serviços Cognitivos (anteriormente Projeto Oxford). É possível obter chaves de assinatura de avaliação gratuita na página de [assinatura dos Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/). Após selecionar o Speech API, selecione **Obter chave de API** para obter a chave. Isso retorna uma chave primária e secundária. Ambas as chaves estão vinculadas à mesma cota, portanto, é possível usar uma das chaves.

Se você quiser usar o reconhecimento *com intenção*, também será necessário inscrever-se no [LUIS (Serviço Inteligente de Reconhecimento Vocal)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Obtenha uma chave de assinatura. Antes de poder usar as bibliotecas de clientes do Speech, será necessário ter uma [chave de assinatura](https://azure.microsoft.com/try/cognitive-services/).
>
> * Use sua chave de assinatura. Com o aplicativo de exemplo fornecido iOS, você precisa atualizar o arquivo Samples/SpeechRecognitionServerExample/settings.plist com sua chave de assinatura. Para saber mais, confira [Compilar e executar exemplos](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Use a biblioteca de clientes de fala

Para adicionar a biblioteca de cliente em um projeto XCode, siga estas [instruções](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Para localizar o cliente de referência da biblioteca para iOS, consulte esta [página da Web](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Compilar e executar amostras

Para saber como criar e executar os exemplos, consulte a [página do Leiame](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Exemplos explicados

### <a name="create-recognition-clients"></a>Criar os clientes de reconhecimento

O código a seguir mostra como criar classes de cliente de reconhecimento, com base em cenários de usuário:

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

A biblioteca de cliente fornece classes de cliente de reconhecimento pré-implementadas para cenários típicos em reconhecimento de fala:

* `DataRecognitionClient`: Reconhecimento de fala com os dados PCM (por exemplo, de uma fonte de arquivo ou áudio). Os dados são divididos em buffers, e cada buffer é enviado ao serviço de fala. Nenhuma modificação é feita para buffers, para que o usuário possa aplicar sua própria detecção de silêncio se desejado. Se os dados são fornecidos a partir de arquivos WAV, você pode enviar dados do arquivo diretamente ao servidor. Se você tiver dados brutos, por exemplo, áudio provenientes de Bluetooth, você primeiro envir um cabeçalho de formato para servidor, seguido dos dados.
* `MicrophoneRecognitionClient`: Reconhecimento de fala com o áudio proveniente do microfone. Verifique se o microfone está ligado e se os dados do microfone são enviados para o serviço de reconhecimento de fala. Um “detector de silêncio” interno é aplicado aos dados do microfone antes de serem enviado para o serviço de reconhecimento.
* `DataRecognitionClientWithIntent` e `MicrophoneRecognitionClientWithIntent`: Além do texto de reconhecimento, esses clientes retornam informações estruturadas sobre a intenção do locutor, que podem ser usadas pelos aplicativos para gerar mais ações. Para usar a “intenção”, será necessário primeiro treinar um modelo usando [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Idioma de reconhecimento

Quando você usa `SpeechRecognitionServiceFactory` para criar o cliente, selecione um idioma. A lista completa dos idiomas com suporte pelo Serviço de Fala, consulte [Suporte para Idiomas](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Você também precisa especificar `SpeechRecognitionMode` ao criar o cliente com `SpeechRecognitionServiceFactory`:

* `SpeechRecognitionMode_ShortPhrase`: Um enunciado de até 15 segundos. Conforme os dados são enviados ao serviço, o cliente recebe vários resultados parciais e um resultado final com várias melhores escolhas.
* `SpeechRecognitionMode_LongDictation`: Um enunciado de até dois minutos. Como os dados são enviados ao serviço, o cliente recebe vários resultados parciais e vários resultados finais, com base em onde o servidor indica pausas de sentença.

### <a name="attach-event-handlers"></a>Anexar Manipuladores de eventos

Você pode anexar vários manipuladores de eventos para o cliente criado por você:

* **Eventos de resultados parciais**: Esse evento é chamado sempre que o Serviço de Fala prevê o que você pode estar dizendo, mesmo antes de você terminar de falar (se você usar `MicrophoneRecognitionClient`) ou terminar de enviar os dados (se você usar `DataRecognitionClient`).
* **Eventos de erro**: Chamados quando o serviço detecta um erro.
* **Eventos de intenção**: Chamados em clientes "WithIntent" (somente no modo ShortPhrase) após o resultado do reconhecimento final ser analisado em uma intenção JSON estruturada.
* **Eventos de resultado**:
  * No modo `SpeechRecognitionMode_ShortPhrase`, esse evento é chamado e retorna os melhores resultados após parar de falar.
  * No modo `SpeechRecognitionMode_LongDictation`, o manipulador de eventos é chamado várias vezes, com base em onde o serviço identifica pausas de sentença.
  * **Para cada uma das melhores escolhas**, um valor de confiança e algumas formas diferentes do texto reconhecido são retornados. Para obter mais informações, consulte o [Formato de saída](../Concepts.md#output-format).

## <a name="related-topics"></a>Tópicos relacionados

* [Referência da Biblioteca do Cliente para iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Introdução à API de Reconhecimento de Fala da Microsoft e/ou Intenção em Java no Android](GetStartedJavaAndroid.md)
* [Introdução à API de Fala da Microsoft em Objective-C no JavaScript](GetStartedJSWebsockets.md)
* [Introdução à API de Fala da Microsoft via REST](GetStartedREST.md)
