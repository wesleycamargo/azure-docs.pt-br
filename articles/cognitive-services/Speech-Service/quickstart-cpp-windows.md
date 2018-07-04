---
title: Início rápido do SDK de Fala para C++ e Windows | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente o SDK de Fala com Windows e C++ em Serviços Cognitivos.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0bcdc3c4357cb8985fad16c607957bffad4a2b8c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049223"
---
# <a name="quickstart-for-c-and-windows"></a>Início rápido para C++ e Windows

A versão atual do SDK de Fala dos Serviços Cognitivos é `0.4.0`.

Descrevemos como criar um aplicativo de console baseado em C++ para a área de trabalho do Windows que usa o SDK de Fala.
O aplicativo é baseado no [Pacote NuGet do SDK dos Serviços Cognitivos da Microsoft](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) e Microsoft Visual Studio 2017.

> [!NOTE]
> Se você estiver procurando um início rápido para C++ e Linux, acesse [aqui](quickstart-cpp-linux.md).<br>
> Se você estiver procurando um início rápido para C# e Windows, acesse [aqui](quickstart-csharp-windows.md).

> [!NOTE]
> Este início rápido requer um PC com um microfone funcionando.<br>
> Para um exemplo que reconhece a fala de um determinado arquivo de entrada de áudio, consulte o [exemplo](speech-to-text-sample.md#speech-recognition-from-a-file).

> [!NOTE]
> Assegure-se de que a instalação do Visual Studio inclui a carga de trabalho de **Desenvolvimento de área de trabalho com C++**.
> Se você não tiver certeza, utilize estas etapas para verificar e corrigir: No Visual Studio 2017, selecione **Ferramentas** \> **Obter Ferramentas e Recursos** e confirme o prompt de Controle de Conta de Usuário, escolhendo**Sim**.
> Na guia **Cargas de Trabalho**, se **Desenvolvimento de área de trabalho com C++** não tiver uma caixa de seleção ao lado, configure-o e clique em **Modificar** para salvar as alterações.

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

## <a name="creating-an-empty-console-application-project"></a>Criar um projeto de aplicativo de console vazio

No Visual Studio 2017, crie um novo Aplicativo de Console do Windows de Área de Trabalho do Windows para Visual C++ com o nome "CppHelloSpeech":

![Criar Aplicativo de Console do Windows de Área de Trabalho do Windows para Visual C++](media/sdk/speechsdk-05-vs-cpp-new-console-app.png)

Se estiver executando em uma instalação do Windows de 64 bits, alterne opcionalmente a plataforma de compilação para `x64`:

![Alterne a plataforma de compilação para x64](media/sdk/speechsdk-07-vs-cpp-switch-to-x64.png)

## <a name="install-and-reference-the-speech-sdk-nuget-package"></a>Instalar e referenciar o pacote NuGet do SDK de Fala

> [!NOTE]
> Assegure-se de que o gerenciador de pacotes NuGet está habilitado para a instalação do Visual Studio 2017.
> No Visual Studio 2017, selecione **Ferramentas** \> **Obter Ferramentas e Recursos** e confirme o prompt de Controle de Conta de Usuário escolhendo **Sim**. Em seguida, selecione a guia **Componentes individuais** e procure o **Gerenciador de Pacotes NuGet** em **Ferramentas de código**.
> Se a caixa de seleção esquerda não estiver configurada, configure-a e clique em **Modificar** para salvar as alterações.
>
> ![Habilitar o gerenciador de pacotes NuGet no Visual Studio ](media/sdk/speechsdk-05-vs-enable-nuget-package-manager.png)

No Gerenciador de Soluções, clique com o botão direito do mouse na solução e clique em **Gerenciar pacotes NuGet para solução**.

![Clicar com o botão direito do mouse em Gerenciar pacotes NuGet para solução](media/sdk/speechsdk-09-vs-cpp-manage-nuget-packages.png)

No canto superior direito, no campo **Origem do Pacote e**, escolha "Nuget.org".
Na guia **Procurar** , procure o pacote "Microsoft.CognitiveServices.Speech", selecione-o e marque as caixas **Projeto** e **CppHelloSpeech** à direita e selecione **Instalar** para instalá-lo no projeto CppHelloSpeech.

![Instalar o pacote NuGet Microsoft.CognitiveServices.Speech](media/sdk/speechsdk-11-vs-cpp-manage-nuget-install.png)

Na tela de licença que é exibida, aceite a licença:

![Aceitar a licença](media/sdk/speechsdk-12-vs-cpp-manage-nuget-license.png)

## <a name="add-the-sample-code"></a>Adicionar o código de exemplo

Substitua o código inicial padrão pelo seguinte:

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-cpp/CppHelloSpeech.cpp#code)]

> [!IMPORTANT]
> Substitua a chave de assinatura por uma que você obteve. <br>
> Substitua a região pela sua região da [API REST do Serviço de Fala](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis), por exemplo, substitua por "westus".

![Adicionar a chave de assinatura](media/sdk/sub-key-recognize-speech-cpp.png)

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

O código deve compilar sem erros:

![Compilar com êxito](media/sdk/speechsdk-16-vs-cpp-build.png)

Inicialize o programa sob o depurador com o botão de Inicialização ou usando o atalho de teclado F5:

![Inicializar o aplicativo na depuração](media/sdk/speechsdk-17-vs-cpp-f5.png)

Uma janela pop-up do console deverá aparecer, solicitando que você fale alguma coisa (em inglês).
O resultado do reconhecimento será exibido na tela.

![Saída do console após o reconhecimento com êxito](media/sdk/speechsdk-18-vs-cpp-console-output-release.png)

## <a name="downloading-the-sample"></a>Baixar o exemplo

Para obter o último conjunto de exemplos, consulte o [Repositório GitHub de exemplo do SDK de Fala dos Serviços Cognitivos](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Próximas etapas

* Visita a [página de exemplos](samples.md) para obter exemplos adicionais.
