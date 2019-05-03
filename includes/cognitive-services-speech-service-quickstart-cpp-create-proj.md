---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: af61d975b540787b9b54d9fdea66773c10aeb6ac
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020776"
---
1. Inicie o Visual Studio 2017.

1. Verifique se a carga de trabalho **Desenvolvimento para Desktop com C++** está disponível. Escolha **Ferramentas** > **Obter Ferramentas e Recursos** na barra de menus do Visual Studio para abrir o Instalador do Visual Studio. Se essa carga de trabalho já estiver habilitada, passe para a próxima etapa.

    ![Captura de tela da guia Cargas de Trabalho do Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

    Caso contrário, marque a caixa de seleção ao lado de **Desenvolvimento para Desktop com C++**.

1. Verifique se o componente **Gerenciador de pacotes NuGet** está disponível. Mude para a guia **Componentes individuais** da caixa de diálogo do Instalador do Visual Studio e selecione **Gerenciador de pacotes NuGet** se ainda não estiver habilitado.

      ![Captura de tela da guia Componentes individuais do Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-nuget-package-manager.png)

1. Se for necessário habilitar a carga de trabalho do C++ ou do NuGet, selecione **Modificar** (no canto inferior direito da caixa de diálogo). A instalação dos novos recursos leva alguns instantes. Se os dois recursos já foram habilitados, feche a caixa de diálogo.

1. Crie um Aplicativo de Console do Windows do Visual C++ para Windows Desktop. Primeiro, escolha **Arquivo** > **Novo** > **Projeto** no menu. Na caixa de diálogo **Novo Projeto**, expanda **Instalado** > **Visual C++** > **Windows Desktop** no painel esquerdo. Em seguida, selecione **Aplicativo de Console do Windows**. Para o nome do projeto, insira *helloworld*.

    ![A captura de tela da caixa de diálogo Novo Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Se você estiver executando o Windows de 64 bits, será possível alternar sua plataforma de build para `x64` usando o menu suspenso na barra de ferramentas do Visual Studio. (As versões de 64 bits do Windows podem executar aplicativos de 32 bits, portanto, esse não é um requisito.)

    ![Captura de tela da barra de ferramentas do Visual Studio, com a opção x64 realçada](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e escolha **Gerenciar Pacotes NuGet para Solução**.

    ![Captura de tela do Gerenciador de Soluções, com a opção Gerenciar Pacotes NuGet para a Solução realçada](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. No canto superior direito, no campo **Package Source**, selecione **nuget.org**. Procure o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **helloworld**.

    ![Captura de tela da caixa de diálogo Gerenciar Pacotes para Solução](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > A versão atual do SDK de Fala dos Serviços Cognitivos é `1.5.0`.

1. Aceite a licença exibida para iniciar a instalação do pacote NuGet.

    ![Captura de tela da caixa de diálogo Aceitação da Licença](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-05-nuget-license.png)

Depois que o pacote for instalado, uma confirmação será exibida no console do gerenciador de pacotes.
