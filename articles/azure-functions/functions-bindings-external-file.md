---
title: "Associações de arquivo externo do Azure Functions (experimental)"
description: "Usando associações de arquivo externo no Azure Functions"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: 4e9c2c336df465d7488de84bd2a02cc5d9e42f30
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Associações de arquivo externo do Azure Functions (experimental)
Este artigo mostra como manipular arquivos de diferentes provedores de SaaS (por exemplo, Dropbox ou Google Drive) no Azure Functions. O Azure Functions dá suporte a associações de gatilho, de entrada e de saída para arquivos externos. Essas associações criam conexões de API com provedores SaaS ou usa conexões de API existentes a partir do grupo de recursos do aplicativo de funções.

> [!IMPORTANT]
> As associações de arquivo externo são experimentais e podem nunca alcançar o status de GA (Geralmente disponível). Elas são incluídas somente no Azure Functions 1.x e não há plano de adicioná-las ao Azure Functions 2.x. Para os cenários que exigem acesso aos dados de provedores de SaaS, considere o uso de [aplicativos lógicos que chamem funções](functions-twitter-email.md). Consulte o [Conector do sistema de arquivos de aplicativos lógicos](../logic-apps/logic-apps-using-file-connector.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>Conexões de arquivo disponíveis

|Conector|Gatilho|Entrada|Saída|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive for Business](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google Drive](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Conexões de arquivo externo também podem ser usadas nos [Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="trigger"></a>Gatilho

O gatilho de arquivo externo permite monitorar uma pasta remota e executar o código da função quando são detectadas alterações.

## <a name="trigger---example"></a>Gatilho - exemplo

Consulte o exemplo específico a um idioma:

* [Script C#](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>Gatilho - exemplo de script C#

O exemplo a seguir mostra uma associação de gatilho de arquivo externo em um arquivo *function.json* e uma [função C# script](functions-reference-csharp.md) que usa a associação. A função registra o conteúdo de cada arquivo adicionado à pasta monitorada.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Aqui está o código de script do C#:

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>Gatilho - exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho de arquivo externo em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função registra o conteúdo de cada arquivo adicionado à pasta monitorada.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>Gatilho – configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json*.

|Propriedade function.json | DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** | Deve ser definido como `apiHubFileTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | O nome da variável que representa o item de evento no código de função. | 
|**conexão**| Identifica a configuração de aplicativo que armazena a cadeia de conexão. A configuração do aplicativo é criada automaticamente quando se adiciona uma conexão à interface de usuário integrada no Portal do Azure.|
|**path** | A pasta a ser monitorada e, opcionalmente, um padrão de nome.|

### <a name="name-patterns"></a>Padrões de nome

É possível especificar um padrão de nome de arquivo na propriedade `path`. A pasta referenciada deve existir no provedor de SaaS.

Exemplos:

```json
"path": "input/original-{name}",
```

Esse caminho deve encontrar um arquivo chamado *original-File1.txt* na *entrada* pasta e o valor da variável `name` no código de função seria `File1.txt`.

Outro exemplo:

```json
"path": "input/{filename}.{fileextension}",
```

Esse caminho também poderia ser um blob chamado *original-File1.txt* e o valor das variáveis `filename` e `fileextension` no código de função seria *original-File1* e *txt*.

Você pode restringir o tipo de arquivo dos arquivos usando um valor fixo para a extensão de arquivo. Por exemplo: 

```json
"path": "samples/{name}.png",
```

Nesse caso, apenas arquivos *.png* na pasta *amostras* disparam a função.

As chaves são caracteres especiais nos padrões de nome. Para especificar nomes de arquivo com chaves, duplique as chaves.
Por exemplo: 

```json
"path": "images/{{20140101}}-{name}",
```

Esse caminho encontraria um arquivo denominado *{20140101}-soundfile.mp3* na pasta *imagens* e o valor da variável `name` no código da função seria *soundfile.mp3*.

## <a name="trigger---usage"></a>Gatilho - uso

Em funções do C#, você associa aos dados do arquivo de entrada usando um parâmetro nomeado na assinatura da função, como `<T> <name>`.
Em que `T` é o tipo de dados no qual você deseja desserializar os dados e `paramName` é o nome especificado no [gatilho JSON](#trigger). Em funções do Node.js, você acessa os dados do arquivo de entrada usando `context.bindings.<name>`.

O arquivo pode ser desserializado em qualquer um destes tipos:

* Qualquer [Objeto](https://msdn.microsoft.com/library/system.object.aspx) – útil para dados de arquivo serializado para JSON.
  Se você declarar um tipo de entrada personalizado (por exemplo, `FooType`), o Azure Functions tenta desserializar os dados JSON para o tipo especificado.
* Cadeia de caracteres – útil para dados de arquivo de texto.

Em funções do C#, você também pode associar a qualquer um dos seguintes tipos e o tempo de execução do Functions tentará desserializar os dados da tabela usando esse tipo:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

<!--- ## Trigger - file receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->

## <a name="trigger---poison-files"></a>Gatilho - arquivos suspeitos

Quando a função de gatilho de um arquivo externo falha, o Azure Functions repete essa função até 5 vezes por padrão (incluindo a primeira tentativa) para determinado arquivo.
Se todas as 5 tentativas falharem, o Functions adicionará uma mensagem para uma fila de armazenamento denominada *webjobs-apihubtrigger-poison*. A mensagem da fila para arquivos suspeitos é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *&lt;nome do aplicativo de funções>*.Functions.*&lt;nome da função>*)
* FileType
* FolderName
* FileName
* ETag (um identificador de versão de arquivo, por exemplo: "0x8D1DC6E70A277EF")

## <a name="input"></a>Entrada

A associação de entrada de arquivo externo do Azure permite que você use um arquivo de uma pasta externa em sua função.

## <a name="input---example"></a>Entrada - exemplo

Consulte o exemplo específico a um idioma:

* [Script C#](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Entrada - exemplo de script C#

O exemplo a seguir mostra associações de saída e de entrada de arquivo externo em um arquivo *function.json* e uma [função de script C#](functions-reference-csharp.md) que usa a associação. A função copia um arquivo de entrada para um arquivo de saída.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Aqui está o código de script do C#:

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>Entrada - exemplo de JavaScript

O exemplo a seguir mostra associações de saída e de entrada de arquivo externo em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função copia um arquivo de entrada para um arquivo de saída.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>Entrada - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json*.

|Propriedade function.json | DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** | Deve ser definido como `apiHubFile`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | O nome da variável que representa o item de evento no código de função. | 
|**conexão**| Identifica a configuração de aplicativo que armazena a cadeia de conexão. A configuração do aplicativo é criada automaticamente quando se adiciona uma conexão à interface de usuário integrada no Portal do Azure.|
|**path** | Deve conter o nome da pasta e o nome do arquivo. Por exemplo, se você tiver um [gatilho de fila](functions-bindings-storage-queue.md) em sua função, poderá usar `"path": "samples-workitems/{queueTrigger}"` para apontar para um arquivo na pasta `samples-workitems` com um nome que corresponda ao nome do arquivo especificado na mensagem de gatilho.   

## <a name="input---usage"></a>Entrada - uso

Em funções do C#, você associa aos dados do arquivo de entrada usando um parâmetro nomeado na assinatura da função, como `<T> <name>`. `T` é o tipo de dados no qual você deseja desserializar os dados e `name` é o nome especificado na associação de entrada. Em funções do Node.js, você acessa os dados do arquivo de entrada usando `context.bindings.<name>`.

O arquivo pode ser desserializado em qualquer um destes tipos:

* Qualquer [Objeto](https://msdn.microsoft.com/library/system.object.aspx) – útil para dados de arquivo serializado para JSON.
  Se você declarar um tipo de entrada personalizado (por exemplo, `InputType`), o Azure Functions tenta desserializar os dados JSON para o tipo especificado.
* Cadeia de caracteres – útil para dados de arquivo de texto.

Em funções do C#, você também pode associar a qualquer um dos seguintes tipos e o tempo de execução do Functions tentará desserializar os dados da tabela usando esse tipo:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>Saída

A associação de saída de arquivo externo do Azure permite que você grave arquivos em uma pasta externa de sua função.

## <a name="output---example"></a>Saída - exemplo

Consulte o [exemplo de associação de entrada](#input---example).

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json*.

|Propriedade function.json | DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** | Deve ser definido como `apiHubFile`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | Deve ser definido como `out`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | O nome da variável que representa o item de evento no código de função. | 
|**conexão**| Identifica a configuração de aplicativo que armazena a cadeia de conexão. A configuração do aplicativo é criada automaticamente quando se adiciona uma conexão à interface de usuário integrada no Portal do Azure.|
|**path** | Deve conter o nome da pasta e o nome do arquivo. Por exemplo, se você tiver um [gatilho de fila](functions-bindings-storage-queue.md) em sua função, poderá usar `"path": "samples-workitems/{queueTrigger}"` para apontar para um arquivo na pasta `samples-workitems` com um nome que corresponda ao nome do arquivo especificado na mensagem de gatilho.   

## <a name="output---usage"></a>Saída - uso

Em funções C#, você faz a associação ao arquivo de saída usando o parâmetro `out` nomeado na assinatura da função, como `out <T> <name>`, em que `T` é o tipo de dados no qual você quer serializar os dados e `name` é o nome especificado na associação de saída. Nas funções do Node.js, você acessa o arquivo de saída usando `context.bindings.<name>`.

Você pode gravar no arquivo de saída usando qualquer um dos seguintes tipos:

* Qualquer [Objeto](https://msdn.microsoft.com/library/system.object.aspx) - útil para serialização JSON.
  Se você declarar um tipo de saída personalizada (por exemplo, `out OutputType paramName`), o Azure Functions tenta serializar o objeto em JSON. Se o parâmetro de saída for nulo quando a função for encerrada, o tempo de execução do Functions criará um arquivo como um objeto nulo.
* Cadeia de caracteres – (`out string paramName`) útil para dados de arquivo de texto. O tempo de execução do Functions só criará um arquivo se o parâmetro de cadeia de caracteres não for nulo quando a função for encerrada.

Em funções do C#, também é possível executar a saída para qualquer um dos seguintes tipos:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
