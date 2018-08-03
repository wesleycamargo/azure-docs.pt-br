---
title: 'Início rápido: Reconhecer fala em C# em um aplicativo UWP usando SDK de Fala dos Serviços Cognitivos | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Saiba como reconhecer fala em um aplicativo UWP usando SDK de Fala dos Serviços Cognitivos
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 751736a4333932a68775589dddeb82ef6fa6f398
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258201"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-using-the-speech-sdk"></a>Início rápido: Reconhecer fala em um aplicativo UWP usando SDK de Fala

Neste artigo, você aprenderá como criar um aplicativo UWP (Plataforma Universal do Windows) usando SDK de Fala dos Serviços Cognitivos para transcrever conversão de fala em texto.
O aplicativo é compilado com o [Pacote NuGet do SDK de Fala dos Serviços Cognitivos da Microsoft](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2017.

> [!NOTE]
> A Plataforma Universal do Windows permite desenvolver aplicativos que executam em qualquer dispositivo com suporte para Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos. Os aplicativos que usam o SDK de Fala ainda não são aprovados pelo WACK (Kit de Certificação de Aplicativos Windows). É possível fazer sideload do aplicativo, mas atualmente não é possível enviá-lo o Windows Store.

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura para o serviço de fala. Veja [Experimente o serviço de fala gratuitamente](get-started.md).
* Um PC com Windows (Windows 10 Fall Creators Update ou posterior) com um microfone funcionando.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition ou superior.
* A carga de trabalho de **desenvolvimento da Plataforma Universal do Windows** no Visual Studio, é possível habilitá-la em **Ferramentas** \> **Obter Ferramentas e Recursos**.

  ![Habilitar o desenvolvimento da Plataforma Universal do Windows](media/sdk/vs-enable-uwp-workload.png)

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. No Visual Studio 2017, crie um novo Aplicativo em Branco Universal do Windows do Visual C#. Na caixa de diálogo **Novo Projeto**, no painel esquerdo, expanda **Instalado** \> **Visual C#** \> **Universal do Windows** e, em seguida, **Aplicativo em Branco (Universal do Windows)**. Para o nome do projeto, insira *helloworld*.

    ![](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Na janela **Novo Projeto da Plataforma Universal do Windows** que é exibida, escolha **Windows 10 Fall Creators Update (10.0; Build 16299)** como **Versão mínima** e essa ou qualquer outra versão posterior como a **Versão de destino** e, em seguida, clique em **OK**.

    ![](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Se estiver executando em uma instalação do Windows de 64 bits, será possível alternar a plataforma de compilação para `x64`.

   ![Alterne a plataforma de compilação para x64](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > No momento, o SDK de Fala dá suporte a processadores compatíveis com Intel, mas não ARM.

1. Instale e faça referência ao pacote do [Speech SDK NuGet](https://aka.ms/csspeech/nuget). No Solution Explorer, clique com o botão direito do mouse na solução e selecione **Manage NuGet Packages for Solution**.

    ![Clicar com o botão direito do mouse em Gerenciar pacotes NuGet para solução](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. No canto superior direito, no campo **Package Source**, selecione **Nuget.org**. Procure e instale o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **helloworld**.

    ![Instale o Pacote NuGet Microsoft.CognitiveServices.Speech] (media/sdk/qs-csharp-uwp-05-nuget-install-0.5.0.png "Instale o pacote Nuget")

1. Aceite a licença na caixa de diálogo exibida.

    ![Aceite a licença](media/sdk/qs-csharp-uwp-06-nuget-license.png "Aceite a licença")

1. A seguinte linha de saída aparece no console do Gerenciador de Pacotes.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. No Gerenciador de Soluções, clique duas vezes em **Package.appxmanifest** para editar o manifesto do aplicativo.
   Selecione a guia **Recursos**, marque a caixa de seleção do recurso **Microfone** e salve as alterações.

   ![](media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Edite a interface do usuário do aplicativo, clicando duas vezes em `MainPage.xaml` no Gerenciador de Soluções. 

    Na exibição XAML do designer, insira o trecho de código XAML a seguir na marca da Grade (entre `<Grid>` e `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Edite o code-behind XAML, clicando duas vezes em `MainPage.xaml.cs` no Gerenciador de Soluções (está agrupado sob o item `MainPage.xaml`).
   Substitua todo o código nesse arquivo pelo seguinte.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. No manipulador `SpeechRecognitionFromMicrophone_ButtonClicked`, substitua a sequência `YourSubscriptionKey` pela sua chave de assinatura.

1. No manipulador `SpeechRecognitionFromMicrophone_ButtonClicked`, substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve todas as alterações no projeto.

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

1. Construa o aplicativo. Na barra de menus, selecione **Build** > **Build Solution**. Agora, o código deve compilar sem erros.

    ![construção bem-sucedida](media/sdk/qs-csharp-uwp-08-build.png "construção bem-sucedida ")

1. Inicie o aplicativo. Na barra de menus, selecione **Depurar**  > **Inicie a depuração**, ou pressione **F5**.

    ![Inicie o aplicativo na depuração](media/sdk/qs-csharp-uwp-09-start-debugging.png "Inicie o aplicativo na depuração")

1. Uma janela de GUI é exibida. Primeiro, clique no botão **Habilitar Microfone** e confirme a solicitação de permissão que é exibida.

    ![Inicie o aplicativo na depuração](media/sdk/qs-csharp-uwp-10-access-prompt.png "Inicie o aplicativo na depuração")

1. Clique no **Reconhecimento de fala com entrada de microfone** e fale uma frase curta no microfone do dispositivo. O texto reconhecido aparece na janela.

    ![](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/csharp-uwp`.

## <a name="next-steps"></a>Próximas etapas

- [Traduzir discurso](how-to-translate-speech.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
