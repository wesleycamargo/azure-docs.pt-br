---
title: 'Início Rápido: Reconhecer fala em Objective-C no iOS usando o SDK de Fala dos Serviços Cognitivos'
titleSuffix: Microsoft Cognitive Services
description: Aprenda a reconhecer fala em Objective-C no iOS usando o SDK de Fala dos Serviços Cognitivos
services: cognitive-services
author: chlandsi
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 09/24/2018
ms.author: chlandsi
ms.openlocfilehash: 3945bf0ae6edc0af0db90efca6811aeb22494592
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883425"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-cognitive-services-speech-sdk"></a>Início Rápido: Reconhecer fala em Objective-C no iOS usando o SDK de Fala dos Serviços Cognitivos

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá a criar um aplicativo iOS em Objective-C usando o SDK de Fala dos Serviços Cognitivos para transcrever um arquivo de áudio com conversão de fala em texto gravada.

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura para o serviço de fala. Veja [Experimente o serviço de fala gratuitamente](get-started.md).
* Um Mac com Xcode 9.4.1 instalado como um ambiente de desenvolvimento do iOS. Este tutorial destina-se a versões 11.4 do iOS. Se você ainda não tiver o Xcode, poderá instalá-lo pela [App Store](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Fala para iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Fala dos Serviços Cognitivos é `1.0.0`.

O SDK de Fala dos Serviços Cognitivos para Mac e iOS atualmente é distribuído como uma Cocoa Framework.
Ele pode ser baixado de https://aka.ms/csspeech/iosbinary. Baixe o arquivo para seu diretório inicial.


## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode 

Inicie o Xcode e iniciar um novo projeto clicando em **Arquivo** > **Novo** > **Projeto**.
Na caixa de diálogo de seleção de modelo, escolha o modelo "Aplicativo de Exibição Única do iOS ".

Nas caixas de diálogo que seguem, faça as seguintes seleções:

1. Caixa de diálogo Opções de Projeto
    1. Insira um nome para o aplicativo de início rápido, por exemplo `helloworld`.
    1. Insira um nome de organização apropriado e o identificador de organização, se você já tiver uma conta de desenvolvedor da Apple. Para fins de teste, você pode escolher qualquer nome, como `testorg`. Para assinar o aplicativo, você também precisa de um perfil de provisionamento adequado. Consulte o [site do desenvolvedor da Apple](https://developer.apple.com/) para obter detalhes.
    1. Verifique se Objective-C é escolhido como o idioma para o projeto.
    1. Desabilite todas as caixas de seleção para testes e dados principais.
    ![Configurações do projeto](media/sdk/qs-objectivec-project-settings.png)
1. Selecionar diretório do projeto
    1. Escolha seu diretório base no qual colocar o projeto. Isso criará um diretório `helloworld` em seu diretório base contendo todos os arquivos para o projeto do Xcode.
    1. Desabilite a criação de um repositório Git para este projeto de exemplo.
    1. Ajuste os caminhos para o SDK em *Configurações do projeto*.
        1. Na guia **Geral** no cabeçalho **Binários inseridos**, adicione a biblioteca SDK como uma estrutura: **Adicionar binários inseridos** > **Adicionar outro…** > Navegue até seu diretório base e escolha o arquivo `MicrosoftCognitiveServicesSpeech.framework`. Isso adicionará automaticamente a biblioteca do SDK ao cabeçalho **Framework Vinculado e Bibliotecas**.
        ![Estrutura Adicionada](media/sdk/qs-objectivec-framework.png)
        1. Vá para a guia **Configurações de Build** e ative **Todas** as configurações.
        1. Adicione o diretório `$(SRCROOT)/..` a *Caminhos de Pesquisa do Framework* no cabeçalho **Caminhos de Pesquisa**.
        ![Configuração de Caminho de Pesquisa de Estrutura](media/sdk/qs-objectivec-framework-search-paths.png)


## <a name="set-up-the-ui"></a>Configurar a interface do usuário

O aplicativo de exemplo terá uma interface do usuário muito simples: um botão para iniciar o processamento do arquivo e um rótulo de texto para exibir o resultado.
A interface do usuário é configurada no `Main.storyboard` como parte do projeto.
Abra a exibição XML do storyboard clicando com o botão direito do mouse na entrada `Main.storyboard` na árvore do projeto e selecionando **Abrir como…** > **Código-Fonte**.
Substitua o XML gerado automaticamente por este:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Baixe o [arquivo wav de amostra](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) clicando com o botão direito do mouse e escolhendo **Salvar destino como…**. Adicione o arquivo wav ao projeto como um recurso arrastando-o de uma janela do Finder para o nível raiz da exibição do Projeto.
Clique em **Concluir** na seguinte caixa de diálogo sem alterar as configurações.
1. Substitua o conteúdo do arquivo `ViewController.m` gerado automaticamente por:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Adicione a solicitação de acesso do microfone. Clique com o botão direito do mouse na entrada info.plist da árvore do projeto e selecione **Abrir como…** > **Código-Fonte**. Adicione as seguintes linhas à seção `<dict>` e, em seguida, salve o arquivo.
    ```xml
    <key>NSMicrophoneUsageDescription</key>

    <string>Need microphone access for speech recognition from microphone.</string>
    ```
1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.
1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).


## <a name="building-and-running-the-sample"></a>Criação e execução da amostra

1. Torne a saída de depuração visível (**Exibição** > **Área de Depuração** > **Ativar Console**).
1. Compile e execute o exemplo de código no simulador do iOS selecionando **Produto** -> **Executar** no menu ou clicando no botão **Reproduzir**. Para executar em um dispositivo iOS, conecte o dispositivo ao seu computador de desenvolvimento e selecione o dispositivo como o destino em execução. No momento, o SDK de Fala só dá suporte à plataforma iOS de 64 bits.
1. Depois de clicar no botão "Reconhecer!" no aplicativo, deverá ver o conteúdo de áudio de arquivos "Como está o clima?" na parte inferior da tela.

 ![Aplicativo do iOS simulado](media/sdk/qs-objectivec-simulated-app.png)

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure esse exemplo na pasta `quickstart/objectivec-ios`.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Obtenha nossas amostras](speech-sdk.md#get-the-samples)
