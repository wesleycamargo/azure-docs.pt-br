---
title: 'Início Rápido: Reconhecer fala, .NET Framework (Windows) – Serviços da API de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de console de conversão de fala em texto usando o .NET Framework para Windows e o SDK de Fala. Quando terminar, você pode usar o microfone do computador para transcrever a conversão de fala em texto em tempo real.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: cff3f526586eb52466883247d41a57dae919dd21
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867211"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Início rápido: Reconhecimento de fala com o SDK de Fala para .NET Framework (Windows)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Use este guia para criar um aplicativo de console de conversão de fala em texto usando o .NET Framework para Windows e o SDK de Fala. Quando terminar, você pode usar o microfone do computador para transcrever a conversão de fala em texto em tempo real.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, você precisará de:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de assinatura para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).
* Acesso ao microfone do seu computador

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua o código gerado automaticamente por este exemplo:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Localize e substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura do Serviço de Fala.

1. Localize e substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura. Por exemplo, se você estiver usando a versão de avaliação gratuita, a região é `westus`.

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Na barra de menus, selecione **Build** > **Build Solution**. Agora, o código deve compilar sem erros.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-dotnet-windows-08-build.png "Build bem-sucedido")

1. Na barra de menus, selecione **Depurar** > **Inicie a depuração**, ou pressione **F5** para iniciar o aplicativo.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Iniciar o aplicativo na depuração")

1. Uma janela do console será exibida, solicitando que você fale. Agora, diga algo em inglês. Sua fala será transmitida para o Serviço de Fala e será transcrita para texto em tempo real. O resultado é impresso no console.

    ![Captura de tela da saída do console após o reconhecimento bem-sucedido](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Saída do console após o reconhecimento bem-sucedido")

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de C# no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
