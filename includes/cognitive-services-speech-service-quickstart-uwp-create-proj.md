---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: c8ef95f6a01b64b722544d9397d928387a69d7c7
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56458401"
---
1. Inicie o Visual Studio 2017.

1. Verifique se a carga de trabalho **Desenvolvimento da Plataforma Universal do Windows** está disponível. Escolha **Ferramentas** > **Obter Ferramentas e Recursos** na barra de menus do Visual Studio para abrir o Instalador do Visual Studio. Se essa carga de trabalho já estiver habilitada, feche a caixa de diálogo.

    ![Captura de tela do Instalador do Visual Studio, com a guia Cargas de Trabalho realçada](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

    Caso contrário, marque a caixa ao lado de **Desenvolvimento multiplataforma do .NET** e selecione **Modificar** no canto inferior direito da caixa de diálogo. A instalação do novo recurso leva alguns instantes.

1. Crie um aplicativo universal do Windows em branco do Visual C#. Primeiro, escolha **Arquivo** > **Novo** > **Projeto** no menu. Na caixa de diálogo **Novo Projeto**, expanda **Instalado** > **Visual C#** > **Universal do Windows** no painel esquerdo. Em seguida, selecione **Aplicativo em Branco (Universal do Windows)**. Para o nome do projeto, insira *helloworld*.

    ![A captura de tela da caixa de diálogo Novo Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. O SDK de Fala exige que o aplicativo seja compilado para o Windows 10 Fall Creators Update ou posterior. Na janela **Novo Projeto da Plataforma Universal do Windows** exibida, escolha **Windows 10 Fall Creators Update (10.0; Build 16299)** como a **Versão mínima**. Na caixa **Versão de destino**, selecione essa ou qualquer versão mais recente e, em seguida, clique em **OK**.

    ![Captura de tela da janela Novo Projeto da Plataforma Universal do Windows](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Se você estiver executando o Windows de 64 bits, alterne a plataforma de build para `x64` usando o menu suspenso na barra de ferramentas do Visual Studio. (O Windows de 64 bits pode executar aplicativos de 32 bits e, portanto, você poderá deixá-lo definido como `x86` se preferir.)

   ![Captura de tela da barra de ferramentas do Visual Studio, com a opção x64 realçada](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > O SDK de Fala dá suporte somente a processadores compatíveis com Intel. No momento, não há suporte para o ARM.

1. Instale e faça referência ao pacote do [Speech SDK NuGet](https://aka.ms/csspeech/nuget). No Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Gerenciar Pacotes NuGet para a Solução**.

    ![Captura de tela do Gerenciador de Soluções, com a opção Gerenciar Pacotes NuGet para a Solução realçada](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. No canto superior direito, no campo **Package Source**, selecione **nuget.org**. Procure o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **helloworld**.

    ![Captura de tela da caixa de diálogo Gerenciar Pacotes para a Solução](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Instalar pacote NuGet")

1. Aceite a licença exibida para iniciar a instalação do pacote NuGet.

    ![Captura de tela da caixa de diálogo Aceitação da Licença](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-06-nuget-license.png "Aceitar a licença")

1. A seguinte linha de saída aparece no console do Gerenciador de Pacotes.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.3.0' to helloworld
   ```

1. Como o aplicativo usa o microfone para a entrada de fala, adicione a funcionalidade **Microfone** ao projeto. No Gerenciador de Soluções, clique duas vezes em **Package.appxmanifest** para editar o manifesto do aplicativo. Em seguida, alterne para a guia **Funcionalidades**, marque a caixa da funcionalidade **Microfone** e salve as alterações.

   ![Captura de tela do manifesto do aplicativo do Visual Studio, com as Funcionalidades e o Microfone realçados](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)
