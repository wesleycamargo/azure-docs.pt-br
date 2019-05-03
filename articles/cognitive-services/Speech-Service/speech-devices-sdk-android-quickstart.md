---
title: 'Início Rápido: Executar os SDK de dispositivos de fala no Android - serviços de fala'
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para a guia de Introdução com um SDK do Android dispositivos de fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: d5af2bb61eeb986f02a31d45ff9236ecc0c8427e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026190"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>Início Rápido: Executar o aplicativo de exemplo do SDK de dispositivos de fala no Android

Neste início rápido, você aprenderá a usar o SDK de dispositivos de fala para o Android para criar um produto habilitados para fala.

Este guia exige uma [serviços Cognitivos do Azure](get-started.md) conta com um recurso de serviços de fala. Se não tiver uma conta, você poderá usar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de assinatura.

O código-fonte para o aplicativo de exemplo é incluído com o SDK de Dispositivos de Fala. Também está [disponível no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a usar o SDK de dispositivos de fala, você precisará:

* Siga as instruções fornecidas com sua [kit de desenvolvimento](get-speech-devices-sdk.md) para ligar o dispositivo.

* Baixe a versão mais recente do [fala dispositivos SDK](https://aka.ms/sdsdk-download)e extraia o arquivo. zip no diretório de trabalho.
   > [!NOTE]
   > O arquivo. zip inclui o aplicativo de exemplo do Android.

* Para obter um [chave de assinatura do Azure para serviços de fala](get-started.md)

* Se você planeja usar os serviços de fala para identificar tentativas (ou ações) de declarações de usuário, será necessário um [Service (Luis reconhecimento vocal)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) assinatura. Para saber mais sobre o LUIS e reconhecimento de intenção, consulte [reconhecer intenções de fala com o LUIS, C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp).

    Você pode [criar um modelo simples de LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/) ou usar o modelo LUIS, LUIS-example.json. O exemplo de modelo LUIS está disponível na [site de download do SDK de Dispositivos de Fala](https://aka.ms/sdsdk-luis). Para carregar o arquivo JSON do seu modelo para o [portal LUIS](https://www.luis.ai/home), selecione **Importar novo aplicativo**e, em seguida, selecione o arquivo JSON.

* Instale o [Android Studio](https://developer.android.com/studio/) e [Vysor](https://vysor.io/download/) em seu computador.

## <a name="set-up-the-device"></a>Configurar o dispositivo

1. Inicie o Vysor no seu computador.

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. Seu dispositivo deve estar listado em **Escolha um dispositivo**. Clique no botão **Exibir** ao lado do dispositivo.

1. Conecte-se à sua rede sem fio, selecionando o ícone de pasta e, em seguida, selecione **Configurações** > **WLAN**.

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

## <a name="run-the-sample-application"></a>Executar o aplicativo de exemplo

Para validar sua instalação do kit de desenvolvimento, compilar e instalar o aplicativo de exemplo:

1. Inicie o Android Studio.

1. Selecionar **Abrir um projeto existente do Android Studio**.

   ![Android Studio - Abrir um projeto existente](media/speech-devices-sdk/qsg-5.png)

1. Vá para C:\SDSDK\Android-Sample-Release\example. Selecione **Ok** para abrir o projeto de exemplo.

1. Adicione sua chave de assinatura de Fala ao código-fonte. Se você quiser experimentar o reconhecimento de intenção, adicione também sua chave de assinatura [Serviço Inteligente de Reconhecimento Vocal](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) e ID do aplicativo.

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

   > [!TIP]
   > Você também pode [criar uma palavra de ativação personalizada](speech-devices-sdk-create-kws.md).

    Para usar uma nova palavra wake, atualize as duas linhas seguintes no `MainActivity.java`e copie o pacote de palavra de ativação para seu aplicativo. Por exemplo, para usar a palavra de ativação 'Machine' de kws de pacote de word o wake-machine.zip:

   * Copie o pacote de palavra de ativação para a pasta "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   * Atualização de `MainActivity.java` com a palavra-chave e o nome do pacote:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Atualize as seguintes linhas, que contêm as configurações de geometria de matriz de microfone:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Esta tabela lista os valores com suporte:

   |Variável|Significado|Valores disponíveis|
   |--------|-------|----------------|
   |`DeviceGeometry`|Configuração de microfone físico|Para um kit de desenvolvimento circular: `Circular6+1` |
   |||Para um kit de desenvolvimento linear: `Linear4`|
   |`SelectedGeometry`|Configuração do microfone do software|Para um kit de desenvolvimento circular que usa todos os microfones: `Circular6+1`|
   |||Para um kit de desenvolvimento circular que usa os quatro microfones: `Circular3+1`|
   |||Para um kit de desenvolvimento linear que usa todos os microfones: `Linear4`|
   |||Para um kit de desenvolvimento linear que usa dois microfones: `Linear2`|

1. Para compilar o aplicativo, no menu **Executar**, selecione **Executar 'aplicativo'**. A caixa de diálogo **Selecionar o Destino de Implantação** aparece.

1. Selecione seu dispositivo e, em seguida, selecione **OK** para implantar o aplicativo no dispositivo.

    ![Selecione a caixa de diálogo Destino da Implantação](media/speech-devices-sdk/qsg-7.png)

1. O aplicativo de exemplo do SDK de Dispositivos de Fala é iniciado e exibe as seguintes opções:

   ![Aplicativo de exemplo e opções do SDK de Dispositivos de Fala de Exemplo](media/speech-devices-sdk/qsg-8.png)

1. Experimento!

## <a name="troubleshooting"></a>solução de problemas

   Se você não pode se conectar ao dispositivo de fala. Digite o seguinte comando em uma janela de Prompt de comando. Ele retornará uma lista de dispositivos:

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > Esse comando usa o Android Debug Bridge `adb.exe`, que é parte da instalação do Android Studio. Essa ferramenta está localizada em C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools. Você pode adicionar esse diretório para o caminho para que seja mais conveniente para invocar `adb`. Caso contrário, você deve especificar o caminho completo para a instalação do adb.exe em cada comando que invoca `adb`.
   >
   > Se você vir um erro `no devices/emulators found` marcar seu cabo USB está conectado e certifique-se de um cabo de alta qualidade é usado.
   >

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Examinar as notas de versão](devices-sdk-release-notes.md)
