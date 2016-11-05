---
title: Associações do Banco de Dados de Documentos do Azure Functions | Microsoft Docs
description: Entenda como usar associações do Banco de Dados de Documentos do Azure no Azure Functions.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: ''
tags: ''
keywords: azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/22/2016
ms.author: chrande; glenga

---
# Associações do Banco de Dados de Documentos do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e codificar associações do Banco de Dados de Documentos do Azure no Azure Functions.

[!INCLUDE [introdução](../../includes/functions-bindings-intro.md)]

## <a id="docdbinput"></a> Associação de entrada do Banco de Dados de Documentos do Azure
As associações de entrada podem carregar um documento de uma coleção do Banco de Dados de Documentos e passá-lo diretamente para sua associação. A identificação do documento pode ser determinada com base no gatilho que invocou a função. Em uma função do C#, quaisquer alterações feitas no registro serão enviadas automaticamente para a coleção quando a função sair com êxito.

#### function.json para associação de entrada do Banco de Dados de Documentos
O arquivo *function.json* fornece as seguintes propriedades:

* `name`: nome da variável usada no código de função para o documento.
* `type`: deve ser definido como "documentdb".
* `databaseName`: o banco de dados que contém o documento.
* `collectionName`: a coleção que contém o documento.
* `id`: a ID do documento a ser recuperado. Essa propriedade oferece suporte a associações semelhantes a "{queueTrigger}", que usará o valor da cadeia de caracteres da mensagem de fila como a identificação do documento.
* `connection`: essa cadeia de caracteres deve ser uma Configuração de Aplicativo definida como o ponto de extremidade para sua conta do Banco de Dados de Documentos. Se você escolher a conta na guia Integrar, a configuração do novo Aplicativo será criada para você com um nome que terá este formato: suaConta\_DOCUMENTDB. Se você precisar criar manualmente a configuração do aplicativo, a cadeia de conexão real deverá ter o seguinte formato: AccountEndpoint = <ponto de extremidade da sua conta>; AccountKey = <chave de acesso primária>;.
* `direction: deve ser definida como *"in"*.

*function.json* de exemplo:

    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "id" : "{queueTrigger}",
          "connection": "MyAccount_DOCUMENTDB",     
          "direction": "in"
        }
      ],
      "disabled": false
    }

#### Exemplo de código de entrada do Banco de Dados de Documentos do Azure para um gatilho de fila do C
Usando o function.json de exemplo acima, a associação de entrada do Banco de Dados de Documentos irá recuperar o documento com a identificação que corresponda à cadeia de mensagem de fila e a passará para o parâmetro 'document'. Se esse documento não for encontrado, o parâmetro 'document' será nulo. O documento será então atualizado com o novo valor de texto quando a função sair.

    public static void Run(string myQueueItem, dynamic document)
    {   
        document.text = "This has changed.";
    }

#### Exemplo de código de entrada do Azure DocumentDB para um gatilho de fila do F
Usando o function.json de exemplo acima, a associação de entrada do Banco de Dados de Documentos irá recuperar o documento com a identificação que corresponda à cadeia de mensagem de fila e a passará para o parâmetro 'document'. Se esse documento não for encontrado, o parâmetro 'document' será nulo. O documento será então atualizado com o novo valor de texto quando a função sair.

    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- "This has changed."

Você precisará de um arquivo `project.json` que usa o NuGet para especificar os pacotes `FSharp.Interop.Dynamic` e `Dynamitey` como dependências do pacote, da seguinte forma:

    {
      "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
          }
        }
      }
    }

Isso usará o NuGet para buscar suas dependências e fará referência a elas em seu script.

#### Exemplo de código de entrada do Banco de Dados de Documentos do Azure para um gatilho de fila do Node.js
Usando o function.json de exemplo acima, a associação de entrada do Banco de Dados de Documentos vai recuperar o documento com a identificação que corresponda à cadeia de mensagem de fila e a passará para a propriedade de associação `documentIn`. Em funções do Node.js, os documentos atualizados não são enviados de volta à coleção. No entanto, você pode passar a associação de entrada diretamente para uma associação de saída do Banco de Dados de Documentos chamada `documentOut` para dar suporte a atualizações. Este exemplo de código atualiza a propriedade de texto do documento de entrada e o define como o documento de saída.

    module.exports = function (context, input) {   
        context.bindings.documentOut = context.bindings.documentIn;
        context.bindings.documentOut.text = "This was updated!";
        context.done();
    };

## <a id="docdboutput"></a> Associações de saída do Banco de Dados de Documentos do Azure
Suas funções podem gravar documentos JSON em um Banco de Dados de Documentos do Azure usando a associação de saída **Documento do Banco de Dados de Documentos do Azure**. Para saber mais sobre o Banco de Dados de Documentos do Azure, examine a [Introdução ao Banco de Dados de Documentos](../documentdb/documentdb-introduction.md) e o [Tutorial de Introdução](../documentdb/documentdb-get-started.md).

#### function.json para associação de saída do Banco de Dados de Documentos
O arquivo function.json fornece as seguintes propriedades:

* `name`: nome da variável usada no código de função para o novo documento.
* `type`: deve ser definido como *"documentdb"*.
* `databaseName`: o banco de dados que contém a coleção na qual o novo documento será criado.
* `collectionName`: a coleção na qual o novo documento será criado.
* `createIfNotExists`: é um valor booliano para indicar se a coleção será criada se ela não existir. O padrão é *false*. O motivo para isso é que as novas coleções são criadas com a taxa de transferência reservada, o que tem implicações de preço. Para obter mais detalhes, visite a [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).
* `connection`: essa cadeia de caracteres deve ser uma **Configuração de Aplicativo** definida como o ponto de extremidade para sua conta do Banco de Dados de Documentos. Se você escolher sua conta na guia **Integrar**, a configuração do novo Aplicativo será criada para você com um nome que terá este formato: `yourAccount_DOCUMENTDB`. Se você precisar criar manualmente a configuração do Aplicativo, a cadeia de conexão real deverá ter este formato: `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;`.
* `direction`: deve ser definido como *out*.

function.json de exemplo:

    {
      "bindings": [
        {
          "name": "document",
          "type": "documentdb",
          "databaseName": "MyDatabase",
          "collectionName": "MyCollection",
          "createIfNotExists": false,
          "connection": "MyAccount_DOCUMENTDB",
          "direction": "out"
        }
      ],
      "disabled": false
    }


#### Exemplo de código de saída do Banco de Dados de Documentos do Azure para um gatilho de fila do Node.js
    module.exports = function (context, input) {

        context.bindings.document = {
            text : "I'm running in a Node function! Data: '" + input + "'"
        }   

        context.done();
    };

O documento de saída:

    {
      "text": "I'm running in a Node function! Data: 'example queue data'",
      "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
    }


#### Exemplo de código de saída do Azure DocumentDB para um gatilho de fila do F
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, document: obj) =
        document?text <- (sprintf "I'm running in an F# function! %s" myQueueItem)

#### Exemplo de código de saída do Banco de Dados de Documentos do Azure para um gatilho de fila do C
    using System;

    public static void Run(string myQueueItem, out object document, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        document = new {
            text = $"I'm running in a C# function! {myQueueItem}"
        };
    }


#### Exemplo de código de saída do Banco de Dados de Documentos que configura um nome do arquivo
Se você deseja definir o nome do documento na função, basta definir o valor `id`. Por exemplo, se o conteúdo JSON de um funcionário tiver sido removido para a fila semelhante à seguinte:

    {
      "name" : "John Henry",
      "employeeId" : "123456",
      "address" : "A town nearby"
    }

Você poderia usar o seguinte código C# em uma função de gatilho de fila:

    #r "Newtonsoft.Json"

    using System;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        dynamic employee = JObject.Parse(myQueueItem);

        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }

Ou o código equivalente em F#:

    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
        id: string
        name: string
        employeeId: string
        address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
        log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
        let employee = JObject.Parse(myQueueItem)
        employeeDocument <-
            { id = sprintf "%s-%s" employee?name employee?employeeId
              name = employee?name
              employeeId = employee?id
              address = employee?address }

Saída de exemplo:

    {
      "id": "John Henry-123456",
      "name": "John Henry",
      "employeeId": "123456",
      "address": "A town nearby"
    }

## Próximas etapas
[!INCLUDE [próximas etapas](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0921_2016-->