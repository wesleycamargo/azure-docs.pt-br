---
title: 'Início Rápido: reconhecer fala em C# no .NET Core no Windows usando o SDK de Fala dos Serviços Cognitivos'
titleSuffix: Microsoft Cognitive Services
description: Saiba como reconhecer fala em C# no .NET Core no Windows usando o SDK de Fala dos Serviços Cognitivos
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: 04cdfd487036e79eccbb4fb879902a12e077d104
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341643"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-by-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em C# no .NET Core no Windows usando SDK de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você cria um aplicativo de console em C# para o .NET Core no Windows usando o [SDK de Fala](speech-sdk.md) dos Serviços Cognitivos. Você transcreve a fala em texto em tempo real por meio do microfone do computador. O aplicativo é criado com o [Pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

> [!NOTE]
> O .NET Core é uma plataforma .NET multiplataforma de software livre que implementa a especificação [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Você precisa de uma chave de assinatura do serviço de Fala para concluir este Início Rápido. Obtenha uma gratuitamente. Confira [Experimentar o serviço de Fala gratuitamente](get-started.md) para obter mais detalhes.


## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Inicie o Visual Studio 2017.

1. Verifique se a carga de trabalho **Desenvolvimento multiplataforma do .NET** está disponível. Escolha **Ferramentas** > **Obter Ferramentas e Recursos** na barra de menus do Visual Studio para abrir o instalador do Visual Studio. Se essa carga de trabalho já estiver habilitada, feche a caixa de diálogo.

    ![Captura de tela do instalador do Visual Studio, com a guia Cargas de Trabalho realçada](media/sdk/vs-enable-net-core-workload.png)

    Caso contrário, marque a caixa ao lado de **Desenvolvimento multiplataforma do .NET Core** e selecione **Modificar** no canto inferior direito da caixa de diálogo. A instalação do novo recurso levará alguns instantes.

1. Crie um novo Aplicativo de Console do .NET Core do Visual C#. Na caixa de diálogo **Novo Projeto**, no painel esquerdo, expanda **Instalado** > **Visual C#** > **.NET Core**. Em seguida, selecione **Aplicativo do Console (.NET Core)**. Para o nome do projeto, insira *helloworld*.

    ![Captura de tela da caixa de diálogo Novo Projeto](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Criar Aplicativo de Console do Visual C# (.NET Core)")

1. Instale e faça referência ao pacote do [Speech SDK NuGet](https://aka.ms/csspeech/nuget). No Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Gerenciar Pacotes NuGet para a Solução**.

    ![Captura de tela do Gerenciador de Soluções, com a opção Gerenciar Pacotes NuGet para a Solução realçada](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Gerenciar Pacotes NuGet para a Solução")

1. No canto superior direito, no campo **Package Source**, selecione **nuget.org**. Procure o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **helloworld**.

    ![Captura de tela da caixa de diálogo Gerenciar Pacotes para a Solução](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Instalar pacote NuGet")

1. Aceite a licença exibida para iniciar a instalação do pacote NuGet.

    ![Captura de tela da caixa de diálogo Aceitação da Licença](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Aceitar a licença")

Depois que o pacote for instalado, uma confirmação será exibida no console do gerenciador de pacotes.


## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua todo o código nesse arquivo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura.

1. Substitua também a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Construa o aplicativo. Na barra de menus, escolha **Compilar** > **Compilar Solução**. O código deve compilar sem erros.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Build bem-sucedido")

1. Inicie o aplicativo. Na barra de menus, escolha **Depurar**  > **Iniciar Depuração** ou pressione **F5**.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Iniciar o aplicativo na depuração")

1. Uma janela do console aparece, solicitando que você diga alguma coisa. Fale uma frase ou expressão em inglês. Sua fala será transmitida para o serviço de Fala e transcrita para texto, que aparecerá na mesma janela.

    ![Captura de tela da saída do console após o reconhecimento bem-sucedido](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Saída do console após o reconhecimento bem-sucedido")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/csharp-dotnetcore-windows`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Reconhecer as intenções da fala usando o SDK de Fala para C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Consulte também

- [Traduzir discurso](how-to-translate-speech-csharp.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
