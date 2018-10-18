---
title: Introdução à API de Reconhecimento de Fala da Microsoft em Java no Android | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use a API de fala da Microsoft para desenvolver aplicativos do Android que convertem o áudio falado em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 4c5243ec14a4494222168bb33b3e840b96f8465e
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345247"
---
[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

# <a name="quickstart-use-the-bing-speech-recognition-api-in-java-on-android"></a>Início Rápido: Usar a API de reconhecimento de Fala do Bing em Java no Android

Com a API de Reconhecimento de Fala do Bing, você pode desenvolver aplicativos do Android que usam o Serviço de Fala do Bing baseado em nuvem para converter o áudio falado em texto. A API dá suporte a streaming em tempo real, para que seu aplicativo possa simultaneamente e assincronamente receber os resultados de reconhecimento parcial ao mesmo tempo em que ele está enviando áudio para o serviço.

Este artigo usa um aplicativo de exemplo para demonstrar como usar a biblioteca de cliente de fala para Android para desenvolver aplicativos de fala em texto em Java para dispositivos Android.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

O exemplo é desenvolvido pelo [Android Studio](http://developer.android.com/sdk/index.html) para Windows em Java.

### <a name="get-the-client-library-and-sample-application"></a>Obter a biblioteca de clientes e o aplicativo de exemplo

A biblioteca de cliente de fala e exemplos para Android estão disponíveis no [SDK de cliente fala para Android](https://github.com/microsoft/cognitive-speech-stt-android). Você pode encontrar o exemplo compilável sob o diretório de exemplos/SpeechRecoExample. Você pode encontrar as duas bibliotecas que  precisa usar em seus próprios aplicativos em SpeechSDK/bibliotecas na pasta armeabi e x86. O tamanho do arquivo libandroid_platform.so é 22 MB, mas ele será reduzido para 4 MB no momento da implantação.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Inscreva-se na API de Fala e receba uma chave de assinatura de avaliação gratuita

O Speech API é parte dos Serviços Cognitivos (anteriormente Projeto Oxford). É possível obter chaves de assinatura de avaliação gratuita na página de [assinatura dos Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/). Após selecionar o Speech API, selecione **Obter chave de API** para obter a chave. Isso retorna uma chave primária e secundária. Ambas as chaves estão vinculadas à mesma cota, portanto, é possível usar uma das chaves.

Se você quiser usar o reconhecimento *com intenção*, também será necessário inscrever-se no [LUIS (Serviço Inteligente de Reconhecimento Vocal)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Obtenha uma chave de assinatura. Antes de poder usar as bibliotecas de clientes do Speech, será necessário ter uma [chave de assinatura](https://azure.microsoft.com/try/cognitive-services/).
>
>* Use sua chave de assinatura. Com o aplicativo de exemplo do Android fornecido, atualize o arquivo samples/SpeechRecoExample/res/values/strings.xml com suas chaves de assinatura. Para saber mais, confira [Compilar e executar exemplos](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Use a biblioteca de clientes de fala

Para usar a biblioteca de cliente em seu aplicativo, execute as [instruções](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Você pode encontrar o cliente de referência da biblioteca para o Android na pasta documentos do [SDK de cliente fala para Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Compilar e executar amostras

Para saber como criar e executar os exemplos, consulte [página do Leiame](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Exemplos explicados

### <a name="create-recognition-clients"></a>Criar os clientes de reconhecimento

O código no exemplo a seguir mostra como criar classes de cliente de reconhecimento, com base em cenários de usuário:

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

A biblioteca de cliente fornece classes de cliente de reconhecimento pré-implementadas para cenários típicos em reconhecimento de fala:

* `DataRecognitionClient`: O reconhecimento de fala com dados PCM (por exemplo, de uma fonte de arquivo ou áudio). Os dados são divididos em buffers, e cada buffer é enviado ao serviço de fala. Nenhuma modificação é feita para buffers, para que o usuário possa aplicar sua própria detecção de silêncio se desejado. Se os dados são fornecidos a partir de arquivos WAV, você pode enviar dados do arquivo diretamente ao serviço de fala. Se você tiver dados brutos, por exemplo, áudio provenientes de Bluetooth, você primeiro envir um cabeçalho de formato para serviço de fala, seguido dos dados.
* `MicrophoneRecognitionClient`: reconhecimento de fala com áudio proveniente do microfone. Verifique se o microfone está ligado e se os dados do microfone são enviados para o serviço de reconhecimento de fala. Um “detector de silêncio” interno é aplicado aos dados do microfone antes de serem enviado para o serviço de reconhecimento.
* `DataRecognitionClientWithIntent` e `MicrophoneRecognitionClientWithIntent`: retornam esses clientes, além de texto de reconhecimento, informações estruturadas sobre a intenção do falante, que pode ser usado para gerar mais ações por seus aplicativos. Para usar a “intenção”, será necessário primeiro treinar um modelo usando [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Idioma de reconhecimento

Quando você usa `SpeechRecognitionServiceFactory` para criar o cliente, selecione um idioma. A lista completa dos idiomas com suporte pelo Serviço de Fala, consulte [Suporte para Idiomas](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Você também precisa especificar `SpeechRecognitionMode` ao criar o cliente com `SpeechRecognitionServiceFactory`:

* `ShortPhrase`: declarações de até 15 segundos de duração. Conforme os dados são enviados ao serviço, o cliente recebe vários resultados parciais e um resultado final com várias melhores escolhas.
* `LongDictation`: uma declaração de até dois minutos. Como os dados são enviados ao serviço, o cliente recebe vários resultados parciais e vários resultados finais, com base em onde o servidor indica pausas de sentença.

### <a name="attach-event-handlers"></a>Anexar Manipuladores de eventos

Você pode anexar vários manipuladores de eventos para o cliente criado por você:

* **Eventos de resultados parciais**: esse evento é chamado sempre que o Serviço de Fala prevê o que está sendo falado, mesmo antes de terminar de falar (se usar `MicrophoneRecognitionClient`) ou terminar de enviar dados (se usar `DataRecognitionClient`).
* **Eventos de erro**: chamado quando o serviço detecta um erro.
* **Eventos de intenção**: chamado em clientes "WithIntent" (somente no modo `ShortPhrase`) após o resultado de reconhecimento final ser analisado em uma intenção JSON estruturada.
* **Eventos de resultado**:
  * No modo `ShortPhrase`, esse evento é chamado e retorna os melhores resultados após parar de falar.
  * No modo `LongDictation`, o manipulador de eventos é chamado várias vezes, com base em onde o serviço identifica pausas de sentença.
  * **Para cada uma das melhores escolhas**, um valor de confiança e algumas formas diferentes do texto reconhecido são retornados. Para obter mais informações, consulte o [Formato de saída](../Concepts.md#output-format).

## <a name="related-topics"></a>Tópicos relacionados

* [Referência da Biblioteca do Cliente para Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Introdução à API do Microsoft Speech em C# para o Windows no .NET](GetStartedCSharpDesktop.md)
* [Introdução à API de Fala da Microsoft em Objective-C no iOS](Get-Started-ObjectiveC-iOS.md)
* [Introdução à API de Fala da Microsoft em Objective-C no JavaScript](GetStartedJSWebsockets.md)
* [Introdução à API de Fala da Microsoft via REST](GetStartedREST.md)
