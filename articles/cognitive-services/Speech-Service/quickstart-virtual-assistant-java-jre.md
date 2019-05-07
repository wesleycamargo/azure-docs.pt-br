---
title: 'Início Rápido: Assistente virtual personalizado que tem como prioridade o uso de voz (versão prévia), Java (Windows, Linux) – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Neste Início Rápido, você aprenderá a usar o SDK (Software Development Kit) de Fala dos Serviços Cognitivos em um aplicativo de console Java. Você aprenderá como é possível conectar seu aplicativo cliente a um bot do Bot Framework criado anteriormente, configurado para usar o canal de Fala do Direct Line e proporcionar uma experiência de assistente virtual que tem como prioridade o uso de voz.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: bidishac
ms.openlocfilehash: 4e9010bed54d0b2a7cb1a95b9e01e5ba02ea9fd5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025362"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>Início Rápido: Criar um assistente virtual que tem como prioridade o uso de voz com o SDK de Fala, Java

Neste artigo, você criará um aplicativo de console Java usando o [SDK de Fala dos Serviços Cognitivos](speech-sdk.md). O aplicativo se conectará a um bot criado anteriormente, configurado para usar o canal de Fala do Direct Line, enviar uma solicitação de voz e retornar uma atividade de resposta de voz (se configurado). O aplicativo é criado com o pacote Maven do SDK de Fala e o Java IDE do Eclipse no Windows, no Ubuntu Linux ou no macOS. Ele é executado em um JRE (Java Runtime Environment) 8 de 64 bits.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema operacional: Windows (64 bits), Ubuntu Linux 16.04/18.04 (64 bits) ou macOS 10.13 ou posterior
* [Java IDE do Eclipse](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Uma chave de assinatura do Azure para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).
* Um bot pré-configurado criado usando o Bot Framework versão 4.2 ou posterior. O bot precisa assinar o novo canal "Fala do Direct Line" para receber entradas de voz.

    > [!NOTE]
    > Atualmente, na versão prévia, o canal de Fala do Direct Line dá suporte apenas à região **westus2**.

    > [!NOTE]
    > A avaliação de 30 dias do tipo de preço Standard descrita em [Experimentar os Serviços de Fala gratuitamente](get-started.md) está restrita à região **westus** (não **westus2**) e, portanto, não é compatível com a Fala do Direct Line. As assinaturas da região **westus2** da Camada gratuita e Standard são compatíveis.

Se você estiver executando o Ubuntu 16.04/18.04, verifique se estas dependências estão instaladas antes de iniciar o Eclipse:

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Se você estiver executando o Windows (64 bits), verifique se instalou os Pacotes Redistribuíveis do Microsoft Visual C++ para a sua plataforma:
* [Baixar Pacotes Redistribuíveis do Microsoft Visual C++ para Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>Opcional: Introdução rápida

Este Início Rápido descreverá, passo a passo, como criar um aplicativo cliente simples para conectá-lo ao bot habilitado para fala. Se você preferir se aprofundar, o código-fonte completo e pronto para compilação usado neste início rápido estará disponível nos [Exemplos de SDK de Fala](https://aka.ms/csspeech/samples) na pasta `quickstart`.

## <a name="create-and-configure-project"></a>Criar e configurar o projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

Além disso, para habilitar o log, atualize o arquivo **pom.xml** para incluir a dependência a seguir.

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao projeto Java, selecione **Arquivo** > **Novo** > **Classe**.

1. Na janela **Nova Classe Java**, insira **speechsdk.quickstart** no campo **Pacote** e **Principal** no campo **Nome**.

   ![Captura de tela da janela Nova Classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Abra a classe **Main** recém-criada e substitua o conteúdo do arquivo `Main.java` pelo código inicial a seguir.

    ```java
    package speechsdk.quickstart;

    import java.io.IOException;
    import java.io.PipedOutputStream;
    import java.util.HashMap;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import com.microsoft.cognitiveservices.speech.ResultReason;
    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }
    }
    ```

1. No método **main**, primeiro você configurará o `BotConnectorConfig` e o usará para criar uma instância do `SpeechBotConnector`. Isso será conectado ao canal de Fala do Direct Line para interagir com seu bot. Uma instância de `AudioConfig` também é usada para especificar a origem da entrada de áudio. Neste exemplo, o microfone padrão é usado com `AudioConfig.fromDefaultMicrophoneInput()`.

    * Substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura, que pode ser obtida [aqui](get-started.md).
    * Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à sua assinatura.
    * Substitua a cadeia de caracteres `YourChannelSecret` por seu segredo do canal de Fala do Direct Line.

    > [!NOTE]
    > Atualmente, na versão prévia, o canal de Fala do Direct Line dá suporte apenas à região **westus2**.

    > [!NOTE]
    > A avaliação de 30 dias do tipo de preço Standard descrita em [Experimentar os Serviços de Fala gratuitamente](get-started.md) está restrita à região **westus** (não **westus2**) e, portanto, não é compatível com a Fala do Direct Line. As assinaturas da região **westus2** da Camada gratuita e Standard são compatíveis.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // your subscription key
    final String region = "YourServiceRegion"; // Your service region. Currently assumed to be westus2
    final BotConnectorConfig botConnectorConfig = BotConnectorConfig.fromSecretKey(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a SpeechjBotConnector instance
    final SpeechBotConnector botConnector = new SpeechBotConnector(botConnectorConfig, audioConfig);
    ```

1. `SpeechBotConnector` depende de vários eventos para comunicar suas atividades de bot, resultados de reconhecimento de fala e outras informações. Adicione esses ouvintes de eventos em seguida.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    botConnector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    botConnector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        botConnector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        String act = activityEventArgs.getActivity().serialize();
        log.info("Received activity: {}", act);
    });
    ```

1. Conecte o `SpeechBotConnector` à Fala do Direct Line invocando o método `connectAsync()`. Para testar seu bot, invoque o método `listenOnceAsync` para enviar a entrada de áudio do microfone. Além disso, use também o método `sendActivityAsync` para enviar uma atividade personalizada como uma cadeia de caracteres serializada. Essas atividades personalizadas podem fornecer dados adicionais que o bot usará na conversa.

    ```java
    botConnector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    botConnector.listenOnceAsync();

    // botConnector.sendActivityAsync(...)
    ```

1. Salve as alterações no arquivo `Main`.

1. Para dar suporte à reprodução de resposta, você adicionará outra classe que incluirá métodos de utilitário para dar suporte ao áudio. Para habilitar o áudio, adicione outra nova classe vazia ao projeto Java: selecione **Arquivo** > **Novo** > **Classe**.

1. Na janela **Nova Classe Java**, insira **speechsdk.quickstart** no campo **Pacote** e **AudioPlayer** no campo **Nome**.

   ![Captura de tela da janela Nova Classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Abra a classe **AudioPlayer** recém-criada e substitua-a pelo código fornecido abaixo.

    ```java
    import static javax.sound.sampled.AudioFormat.Encoding.PCM_SIGNED;

    import java.io.InputStream;
    import java.io.PipedInputStream;
    import java.io.PipedOutputStream;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    import java.util.concurrent.atomic.AtomicBoolean;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;


    public class AudioPlayer {

        public static final int SAMPLE_RATE = 16000; // 16Hz sampling rate
        public static final int SAMPLE_SIZE_IN_BITS = 16; // 16 bit PCM
        public static final int CHANNELS = 1; // Use Mono / Single channel

        public static final int FRAME_RATE = 16000;
        public static final int FRAME_SIZE = 2;

        private static final Logger log = LoggerFactory.getLogger(AudioPlayer.class);
        private AtomicBoolean isPlaying = new AtomicBoolean(false);
        private ExecutorService executorService = Executors.newSingleThreadExecutor();

        public boolean isPlaying() {
            return isPlaying.get();
        }

        public void stopPlaying() {
            isPlaying.set(false);
        }

        public void play(final PipedOutputStream pipedOutputStream) {
            // The current audio supported by the Microsoft Bot framework ~ 16-bit PCM encoding, 16KHz sampling rate.
            final AudioFormat defaultFormat = new AudioFormat(PCM_SIGNED, SAMPLE_RATE, SAMPLE_SIZE_IN_BITS, CHANNELS, FRAME_SIZE, FRAME_RATE, false);
            try {
                final PipedInputStream inputStream = new PipedInputStream(pipedOutputStream);

                executorService.submit(() -> {
                    try {
                        isPlaying.set(true);
                        play(inputStream, defaultFormat);
                        inputStream.close();
                    } catch (Exception e) {
                        log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
                    }
                });
            } catch (Exception e) {
                log.error("Exception thrown during playback. Message: {}", e.getMessage(), e);
            }
        }

        private void play(final InputStream inputStream, final AudioFormat targetFormat) throws Exception {
            final byte[] buffer = new byte[1024];
            final DataLine.Info info = new DataLine.Info(SourceDataLine.class, targetFormat);
            final SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
            line.open();
            if (line != null) {
                line.start();
                int bytesRead = 0;
                while (bytesRead != -1) {
                    bytesRead = inputStream.read(buffer, 0, buffer.length);
                    if (bytesRead != -1) {
                        line.write(buffer, 0, bytesRead);
                    }
                }
                line.drain();
                line.stop();
                line.close();
            }
        }
    }
    ```

1. Salve as alterações no arquivo `AudioPlayer`.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

Pressione F11 ou selecione **Executar** > **Depurar**.
O console exibirá uma mensagem "Diga algo". Nesse momento, você poderá falar uma frase ou uma sentença em inglês que o bot entenderá. Sua fala será transmitida ao bot por meio do canal de Fala do Direct Line, no qual ela será reconhecida e processada pelo bot, e a resposta será retornada como uma atividade. Se o bot retornar uma fala como resposta, o áudio será reproduzido usando a classe `AudioPlayer`.

![Captura de tela da saída do console após o reconhecimento com êxito](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Próximas etapas

Amostras adicionais, por exemplo, como ler a fala de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar amostras de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Início Rápido: Tradução de fala, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
