---
title: Criar sua primeira função durável no Azure usando JavaScript
description: Criar e publicar uma Durable Function do Azure usando o Visual Studio Code.
services: functions
documentationcenter: na
author: ColbyTresness
manager: jeconnoc
keywords: azure functions, funções, processamento de eventos, computação, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: azfuncdf, cotresne, glenga
ms.openlocfilehash: ad17b6ef032c7bc25a019d53f12cc33baa3163f3
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53340888"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Criar sua primeira função durável em JavaScript

*Durable Functions* são uma extensão do [Azure Functions](../functions-overview.md) que permitem que você escreva funções com estado em um ambiente sem servidor. A extensão gerencia estado, pontos de verificação e reinicializações para você.

Neste artigo, você aprenderá a usar a extensão do Azure Functions do Visual Studio Code para criar e testar localmente uma função durável "hello world".  Essa função orquestrará e encadeará chamadas para outras funções. Em seguida, você publicará o código de função no Azure.

![Como executar funções duráveis no Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale o [Visual Studio Code](https://code.visualstudio.com/download).

* Verifique se você tem as [ferramentas mais recentes do Azure Functions](../functions-develop-vs.md#check-your-tools-version).

* Em um computador Windows, verifique se você tem o [Emulador de Armazenamento do Azure](../../storage/common/storage-use-emulator.md) instalado e em execução. Em um computador Mac ou Linux, você deve usar uma conta de armazenamento real do Azure.

* Verifique se você tem a versão 8.0 ou posterior do [Node.js](https://nodejs.org/) instalada.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>Instalar o pacote npm de Durable Functions

1. Instale o pacote npm `durable-functions` executando `npm install durable-functions` no diretório raiz do aplicativo de funções.

## <a name="create-a-starter-function"></a>Criar uma Starter Function

Primeiro, crie uma função disparada por HTTP que inicia uma orquestração de função durável.

1. Do **Azure: Functions**, escolha o ícone Criar Função.

    ![Criar uma função](./media/quickstart-js-vscode/create-function.png)

2. Selecione a pasta com seu projeto de aplicativo de funções e selecione o modelo de função **gatilho HTTP**.

    ![Escolher o modelo de gatilho HTTP](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Digite `HttpStart` como o nome da função e pressione Enter, depois selecione autenticação **Anônima**.

    ![Escolher autenticação anônima](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

    Uma função é criada na linguagem escolhida por você usando o modelo de uma função disparada por HTTP.

4. Substitua index.js pelo JavaScript abaixo:

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

5. Substitua o function.json pelo o JSON abaixo:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

Agora, criamos um ponto de entrada em nossa Durable Function. Vamos adicionar um orquestrador.

## <a name="create-an-orchestrator-function"></a>Criar uma Orchestrator Function

Em seguida, você pode criar outra função para ser o orchestrator. Usamos o modelo de função de gatilho HTTP para sua conveniência. O próprio código de função é substituído pelo código do orquestrador.

1. Repita as etapas da seção anterior para criar uma segunda função usando o modelo de gatilho HTTP. Agora, nomeie a função como `OrchestratorFunction`.

2. Abra o arquivo index.js da nova função e substitua o conteúdo pelo seguinte código:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

3. Abra o arquivo function.json e substitua-o pelo JSON a seguir:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Adicionamos um orquestrador para coordenar as funções de atividade. Agora, vamos adicionar a função de atividade referenciada.

## <a name="create-an-activity-function"></a>Criar uma Activity Function

1. Repita as etapas da seção anterior para criar uma terceira função usando o modelo de gatilho HTTP. Porém, dessa vez, nomeie a função como `SayHello`.

2. Abra o arquivo index.js da nova função e substitua o conteúdo pelo seguinte código:

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

3. Substitua o function.json pelo o JSON abaixo:

    [!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

Agora adicionamos todos os componentes necessários para iniciar uma orquestração e unir as funções de atividade.

## <a name="test-the-function-locally"></a>Testar a função localmente

As Ferramentas Principais do Azure Functions permitem executar um projeto do Azure Functions no seu computador de desenvolvimento local. É solicitado que você instale essas ferramentas na primeira vez em que inicia uma função no Visual Studio Code.  

1. Em um computador Windows, inicie o Emulador de Armazenamento do Azure e verifique se a propriedade **AzureWebJobsStorage** de local.settings.json está definida como `UseDevelopmentStorage=true`. Em um computador Mac ou Linux, defina a propriedade **AzureWebJobsStorage** na cadeia de conexão de uma conta de armazenamento do Azure existente. Você pode criar a conta de armazenamento mais tarde neste artigo.

2. Para testar sua função, defina um ponto de interrupção no código da função e pressione F5 para iniciar o projeto de aplicativo de funções. A saída do Core Tools é exibida no painel **Terminal**. Se essa for a primeira vez que você usa Durable Functions, a extensão Durable Functions será instalada e a compilação poderá levar alguns segundos.

    > [!NOTE]
    > Durable Functions em JavaScript exigem a versão **1.7.0** ou superior da extensão **Microsoft.Azure.WebJobs.Extensions.DurableTask**. Verifique se a versão da extensão de Durable Functions em seu arquivo `extensions.csproj` cumpre esse requisito. Caso não cumpra, interrompa o aplicativo de funções, altere a versão e pressione F5 para reiniciar o aplicativo de funções.

3. No painel **Terminal**, copie o ponto de extremidade de URL da sua função disparada por HTTP.

    ![Saída local do Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Cole a URL para a solicitação HTTP na barra de endereços do navegador e observe o status da orquestração.

5. Para interromper a depuração, pressione Shift + F1.

Após verificar se a função foi executada corretamente no computador local, é hora de publicar o projeto no Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testar sua função no Azure

1. Copie a URL do gatilho de HTTP do painel **Saída**. A URL que chama a função HTTP disparada deve estar no seguinte formato:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Cole essa nova URL para a solicitação HTTP na barra de endereços do navegador. Ao usar o aplicativo publicado, você deve obter a mesma resposta de status como antes.

## <a name="next-steps"></a>Próximas etapas

Você usou o Visual Studio Code para criar e publicar um aplicativo de funções duráveis de JavaScript.

> [!div class="nextstepaction"]
> [Saiba mais sobre os padrões comuns de função durável](durable-functions-overview.md)