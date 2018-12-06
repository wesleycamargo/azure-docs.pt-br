---
title: Criar Durable Functions usando o portal do Azure
description: Aprenda a instalar a extensão Durable Functions doa Azure Functions para o desenvolvimento do portal.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: acbba991e6dcce56fad7f27c45f85214cc8fc707
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52637001"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Criar Durable Functions usando o portal do Azure

A extensão [Funções Duráveis](durable-functions-overview.md) do Azure Functions é fornecida no pacote de NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Esta extensão deve ser instalada em seu aplicativo de função. Este artigo mostra como instalar esse pacote para que você possa desenvolver funções duráveis no portal do Azure.

>[!NOTE]
>
>* Se você estiver desenvolvendo funções duráveis em C#, deverá considerar o [Desenvolvimento do Visual Studio 2017](durable-functions-create-first-csharp.md).
* Se você estiver desenvolvendo funções duráveis em JavaScript, você deve considerar o **Desenvolvimento de código do Visual Studio Code**.
>
>Criação de Durable Functions usando JavaScript ainda não é suportado no portal. Use o Visual Studio Code.

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de função para hospedar a execução de qualquer função. Um aplicativo de função permite agrupar suas funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos. Você deve criar um aplicativo de função C#, já que os modelos JavaScript ainda não são suportados para Durable Functions.  

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Por padrão, o aplicativo de função criado usa a versão 2.x do tempo de execução do Azure Functions. A extensão Durable Functions funciona nas duas versões 1.xe 2.x do tempo de execução do Azure Functions. No entanto, os modelos só estão disponíveis ao segmentar a versão 2.x do tempo de execução.

## <a name="create-an-orchestrator-function"></a>Crie uma função de orquestrador

1. Expanda seu aplicativo de funções e clique no botão **+** ao lado de **Functions**. Se essa for a primeira função em seu aplicativo de funções, selecione **No portal** e depois **Continuar**. Caso contrário, vá para a etapa três.

   ![Página de início rápido de funções no portal do Azure](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Escolha **Mais modelos** e, em seguida, **Concluir e exibir modelos**.

    ![Início Rápido do Functions, escolher mais modelos](./media/durable-functions-create-portal/add-first-function.png)

1. No campo de pesquisa, digite `durable` e escolha o modelo **Durable Functions HTTP starter**.

1. Quando solicitado, selecione **Instalar** para instalar a extensão Azure DurableTask em quaisquer dependências do aplicativo de função. Você só precisa instalar a extensão uma vez para obter um aplicativo de função. Após a instalação ser bem-sucedida, selecione **Continuar**.

    ![Instalar extensões de associação](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Depois de concluir a instalação, nomeie a nova função `HttpStart` e escolha **Criar**. A função criada é usada para iniciar a orquestração.

1. Criar outra função no aplicativo de funções, desta vez usando o modelo **Durable Functions**. Nomeie sua nova função de orquestração `HelloSequence`.

1. Crie uma terceira função denominada `Hello` usando o modelo **Durable Functions Activity**.

## <a name="test-the-durable-function-orchestration"></a>Teste a orquestração de função durável

1. Volte para a função **HttpStart**, escolha **</> Obtenha o URL da função** e **Copie** o URL. Use essa URL para iniciar o **HelloSequence** função.

1. Use uma ferramenta HTTP, como Postman ou cURL, para enviar uma solicitação POST ao URL que você copiou. O exemplo a seguir é um comando cURL que envia uma solicitação POST para a função durável:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    Neste exemplo, `{your-function-app-name}` é o domínio que é o nome do seu aplicativo de função. A mensagem de resposta contém um conjunto de nós de extremidade do URI que você pode usar para monitorar e gerenciar a execução, que se parece com o exemplo a seguir:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Chame o `statusQueryGetUri` endpoint URI e você verá o status atual da função durável, que pode ser semelhante a este exemplo:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Continue chamando o endpoint `statusQueryGetUri` até que o status seja alterado para **Completed** e você veja uma resposta como o exemplo a seguir: 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

A primeira função durável está agora em execução no Azure.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre os padrões comuns de função durável](durable-functions-overview.md)