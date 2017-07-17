---
title: "Criar sua primeira função no Azure usando o Visual Studio | Microsoft Docs"
description: "Criar e publicar uma função disparada por HTTP simples no Azure usando Ferramentas do Azure Functions para Visual Studio."
services: functions
documentationcenter: na
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação, arquitetura sem servidor"
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/25/2017
ms.author: rachelap, glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 64769458ad90f14c2f7a87b9a405b80616a478be
ms.contentlocale: pt-br
ms.lasthandoff: 06/28/2017


---
# Criar sua primeira função usando o Visual Studio
<a id="create-your-first-function-using-visual-studio" class="xliff"></a> 

O Azure Functions lhe permite executar seu código em um ambiente sem servidor sem que seja preciso primeiro criar uma VM ou publicar um aplicativo Web. 

Neste tópico, você aprenderá a usar as Ferramentas do Azure Functions para Visual Studio 2017 a fim de criar e testar uma função de "Olá, Mundo" localmente. Em seguida, você publicará o código de função no Azure.

![Código do Azure Functions em um projeto do Visual Studio](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

## Pré-requisitos
<a id="prerequisites" class="xliff"></a>

Para concluir este tutorial, instale:

* [Versão prévia do Visual Studio 2017 15.3](https://www.visualstudio.com/vs/preview/), incluindo a carga de trabalho **Desenvolvimento do Azure**.
    
    ![Instalar o Visual Studio de 2017 com a carga de trabalho de desenvolvimento do Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Instalar as Ferramentas do Azure Functions para Visual Studio 2017
<a id="install-azure-functions-tools-for-visual-studio-2017" class="xliff"></a>

Antes de começar, você deve baixar e instalar as Ferramentas do Azure Functions para Visual Studio 2017. Essas ferramentas podem ser usadas apenas com a Versão prévia do Visual Studio 2017 15.3 ou uma versão posterior. Se você já instalou as Ferramentas do Azure Functions, poderá ignorar esta seção.

[!INCLUDE [Install the Azure Functions Tools for Visual Studio](../../includes/functions-install-vstools.md)]   

## Criar um projeto do Azure Functions no Visual Studio
<a id="create-an-azure-functions-project-in-visual-studio" class="xliff"></a>

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Agora que você criou o projeto, poderá criar sua primeira função.

## Criar a função
<a id="create-the-function" class="xliff"></a>

No **Gerenciador de Soluções**, clique com o botão direito do mouse no nó do projeto e selecione **Adicionar** > **Novo Item**. Selecione **Azure Function** e clique em **Adicionar**.

Selecione **HttpTrigger**, digite um **Nome da Função**, selecione **Anônimo** para **Direitos de Acesso**e clique em **Criar**. A função criada é acessada por uma solicitação HTTP de qualquer cliente. 

![Criar uma nova função do Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

Agora que você criou uma função disparada por HTTP, poderá testá-la em seu computador local.

## Testar a função localmente
<a id="test-the-function-locally" class="xliff"></a>

[!INCLUDE [Test the function locally](../../includes/functions-vstools-test.md)]

Copie a URL da sua função da saída de tempo de execução do Azure Functions.  

![Tempo de execução local do Azure](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

 Cole a URL para a solicitação HTTP na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `&name=<yourname>` a essa URL e execute a solicitação. O exemplo a seguir mostra a resposta no navegador à solicitação GET local retornada pela função: 

![Resposta da função localhost no navegador](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

Para interromper a depuração, clique no botão **Parar** na barra de ferramentas do Visual Studio.

Após verificar se a função foi executada corretamente no computador local, é hora de publicar o projeto no Azure.

## Publicar o projeto no Azure
<a id="publish-the-project-to-azure" class="xliff"></a>

Você deve ter um aplicativo de funções em sua assinatura do Azure antes de publicar seu projeto. Você pode criar um aplicativo de funções diretamente no Visual Studio.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## Testar sua função no Azure
<a id="test-your-function-in-azure" class="xliff"></a>

Copie a URL base do aplicativo de funções da página de perfil de publicação. Substitua a parte `localhost:port` da URL que você usou ao testar a função localmente pela nova URL base. Como anteriormente, acrescente o valor de cadeia de consulta `&name=<yourname>` a essa URL e execute a solicitação.

A URL que chama a função disparada por HTTP é mais ou menos assim:

    http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

Cole essa nova URL para a solicitação HTTP na barra de endereços do navegador. O exemplo a seguir mostra a resposta no navegador à solicitação GET remota retornada pela função: 

![Resposta da função no navegador](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)
 
## Próximas etapas
<a id="next-steps" class="xliff"></a>

Você usou o Visual Studio para criar um aplicativo de funções C# com uma função disparada por HTTP simples. 

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]

Para saber mais sobre o local de teste e a depuração usando as Ferramentas Principais do Azure Functions, confira [Codificar e testar o Azure Functions localmente](functions-run-local.md). 


