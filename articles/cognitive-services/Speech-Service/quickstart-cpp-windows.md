---
title: 'Início Rápido: Saiba como reconhecer fala em C++ no Windows Desktop usando o SDK de Fala dos Serviços Cognitivos'
titleSuffix: Microsoft Cognitive Services
description: Saiba como reconhecer fala em C++ no Windows Desktop usando o SDK de Fala dos Serviços Cognitivos
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 9fae855de2a746084f4775f194e04c6dbe09f684
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127284"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-desktop-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em C++ no Windows Desktop usando o SDK de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Descrevemos como criar um aplicativo de console baseado em C++ para a área de trabalho do Windows que usa o SDK de Fala.
O aplicativo é baseado no [Pacote NuGet do SDK de Fala dos Serviços Cognitivos da Microsoft](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura para o serviço de fala. Veja [Experimente o serviço de fala gratuitamente](get-started.md).
* Um PC com Windows com um microfone funcionando.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition ou superior.
* A carga de trabalho de **Desenvolvimento para desktop com C++** no Visual Studio e o componente do **gerenciador de pacotes NuGet** no Visual Studio.
  É possível habilitar ambos em **Ferramentas** \> **Obter Ferramentas e Recursos**, nas guias **Cargas de trabalho** e **Componentes individuais**, respectivamente:

  ![Habilitar carga de trabalho de Desenvolvimento para desktop com C++](media/sdk/vs-enable-cpp-workload.png)

  ![Habilitar o gerenciador de pacotes NuGet no Visual Studio ](media/sdk/vs-enable-nuget-package-manager.png)

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

No Visual Studio 2017, crie um novo Aplicativo de Console do Windows de Área de Trabalho do Windows para Visual C++. Na caixa de diálogo **Novo Projeto**, no painel esquerdo, expanda **Instalado** \> **Visual C++** \> **Windows Desktop** e, em seguida, selecione **Aplicativo de Console do Windows**. Para o nome do projeto, insira *helloworld*.

![Criar Aplicativo de Console do Windows de Área de Trabalho do Windows para Visual C++](media/sdk/qs-cpp-windows-01-new-console-app.png)

Se estiver executando em uma instalação do Windows de 64 bits, alterne opcionalmente a plataforma de compilação para `x64`:

![Alterne a plataforma de compilação para x64](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Instalar e referenciar o pacote NuGet do SDK de Fala

No Gerenciador de Soluções, clique com o botão direito do mouse na solução e clique em **Gerenciar pacotes NuGet para solução**.

![Clicar com o botão direito do mouse em Gerenciar pacotes NuGet para solução](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

No canto superior direito, no campo **Origem do Pacote e**, escolha "Nuget.org".
Na guia **Procurar**, pesquise o pacote "Microsoft.CognitiveServices.Speech", selecione-o e marque as caixas **Projeto** e **helloworld** à direita e selecione **Instalar** para instalá-lo no projeto helloworld.

> [!NOTE]
> A versão atual do SDK de Fala dos Serviços Cognitivos é `0.6.0`.

![Instalar o pacote NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-cpp-windows-04-nuget-install-0.5.0.png)

Na tela de licença que é exibida, aceite a licença:

![Aceitar a licença](media/sdk/qs-cpp-windows-05-nuget-license.png)

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

1. Substitua o código inicial padrão pelo seguinte:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

1. Construa o aplicativo. Na barra de menus, selecione **Build** > **Build Solution**. O código deve compilar sem erros agora:

   ![Compilar com êxito](media/sdk/qs-cpp-windows-06-build.png)

1. Inicie o aplicativo. Na barra de menus, selecione **Depurar**  > **Inicie a depuração**, ou pressione **F5**.

   ![Inicializar o aplicativo na depuração](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Uma janela do console aparece, solicitando que você diga alguma coisa (em inglês).
   O resultado do reconhecimento será exibido na tela.

   ![Saída do console após o reconhecimento com êxito](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/cpp-windows`.

## <a name="next-steps"></a>Próximas etapas

* [Obtenha nossas amostras](speech-sdk.md#get-the-samples)
