---
title: Como gerenciar conexões no Azure Functions
description: Saiba como evitar problemas de desempenho no Azure Functions usando clientes de conexão estática.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: tdykstra
ms.openlocfilehash: 6c0af8f6f7e1d4aea8880a7af311aaa21f474f7e
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968997"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Como gerenciar conexões no Azure Functions

Funções em um aplicativo de funções compartilham recursos e, entre os recursos compartilhados estão &mdash; conexões HTTP, conexões de banco de dados e conexões com os serviços do Azure como Armazenamento. Quando muitas funções estão sendo executadas simultaneamente, é possível ficar sem conexões disponíveis. Este artigo explica como codificar as funções para evitar o uso de mais conexões do que realmente precisam.

## <a name="connections-limit"></a>Limite de conexões

O número de conexões disponíveis é limitado em parte porque um aplicativo de funções executa na [área restrita do Serviço de Aplicativo do Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Uma das restrições que a área restrita impõe ao código é um [limite no número de conexões, atualmente 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). Ao alcançar esse limite, o tempo de execução das funções criará um log com a seguinte mensagem: `Host thresholds exceeded: Connections`.

As chances de exceder o limite aumentam quando [controlador de escala adiciona instâncias de aplicativo de funções](functions-scale.md#how-the-consumption-plan-works). Cada instância de aplicativo de funções pode invocar funções muitas vezes de uma só vez e todas essas funções estão usando conexões que contam para o limite de 300.

## <a name="use-static-clients"></a>Usar clientes estáticos

Para evitar manter mais conexões que o necessário, reutilize as instâncias do cliente em vez de criar novas com cada invocação de função. Clientes .NET como os clientes `HttpClient`, `DocumentClient` e Armazenamento do Microsoft Azure poderão gerenciar conexões se você usar um cliente único e estático.

A seguir são apresentadas algumas diretrizes a serem seguidas ao usar um cliente específico do serviço em um aplicativo do Azure Functions:

- **não** crie um novo cliente com cada invocação de função.
- **CRIE** um cliente único e estático que possa ser usado por todas as invocações de funções.
- **CONSIDERE** criar um cliente único e estático em uma classe do auxiliar compartilhada, se diferentes funções usarem o mesmo serviço.

## <a name="httpclient-code-example"></a>Exemplo de código HttpClient

A seguir, está um exemplo de código de função que cria um `HttpClient` estático:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

Uma pergunta comum sobre o .NET `HttpClient` é "Devo estar descartando o cliente?" Em geral, você descarta objetos que implementam `IDisposable` ao terminar de usá-los. Mas você não descarta um cliente estático porque não termina de usá-lo quando a função é encerrada. Você quer que o cliente estático permaneça durante a duração do aplicativo.

## <a name="documentclient-code-example"></a>Exemplo de código do DocumentClient

`DocumentClient` conecta uma instância do Cosmos DB. A documentação do Cosmos DB recomenda [utilizar um cliente singleton do Azure Cosmos DB para o tempo de vida do aplicativo](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). O exemplo a seguir mostra um padrão para fazer isso em uma função.

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client; 

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o motivo pelo qual os clientes estáticos são recomendados, consulte [Antipadrão de instanciação inadequada](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Para obter mais dicas de desempenho do Azure Functions, consulte [Melhore o desempenho e a confiabilidade do Azure Functions](functions-best-practices.md).