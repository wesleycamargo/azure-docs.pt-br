---
title: Usar um ponto de extremidade de fala personalizado – Serviço de Fala Personalizado
titlesuffix: Azure Cognitive Services
description: Saiba como usar um ponto de extremidade personalizado de conversão de fala em texto com o Serviço de Fala Personalizado.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 98c1b58e58490199b0258dfcc8df183c3fe9a8bd
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223365"
---
# <a name="use-a-custom-speech-to-text-endpoint"></a>Usar um ponto de extremidade personalizado de conversão de fala em texto
Você pode enviar solicitações para um ponto de extremidade de conversão fala em texto do Serviço de Fala Personalizada do Azure, de maneira semelhante possível ao ponto de extremidade de fala padrão dos Serviços Cognitivos. Esses pontos de extremidade são funcionalmente idênticos aos pontos de extremidade padrão da API de Fala. Assim, a mesma funcionalidade que está disponível via a biblioteca de cliente ou a API REST para a API de Fala também está disponível para o ponto de extremidade personalizado.

Os pontos de extremidade que você cria usando este serviço podem processar diferentes números de solicitações simultâneas. O volume depende do tipo de preço associado à sua assinatura. Se muitas solicitações forem recebidas, ocorrerá um erro. A camada gratuita tem um limite mensal de solicitações.

O serviço pressupõe que os dados são transmitidos em tempo real. Se for enviado mais rapidamente, a solicitação é considerada em execução até que sua duração de áudio em tempo real tenha passado.

> [!NOTE]
> Oferecemos suporte para os [novos soquetes web](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) ainda. Se você planeja usar soquetes web com seu ponto de extremidade de fala personalizado, siga as instruções aqui.
>
> O novo suporte da [API REST](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) estará disponível em breve. Se você planeja chamar seu ponto de extremidade personalizado por meio de HTTP, siga as instruções aqui.
>

## <a name="send-requests-by-using-the-speech-client-library"></a>Enviar solicitações usando a biblioteca de clientes de fala

Para enviar solicitações para seu ponto de extremidade personalizado usando a biblioteca de clientes de fala, inicie o cliente de reconhecimento. Use o SDK de Fala de Cliente do [NuGet](http://nuget.org/). Procure *reconhecimento de fala* e selecione o pacote de reconhecimento de fala da Microsoft para sua plataforma. Alguns códigos de exemplo podem ser encontrados no [GitHub](https://github.com/Microsoft/Cognitive-Speech-STT-Windows). O SDK de Fala de Cliente fornece uma classe de fábrica **SpeechRecognitionServiceFactory**, que oferece os seguintes métodos:

  *   ```CreateDataClient(...)```: Um cliente de reconhecimento de dados.
  *   ```CreateDataClientWithIntent(...)```: Um cliente de reconhecimento de dados com intenção.
  *   ```CreateMicrophoneClient(...)```: Um cliente de reconhecimento de microfone.
  *   ```CreateMicrophoneClientWithIntent(...)```: Um cliente de reconhecimento de microfone com intenção.

Para obter a documentação detalhada, confira [API de Fala do Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home). Os pontos de extremidade do Serviço de Fala Personalizada oferece suporte para o mesmo SDK.

O cliente de reconhecimento de dados é apropriado para o reconhecimento de fala de dados, como um arquivo ou outra fonte de áudio. O cliente de reconhecimento de microfone é apropriado para o reconhecimento de fala do microfone. O uso de intenção de qualquer cliente pode retornar resultados de intenção estruturados do [Serviço Inteligente de Reconhecimento Vocal](https://www.luis.ai/) (LUIS), se você tiver criado um aplicativo LUIS para seu cenário.

Todos os quatro tipos de clientes podem ser instanciados de duas maneiras. O primeiro modo usa a API de Fala padrão dos Serviços Cognitivos. A segunda maneira permite que você especifique uma URL que corresponde ao seu ponto de extremidade personalizado criado com o Serviço de Fala Personalizada.

Por exemplo, você pode criar um **DataRecognitionClient** que envia solicitações para um ponto de extremidade personalizado usando o seguinte método:

```csharp
public static DataRecognitionClient CreateDataClient(SpeeechRecognitionMode speechRecognitionMode, string language, string primaryOrSecondaryKey, **string url**);
```

O **your_subscriptionId** e o **endpointURL** fazem referência à chave de assinatura e ao URL de soquetes web, respectivamente, na página **Informações sobre a implantação**.

A **AuthenticationUri** é usada para receber um token do serviço de autenticação. O URI deve ser configurado separadamente, como mostrado no código de exemplo a seguir.

Este código de exemplo mostra como usar o SDK de cliente:

```csharp
var dataClient = SpeechRecognitionServiceFactory.CreateDataClient(
  SpeechRecognitionMode.LongDictation,
  "en-us",
  "your_subscriptionId",
  "your_subscriptionId",
  "endpointURL");
// set the authorization Uri
dataClient.AuthenticationUri = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
```

> [!NOTE]
> Ao usar métodos **Criar** no SDK, você deve fornecer a ID de assinatura duas vezes devido à sobrecarga dos métodos **Criar**.
>

O Serviço de Fala Personalizada usa duas URLs diferentes para o reconhecimento de formato curto e formato longo. Ambas estão listadas na página **Implantações**. Use a URL de ponto de extremidade correta para a forma específica que deseja usar.

Para obter mais informações sobre como invocar os vários clientes de reconhecimento com seu ponto de extremidade personalizado, consulte a classe [SpeechRecognitionServiceFactory](https://www.microsoft.com/cognitive-services/Speech-api/documentation/GetStarted/GetStartedCSharpDesktop). A documentação nesta página faz referência à adaptação do modelo acústico, mas aplica todos os pontos de extremidade criados usando o Serviço de Fala Personalizado.

## <a name="send-requests-by-using-the-speech-protocol"></a>Enviar solicitações usando o Protocolo de Fala

Os pontos de extremidade mostrados para o [Protocolo de Fala](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/websocketprotocol) são pontos de extremidade para Abrir o Protocolo de Fala do soquete web de origem.

Atualmente, a única implementação de cliente oficial é para [JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Se você quiser iniciar com o exemplo fornecido, faça as seguintes alterações no código e criar o exemplo novamente:

1. Em _src\sdk\speech.browser\SpeechConnectionFactory.ts_, substitua o nome de host "wss://speech.platform.bing.com" com o nome de host mostrado na página de detalhes da sua implantação. Não insira o URI completo mas apenas o esquema de protocolo *wss* e o nome do host. Por exemplo: 

    ```JavaScript
    private get Host(): string {
        return Storage.Local.GetOrAdd("Host", "wss://<your_key_goes_here>.api.cris.ai");
    }
    ```

2. Defina o parâmetro _recognitionMode_ em _samples\browser\Samples.html_ de acordo com seus requisitos:
    * _RecognitionMode.Interactive_ dá suporte a solicitações de até 15 segundos.
    * _RecognitionMode.Conversation_ e _RecognitionMode.Dictation_ (ambos são equivalentes no Serviço de Fala Personalizado) dão suporte a solicitações de até 10 minutos.

3. Crie o exemplo usando “gulp build” antes de usá-lo.

> [!NOTE]
> Certifique-se de usar o URI correto para esse protocolo. O esquema necessário é *wss* (não *http* como no protocolo de cliente). 

Para obter mais informações, consulte a documentação da [API de Fala do Bing](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedclientlibraries).

## <a name="send-requests-by-using-http"></a>Enviar solicitações usando HTTP

Enviar uma solicitação para o ponto de extremidade personalizado usando um HTTP post é semelhante ao envio de uma solicitação HTTP para a API de Fala do Bing dos Serviços Cognitivos. Modifique a URL para refletir o endereço de sua implantação personalizada.

Há algumas restrições sobre solicitações enviadas via HTTP para o ponto de extremidade de Fala dos Serviços Cognitivos e os pontos de extremidade personalizados criados com esse serviço. A solicitação HTTP não pode retornar resultados parciais durante o processo de reconhecimento. Além disso, a duração das solicitações é limitada a 10 segundos para o conteúdo de áudio e 14 segundos em geral.

Para criar uma solicitação post, siga o mesmo processo que você usa para a API de Fala dos Serviços Cognitivos.

1. Obtenha um token de acesso usando sua ID de assinatura. Esse token é necessário para acessar o ponto de extremidade de reconhecimento. Ele pode ser reutilizado por 10 minutos.

    ```
    curl -X POST --header "Ocp-Apim-Subscription-Key:<subscriptionId>" --data "" "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    ```
      A **subscriptionId** deve ser definida para a ID de assinatura que você usa para essa implantação. A resposta é o token simples que é necessário para a próxima solicitação.

2. Postagem de áudio para o ponto de extremidade usando POST novamente.

    ```
    curl -X POST --data-binary @example.wav -H "Authorization: Bearer <token>" -H "Content-Type: application/octet-stream" "<https_endpoint>"
    ```

    O **token** é o token de acesso que você recebeu com a chamada anterior. O **https_endpoint** é o endereço completo do seu ponto de extremidade de conversão de fala em texto personalizado, mostrado na página **Informações sobre a implantação**.

Para obter mais informações sobre parâmetros de postagem HTTP e o formato de resposta, consulte a [API HTTP de Fala do Bing dos Serviços Cognitivos da Microsoft](https://www.microsoft.com/cognitive-services/speech-api/documentation/API-Reference-REST/BingVoiceRecognition#SampleImplementation).

## <a name="send-requests-by-using-the-service-library"></a>Enviar solicitações usando a biblioteca de serviço
A biblioteca de serviço permite que seu serviço faça uso de nuvem de transcrição do Microsoft Speech para converter linguagem falada em texto em tempo real, para que seu aplicativo cliente possa enviar áudio e receber os resultados do reconhecimento parcial simultaneamente e de maneira assíncrona. Detalhes do SDK do Serviço podem ser encontrados [aqui](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedcsharpservicelibrary)

> [!NOTE]
> Ao usar a biblioteca de serviço você precisa alterar o URI do provedor de autorização na implementação de **IAuthorizationProvider** para https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken.

## <a name="next-steps"></a>Próximas etapas
* Aumentar a precisão com seu [modelo acústico personalizado](cognitive-services-custom-speech-create-acoustic-model.md).
* Aumentar a precisão com um [modelo de linguagem personalizado](cognitive-services-custom-speech-create-language-model.md).
