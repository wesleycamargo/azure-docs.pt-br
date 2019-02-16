---
title: 'Início Rápido: Reconhecer fala, Java (Android) – Serviços da API de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em Java no Android usando SDK do Serviço de Fala
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: ac5798d61ecd0a45f7939be928429ababdee406f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877449"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em Java no Android usando SDK de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá a desenvolver um aplicativo Java para Android usando Speech SDK dos Serviços Cognitivos para transcrever conversão de fala em texto.
O aplicativo é baseado no Pacote do Maven do Speech SDK dos Serviços Cognitivos da Microsoft, versão 1.2.0 e Android Studio 3.1.
O SDK de Fala é atualmente compatível com dispositivos Android que têm processadores ARM de 32/64 bits e Intel x86/x64 compatíveis.

> [!NOTE]
> Para o SDK dos Dispositivos de Fala e o dispositivo Roobo, confira [SDK dos Dispositivos de Fala](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura do serviço de Fala para concluir este Início Rápido. Obtenha uma gratuitamente. Confira [Experimentar o serviço de Fala gratuitamente](get-started.md) para obter mais detalhes.

## <a name="create-and-configure-a-project"></a>Criar e configurar um projeto

1. Inicialize o Android Studio e escolha **Iniciar um novo projeto Android Studio** na janela Boas-Vindas.

    ![Captura de tela da janela de Boas-Vindas do Android Studio](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. O assistente **Criar Novo Projeto** é exibido. Na tela **Criar um Projeto Android**, insira **Início Rápido** como **nome do aplicativo**, **samples.speech.cognitiveservices.microsoft.com** como **domínio da empresa** e escolha um diretório do projeto. Deixe as caixas de seleção C++ e Kotlin desmarcadas e selecione **Avançar**.

   ![Captura de tela do assistente Criar Novo Projeto](media/sdk/qs-java-android-02-create-android-project.png)

1. Na tela **Dispositivos Android de Destino**, selecione apenas **Telefone e Tablet**. Na lista suspensa abaixo dele, escolha **API 23: Android 6.0 (Marshmallow)** e selecione **Avançar**.

   ![Captura de tela do assistente Criar Novo Projeto](media/sdk/qs-java-android-03-target-android-devices.png)

1. Na tela **Adicionar uma Atividade para Dispositivo Móvel**, selecione **Atividade Vazia** e clique em **Avançar**.

   ![Captura de tela do assistente Criar Novo Projeto](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. Na tela **Configurar Atividade**, use **MainActivity** como o nome da atividade e **atividade\_principal** como o nome do layout. Marque ambas as caixas de seleção e selecione **Concluir**.

   ![Captura de tela do assistente Criar Novo Projeto](media/sdk/qs-java-android-05-configure-activity.png)

O Android Studio demora algum tempo para preparar seu novo projeto Android. Em seguida, configure o projeto para saber sobre o SDK de Fala e usar o Java 8.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Fala dos Serviços Cognitivos é `1.2.0`.

O Speech SDK para Android é empacotado como um [AAR (Biblioteca Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias e as permissões necessárias do Android.
Está hospedado em um repositório Maven em https://csspeechstorage.blob.core.windows.net/maven/.

Configure seu projeto para usar o SDK de Fala. Abra a janela de estrutura do projeto escolhendo **Arquivo** > **Estrutura do Projeto** na barra de menus do Android Studio. Na janela Estrutura do Projeto, faça as seguintes alterações:

1. Na lista no lado esquerdo da janela, selecione **Projeto**. Edite as configurações de **Repositório de Biblioteca Padrão** anexando uma vírgula e nossa URL do repositório Maven entre aspas simples. 'https://csspeechstorage.blob.core.windows.net/maven/'

   ![Captura de tela da janela Estrutura do Projeto](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Na mesma tela, no lado esquerdo, selecione **aplicativo**. Em seguida, selecione a guia **Dependências** na parte superior da janela. Selecione o sinal de mais (+) verde e escolha **Dependência da biblioteca** no menu suspenso.

   ![Captura de tela da janela Estrutura do Projeto](media/sdk/qs-java-android-07-add-module-dependency.png)

1. Na janela que for exibida, insira o nome e a versão do SDK de Fala para Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.2.0`. Depois, selecione **OK**.
   O SDK de Fala deve ser adicionado à lista de dependências agora, conforme mostrado abaixo:

   ![Captura de tela da janela Estrutura do Projeto](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Clique na guia **Propriedades**. Para **Compatibilidade de Origem** e **Compatibilidade de Destino**, selecione **1.8**.

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Selecione **OK** para fechar a janela Estrutura do Projeto e aplicar as alterações ao projeto.

## <a name="create-user-interface"></a>Criar interface do usuário

Vamos criar uma interface do usuário básica para o aplicativo. Edite o layout da atividade principal `activity_main.xml`. Inicialmente, o layout inclui uma barra de título com o nome do aplicativo e um TextView contendo o texto "Olá, Mundo!".

* Clique no elemento TextView. Altere o atributo da ID no canto superior direito para `hello`.

* Na Paleta, no canto superior esquerdo da janela `activity_main.xml`, arraste um botão para o espaço vazio acima do texto.

* Nos atributos do botão à direita, no valor para o atributo `onClick`, digite `onSpeechButtonClicked`. Vamos escrever um método com esse nome para manipular o evento do botão.  Altere o atributo da ID no canto superior direito para `button`.

* Use o ícone de varinha mágica na parte superior do designer para inferir restrições de layout.

  ![Captura de tela do ícone de varinha mágica](media/sdk/qs-java-android-10-infer-layout-constraints.png)

O texto e a representação gráfica da sua interface do usuário agora devem ser semelhantes a isto:

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o arquivo de origem `MainActivity.java`. Substitua todo o código nesse arquivo pelo seguinte.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * O método `onCreate` inclui código que solicita permissões de microfone e Internet, além de inicializar a associação de plataforma nativa. Só é necessário configurar as associações de plataforma nativa uma vez. Isso deve ser feito no início, durante a inicialização do aplicativo.

   * O método `onSpeechButtonClicked` é, conforme observado anteriormente, o manipulador de clique do botão. Um pressionamento de botão aciona a transcrição de conversão de fala em texto.

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura.

1. Substitua também a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Conecte o dispositivo Android ao PC de desenvolvimento. Verifique se você habilitou o [modo de desenvolvimento e depuração de USB](https://developer.android.com/studio/debug/dev-options) no dispositivo.

1. Para compilar o aplicativo, pressione Ctrl+F9 ou escolha **Construir** > **Criar um Projeto** na barra de menus.

1. Para inicializar o aplicativo, pressione Shift+F10 ou escolha **Executar** > **Executar 'aplicativo'**.

1. Na janela de destino da implantação exibida, escolha seu dispositivo Android.

   ![Captura de tela da janela Selecionar Destino da Implantação](media/sdk/qs-java-android-12-deploy.png)

Pressione o botão no aplicativo para iniciar uma seção de reconhecimento de fala. Os próximos 15 segundos de fala em inglês serão enviados para o serviço de Fala e transcritos. O resultado é exibido no aplicativo Android e na janela logcat no Android Studio.

![Captura de tela do aplicativo Android](media/sdk/qs-java-android-13-gui-on-device.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
