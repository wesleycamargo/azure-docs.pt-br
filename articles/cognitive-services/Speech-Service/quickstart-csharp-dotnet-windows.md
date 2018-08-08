---
title: 'Início Rápido: Saiba como reconhecer fala em C# no Framework no Windows usando SDK de Fala dos Serviços Cognitivos'
titleSuffix: Microsoft Cognitive Services
description: Saiba como reconhecer fala em C# no .NET Framework no Windows usando SDK de Fala dos Serviços Cognitivos
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 587ae9f6452f85dee867047e47ccc272ee508b81
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325183"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-using-the-speech-sdk"></a>Início rápido: Reconhecer fala em C# no .NET Framework no Windows usando SDK de Fala

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá a criar um aplicativo de console C# para .NET Framework no Windows usando SDK de Fala dos Serviços Cognitivos para transcrever conversão de fala em texto.
O aplicativo é compilado com o [Pacote NuGet do SDK de Fala dos Serviços Cognitivos da Microsoft](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura para o serviço de fala. Veja [Experimente o serviço de fala gratuitamente](get-started.md).
* Um PC com Windows com um microfone funcionando.
* Visual Studio 2017, Community Edition ou superior.
* A carga de trabalho **do desenvolvimento da área de trabalho .NET** no Visual Studio. Você pode ativá-lo em  **Ferramentas** \>  **Obter ferramentas e recursos**.

  ![Habilitar desenvolvimento para área de trabalho .NET](media/sdk/vs-enable-net-desktop-workload.png)

  ![Habilitar desenvolvimento multiplataforma com .NET Core](media/sdk/vs-enable-net-desktop-workload.png)

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. No Visual Studio 2017, crie um novo aplicativo de console do Visual C#. Na caixa de diálogo **Novo Projeto**, no painel esquerdo, expanda **Instalado** \> **Visual C#** \> **Windows Desktop** e, em seguida, selecione **Aplicativo de Console (.NET Framework)**. Para o nome do projeto, insira *helloworld*.

    ![Criar aplicativo do console do Visual C # (.NET Framework)](media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Criar aplicativo de console do Visual C# (.NET Framework)")

1. Instale e faça referência ao pacote do [Speech SDK NuGet](https://aka.ms/csspeech/nuget). No Solution Explorer, clique com o botão direito do mouse na solução e selecione **Manage NuGet Packages for Solution**.

    ![Clique com o botão direito em Gerenciar pacotes NuGet para solução] (media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Gerenciar pacotes NuGet para solução")

1. No canto superior direito, no campo **Package Source**, selecione **Nuget.org**. Procure o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **helloworld**.

    ![Instale o Pacote NuGet Microsoft.CognitiveServices.Speech] (media/sdk/qs-csharp-dotnet-windows-03-nuget-install-0.5.0.png "Instale o pacote Nuget")

1. Aceite a licença na caixa de diálogo exibida.

    ![Aceite a licença](media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Aceite a licença")

1. A seguinte linha de saída aparece no console do Gerenciador de Pacotes.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>Crie uma configuração de plataforma que corresponda à arquitetura do seu PC

Nesta seção, você adiciona uma nova plataforma à configuração que corresponde à arquitetura do seu processador.

1. Inicie o Gerenciador de Configuração. Selecione **Gerencie** > **Gerenciador de Configuração**.

    ![Ative o gerenciador de configuração](media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Ative o gerenciador de configuração")

1. Na caixa de diálogo **Configuration Manager**, adicione uma nova plataforma. Na lista suspensa da **Plataforma de solução ativa**, selecione **Novo**.

    ![Adicione uma nova plataforma na janela do gerenciador de configuração](media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Adicione uma nova plataforma na janela do gerenciador de configuração")

1. Se você estiver executando o Windows de 64 bits, crie uma nova configuração de plataforma denominada`x64`. Se você estiver executando o Windows de 32 bits, crie uma nova configuração de plataforma denominada`x86`. Neste artigo, você cria uma configuração de plataforma`x64`.

    ![No Windows de 64 bits, adicione uma nova plataforma chamada "x64"](media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Adicione a plataforma x64")

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Abra `Program.cs` no projeto do Visual Studio e substitua todo o código desse arquivo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

1. Construa o aplicativo. Na barra de menus, selecione **Build** > **Build Solution**. Agora, o código deve compilar sem erros.

    ![construção bem-sucedida](media/sdk/qs-csharp-dotnet-windows-08-build.png "construção bem-sucedida ")

1. Inicie o aplicativo. Na barra de menus, selecione **Depurar**  > **Inicie a depuração**, ou pressione **F5**.

    ![Inicie o aplicativo na depuração](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Inicie o aplicativo na depuração")

1. Uma janela do console aparece, solicitando que você diga alguma coisa (em inglês). O texto reconhecido aparecerá na mesma janela.

    ![Saída do console após o reconhecimento bem-sucedido](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Saída do console após o reconhecimento bem-sucedido")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/csharp-dotnet-windows`.

## <a name="next-steps"></a>Próximas etapas

- [Traduzir discurso](how-to-translate-speech-csharp.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
