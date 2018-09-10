---
title: Introdução ao SDK de Dispositivos de Fala
description: Pré-requisitos e instruções para começar a trabalhar com o SDK de Dispositivos de Fala.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 463a015b7c01dafc5b30de56b95fa0510ffb98e4
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42424362"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>Introdução ao SDK de Dispositivos de Fala

Este artigo descreve como configurar seu PC de desenvolvimento e o kit de desenvolvimento de Dispositivo de Fala para o desenvolvimento de dispositivos habilitados para fala usando o SDK de Dispositivos de Fala. Você, em seguida, criará e implantará um aplicativo de exemplo para o dispositivo. 

O código-fonte para o aplicativo de exemplo é incluído com o SDK de Dispositivos de Fala e também está [disponível no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar o desenvolvimento com o SDK de Dispositivos de Fala, obtenha as informações e o software necessários.

* Obtenha um kit de desenvolvimento [de Roobo](http://ddk.roobo.com/). Os kits estão disponíveis com configurações de matriz de microfone linear ou circular; escolha o apropriado para suas necessidades.

    |Configuração do kit de desenvolvimento|Local do locutor|
    |-----------------------------|------------|
    |Circular|Qualquer direção do dispositivo|
    |Linear|Na frente do dispositivo|

* Obter a versão mais recente do SDK de Dispositivos de Fala, incluindo um aplicativo de exemplo do Android, a partir do [site de download](https://shares.datatransfer.microsoft.com/) do SDK de Dispositivos de Fala. Extraia o arquivo ZIP para uma pasta local (como `C:\SDSDK`).

* Instale o [Android Studio](https://developer.android.com/studio/) e [Vysor](http://vysor.io/download/) em seu computador.

* Obtenha uma [chave de assinatura](get-started.md) do serviço de Fala. Você pode obter uma avaliação gratuita de 30 dias, ou obter uma chave do seu painel do Azure.

* Se você quiser usar o reconhecimento de intenção do serviço de Fala, assine o [Serviço de reconhecimento vocal](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUÍS) e [obter uma chave de assinatura](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription). 

    Você pode [criar um modelo simples de LUIS](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/) ou usar o modelo de exemplo de LUIS `LUIS-example.json`, disponível no [site de download](https://shares.datatransfer.microsoft.com/) do SDK de Dispositivos a Fala. Carregue o arquivo JSON do modelo para o [portal LUIS](https://www.luis.ai/home) clicando em **Importar novo aplicativo** e escolhendo o arquivo JSON.

## <a name="set-up-the-development-kit"></a>Configurar o kit de desenvolvimento

1. Potencialize o kit de desenvolvimento usando um cabo mini USB conectado a um PC ou um adaptador de energia. Um indicador de energia verde deve acender sob o quadro superior.

1. Conecte o kit de desenvolvimento a um computador usando um segundo cabo mini USB.

    ![conectar o kit de desenvolvimento](media/speech-devices-sdk/qsg-1.png)

1. Posicione o kit de desenvolvimento adequadamente.

    |Configuração do kit de desenvolvimento|Orientação|
    |-----------------------------|------------|
    |Circular|Na vertical, com microfones voltada para o teto|
    |Linear|Em seu lado, com microfones voltada para você (mostrado abaixo)|

    ![orientação do kit de desenvolvimento linear](media/speech-devices-sdk/qsg-2.png)

1. Instalar os certificados e o arquivo de tabela (palavra-chave) do word de ativação e defina as permissões do dispositivo de som. Digite os seguintes comandos em uma janela de comando.

    > [!NOTE]
    > Esses comandos usam a ponte de depuração do Android, `adb.exe`, que faz parte da instalação do Android Studio. Esta ferramenta pode ser encontrada em `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools`. Você pode adicionar esse diretório para o caminho para que seja mais conveniente para invocar `adb`. Caso contrário, você deve especificar o caminho completo para a instalação do `adb.exe` em cada comando que invoca `adb`.

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > Ative o mudo do microfone e alto-falantes de seu computador. Dessa forma, você pode se certificar se está trabalhando com os microfones do kit de desenvolvimento, e não ativará acidentemente o dispositivo com o áudio do seu PC.
    
1.  Inicie o Vysor no seu computador.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  O dispositivo deve estar listado em "Escolher um dispositivo". Clique no botão **Exibir** ao lado dele. 
 
1.  Conecte-se à rede sem fio clicando no ícone de pasta, depois em **Configurações** e, em seguida, **WLAN**.

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)
 
 > [!NOTE]
 > Se a sua empresa tem políticas relacionadas à conexão de dispositivos ao sistema Wi-Fi, é necessário obter o Endereço Mac e entrar em contato com o departamento de TI para saber como conectar-se ao sistema Wi-Fi. Para localizar o Endereço Mac do kit de desenvolvimento, clique no ícone de pasta de arquivo na área de trabalho do kit de desenvolvimento, depois em **Configurações**, pesquise "Endereço Mac" e, sem seguida, clique em **Endereço Mac** para acessar **WLAN Avançada** e anote o endereço Mac encontrado na parte inferior. Além disso, algumas empresas podem ter um tempo limite de quanto tempo um dispositivo pode ser conectado a seus sistemas Wi-Fi. Talvez seja necessário estender o registro do kit de desenvolvimento com o sistema Wi-Fi após um determinado número de dias.  
 
 
   ![Pasta de arquivo do Vysor](media/speech-devices-sdk/qsg-10.png)
   
   ![Endereço Mac do Vysor](media/speech-devices-sdk/qsg-11.png)
   
   
 > Se você quiser conectar um alto-falante ao o kit de desenvolvimento, você pode conectá-lo à saída de linha de Áudio. Você também deve escolher um alto-falante de 3,5 mm de boa qualidade.
 
   ![Áudio do Vysor](media/speech-devices-sdk/qsg-14.png)
 
## <a name="run-a-sample-application"></a>Executar um aplicativo de exemplo

Para executar os testes Roobo e validar sua instalação do kit de desenvolvimento, crie e instale o aplicativo de exemplo.

1.  Inicie o Android Studio.

1.  Escolha abrir um projeto existente do Android Studio.

    ![Android Studio - Abrir projeto existente](media/speech-devices-sdk/qsg-5.png)
 
1.  Navegue até `C:\SDSDK\Android-Sample-Release\example`, em seguida, clique em **OK** para abrir o projeto de exemplo.
 
1.  Adicione sua chave de assinatura de Fala ao código-fonte. Se você quiser experimentar o reconhecimento de intenção, também adicione sua chave de assinatura e a ID do aplicativo do [Serviço de reconhecimento vocal](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

    As chaves e informações do aplicativo vai nas seguintes linhas no arquivo de origem `MainActivity.java`.

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. A palavra de ativação padrão (palavra-chave) é "Computador".  Se desejar, você pode tentar uma das outras palavras de ativação fornecidas, "Máquina" e "Assistente". Os arquivos de recurso para essas palavras alternativas podem ser encontrados no SDK de Dispositivos de Fala na pasta "palavra-chave". Por exemplo, `C:\SDSDK\Android-Sample-Release\keyword\Computer` contém os arquivos usados para "Computador".

    Você também pode [criar uma palavra de ativação personalizada](speech-devices-sdk-create-kws.md).

    Para instalar a palavra de ativação desejada:
 
    * Criar uma pasta `keyword` na pasta \data\ no dispositivo, executando os comandos a seguir na janela de comando.

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * Copie os arquivos `kws.table`, `kws_g.fst`, `kws_k.fst`, e `words_kw.txt`) para a pasta de \data\keyword\ do dispositivo. Execute os seguintes comandos em uma janela de comando. Se você tiver criado uma [palavra de ativação personalizada](speech-devices-sdk-create-kws.md), o arquivo kws.table gerado a partir da Web estará no mesmo diretório que os arquivos `kws.table`, `kws_g.fst`, `kws_k.fst` e `words_kw.txt`. Use adb push C:\SDSDK\Android-Sample-Release\keyword\[wake_word_name]\kws.table/data/palavra-chave de comando para efetuar push do arquivo kws.table para o kit de desenvolvimento.

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_g.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```
    
    * Consulte esses arquivos no aplicativo de exemplo. Localize as seguintes linhas em `MainActivity.java`. Certifique-se de que a palavra-chave especificada é a que você está usando e que o caminho aponta para o arquivo `kws.table` enviado para o dispositivo.
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > Em seu próprio código, você pode usar o arquivo `kws.table` para criar uma instância de modelo de palavra-chave e iniciar o reconhecimento da seguinte maneira.
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  Atualize as linhas a seguir que contém as configurações de geometria de matriz do microfone.

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |Variável|Significado|Valores disponíveis|
    |--------|-------|----------------|
    |`DeviceGeometry`|Configuração de microfone físico|`Circular6+1` para kit de desenvolvimento circular|
    ||| `Linear4` para kit de desenvolvimento linear|
    |`SelectedGeometry`|Configuração do microfone do software|`Circular6+1` para kit de desenvolvimento circular que usam todos os microfones|
    |||`Circular3+1` para kit de desenvolvimento circular que usam quatro microfones|
    |||`Linear4` para kit de desenvolvimento linear que usam todos os microfones|
    |||`Linear2` para kit de desenvolvimento linear que usam dois microfones|


1.  Criar o aplicativo escolhendo **Executar 'aplicativo'** no menu de execução. A caixa de diálogo Selecionar destino de implantação é exibida. Escolha seu dispositivo e clique em **OK** para implantar o aplicativo no dispositivo.

    ![selecionar destino de implantação](media/speech-devices-sdk/qsg-7.png)
 
1.  O aplicativo de exemplo do SDK de Dispositivos de Fala é iniciado, exibindo as opções mostradas aqui.

    ![aplicativo de dispositivo de fala de exemplo](media/speech-devices-sdk/qsg-8.png)

1. Brinque com ele!

## <a name="troubleshooting"></a>solução de problemas

Se houver falhas de certificado ao usar o serviço de Fala, verifique se o dispositivo tem a data e hora corretas. Vá para **Configurações**, clique em **Data e hora** em Sistema e **Selecionar fuso horário** para ser o fuso horário atual. Mantenha **Data e hora automática** ativada (ON). Ao ver que a hora do kit de desenvolvimento corresponde à hora do seu PC, você saberá que o kit de desenvolvimento está conectado à internet. 

 ![Pasta de arquivo do Vysor](media/speech-devices-sdk/qsg-12.png)
 
 ![Pasta de arquivo do Vysor](media/speech-devices-sdk/qsg-13.png)

Para obter informações de desenvolvimento, consulte [guia de desenvolvimento](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf) do Roobo.

O Roobo fornece uma ferramenta que captura todo o áudio para flash de memória, o que pode ajudar a solucionar problemas de áudio. Uma versão da ferramenta é fornecida para cada configuração do kit de desenvolvimento. Escolha seu dispositivo no [site de Roobo](http://ddk.roobo.com/), em seguida, clique no link **ferramentas de ROOBO** na parte inferior da página.
