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
ms.date: 07/13/2018
ms.author: tdykstra
ms.openlocfilehash: 9e5c56dc3679e9ffbd67d906ca7d971439319ee5
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125369"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Como gerenciar conexões no Azure Functions

Funções em um aplicativo de funções compartilham recursos e, entre os recursos compartilhados estão &mdash; conexões HTTP, conexões de banco de dados e conexões com os serviços do Azure como Armazenamento. Quando muitas funções estão em execução simultaneamente, é possível ficar sem conexões disponíveis. Este artigo explica como codificar as funções para evitar o uso de mais conexões do que realmente precisam.

## <a name="connections-limit"></a>Limite de conexões

O número de conexões disponíveis é limitado em parte porque um aplicativo de funções executa na [área restrita do Serviço de Aplicativo do Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Uma das restrições que a área restrita impõe ao código é um [limite no número de conexões, atualmente 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). Ao alcançar esse limite, o tempo de execução das funções criará um log com a seguinte mensagem: `Host thresholds exceeded: Connections`.

A probabilidade de exceder o limite aumenta quando o [controlador de escala adiciona instâncias de aplicativo de funções](functions-scale.md#how-the-consumption-plan-works) para lidar com mais solicitações. Cada instância de aplicativo de funções pode executar várias funções de uma vez, todas usando conexões que contam para o limite de 300.

## <a name="use-static-clients"></a>Usar clientes estáticos

Para evitar manter mais conexões que o necessário, reutilize as instâncias do cliente em vez de criar novas com cada invocação de função. Clientes .NET como o [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), o [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) e os clientes de Armazenamento do Microsoft Azure poderão gerenciar conexões, se você usar um único cliente estático.

A seguir são apresentadas algumas diretrizes a serem seguidas ao usar um cliente específico do serviço em um aplicativo do Azure Functions:

- **não** crie um novo cliente com cada invocação de função.
- **CRIE** um cliente único e estático que possa ser usado por todas as invocações de funções.
- **CONSIDERE** criar um cliente único e estático em uma classe do auxiliar compartilhada, se diferentes funções usarem o mesmo serviço.

## <a name="httpclient-code-example"></a>Exemplo de código HttpClient

Aqui está um exemplo de código de função que cria um [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx):

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

Uma pergunta comum sobre o .NET [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) é "Eu devo estar descartando o cliente?" Em geral, você descarta objetos que implementam `IDisposable` ao terminar de usá-los. Mas você não descarta um cliente estático porque não termina de usá-lo quando a função é encerrada. Você quer que o cliente estático permaneça durante a duração do aplicativo.

## <a name="documentclient-code-example"></a>Exemplo de código do DocumentClient

O [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) conecta uma instância do Azure Cosmos DB. A documentação do Azure Cosmos DB recomenda que você [use um cliente singleton do Azure Cosmos DB pelo tempo de vida do aplicativo](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). O exemplo a seguir mostra um padrão para fazer isso em uma função:

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

## <a name="sqlclient-connections"></a>Conexões SqlClient

O código de função pode usar o Provedor de Dados .NET Framework para SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) para fazer conexões com um Banco de Dados SQL relacional. Esse também é o provedor subjacente de estruturas de dados que dependem do ADO.NET, como o Entity Framework. Ao contrário das conexões [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), o ADO.NET implementa o pool de conexões por padrão. No entanto, como você ainda pode ficar sem conexões, é necessário otimizar as conexões com o banco de dados. Para obter mais informações, consulte [Pool de Conexões do SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Algumas estruturas de dados, como [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx), normalmente obtêm cadeias de conexão da seção **ConnectionStrings** de um arquivo de configuração. Nesse caso, é necessário adicionar explicitamente as cadeias de conexão de banco de dados SQL à coleção **Cadeias de conexão** das configurações de aplicativo de funções e no [local.settings.json file](functions-run-local.md#local-settings-file) no projeto local. Se estiver criando um [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) no código de função, você deverá armazenar o valor da cadeia de conexão em **Configurações de aplicativos** com as outras conexões.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o motivo pelo qual os clientes estáticos são recomendados, consulte [Antipadrão de instanciação inadequada](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Para obter mais dicas de desempenho do Azure Functions, consulte [Melhore o desempenho e a confiabilidade do Azure Functions](functions-best-practices.md).