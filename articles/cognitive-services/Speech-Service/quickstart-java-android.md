---
title: 'Início Rápido: Saiba como reconhecer fala em Java no Android usando SDK de Fala dos Serviços Cognitivos'
titleSuffix: Microsoft Cognitive Services
description: Saiba como reconhecer fala em Java no Android usando SDK de Fala dos Serviços Cognitivos
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: fmegen
ms.openlocfilehash: bbf10d9bd0912dab22c81e2bb850ede52a3bd9e3
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "41929906"
---
# <a name="quickstart-recognize-speech-in-java-on-android-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em Java no Android usando SDK de Fala

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá como criar um aplicativo Java para Android usando SDK de Fala dos Serviços Cognitivos para transcrever conversão de fala em texto.
O aplicativo é baseado no Pacote do Maven do SDK de Fala dos Serviços Cognitivos da Microsoft, versão 0.6.0, Android Studio 3.1.

> [!NOTE]
> Para o SDK dos Dispositivos de Fala e o dispositivo Roobo, visite a página [SDK dos Dispositivos de Fala](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura para o serviço de fala. Veja [Experimente o serviço de fala gratuitamente](get-started.md).
* Um PC (Windows, Linux, Mac) com capacidade para executar o Android Studio.
* Versão 3.1 do [Android Studio](https://developer.android.com/studio/).
* Um dispositivo Android baseado em ARM (API 23: Android 6.0 Marshmallow ou superior) [habilitado para desenvolvimento](https://developer.android.com/studio/debug/dev-options) com um microfone funcionando.

## <a name="create-an-android-studio-project"></a>Criar um projeto do Android Studio

Inicie o Android Studio e selecione **Iniciar um novo projeto do Android Studio**.

![](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

No assistente **Criar Novo Projeto** que aparece, faça as seguintes opções:

1. Na tela **Criar um Projeto Android**, insira **Início Rápido** como **nome do aplicativo**, **samples.speech.cognitiveservices.microsoft.com** como **domínio da empresa** e escolha um local do projeto. Deixe as caixas de seleção desmarcadas e clique em **Avançar**.

   ![](media/sdk/qs-java-android-02-create-android-project.png)

1. Na tela **Direcionar Dispositivos Android**, marque **Telefone e Tablet** como a única opção e escolha **API 23: Android 6.0 (Marshmallow)** no menu suspenso abaixo dele e clique em **Avançar**.

   ![](media/sdk/qs-java-android-03-target-android-devices.png)

1. Na tela **Adicionar uma Atividade para Celular**, selecione **Atividade Vazia** e clique em **Avançar**.

   ![](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. Na tela **Configurar Atividade**, use **MainActivity** como o Nome da Atividade e **atividade\_principal** como o Nome do Layout. Marque ambas as caixas de seleção e clique em **Concluir**.

   ![](media/sdk/qs-java-android-05-configure-activity.png)

Após executar por um tempo, o projeto do Android Studio criado recentemente deverá aparecer.

## <a name="configure-your-project-for-the-speech-sdk"></a>Configurar o projeto para o SDK de Fala

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Fala dos Serviços Cognitivos é `0.6.0`.

O SDK de Fala para Android é empacotado como um [AAR (Biblioteca Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias, bem como as permissões necessárias do Android para usá-lo.
Está hospedado em um repositório Maven em https://csspeechstorage.blob.core.windows.net/maven/.

Abaixo está descrito como configurar o projeto para usar o SDK de Fala.

Abra a janela da estrutura do projeto em **Arquivo** \> **Estrutura do Projeto**.
Na janela que for exibida, faça as alterações a seguir (clique em **OK** somente após concluir todas as etapas):

1. Selecione **Projeto** e edite as configurações **Repositório de Biblioteca Padrão**, anexando uma vírgula e a URL do repositório Maven entre aspas simples `'https://csspeechstorage.blob.core.windows.net/maven/'`:

  ![](media/sdk/qs-java-android-06-add-maven-repository.png)

1. Ainda na mesma tela, no lado esquerdo, selecione o módulo **Aplicativo**, e na parte superior da guia **Dependências**. Em seguida, clique no sinal de mais verde no canto superior direito e selecione **Dependência de biblioteca**.

  ![](media/sdk/qs-java-android-07-add-module-dependency.png)

1. Na janela que for exibida, insira o nome e a versão do SDK de Fala para Android, `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` e, em seguida, clique em **OK**.
   O SDK de Fala deve ser adicionado à lista de dependências agora, conforme mostrado abaixo:

  ![](media/sdk/qs-java-android-08-dependency-added.png)

1. Na parte superior, selecione a guia **Propriedades**. Selecione **1.8** para **Compatibilidade de Origem** e **Compatibilidade do Destino**.

  ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Por fim, clique em **OK** para fechar as janelas da **Estrutura do Projeto** e aplique todas as atualizações.

## <a name="create-a-minimal-ui"></a>Criar uma interface do usuário mínima

Edite o layout da atividade principal `activity_main.xml`.
Por padrão, ele deve criar uma barra de título com o nome do aplicativo e um TextView que diga "Hello World!".

* Clique no TextView. Altere o atributo da ID no canto superior direito para `hello`.

* Na Paleta, no canto superior esquerdo da janela `activity_main.xml`, arraste um Botão para o espaço vazio acima do texto.

* Nos atributos do botão direito, no valor do atributo `onClick`, insira `onSpeechButtonClicked`, que será o nome do nosso manipulador de botão.
  Altere o atributo da ID no canto superior direito para `button`.

* Use o ícone de varinha mágica na parte superior do designer, se quiser inferir restrições de layout.

  ![](media/sdk/qs-java-android-10-infer-layout-constraints.png)

O texto e a versão gráfica da interface do usuário agora devem ter uma aparência semelhante a esta:

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

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Edite o arquivo de origem `MainActivity.java` e substitua o código pelo seguinte (abaixo da instrução do pacote):

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * O método `onCreate` inclui código que solicita permissões de microfone e Internet, além de inicializar a associação de plataforma nativa. A configuração das associações de plataforma nativa será necessária apenas uma vez, ou seja, deverá ser feita no início, durante a inicialização do aplicativo.
   
   * O método `onSpeechButtonClicked` foi previamente conectado como o manipulador de clique do botão. Um pressionamento de botão aciona o reconhecimento de fala real.

1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

* Para compilar, pressione Ctrl+F9 ou selecione**Compilar** \> **Fazer Projeto**.

* Conecte o dispositivo Android ao PC de desenvolvimento. Certifique-se de ter o [modo de desenvolvimento e depuração USB habilitados](https://developer.android.com/studio/debug/dev-options).

* Para inicializar o aplicativo, pressione Shift+F10 ou selecione **Executar** \> **Executar 'app'**.

* Nas janelas de destino de implantação, selecione o dispositivo Android.

  ![Inicializar o aplicativo na depuração](media/sdk/qs-java-android-12-deploy.png)

* O aplicativo deve ser inicializado no dispositivo.
  Após pressionar o botão, os próximos 15 segundos serão reconhecidos e exibidos na interface do usuário (também será possível ver a resposta na janela do logcat no Android Studio):

  ![Interface do usuário após reconhecimento com êxito](media/sdk/qs-java-android-13-gui-on-device.png)

Essa captura de tela conclui o Início Rápido para Android. O código de exemplo do projeto completo pode ser baixado do repositório de exemplos.

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/java-android`.

## <a name="next-steps"></a>Próximas etapas

* [Obtenha nossas amostras](speech-sdk.md#get-the-samples)
