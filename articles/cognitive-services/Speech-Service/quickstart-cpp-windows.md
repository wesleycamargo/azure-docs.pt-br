---
title: 'Início Rápido: Reconhecer fala em C++ no Windows usando o SDK do Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em C++ no Windows Desktop usando o SDK do Serviços de Fala
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: ddac12c6414365f8d20c42d324695be9855774dc
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469860"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em C++ no Windows usando o SDK de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você criará um aplicativo de console C++ para o Windows. Você usará o [SDK de Fala](speech-sdk.md) dos Serviços Cognitivos para transcrever a conversão de fala em texto em tempo real do microfone do seu computador. O aplicativo é criado com o [Pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura do serviço de Fala para concluir este Início Rápido. Obtenha uma gratuitamente. Confira [Experimentar o serviço de Fala gratuitamente](get-started.md) para obter mais detalhes.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

1. Inicie o Visual Studio 2017.

1. Verifique se a carga de trabalho **Desenvolvimento para Desktop com C++** está disponível. Escolha **Ferramentas** > **Obter Ferramentas e Recursos** na barra de menus do Visual Studio para abrir o Instalador do Visual Studio. Se essa carga de trabalho já estiver habilitada, passe para a próxima etapa. 

    ![Captura de tela da guia Cargas de Trabalho do Visual Studio](media/sdk/vs-enable-cpp-workload.png)

    Caso contrário, marque a caixa de seleção ao lado de **Desenvolvimento para Desktop com C++**. 

1. Verifique se o componente **Gerenciador de pacotes NuGet** está disponível. Mude para a guia **Componentes individuais** da caixa de diálogo do Instalador do Visual Studio e selecione **Gerenciador de pacotes NuGet** se ainda não estiver habilitado.

      ![Captura de tela da guia Componentes individuais do Visual Studio](media/sdk/vs-enable-nuget-package-manager.png)

1. Se for necessário habilitar a carga de trabalho do C++ ou do NuGet, selecione **Modificar** (no canto inferior direito da caixa de diálogo). A instalação dos novos recursos leva alguns instantes. Se os dois recursos já foram habilitados, feche a caixa de diálogo.

1. Crie um Aplicativo de Console do Windows do Visual C++ para Windows Desktop. Primeiro, escolha **Arquivo** > **Novo** > **Projeto** no menu. Na caixa de diálogo **Novo Projeto**, expanda **Instalado** > **Visual C++** > **Windows Desktop** no painel esquerdo. Em seguida, selecione **Aplicativo de Console do Windows**. Para o nome do projeto, insira *helloworld*.

    ![A captura de tela da caixa de diálogo Novo Projeto](media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Se você estiver executando o Windows de 64 bits, será possível alternar sua plataforma de build para `x64` usando o menu suspenso na barra de ferramentas do Visual Studio. (As versões de 64 bits do Windows podem executar aplicativos de 32 bits, portanto, esse não é um requisito.)

    ![Captura de tela da barra de ferramentas do Visual Studio, com a opção x64 realçada](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e escolha **Gerenciar Pacotes NuGet para Solução**.

    ![Captura de tela do Gerenciador de Soluções, com a opção Gerenciar Pacotes NuGet para a Solução realçada](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. No canto superior direito, no campo **Package Source**, selecione **nuget.org**. Procure o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **helloworld**.

    ![Captura de tela da caixa de diálogo Gerenciar Pacotes para Solução](media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > A versão atual do SDK de Fala dos Serviços Cognitivos é `1.0.1`.

1. Aceite a licença exibida para iniciar a instalação do pacote NuGet.

    ![Captura de tela da caixa de diálogo Aceitação da Licença](media/sdk/qs-cpp-windows-05-nuget-license.png)

Depois que o pacote for instalado, uma confirmação será exibida no console do gerenciador de pacotes.

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o arquivo de origem *helloworld.cpp*. Substitua todo o código abaixo da instrução include inicial (`#include "stdafx.h"` ou `#include "pch.h"`) pelo seguinte:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Construa o aplicativo. Na barra de menus, escolha **Compilar** > **Compilar Solução**. O código deve compilar sem erros.

   ![Captura de tela do aplicativo do Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-cpp-windows-06-build.png)

1. Inicie o aplicativo. Na barra de menus, escolha **Depurar**  > **Iniciar Depuração** ou pressione **F5**.

   ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Uma janela do console aparece, solicitando que você diga alguma coisa. Fale uma frase ou expressão em inglês. Sua fala será transmitida para o serviço de Fala e transcrita para texto, que aparecerá na mesma janela.

   ![Captura de tela da saída do console após o reconhecimento com êxito](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/cpp-windows`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Reconhecer intenções da fala usando o SDK de Fala para C++](how-to-recognize-intents-from-speech-cpp.md)

## <a name="see-also"></a>Consulte também

- [Traduzir discurso](how-to-translate-speech-csharp.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
