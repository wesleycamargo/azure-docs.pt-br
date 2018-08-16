---
title: 'Início Rápido: Saiba como reconhecer fala em C# no .NET Core no Windows usando SDK de Fala dos Serviços Cognitivos'
titleSuffix: Microsoft Cognitive Services
description: Saiba como reconhecer fala em C# no .NET Core no Windows usando SDK de Fala dos Serviços Cognitivos
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 74fb62ffa707188779bcf827d427c61a0b9a4347
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576331"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-using-the-speech-sdk"></a>Início rápido: Reconhecer fala em C# no .NET Core no Windows usando SDK de Fala

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá a criar um aplicativo de console C# para .NET Core no Windows usando SDK de Fala dos Serviços Cognitivos para transcrever conversão de fala em texto.
O aplicativo é compilado com o [Pacote NuGet do SDK de Fala dos Serviços Cognitivos da Microsoft](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2017.

> [!NOTE]
> O .NET Core é uma plataforma .NET multiplataforma de software livre que implementa a especificação [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura para o serviço de fala. Veja [Experimente o serviço de fala gratuitamente](get-started.md).
* Um PC com Windows com um microfone funcionando.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition ou posterior.
* A carga de trabalho de **desenvolvimento multiplataforma com .NET Core** no Visual Studio. Você pode ativá-lo em  **Ferramentas** \>  **Obter ferramentas e recursos**.

  ![Habilitar desenvolvimento multiplataforma com .NET Core](media/sdk/vs-enable-net-core-workload.png)

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. No Visual Studio 2017, crie um novo Aplicativo de Console do .NET Core do Visual C#. Na caixa de diálogo **Novo Projeto**, no painel esquerdo, expanda **Instalado** \> **Visual C#** \> **.NET Core** e, em seguida, selecione **Aplicativo de Console (.NET Core)**. Para o nome do projeto, insira *helloworld*.

    ![Criar aplicativo do console do Visual C# (.NET Core)](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Criar aplicativo de console do Visual C# (.NET Core)")

1. Instale e faça referência ao pacote do [Speech SDK NuGet](https://aka.ms/csspeech/nuget). No Solution Explorer, clique com o botão direito do mouse na solução e selecione **Manage NuGet Packages for Solution**.

    ![Clique com o botão direito em Gerenciar pacotes NuGet para solução](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Gerenciar pacotes NuGet para solução")

1. No canto superior direito, no campo **Package Source**, selecione **Nuget.org**. Procure e instale o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **helloworld**.

    ![Instale o Pacote NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-0.5.0.png "Instale o pacote Nuget")

1. Aceite a licença exibida.

    ![Aceite a licença](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Aceite a licença")

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Abra `Program.cs` e substitua todo o código nesse arquivo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore-windows/helloworld/Program.cs#code)]

1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

1. Construa o aplicativo. Na barra de menus, escolha **Compilar** > **Compilar Solução**. Agora, o código deve compilar sem erros.

    ![construção bem-sucedida](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "construção bem-sucedida ")

1. Inicie o aplicativo. Na barra de menus, escolha **Depurar**  > **Iniciar Depuração** ou pressione **F5**.

    ![Inicie o aplicativo na depuração](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Inicie o aplicativo na depuração")

1. Uma janela do console aparece, solicitando que você diga alguma coisa (em inglês). O texto reconhecido aparecerá na mesma janela.

    ![Saída do console após o reconhecimento bem-sucedido](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Saída do console após o reconhecimento bem-sucedido")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/csharp-dotnetcore-windows`.

## <a name="next-steps"></a>Próximas etapas

- [Traduzir discurso](how-to-translate-speech-csharp.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
