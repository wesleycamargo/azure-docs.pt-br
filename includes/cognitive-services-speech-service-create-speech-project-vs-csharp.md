---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 7a028f65467c517be59be6f0c6d2c45ef9a7685e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285752"
---
1. Inicie o Visual Studio 2017.

1. Na barra de menus no Visual Studio, selecione **Ferramentas > Obter Ferramentas** e certifique-se de que a carga de trabalho **Desenvolvimento de área de trabalho em .Net** esteja disponível. Se a carga de trabalho não estiver instalada, marque a caixa de seleção e clique em **Modificar** para iniciar a instalação. Talvez o download e a instalação demore alguns minutos.

   Se a caixa de seleção ao lado **Desenvolvimento de área de trabalho em .NET** estiver marcada, feche a caixa de diálogo.

   ![Habilitar desenvolvimento para área de trabalho .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Agora, vamos criar um projeto. Na barra de menus, selecione **Arquivo > Novo > Projeto**. Quando a caixa de diálogo for exibida, no painel esquerdo, expanda estas seções **Instalado > Visual C# > Windows Desktop** e selecione **Aplicativo de Console (.NET Framework)**. Chame esse projeto de *helloworld*.

    ![Criar aplicativo do console do Visual C # (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Criar aplicativo de console do Visual C# (.NET Framework)")

1. Agora que o projeto está configurado, precisamos instalar o [pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e fazer uma referência a ele em nosso código. Localize o Gerenciador de Soluções e clique com o botão direito em helloworld. No menu, selecione **Gerenciar Pacotes NuGet...**.

   ![Clique com o botão direito em Gerenciar pacotes NuGet para solução](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Gerenciar pacotes NuGet para solução")

1. No canto superior direito do Gerenciador de Pacotes do NuGet, localize o menu suspenso **Origem do Pacote** e selecione **nuget.org**. Depois, selecione **Procurar**, pesquise pelo pacote `Microsoft.CognitiveServices.Speech` e instale a versão estável mais recente.

   ![Instalar o Pacote NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Instalar o pacote NuGet")

1. Aceite todos os contratos e licenças para iniciar a instalação.

   ![Aceite a licença](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Aceite a licença")

    Depois que o pacote for instalado, uma confirmação será exibida no console do gerenciador de pacotes.

1. A próxima etapa é criar uma configuração de plataforma que corresponda à arquitetura do computador que você está usando para compilar e executar o aplicativo de console. Na barra de menus, selecione **Compilar** > **Gerenciador de Configuração...**.

    ![Ative o gerenciador de configuração](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Ative o gerenciador de configuração")

1. Na caixa de diálogo **Gerenciador de Configuração**, localize a lista suspensa **Plataforma da solução ativa** e selecione **Novo**.

    ![Adicione uma nova plataforma na janela do gerenciador de configuração](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Adicione uma nova plataforma na janela do gerenciador de configuração")

1. Se você estiver executando o Windows 64 bits, quando receber a solicitação **Digite ou selecione a nova plataforma**, `x64`. Se estiver executando o Windows 32 bits, selecione `x86`. Quando tiver terminado, clique em **OK**.

    ![No Windows de 64 bits, adicione uma nova plataforma chamada "x64"](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Adicione a plataforma x64")
