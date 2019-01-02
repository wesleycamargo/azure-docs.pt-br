---
title: Introdução ao SDK de Dispositivos de Fala – Serviços de Fala
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para começar a trabalhar com o SDK de Dispositivos de Fala.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 46f7762a815a7fa4aa4663d9ac6e7c6001ea345c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097175"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Introdução ao SDK de Dispositivos de Fala

Este artigo descreve como configurar seu PC de desenvolvimento e o kit de desenvolvimento de Dispositivo de Fala para o desenvolvimento de dispositivos habilitados para fala usando o SDK de Dispositivos de Fala. Em seguida, você criará e implantará um aplicativo de exemplo para o dispositivo.

O código-fonte para o aplicativo de exemplo é incluído com o SDK de Dispositivos de Fala. Também está [disponível no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar o desenvolvimento com o SDK de Dispositivos de Fala, obtenha as informações e o software necessários:

* Obtenha um [kit de desenvolvimento de ROOBO](http://ddk.roobo.com/). Os kits estão disponíveis com configurações de matriz de microfone linear ou circular. Escolha a configuração correta para suas necessidades.

    |Configuração do kit de desenvolvimento|Local do locutor|
    |-----------------------------|------------|
    |Circular|Qualquer direção do dispositivo|
    |Linear|Na frente do dispositivo|

* Obter a versão mais recente do SDK de Dispositivos de Fala, que inclui um aplicativo de exemplo do Android, a partir do [site de download do SDK de dispositivos de fala](https://shares.datatransfer.microsoft.com/). Extraia o arquivo ZIP para uma pasta local como C:\SDSDK.

* Instale o [Android Studio](https://developer.android.com/studio/) e [Vysor](http://vysor.io/download/) em seu computador.

* Obter uma [chave de assinatura do serviço de fala](get-started.md). Você pode obter uma avaliação gratuita de 30 dias ou obter uma chave do painel do Azure.

* Se você quiser o reconhecimento de intenção de serviço de fala, increva-se no [Serviço Inteligente de Reconhecimento Vocal](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) e [obtenha uma chave de assinatura](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription).

    Você pode [criar um modelo simples de LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) ou usar o modelo LUIS, LUIS-example.json. O exemplo de modelo LUIS está disponível na [site de download do SDK de Dispositivos de Fala](https://shares.datatransfer.microsoft.com/). Para carregar o arquivo JSON do seu modelo para o [portal LUIS](https://www.luis.ai/home), selecione **Importar novo aplicativo**e, em seguida, selecione o arquivo JSON.

## <a name="set-up-the-development-kit"></a>Configurar o kit de desenvolvimento

1. Conectar o kit de desenvolvimento para um PC ou adaptador de energia usando um cabo USB mini. Quando o kit é conectado, um indicador de energia verde acende no painel superior.

1. Conecte o kit de desenvolvimento a um computador usando um segundo cabo USB mini.

    ![Conectar o kit de desenvolvimento](media/speech-devices-sdk/qsg-1.png)

1. Oriente seu kit de desenvolvimento para configuração circular ou linear.

    |Configuração do kit de desenvolvimento|Orientação|
    |-----------------------------|------------|
    |Circular|Na vertical, com microfones voltada para o teto|
    |Linear|Em seu lado, com os microfones voltados para o usuário (mostrado na imagem a seguir)|

    ![Orientação do kit de desenvolvimento linear](media/speech-devices-sdk/qsg-2.png)

1. Instalar os certificados e o arquivo de tabela (palavra-chave) do word de ativação e defina as permissões do dispositivo de som. Digite os seguintes comandos em uma janela de Prompt de comando:

   ```
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Esses comandos usam a ponte de depuração do Android, `adb.exe`, que faz parte da instalação do Android Studio. Essa ferramenta está localizada em C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools. Você pode adicionar esse diretório para o caminho para que seja mais conveniente para invocar `adb`. Caso contrário, você deve especificar o caminho completo para a instalação do adb.exe em cada comando que invoca `adb`.

    > [!TIP]
    > Coloque o microfone e alto-falante no mudo para certificar-se de que você está trabalhando com microfones do kit de desenvolvimento de seu PC. Dessa forma, você não dispara acidentalmente o dispositivo com o áudio do PC.

1.  Inicie o Vysor no seu computador.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  Seu dispositivo deve estar listado em **Escolha um dispositivo**. Clique no botão **Exibir** ao lado do dispositivo.

1.  Conecte-se à sua rede sem fio, selecionando o ícone de pasta e, em seguida, selecione **Configurações** > **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > Se sua empresa tem políticas sobre como conectar dispositivos ao seu sistema de Wi-Fi, é neecessário obter o endereço MAC e entrar em contato com seu departamento de TI sobre como conectar-se ao Wi-Fi de sua empresa.
    >
    > Para localizar o endereço MAC do kit de desenvolvimento, selecione o ícone de pasta de arquivo na área de trabalho do kit de desenvolvimento.
    >
    >  ![Pasta de arquivo do Vysor](media/speech-devices-sdk/qsg-10.png)
    >
    > Escolha a opção **Configurações**. Pesquise "endereço mac" e, em seguida, selecione **endereço Mac** > **WLAN Avançado**. Anote o endereço MAC que aparece na parte inferior da caixa de diálogo.
    >
    > ![Endereço MAC do Vysor](media/speech-devices-sdk/qsg-11.png)
    >
    > Algumas empresas podem ter um tempo limite de quanto tempo um dispositivo pode ser conectado a seus sistemas Wi-Fi. Talvez seja necessário estender o registro do kit de desenvolvimento com o sistema Wi-Fi após um determinado número de dias.
    >
    > Se você quiser conectar um alto-falante ao o kit de desenvolvimento, você pode conectá-lo à saída de linha de Áudio. Você deve escolher um palestrante de boa qualidade, mm 3.5.
    >
    > ![Áudio do Vysor](media/speech-devices-sdk/qsg-14.png)

## <a name="run-a-sample-application"></a>Executar um aplicativo de exemplo

Para executar os testes ROOBO e validar sua instalação do kit de desenvolvimento, crie e instale o aplicativo de exemplo:

1.  Inicie o Android Studio.

1.  Selecionar **Abrir um projeto existente do Android Studio**.

    ![Android Studio - Abrir um projeto existente](media/speech-devices-sdk/qsg-5.png)

1.  Vá para C:\SDSDK\Android-Sample-Release\example. Selecione **Ok** para abrir o projeto de exemplo.

1.  Adicione sua chave de assinatura de Fala ao código-fonte. Se você quiser experimentar o reconhecimento de intenção, adicione também sua chave de assinatura [Serviço Inteligente de Reconhecimento Vocal](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) e ID do aplicativo.

    Suas chaves e as informações do aplicativo vão nas linhas a seguir no arquivo de origem MainActivity.java:

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app ID]"
    ```

1. A palavra de ativação padrão (palavra-chave) é "Computador". Você também pode tentar uma das outras palavras de ativação fornecidas, "Máquina" e "Assistente". Os arquivos de recurso para essas palavras alternativas de ativação estão no SDK de Dispositivos de Fala, na pasta de palavra-chave. Por exemplo, C:\SDSDK\Android-Sample-Release\keyword\Computer contém os arquivos usados para a palavra de ativação "Computador".

    Você também pode [criar uma palavra de ativação personalizada](speech-devices-sdk-create-kws.md).

    Para instalar a palavra de ativação que você deseja usar:

    * Crie uma pasta de palavra-chave na pasta de dados no dispositivo, executando os seguintes comandos em uma janela de Prompt de comando:

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * Copie os arquivos kws.table, kws_k.fst e words_kw.txt para a pasta \ data \ keyword do dispositivo. Execute os seguintes comandos em uma janela de Prompt de comando. Se você criou uma [palavra de ativação personalizada](speech-devices-sdk-create-kws.md), o arquivo kws.table gerado a partir da Web estará no mesmo diretório que os arquivos kws.table, kws_k.fst e words_kw.txt. Para uma palavra de ativação personalizado, use o `adb push C:\SDSDK\Android-Sample-Release\keyword\[wake_word_name]\kws.table /data/keyword` comando para enviar por push o arquivo kws.table ao kit de desenvolvimento:

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```

    * Consulte esses arquivos no aplicativo de exemplo. Localize as seguintes linhas em MainActivity.java. Certifique-se de que a palavra-chave especificada é a que você está usando e que o caminho aponta para o arquivo `kws.table` enviado para o dispositivo.

        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > Em seu próprio código, você pode usar o arquivo kws.table para criar uma instância de modelo de palavra-chave e iniciar o reconhecimento:
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Atualize as seguintes linhas, que contêm as configurações de geometria de matriz de microfone:

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```
    A tabela a seguir descreve os valores disponíveis:

    |Variável|Significado|Valores disponíveis|
    |--------|-------|----------------|
    |`DeviceGeometry`|Configuração de microfone físico|Para um kit de desenvolvimento circular: `Circular6+1` |
    |||Para um kit de desenvolvimento linear: `Linear4`|
    |`SelectedGeometry`|Configuração do microfone do software|Para um kit de desenvolvimento circular que usa todos os microfones: `Circular6+1`|
    |||Para um kit de desenvolvimento circular que usa os quatro microfones: `Circular3+1`|
    |||Para um kit de desenvolvimento linear que usa todos os microfones: `Linear4`|
    |||Para um kit de desenvolvimento linear que usa dois microfones: `Linear2`|


1.  Para compilar o aplicativo, no menu **Executar**, selecione **Executar 'aplicativo'**. A caixa de diálogo **Selecionar o Destino de Implantação** aparece.

1. Selecione seu dispositivo e, em seguida, selecione **OK** para implantar o aplicativo no dispositivo.

    ![Selecione a caixa de diálogo Destino da Implantação](media/speech-devices-sdk/qsg-7.png)

1.  O aplicativo de exemplo do SDK de Dispositivos de Fala é iniciado e exibe as seguintes opções:

    ![Aplicativo de exemplo e opções do SDK de Dispositivos de Fala de Exemplo](media/speech-devices-sdk/qsg-8.png)

1. Experimento!

## <a name="troubleshooting"></a>solução de problemas

### <a name="certificate-failures"></a>Falhas de certificado

Se você receber falhas de certificado ao usar o serviço de Fala, certifique-se de que seu dispositivo esteja com a data e hora corretas:

1. Vá para **Configurações**. Em **Sistema**, selecione **Data e hora**.

    ![Em Configurações, selecione a Data e hora](media/speech-devices-sdk/qsg-12.png)

1. Mantenha a opção selecionada **Data e hora automática**. Em **Selecionar fuso horário**, selecione seu fuso horário atual.

    ![Selecione as opções de data e fuso horário](media/speech-devices-sdk/qsg-13.png)

    Quando você vir que o tempo do kit de desenvolvimento corresponde o tempo em seu computador, o kit de desenvolvimento estará conectado à internet.

    Para obter mais informações de desenvolvimento, consulte o [Guia de desenvolvimento ROOBO](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

### <a name="audio"></a>Áudio

ROOBO fornece uma ferramenta que captura todo áudio da memória flash. Isso pode ajudá-lo a solucionar problemas de áudio. Uma versão da ferramenta é fornecida para cada configuração do kit de desenvolvimento. No site [ROOBO](http://ddk.roobo.com/), selecione seu dispositivo e, em seguida, selecione o link **ROOBO Tools** na parte inferior da página.
