---
title: Criar sua primeira função no Azure usando o Visual Studio Code
description: Crie e publique uma função disparada por HTTP simples no Azure usando a extensão Azure Functions no Visual Studio Code.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, funções, processamento de eventos, computação, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 09/07/2018
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: d265c391b12a46f509c8663c64892372c8d806ba
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54104515"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Criar sua primeira função usando o Visual Studio Code

O Azure Functions lhe permite executar seu código em um ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/) sem que seja preciso primeiro criar uma VM ou publicar um aplicativo Web.

Neste artigo, você aprenderá a usar a [extensão Azure Functions para Visual Studio Code] para criar e testar uma função "hello world" no computador local usando o Microsoft Visual Studio Code. Em seguida, você publicará o código de função no Azure do Visual Studio Code.

![Código do Azure Functions em um projeto do Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

No momento, a extensão é totalmente compatível com as funções C#, JavaScript e Java. Com o Python ela é compatível na versão prévia. As etapas neste artigo podem variar dependendo de sua escolha de linguagem de programação para o seu projeto do Azure Functions. Atualmente, a extensão está em versão prévia. Para obter mais informações, consulte a página da extensão [Extensão Azure Functions para Visual Studio Code].

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instale o [Visual Studio Code](https://code.visualstudio.com/) em uma das [plataformas compatíveis](https://code.visualstudio.com/docs/supporting/requirements#_platforms). Este artigo foi desenvolvido e testado em um dispositivo executando o macOS (High Sierra).

* Instale a versão 2.x do [Azure Functions Core Tools](functions-run-local.md#v2), que ainda está em versão prévia.

* Instale os requisitos específicos para a linguagem de programação escolhida:

    | Linguagem | Extensão |
    | -------- | --------- |
    | **C#** | [C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)<br/>[Ferramentas da CLI do .NET Core](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)*   |
    | **Java** | [Depurador para Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3+](https://maven.apache.org/) |
    | **JavaScript** | [Node 8.0+](https://nodejs.org/)  |

    \* Também é requerido pelo Core Tools.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

## <a name="create-an-http-triggered-function"></a>Crie uma função disparada por HTTP

1. Do **Azure: Functions**, escolha o ícone Criar Função.

    ![Criar uma função](./media/functions-create-first-function-vs-code/create-function.png)

1. Selecione a pasta com seu projeto de aplicativo de funções e selecione o modelo de função **gatilho HTTP**.

    ![Escolher o modelo de gatilho HTTP](./media/functions-create-first-function-vs-code/create-function-choose-template.png)

1. Digite `HTTPTrigger` como o nome da função e pressione Enter, depois selecione autenticação **Anônima**.

    ![Escolher autenticação anônima](./media/functions-create-first-function-vs-code/create-function-anonymous-auth.png)

    Uma função é criada na linguagem escolhida por você usando o modelo de uma função disparada por HTTP.

    ![Modelo de função disparada por HTTP no Visual Studio Code](./media/functions-create-first-function-vs-code/new-function-full.png)

Você pode adicionar associações de entrada e de saída à função modificando o arquivo function.json. Para obter mais informações, consulte [Gatilhos e conceitos de associações do Azure Functions](functions-triggers-bindings.md).

Agora que você criou o seu projeto de função e uma função disparada por HTTP, poderá testá-la em seu computador local.

## <a name="test-the-function-locally"></a>Testar a função localmente

As Ferramentas Principais do Azure Functions permitem executar um projeto do Azure Functions no seu computador de desenvolvimento local. É solicitado que você instale essas ferramentas na primeira vez em que inicia uma função no Visual Studio Code.  

1. Para testar sua função, defina um ponto de interrupção no código da função e pressione F5 para iniciar o projeto de aplicativo de funções. A saída do Core Tools é exibida no painel **Terminal**.

1. No painel **Terminal**, copie o ponto de extremidade de URL da sua função disparada por HTTP.

    ![Saída local do Azure](./media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Cole a URL para a solicitação HTTP na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `?name=<yourname>` a essa URL e execute a solicitação. A execução é pausada quando o ponto de interrupção é atingido.

    ![Função atingindo o ponto de interrupção no Visual Studio Code](./media/functions-create-first-function-vs-code/function-debug-vscode-js.png)

1. Quando você continua a execução, o exemplo a seguir mostra a resposta no navegador à solicitação GET:

    ![Resposta da função localhost no navegador](./media/functions-create-first-function-vs-code/functions-test-local-browser.png)

1. Para interromper a depuração, pressione Shift + F5.

Após verificar se a função foi executada corretamente no computador local, é hora de publicar o projeto no Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testar sua função no Azure

1. Copie a URL do gatilho de HTTP do painel **Saída**. Assim como anteriormente, certifique-se de adicionar o valor de cadeia de caracteres de consulta `?name=<yourname>` a essa URL e execute a solicitação.

    A URL que chama a função HTTP disparada deve estar no seguinte formato:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

1. Cole essa nova URL para a solicitação HTTP na barra de endereços do navegador. O exemplo a seguir mostra a resposta no navegador à solicitação GET remota retornada pela função: 

    ![Resposta da função no navegador](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Próximas etapas

Você usou o Visual Studio Code para criar um aplicativo de funções com uma função disparada por HTTP simples. Para saber mais sobre como desenvolver funções em uma linguagem de programação específica, consulte os guias de referência de linguagem para [JavaScript](functions-reference-node.md), [.NET](functions-dotnet-class-library.md) ou [Java](functions-reference-java.md).

Em seguida, você talvez queira saber mais sobre depuração e testes locais do Terminal ou do prompt de comando usando o Azure Functions Core Tools.

> [!div class="nextstepaction"]
> [Codificar e testar localmente](functions-run-local.md)

[Azure Functions Core Tools]: functions-run-local.md
[Extensão Azure Functions para Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
