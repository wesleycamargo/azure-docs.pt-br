---
title: "Armazenar dados não estruturados usando o Azure Functions e o Cosmos DB"
description: "Armazenar dados não estruturados usando o Azure Functions e o Cosmos DB"
services: functions
documentationcenter: functions
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: "azure functions, functions, processamento de eventos, Cosmos DB, computação dinâmica, arquitetura sem servidor"
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: ms-hero
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/08/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 46022530c69b292878a4500c0e325bda878e6188
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017


---
# <a name="store-unstructured-data-using-azure-functions-and-cosmos-db"></a>Armazenar dados não estruturados usando o Azure Functions e o Cosmos DB

O Azure Cosmos DB é uma ótima maneira de armazenamento não estruturado e dados JSON. Combinado com o Azure Functions, o Cosmos DB torna o armazenamento de dados rápido e fácil com muito menos código do que o necessário para armazenar dados em um banco de dados relacional.

Este tutorial explica como usar o Portal do Azure para criar uma Função do Azure que armazena dados não estruturados em um documento do Cosmos DB. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-a-function"></a>Criar uma função

Criar um novo WebHook C# genérico chamado de `MyTaskList`.

1. Expanda a lista de funções existentes e clique o sinal + para criar uma nova função
1. Selecione GenericWebHook-CSharp e nomeie-o `MyTaskList`

![Adicionar novo Aplicativo de Funções WebHook C# genérico](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-new-functionapp.png)

## <a name="add-an-output-binding"></a>Adicionar uma associação de saída

Uma função do Azure pode ter um gatilho e qualquer número de associações de entrada ou saída. Para este exemplo, vamos usar um gatilho de Solicitação HTTP e o documento do Cosmos DB como a associação de saída.

1. Clique na guia *Integrar* da função para exibir ou modificar o gatilho e associações da função.
1. Escolha o link *Nova Saída* localizado na parte superior direita da página.

Observação: o disparador de Solicitação HTTP já está configurado. No entanto, você deve adicionar a associação de documento do Cosmos DB.

![Adicionar associação de saída do novo Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-add-new-output-binding.png)

1. Insira as informações necessárias para criar a associação. Use a tabela abaixo para determinar os valores.

![Configurar associação de saída do Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-integrate-tab-configure-cosmosdb-binding.png)

|  Campo | Valor  |
|---|---|
| Nome do parâmetro do documento | Nome que se refere ao objeto do Cosmos DB no código |
| Nome do banco de dados | Nome do banco de dados para salvar os documentos |
| Nome da coleção | Nome do agrupamento dos bancos de dados Cosmos DB |
| Você gostaria que o Cosmos DB e a coleção criada para você | Sim ou não |
| Conexão da conta do Cosmos DB | Cadeia de conexão apontando para o banco de dados Cosmos DB |

Você também deve configurar a conexão para o banco de dados do Cosmos DB.

1. Clique no link "Novo" ao lado do *Conexão do documento do Cosmos DB".
1. Preencha os campos e selecione as opções apropriadas necessárias para criar o documento do Cosmos DB.

![Configurar a conexão do Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-create-CosmosDB.png)

|  Campo | Valor  |
|---|---|
| ID | ID exclusiva para o banco de dados do Cosmos DB  |
| API NoSQL | Cosmos DB ou do MongoDB  |
| Assinatura | Assinatura do MSDN  |
| Grupo de recursos  | Crie um novo ou selecione um existente.  |
| Local  | WestEurope  |

1. Clique no botão *Ok* . Talvez seja necessário aguardar alguns minutos enquanto o Azure cria os recursos.
1. Clique no botão *Salvar* .

## <a name="update-the-function-code"></a>Atualizar o código de função

Substitua o código de modelo da função pelo seguinte:

Observe que o código para esse exemplo está apenas em C#.

```csharp
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, out object taskDocument, TraceWriter log)
{
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    string task = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "task", true) == 0)
        .Value;

    string duedate = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "duedate", true) == 0)
        .Value;

    taskDocument = new {
        name = name,
        duedate = duedate.ToString(),
        task = task
    };

    if (name != "" && task != "") {
        return req.CreateResponse(HttpStatusCode.OK);
    }
    else {
        return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}

```

Este exemplo de código lê as cadeias de caracteres de consulta de Solicitação HTTP e as atribui como membros de um objeto `taskDocument`. O objeto `taskDocument` salva automaticamente os dados no banco de dados Cosmos DB e até cria o banco de dados no primeiro uso.

## <a name="test-the-function-and-database"></a>Testar a função e o banco de dados

1. Na guia de função, clique no link *Teste* à direita do portal e insira as seguintes cadeias de consulta HTTP:

| Cadeia de Consulta | Valor |
|---|---|
| name | Chris P. Bacon |
| task | Fazer um BLT sandwich |
| duedate | 05/12/2017 |

1. Clique no link *Executar*.
1. Verifique se a função retornou um Código de resposta *HTTP 200 OK*.

![Configurar associação de saída do Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png)

Confirme que uma entrada foi feita no banco de dados Cosmos DB.

1. Localize o banco de dados no portal do Azure e selecione-o.
1. Selecione a opção *Data Explorer*.
1. Expanda os nós até acessar as entradas do documento.
1. Confirme a entrada do banco de dados. Haverá metadados adicionais no banco de dados juntamente com seus dados.

![Verifique a entrada do Cosmos DB](./media/functions-integrate-store-unstructured-data-cosmosdb/functions-verify-cosmosdb-output.png)

Se os dados estiverem no documento, isso significa que você criou com êxito uma função do Azure que armazena dados não estruturados em um banco de dados do Cosmos DB.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Confira estes tópicos para obter mais informações sobre o Azure Functions:

[!INCLUDE [Getting help note](../../includes/functions-get-help.md)]

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps.md)]
