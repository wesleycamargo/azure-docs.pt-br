---
title: 'Início Rápido: Reconhecer a fala em C# em um aplicativo UWP usando o SDK de Fala dos Serviços Cognitivos'
titleSuffix: Microsoft Cognitive Services
description: Saiba como reconhecer a fala em um aplicativo UWP usando o SDK de Fala dos Serviços Cognitivos
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: d9a90869e060d2f8f1a1c522a4528e74841caada
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339607"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Início Rápido: Reconhecer a fala em um aplicativo UWP usando o SDK de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você criará um aplicativo UWP (Plataforma Universal do Windows) em C# usando o [SDK de Fala](speech-sdk.md) dos Serviços Cognitivos. Você transcreverá a fala em texto em tempo real por meio do microfone do dispositivo. O aplicativo foi criado com o [Pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

> [!NOTE]
> A Plataforma Universal do Windows permite desenvolver aplicativos que executam em qualquer dispositivo com suporte para Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura do serviço de Fala para concluir este Início Rápido. Obtenha uma gratuitamente. Confira [Experimentar o serviço de Fala gratuitamente](get-started.md) para obter mais detalhes.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Inicie o Visual Studio 2017.

1. Verifique se a carga de trabalho **Desenvolvimento da Plataforma Universal do Windows** está disponível. Escolha **Ferramentas** > **Obter Ferramentas e Recursos** na barra de menus do Visual Studio para abrir o Instalador do Visual Studio. Se essa carga de trabalho já estiver habilitada, feche a caixa de diálogo. 

    ![Captura de tela do Instalador do Visual Studio, com a guia Cargas de Trabalho realçada](media/sdk/vs-enable-uwp-workload.png)

    Caso contrário, marque a caixa ao lado de **Desenvolvimento multiplataforma do .NET** e selecione **Modificar** no canto inferior direito da caixa de diálogo. A instalação do novo recurso leva alguns instantes.

1. Crie um aplicativo universal do Windows em branco do Visual C#. Primeiro, escolha **Arquivo** > **Novo** > **Projeto** no menu. Na caixa de diálogo **Novo Projeto**, expanda **Instalado** > **Visual C#** > **Universal do Windows** no painel esquerdo. Em seguida, selecione **Aplicativo em Branco (Universal do Windows)**. Para o nome do projeto, insira *helloworld*.

    ![A captura de tela da caixa de diálogo Novo Projeto](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. O SDK de Fala exige que o aplicativo seja compilado para o Windows 10 Fall Creators Update ou posterior. Na janela **Novo Projeto da Plataforma Universal do Windows** exibida, escolha **Windows 10 Fall Creators Update (10.0; Build 16299)** como a **Versão mínima**. Na caixa **Versão de destino**, selecione essa ou qualquer versão mais recente e, em seguida, clique em **OK**.

    ![Captura de tela da janela Novo Projeto da Plataforma Universal do Windows](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Se você estiver executando o Windows de 64 bits, alterne a plataforma de build para `x64` usando o menu suspenso na barra de ferramentas do Visual Studio. (O Windows de 64 bits pode executar aplicativos de 32 bits e, portanto, você poderá deixá-lo definido como `x86` se preferir.)

   ![Captura de tela da barra de ferramentas do Visual Studio, com a opção x64 realçada](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > O SDK de Fala dá suporte somente a processadores compatíveis com Intel. No momento, não há suporte para o ARM.

1. Instale e faça referência ao pacote do [Speech SDK NuGet](https://aka.ms/csspeech/nuget). No Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Gerenciar Pacotes NuGet para a Solução**.

    ![Captura de tela do Gerenciador de Soluções, com a opção Gerenciar Pacotes NuGet para a Solução realçada](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. No canto superior direito, no campo **Package Source**, selecione **nuget.org**. Procure o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **helloworld**.

    ![Captura de tela da caixa de diálogo Gerenciar Pacotes para a Solução](media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Instalar pacote NuGet")

1. Aceite a licença exibida para iniciar a instalação do pacote NuGet.

    ![Captura de tela da caixa de diálogo Aceitação da Licença](media/sdk/qs-csharp-uwp-06-nuget-license.png "Aceitar a licença")

1. A seguinte linha de saída aparece no console do Gerenciador de Pacotes.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.0.1' to helloworld
   ```

1. Como o aplicativo usa o microfone para a entrada de fala, adicione a funcionalidade **Microfone** ao projeto. No Gerenciador de Soluções, clique duas vezes em **Package.appxmanifest** para editar o manifesto do aplicativo. Em seguida, alterne para a guia **Funcionalidades**, marque a caixa da funcionalidade **Microfone** e salve as alterações.

   ![Captura de tela do manifesto do aplicativo do Visual Studio, com as Funcionalidades e o Microfone realçados](media/sdk/qs-csharp-uwp-07-capabilities.png)


## <a name="add-sample-code"></a>Adicionar código de exemplo

1. A interface do usuário do aplicativo é definida usando XAML. Abra `MainPage.xaml` no Gerenciador de Soluções. Na exibição XAML do designer, insira o snippet de código XAML a seguir na marca da Grade (entre `<Grid>` e `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Abra o arquivo de origem code-behind `MainPage.xaml.cs` (encontre-o agrupado em `MainPage.xaml`). Substitua todo o código nesse arquivo pelo código a seguir.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. No manipulador `SpeechRecognitionFromMicrophone_ButtonClicked` desse arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. No manipulador `SpeechRecognitionFromMicrophone_ButtonClicked`, substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve todas as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Construa o aplicativo. Na barra de menus, selecione **Build** > **Build Solution**. Agora, o código deve compilar sem erros.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-uwp-08-build.png "Build bem-sucedido")

1. Inicie o aplicativo. Na barra de menus, selecione **Depurar**  > **Inicie a depuração**, ou pressione **F5**.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-uwp-09-start-debugging.png "Iniciar o aplicativo na depuração")

1. Uma janela é exibida. Selecione **Habilitar Microfone** e reconheça a solicitação de permissão exibida.

    ![Captura de tela da solicitação de permissão](media/sdk/qs-csharp-uwp-10-access-prompt.png "Iniciar o aplicativo na depuração")

1. Selecione **Reconhecimento de fala com a entrada do microfone** e fale uma frase ou uma oração em inglês no microfone do dispositivo. Sua fala será transmitida para o serviço de Fala e transcrita para texto, que será exibida na janela.

    ![Captura de tela da interface do usuário do reconhecimento de fala](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/csharp-uwp`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Reconhecer as intenções da fala usando o SDK de Fala para C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Consulte também

- [Traduzir discurso](how-to-translate-speech-csharp.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
