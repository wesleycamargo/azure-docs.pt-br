---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 8ff8e8341b6f39f66c2cc8014d41d3d3a2918d2b
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454353"
---
1. Inicie o Visual Studio 2017.
 
1. Verifique se a carga de trabalho **ambiente de área de trabalho do .NET** está disponível. Escolha **Ferramentas** \> **Obter Ferramentas e Recursos** na barra de menus do Visual Studio para abrir o instalador do Visual Studio. Se essa carga de trabalho já estiver habilitada, feche a caixa de diálogo. 

    Caso contrário, marque a caixa de seleção ao lado de **Desenvolvimento para área de trabalho do .NET** e, em seguida, clique no botão **Modificar** no canto inferior direito da caixa de diálogo. Instalação do novo recurso levará alguns instantes.

    ![Habilitar desenvolvimento para área de trabalho .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Crie um aplicativo de console Visual C#. Na caixa de diálogo **Novo Projeto**, no painel esquerdo, expanda **Instalado** \> **Visual C#** \> **Windows Desktop** e, em seguida, selecione **Aplicativo de Console (.NET Framework)**. Para o nome do projeto, insira *helloworld*.

    ![Criar aplicativo do console do Visual C # (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Criar aplicativo de console do Visual C# (.NET Framework)")

1. Instale e faça referência ao pacote do [Speech SDK NuGet](https://aka.ms/csspeech/nuget). No Solution Explorer, clique com o botão direito do mouse na solução e selecione **Manage NuGet Packages for Solution**.

    ![Clique com o botão direito em Gerenciar pacotes NuGet para solução](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Gerenciar pacotes NuGet para solução")

1. No canto superior direito, no campo **Origem do Pacote**, selecione **nuget.org**. Procure o pacote `Microsoft.CognitiveServices.Speech` e instale-o no projeto **helloworld**.

    ![Instalar o Pacote NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Instalar o pacote NuGet")

1. Aceite a licença exibida para iniciar a instalação do pacote NuGet.

    ![Aceite a licença](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Aceite a licença")

    Depois que o pacote for instalado, uma confirmação será exibida no console do gerenciador de pacotes.

1. Crie uma configuração de plataforma correspondente à sua arquitetura de computador por meio do Configuration Manager. Selecione **Gerencie** > **Gerenciador de Configuração**.

    ![Ative o gerenciador de configuração](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Ative o gerenciador de configuração")

1. Na caixa de diálogo **Configuration Manager**, adicione uma nova plataforma. Na lista suspensa da **Plataforma de solução ativa**, selecione **Novo**.

    ![Adicione uma nova plataforma na janela do gerenciador de configuração](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Adicione uma nova plataforma na janela do gerenciador de configuração")

1. Se você estiver executando o Windows de 64 bits, crie uma nova configuração de plataforma denominada`x64`. Se você estiver executando o Windows de 32 bits, crie uma nova configuração de plataforma denominada`x86`.

    ![No Windows de 64 bits, adicione uma nova plataforma chamada "x64"](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Adicione a plataforma x64")


