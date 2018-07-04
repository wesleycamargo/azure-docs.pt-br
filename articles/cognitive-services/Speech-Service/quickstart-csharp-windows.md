---
title: 'Início Rápido: Reconhecer fala usando o SDK C # do Cognitive Services Speech para Windows | Microsoft Docs'
description: 'Aprenda a reconhecer fala usando o SDK do C # para serviço de fala.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 64281215d139731b61365936bc1b837798ad8fbf
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016827"
---
# <a name="quickstart-recognize-speech-using-the-cognitive-services-speech-c-sdk"></a>Início rápido: reconhecer o discurso usando o SDK C # do Speech Services Cognitive

Neste artigo, você aprende como criar um aplicativo de console C # no Windows usando o SDK de Fala dos Serviços Cognitivos para transcrever fala em texto.

## <a name="prerequisites"></a>pré-requisitos

* Uma chave de assinatura para o serviço de fala. Veja [Experimente o serviço de fala gratuitamente](get-started.md).
* Visual Studio 2017, Community Edition ou superior.
* A carga de trabalho **do desenvolvimento da área de trabalho .NET** no Visual Studio. Você pode ativá-lo em  **Ferramentas** \>  **Obter ferramentas e recursos**. 

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. No Visual Studio 2017, crie um novo aplicativo de console do Visual C#. Na caixa de diálogo **Novo projeto**, no painel esquerdo, expanda **Instalado** e selecione **Console App (.NET Framework)**. Para o nome do projeto, insira *CsharpHelloSpeech*.

    ![Criar aplicativo do console do Visual C # (.NET Framework)](media/sdk/speechsdk-05-vs-cs-new-console-app.png "Criar aplicativo do console do Visual C#")

2. Instale e faça referência ao pacote do [Speech SDK NuGet](https://aka.ms/csspeech/nuget). No Solution Explorer, clique com o botão direito do mouse na solução e selecione **Manage NuGet Packages for Solution**.

    ![Clique com o botão direito em Gerenciar pacotes NuGet para solução] (media/sdk/speechsdk-06-vs-cs-manage-nuget-packages.png "Gerenciar pacotes NuGet para solução")

3. No canto superior direito, no campo **Package Source**, selecione **Nuget.org**. Procure e instale o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **CsharpHelloSpeech** .

    ![Instale o Pacote NuGet Microsoft.CognitiveServices.Speech] (media/sdk/speechsdk-08-vs-cs-nuget-install.png "Instale o pacote Nuget")

4. Na tela de licença que aparece, aceite a licença:

    ![Aceite a licença](media/sdk/speechsdk-09-vs-cs-nuget-license.png "Aceite a licença")

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>Crie uma configuração de plataforma que corresponda à arquitetura do seu PC

Nesta seção, você adiciona uma nova plataforma à configuração que corresponde à arquitetura do seu processador.

1. Inicie o Gerenciador de Configuração. Selecione **Gerencie** > **Gerenciador de Configuração**.

    ![Ative o gerenciador de configuração](media/sdk/speechsdk-12-vs-cs-cfg-manager-click.png "Ative o gerenciador de configuração")

2. Na caixa de diálogo **Configuration Manager**, adicione uma nova plataforma. Na lista suspensa da **Plataforma de solução ativa**, selecione **Novo**.

    ![Adicione uma nova plataforma na janela do gerenciador de configuração](media/sdk/speechsdk-14-vs-cs-cfg-manager-new.png "Adicione uma nova plataforma na janela do gerenciador de configuração")

3. Se você estiver executando o Windows de 64 bits, crie uma nova configuração de plataforma denominada`x64`. Se você estiver executando o Windows de 32 bits, crie uma nova configuração de plataforma denominada`x86`. Neste artigo, você cria uma configuração de plataforma`x64`. 

    ![No Windows de 64 bits, adicione uma nova plataforma chamada "x64"](media/sdk/speechsdk-15-vs-cs-cfg-manager-add-x64.png "Adicione a plataforma x64")

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. No`Program.cs` para o seu projeto do Visual Studio, substitua o corpo da classe`Program`pelo seguinte. Certifique-se de substituir a chave de assinatura e a região por uma que você obteve para o serviço.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#code)]

2. Depois de colar o código, o método `Main()` deve ser semelhante ao mostrado na captura de tela a seguir:

    ![Método principal depois de colar o código ](media/sdk/speechsdk-17-vs-cs-paste-code.png "Final Main method")

3. O Visual Studio IntelliSense destaca as referências às classes de SDKs de fala que não puderam ser resolvidas. Para corrigir esse erro, adicione a seguinte instrução `using`ao início do código (manualmente ou usando as ações rápidas [do Visual Studio](https://docs.microsoft.com/visualstudio/ide/quick-actions)).

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#usingstatement)]

    ![Use a ação rápida para adicionar a declaração using missing](media/sdk/speechsdk-18-vs-cs-add-using.png "Resolver problemas do IntelliSense")

4. Certifique-se de que o realce do IntelliSense esteja resolvido e salve as alterações no projeto.

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

1. Construa o aplicativo. Na barra de menus, selecione **Build** > **Build Solution**. O código deve compilar sem erros agora:

    ![construção bem-sucedida](media/sdk/speechsdk-20-vs-cs-build.png "construção bem-sucedida ")

2. Inicie o aplicativo. Na barra de menus, selecione **Depurar**  > **Inicie a depuração**, ou pressione **F5**. 

    ![Inicie o aplicativo na depuração](media/sdk/speechsdk-21-vs-cs-f5.png "Inicie o aplicativo na depuração")

3. Uma janela do console aparece, solicitando que você diga alguma coisa (em inglês).
O resultado do reconhecimento é exibido na tela.

    ![Saída do console após o reconhecimento bem-sucedido](media/sdk/speechsdk-22-cs-vs-console-output.png "Saída do console após o reconhecimento bem-sucedido")

## <a name="download-code"></a>Código de download

Para obter o conjunto de amostras mais recente, consulte o [repositório GitHub de Amostra do SDK do Speech Services Cognitive Services](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Próximas etapas

- [Traduzir discurso](how-to-translate-speech.md)
- [Personalizar modelos de fala](how-to-customize-speech-models.md)
