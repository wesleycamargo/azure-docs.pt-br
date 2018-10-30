---
title: 'Início Rápido: Reconhecer fala em C# no .NET Framework no Windows usando o SDK do Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em C# no .NET Framework no Windows usando o SDK do Serviço de Fala
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 06d4a41a4a67d077f8d79eee68938dff65adf0e8
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468510"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-by-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em C# no .NET Framework no Windows usando SDK de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você cria um aplicativo de console em C# do .NET Framework no Windows usando o [SDK de Fala](speech-sdk.md). Você transcreverá a fala em texto em tempo real por meio do microfone do computador. O aplicativo é criado com o [Pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura do serviço de Fala para concluir este Início Rápido. Obtenha uma gratuitamente. Confira [Experimentar o serviço de Fala gratuitamente](get-started.md) para obter mais detalhes.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua todo o código nesse arquivo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura do serviço de Fala.

1. Substitua também a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Construa o aplicativo. Na barra de menus, selecione **Build** > **Build Solution**. Agora, o código deve compilar sem erros.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-dotnet-windows-08-build.png "Build bem-sucedido")

1. Inicie o aplicativo. Na barra de menus, selecione **Depurar**  > **Inicie a depuração**, ou pressione **F5**.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Iniciar o aplicativo na depuração")

1. Uma janela do console aparece, solicitando que você diga alguma coisa. Fale uma frase ou expressão em inglês. Sua fala será transmitida para o serviço de Fala e transcrita para texto, que aparecerá na mesma janela.

    ![Captura de tela da saída do console após o reconhecimento bem-sucedido](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Saída do console após o reconhecimento bem-sucedido")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/csharp-dotnet-windows`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Reconhecer as intenções da fala usando o SDK de Fala para C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Consulte também

- [Traduzir discurso](how-to-translate-speech-csharp.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
