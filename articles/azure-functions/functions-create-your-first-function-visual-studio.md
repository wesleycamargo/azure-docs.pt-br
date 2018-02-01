---
title: "Criar sua primeira função no Azure usando o Visual Studio | Microsoft Docs"
description: "Criar e publicar uma função disparada por HTTP simples no Azure usando Ferramentas do Azure Functions para Visual Studio."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação, arquitetura sem servidor"
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/17/2018
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: e8a43febdb2958f58ecb8d82f9f42b39c591522d
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="create-your-first-function-using-visual-studio"></a>Criar sua primeira função usando o Visual Studio

O Azure Functions lhe permite executar seu código em um ambiente [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) sem que seja preciso primeiro criar uma VM ou publicar um aplicativo Web.

Neste artigo, você aprenderá a usar as ferramentas do Visual Studio 2017 para Azure Functions para criar e testar uma função "hello world". Em seguida, você publicará o código de função no Azure. Essas ferramentas estão disponíveis como parte da carga de trabalho de desenvolvimento do Azure no Visual Studio 2017.

![Código do Azure Functions em um projeto do Visual Studio](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

Este tópico inclui [um vídeo](#watch-the-video) que demonstra as mesmas etapas básicas.

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial:

* Instale o [Visual Studio 2017 versão 15.4](https://www.visualstudio.com/vs/) ou uma versão posterior, incluindo a carga de trabalho de **Desenvolvimento do Azure**.

    ![Instalar o Visual Studio de 2017 com a carga de trabalho de desenvolvimento do Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

* Verifique se você atualizou para a versão mais recente do Azure Functions e das Ferramentas WebJobs. Faça isso em **Atualizações** > **Visual Studio Marketplace** em **Extensões e atualizações**.
    
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

## <a name="create-a-function-app-project"></a>Crie um projeto de aplicativo de funções

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

O Visual Studio cria um projeto com uma classe que contém o código padronizado para o tipo de função escolhida. O atributo **FunctionName** no método define o nome da sua função. O atributo **HttpTrigger** especifica que a função é disparada por uma solicitação HTTP. O código padronizado envia uma resposta HTTP que inclui um valor do corpo de solicitação ou da cadeia de consulta. Você pode adicionar associações de entrada e saída para uma função aplicando os atributos apropriados para o método. Para obter mais informações, consulte a seção [Gatilhos e associações](functions-dotnet-class-library.md#triggers-and-bindings) da [referência do desenvolvedor C# do Azure Functions](functions-dotnet-class-library.md).

![Arquivo do código de função](./media/functions-create-your-first-function-visual-studio/functions-code-page.png)

Agora que você criou o seu projeto de função e uma função disparada por HTTP, poderá testá-la em seu computador local.

## <a name="test-the-function-locally"></a>Testar a função localmente

As Ferramentas Principais do Azure Functions permitem executar um projeto do Azure Functions no seu computador de desenvolvimento local. Você precisa instalar essas ferramentas na primeira vez em que inicia uma função no Visual Studio.  

1. Para testar sua função, pressione F5. Se solicitado, aceite a solicitação do Visual Studio para baixar e instalar as ferramentas principais (CLI) do Azure Functions. Você também precisará habilitar a exceção de firewall de forma que as ferramentas possam lidar com solicitações HTTP.

2. Copie a URL da sua função da saída de tempo de execução do Azure Functions.  

    ![Tempo de execução local do Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

3. Cole a URL para a solicitação HTTP na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `?name=<yourname>` a essa URL e execute a solicitação. O exemplo a seguir mostra a resposta no navegador à solicitação GET local retornada pela função: 

    ![Resposta da função localhost no navegador](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

4. Para interromper a depuração, clique no botão **Parar** na barra de ferramentas do Visual Studio.

Após verificar se a função foi executada corretamente no computador local, é hora de publicar o projeto no Azure.

## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

Você deve ter um aplicativo de funções em sua assinatura do Azure antes de publicar seu projeto. Você pode criar um aplicativo de funções diretamente no Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testar sua função no Azure

1. Copie a URL base do aplicativo de funções da página de perfil de publicação. Substitua a parte `localhost:port` da URL que você usou ao testar a função localmente pela nova URL base. Como anteriormente, acrescente o valor de cadeia de consulta `?name=<yourname>` a essa URL e execute a solicitação.

    A URL que chama a função HTTP disparada deve ser no seguinte formato:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

2. Cole essa nova URL para a solicitação HTTP na barra de endereços do navegador. O exemplo a seguir mostra a resposta no navegador à solicitação GET remota retornada pela função: 

    ![Resposta da função no navegador](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)

## <a name="watch-the-video"></a>Assista ao vídeo

> [!VIDEO https://www.youtube-nocookie.com/embed/DrhG-Rdm80k]

## <a name="next-steps"></a>Próximas etapas

Você usou o Visual Studio para criar um aplicativo de funções C# com uma função disparada por HTTP simples. 

+ Para saber como configurar seu projeto para dar suporte a outros tipos de gatilhos e associações, consulte a seção [Configurar o projeto para desenvolvimento local](functions-develop-vs.md#configure-the-project-for-local-development) em [Ferramentas do Azure Functions para Visual Studio](functions-develop-vs.md).
+ Para saber mais sobre o local de teste e a depuração usando as Ferramentas Principais do Azure Functions, confira [Codificar e testar o Azure Functions localmente](functions-run-local.md). 
+ Para saber mais sobre como desenvolver funções como bibliotecas de classes do .NET, veja [Como usar bibliotecas de classes do .NET com o Azure Functions](functions-dotnet-class-library.md). 

