---
title: Introdução à API de Reconhecimento de Fala da Microsoft usando a biblioteca de serviço em C# | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use a biblioteca de serviço de reconhecimento de Fala do Bing para converter o idioma falado em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: 5716f7a0af16bd3e40dea4468e2fae884f911718
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967550"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Início Rápido: Introdução ao serviço de reconhecimento de Fala do Bing em C&#35; para .NET no Windows

A biblioteca de serviço é para desenvolvedores que têm seu próprio serviço de nuvem e desejam chamar o serviço de fala do seu serviço. Se você deseja chamar o serviço de reconhecimento de fala de aplicativos de dispositivo associados, não use esse SDK. (usar outras bibliotecas de cliente ou APIs REST para fazer isso).

Para usar a biblioteca de serviço C#, instale o [pacote NuGet Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). Para a referência de API de biblioteca de clientes, consulte a [Biblioteca de área de trabalho em C# do Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

As seções a seguir descrevem como instalar, compilar e executar o aplicativo de exemplo em C#, usando a biblioteca de área de trabalho em C#.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="platform-requirements"></a>Requisitos de plataforma

O exemplo a seguir foi desenvolvido para Windows 8+ e o .NET Framework 4.5+ usando [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Obter o aplicativo de exemplo

Clone o exemplo do repositório [Exemplo de biblioteca de serviço em C# do Speech](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Inscreva-se na API de Reconhecimento de Fala e receba uma chave de assinatura de avaliação gratuita

O Speech API é parte dos Serviços Cognitivos (anteriormente Projeto Oxford). É possível obter chaves de assinatura de avaliação gratuita na página de [assinatura dos Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/). Após selecionar o Speech API, selecione **Obter chave de API** para obter a chave. Isso retorna uma chave primária e secundária. Ambas as chaves estão vinculadas à mesma cota, portanto, é possível usar uma das chaves.

> [!IMPORTANT]
> * Obtenha uma chave de assinatura. Antes de poder usar as bibliotecas de clientes do Speech, será necessário ter uma [chave de assinatura](https://azure.microsoft.com/try/cognitive-services/).
>
> * Use sua chave de assinatura. Com o aplicativo de exemplo de biblioteca de serviço C#, você precisa fornecer a chave de assinatura como um dos parâmetros de linha de comando. Para obter mais informações, consulte [Executar o aplicativo de exemplo](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Etapa 1: instalar o aplicativo de exemplo

1. Inicie o Visual Studio 2015 e selecione **Arquivo** > **Abrir** > **Projeto/Solução**.

2. Clique duas vezes para abrir o arquivo de Solução (.sln) do Visual Studio 2015 nomeado SpeechClient.sln. A solução é aberta no Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Etapa 2: compilar o aplicativo de exemplo

Pressione Ctrl+Shift+B ou selecione **Compilar** no menu de faixa de opções. Em seguida, selecione **Compilar Solução**.

## <a name="step-3-run-the-sample-application"></a>Etapa 3: executar o aplicativo de exemplo

1. Após a conclusão da compilação, pressione F5 ou selecione **Iniciar** no menu de faixa de opções para executar o exemplo.

2. Abra o diretório de saída para o exemplo, por exemplo, SpeechClientSample\bin\Debug. Pressione Shift + clique e selecione **Abrir janela de comando aqui**.

3. Execute `SpeechClientSample.exe` com os seguintes parâmetros

   * Arg [0]: Especifique arquivos WAV do áudio de entrada.
   * Arg [1]: Especifique a localidade de áudio.
   * Arg [2]: Especifique os modos de reconhecimento: *curto* para o `ShortPhrase` modo e *longo* para o `LongDictation` modo.
   * Arg [3]: Especifique a chave de assinatura para acessar o serviço de reconhecimento de fala.

## <a name="samples-explained"></a>Exemplos explicados

### <a name="recognition-modes"></a>Modos de reconhecimento

* `ShortPhrase`modo: uma declaração de até 15 segundos de duração. Conforme os dados são enviados ao servidor, o cliente recebe vários resultados parciais e um resultado final melhor.
* `LongDictation`modo: uma declaração de até 10 segundos de duração. Como os dados são enviados ao servidor, o cliente recebe vários resultados parciais e vários resultados finais, com base em onde o servidor indica pausas de sentença.

### <a name="supported-audio-formats"></a>Formatos com suporte de áudio

A API de fala suporta áudio/WAV usando os seguintes codecs:

* Canal único PCM
* Siren
* SirenSR

### <a name="preferences"></a>Preferências

Para criar um SpeechClient, você precisa primeiro criar um objeto de Preferências. O objeto de Preferências é um conjunto de parâmetros que define o comportamento do serviço de fala. Consiste nos seguintes campos:

* `SpeechLanguage`: o local do áudio enviado para o serviço de fala.
* `ServiceUri`: o ponto de extremidade usado para chamar o serviço de fala.
* `AuthorizationProvider`: Uma implementação de IAuthorizationProvider usada para buscar tokens para acessar o serviço de fala. Embora o exemplo forneça um provedor de autorização de serviços cognitivas, é altamente recomendável que você crie sua própria implementação para lidar com o cache de token.
* `EnableAudioBuffering`: Uma opção avançada. Consulte [Gerenciamento de Conexão](#connection-management).

### <a name="speech-input"></a>Entrada de fala

O objeto SpeechInput consiste em dois campos:

* **Áudio**: uma implementação de fluxo de sua escolha de onde o SDK recebe áudio. Ele pode ser qualquer [fluxo](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) que oferece suporte à leitura.
   > [!NOTE]
   > O SDK detecta o final do fluxo de quando o fluxo retorna **0** na leitura.

* **RequestMetadata**: metadados sobre a solicitação de fala. Para obter mais informações, veja a [referência](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Solicitação de fala

Após você ter instanciado objetos SpeechClient e SpeechInput, use RecognizeAsync para fazer uma solicitação de serviço de fala.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

Após a conclusão terminar, a tarefa retornada por RecognizeAsync termina. O último RecognitionResult é o final do reconhecimento. A tarefa pode falhar se o serviço ou o SDK falhar inesperadamente.

### <a name="speech-recognition-events"></a>Eventos de reconhecimento de fala

#### <a name="partial-results-event"></a>Eventos de resultados parciais

Esse evento é chamado sempre que o serviço de fala prevê o que você pode estar dizendo, mesmo antes de terminar de falar (se você usar `MicrophoneRecognitionClient`) ou terminou de enviar dados (se você usar `DataRecognitionClient`). Você pode assinar o evento usando `SpeechClient.SubscribeToPartialResult()`. Ou você pode usar o método de assinatura de eventos genéricos `SpeechClient.SubscribeTo<RecognitionPartialResult>()`.

**Formato de retorno** | DESCRIÇÃO |
------|------
**LexicalForm** | Este formulário é ideal para uso por aplicativos que precisam de resultados de reconhecimento de fala bruto, não processados.
**DisplayText** | A frase reconhecida com normalização de texto inverso, letras maiúsculas, pontuação e mascaramento de obscenidades aplicado. Obscenidades são mascaradas com asteriscos após o caractere inicial, por exemplo, "d\*\*\*." Este formulário é ideal para uso por aplicativos que exibem os resultados de reconhecimento de fala a um usuário.
**Confidence** | O nível de confiança da frase reconhecida representa o áudio associado, conforme definido pelo servidor de reconhecimento de fala.
**MediaTime** | A hora atual em relação ao início do fluxo de áudio (em unidades de 100 nanossegundos de tempo).
**MediaDuration** | A hora atual em relação ao início do fluxo de áudio (em unidades de 100 nanossegundos de tempo).

#### <a name="result-event"></a>Evento de resultado
Quando você termina de falar (no `ShortPhrase` modo), este evento é chamado. Você receberá n melhores opções para o resultado. No `LongDictation` modo, o evento pode ser chamado várias vezes, com base em onde o servidor indica a pausa da sentença. Você pode assinar o evento usando `SpeechClient.SubscribeToRecognitionResult()`. Ou você pode usar o método de assinatura de eventos genéricos `SpeechClient.SubscribeTo<RecognitionResult>()`.

**Formato de retorno** | DESCRIÇÃO |
------|------|
**RecognitionStatus** | O status em como o reconhecimento foi produzido. Por exemplo, foi gerado como resultado de reconhecimento bem-sucedido ou como resultado para cancelar a conexão, etc.
**Frases** | O conjunto de frases reconhecidas n-melhor com confiança o reconhecimento.

Para obter mais informações sobre os resultados de reconhecimento, consulte [Formato de saída](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Resposta de reconhecimento de fala

Exemplo de resposta de fala:
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High) 
```

## <a name="connection-management"></a>Gerenciamento de Conexão

A API utiliza uma única conexão de WebSocket por solicitação. Para excelente experiência do usuário, o SDK tentará reconectar-se ao serviço de fala e iniciar o reconhecimento do último RecognitionResult que recebeu. Por exemplo, se a solicitação de áudio for dois minutos, o SDK recebeu um RecognitionEvent na marca de um minuto, e ocorreu uma falha de rede depois de cinco segundos, o SDK inicia uma nova conexão que inicia a partir da marca de um minuto.

>[!NOTE]
>O SDK não busca novamente até a marca de um minuto, porque o fluxo não é compatível com a busca. Em vez disso, o SDK mantém um buffer interno que usa para armazenar em buffer o áudio e limpa o buffer que recebe eventos RecognitionResult.

## <a name="buffering-behavior"></a>Comportamento de buffer

Por padrão, os SDK realizar o buffer do áudio de forma que possa recuperar quando uma interrupção de rede ocorre. Em um cenário em que é preferível descartar o áudio perdido durante a desconexão da rede e reiniciar a conexão, é aconselhável desabilitar o buffer de áudio definindo `EnableAudioBuffering` o Objeto de preferências para `false`.

## <a name="related-topics"></a>Tópicos relacionados

[Referência de biblioteca de C# do Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
