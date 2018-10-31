---
title: Referência do desenvolvedor de Java do Azure Functions | Microsoft Docs
description: Entenda como desenvolver funções usando Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: 423661b8a459abf0b3028da92d6fd3ec885bb2c9
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025015"
---
# <a name="azure-functions-java-developer-guide"></a>Guia do desenvolvedor de Java do Azure Functions

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>Modelo de programação 

Sua função do Azure deve ser um método de classe sem monitoração de estado que processa uma entrada e produz uma saída. Embora você possa escrever métodos de instância, sua função não deve depender de nenhum campo de instância da classe. Todos os métodos de função devem ter um modificador de acesso `public`.

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas para um projeto de script Java é semelhante à seguinte:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

Há um arquivo [host.json](functions-host-json.md) compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função possui seu próprio arquivo de código (.java) e arquivo de configuração de associação (function.json).

Você pode colocar mais de uma função em um projeto. Evite colocar suas funções no jars separados. O FunctionApp no diretório de destino é o que é implantado para seu aplicativo de funções no Azure.

## <a name="triggers-and-annotations"></a>Gatilhos e anotações

 As funções do Azure são chamadas por um disparador, como uma solicitação HTTP, um cronômetro ou uma atualização de dados. A função precisa processar esse gatilho e quaisquer outras entradas para produzir uma ou mais saídas.

Use as anotações Java incluídas no pacote [com.microsoft.azure.functions.annotation. *](/java/api/com.microsoft.azure.functions.annotation) para ligar entradas e saídas a seus métodos. O código de amostra usando as anotações está disponível nos [documentos de referência Java](/java/api/com.microsoft.azure.functions.annotation) para cada anotação e na documentação de referência de vinculação do Azure Functions, como a dos [gatilhos HTTP](/azure/azure-functions/functions-bindings-http-webhook).

Gatilho de entradas e saídas também podem ser definidas na [Function. JSON](/azure/azure-functions/functions-reference#function-code) para sua função em vez de por meio de anotações. Usar `function.json` em vez de anotações dessa maneira não é recomendado.

> [!IMPORTANT] 
> Você precisa configurar uma conta de Armazenamento do Azure em seu [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) para executar gatilhos de Tabela, Fila ou Azure Storage Blob localmente.

Exemplo de uso de anotações:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

A mesma função escrita sem anotações:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

com o `function.json` correspondente:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>Suporte e disponibilidade de tempo de execução do JDK 

Baixe e use o [Azul Zulu para Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) JDKs do [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/) para desenvolvimento local de aplicativos de funções Java. JDKs estão disponíveis para Windows, Linux e macOS e o [Suporte do Azure](https://support.microsoft.com/en-us/help/4026305/sql-contact-microsoft-azure-support) está disponível para os problemas encontrados durante o desenvolvimento com um [plano de suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="third-party-libraries"></a>Bibliotecas de terceiros 

O Azure Functions oferece suporte ao uso de bibliotecas de terceiros. Por padrão, todas as dependências especificadas no arquivo do `pom.xml`projeto serão agrupadas automaticamente durante a meta`mvn package`. Para bibliotecas não especificadas como dependências no `pom.xml`arquivo, coloque-as em um diretório`lib` no diretório-raiz da função. Dependências colocadas o `lib` diretório será adicionado ao carregador de classe do sistema em tempo de execução.

A `com.microsoft.azure.functions:azure-functions-java-library` dependência é fornecida no caminho de classe por padrão e não precisa ser incluído no `lib` diretório.

## <a name="data-type-support"></a>Suporte ao tipo de dados

Você pode usar qualquer tipo de dados em Java para os dados de entrada e saída, incluindo tipos nativos; tipos Java personalizados e tipos especializados do Azure definidos no `azure-functions-java-library`pacote. O tempo de execução do Azure Functions tenta converter a entrada recebida no tipo solicitado por seu código.

### <a name="strings"></a>Cadeias de caracteres

Os valores passados para os métodos de função serão convertidos em cadeias de caracteres se o tipo de parâmetro de entrada correspondente da função for `String`. 

### <a name="plain-old-java-objects-pojos"></a>POJOs (objetos Java básicos)

Cadeias formatadas com JSON serão convertidas em tipos Java se a assinatura de entrada da função esperar esse tipo de Java. Essa conversão permite que você passe em JSON e trabalhe com tipos Java.

Tipos POJO usados como entradas de funções devem ter o mesmo modificador de acesso `public` que os métodos de função em que estão sendo usados. Você não precisa declarar campos de classe POJO `public`. Por exemplo, uma cadeia de caracteres JSON `{ "x": 3 }` pode ser convertida no tipo POJO a seguir:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Dados binários

Dados binários são representados como um `byte[]` em seu código de funções do Azure. Associe entradas ou saídas binárias a suas funções definindo o campo `dataType` em seu function.json como `binary`:

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

Em seguida, use-o em seu código de função:

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Valores de entrada vazios podem ser `null`como seu argumento de funções, mas uma maneira recomendada de lidar com possíveis valores vazios é usar`Optional<T>`.


## <a name="function-method-overloading"></a>Sobrecarga de método de função

Você pode sobrecarregar métodos de função com o mesmo nome, mas com tipos diferentes. Por exemplo, você pode ter ambos `String echo(String s)` e `String echo(MyType s)` em uma classe. O Azure Functions decide qual método invocar com base no tipo de entrada (para o tipo de MIME de entrada, de HTTP `text/plain` leva a `String` enquanto `application/json` representa `MyType`).

## <a name="inputs"></a>Entradas

As entradas são divididas em duas categorias no Azure Functions: uma é a entrada de gatilho e a outra é a entrada adicional. Embora elas sejam diferentes em `function.json`, seu uso é idêntico no código Java. Como exemplo, vejamos o snippet de código a seguir:

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String in,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") MyObject obj
    ) {
        return "Hello, " + in + " and " + obj.getKey() + ".";
    }

    public static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

Quando esta função é acionada, o pedido HTTP é passado para a função por `String in`. Uma entrada será recuperada do Armazenamento de Tabela do Azure com base na ID na URL da rota e disponibilizada como `obj` no corpo da função.

## <a name="outputs"></a>outputs

As saídas podem ser expressas em parâmetros de saída ou em valores retornados. Se houver apenas uma saída, é recomendável usar o valor retornado. Para várias saídas, você precisa usar parâmetros de saída.

O valor retornado é a forma mais simples de saída, basta retornar o valor de qualquer tipo e o tempo de execução do Azure Functions tentará realizar seu marshaling de volta para o tipo real (como uma resposta HTTP).  Você poderia aplicar quaisquer anotações de saída ao método da função (a propriedade name da anotação deve ser $ return) para definir a saída do valor de retorno.

Para produzir vários valores de saída, use o tipo `OutputBinding<T>` definido no pacote `azure-functions-java-library`. Se precisar fazer uma resposta HTTP e enviar por push uma mensagem para uma fila, você poderá escrever algo como:

Por exemplo, uma função de cópia de conteúdo blob pode ser definida como o código a seguir. A anotação `@StorageAccount` é usada aqui para evitar a duplicação da propriedade de conexão para `@BlobTrigger` e `@BlobOutput`.

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class MyClass {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Use `OutputBinding<byte[]`> fazer um binário de saída de valor (para parâmetros); para valores de retorno, basta usar `byte[]`.

## <a name="specialized-types"></a>Tipos especializados

Às vezes, uma função precisa ter controle detalhado sobre entradas e saídas. Tipos especializados no pacote `azure-functions-java-core` são fornecidos para você manipular informações de solicitação e adequar o status de retorno de um acionador HTTP:

| Tipo especializado      |       Destino        | Uso típico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Gatilho de HTTP     | Obter método, cabeçalhos ou consultas |
| `HttpResponseMessage<T>` | Associação de saída HTTP | Retornar status diferentes de 200   |

> [!NOTE] 
> Você também pode usar a anotação `@BindingName` para obter cabeçalhos e consultas HTTP. Por exemplo, `@BindingName("name") String query` itera os cabeçalhos e consultas de solicitação HTTP e passa esse valor para o método. Por exemplo, `query` será `"test"` se a URL de solicitação for `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Metadados

Os metadados vêm de fontes diferentes, como cabeçalhos HTTP, consultas HTTP e [metadados de gatilho](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Use a anotação `@BindingName` junto com o nome de metadados para obter o valor.

Por exemplo, o `queryValue` no snippet de código a seguir será `"test"` se a URL solicitada for `http://{example.host}/api/metadata?name=test`.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="execution-context"></a>Contexto de execução

Interagir com o ambiente de execução de funções do Azure por meio de `ExecutionContext` objeto definido no `azure-functions-java-library` pacote. Use o objeto `ExecutionContext` para usar informações sobre invocação e informações sobre tempo de execução de funções em seu código.

### <a name="custom-logging"></a>Registro em log personalizado

O acesso ao agente de tempo de execução das funções está disponível por meio do objeto `ExecutionContext`. Esse agente é vinculado ao Azure Monitor e permite sinalizar avisos e erros encontrados durante a execução da função.

O exemplo de código a seguir registra uma mensagem de aviso quando o corpo da solicitação recebida está vazio.

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Exibir logs e rastreamento

Você pode usar a CLI do Azure para transmitir o log de erros e de saída padrão do Java, bem como o log de outros aplicativos. Primeiro, configure seu aplicativo Function para gravar o log do aplicativo usando a CLI do Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Para transmitir a saída de log para seu aplicativo de Função usando a CLI do Azure, abra um novo prompt de comando, Bash ou sessão de Terminal e digite o seguinte comando:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
O [final de log do az webapp](/cli/azure/webapp/log) comando tem opções para filtrar a saída usando o `--provider` opção. 

Para baixar os arquivos de log como um único arquivo ZIP usando a CLI do Azure, abra um novo prompt de comando, Bash ou sessão de Terminal e digite o seguinte comando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Você deve ter habilitado o log do sistema de arquivos no Portal do Azure ou na CLI do Azure antes de executar este comando.

## <a name="environment-variables"></a>Variáveis de ambiente

Mantenha informações secretas, como chaves ou tokens, fora do seu código-fonte por motivos de segurança. Use chaves e tokens em seu código de função, lendo-os a partir de variáveis de ambiente.

Para definir variáveis de ambiente ao executar as Funções do Azure localmente, você pode optar por adicionar essas variáveis ao arquivo local.settings.json. Se não houver um no diretório raiz do seu projeto de função, você poderá criar um. Veja como o arquivo deve se parecer:

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Cada chave / valor de mapeamento no mapa `values` estará disponível no tempo de execução como uma variável de ambiente, acessível ao chamar `System.getenv("<keyname>")`, por exemplo, `System.getenv("AzureWebJobsStorage")`. A adição de mais pares de chaves/valores é uma prática aceita e recomendada.

> [!NOTE]
> Se essa abordagem for tomada, certifique-se de incluir o arquivo local.settings.json em seu arquivo ignore do repositório, para que ele não seja confirmado.

Com seu código agora dependendo dessas variáveis de ambiente, você pode entrar no portal do Azure para definir os mesmos pares de chave / valor nas configurações do aplicativo de função, para que o código funcione de maneira equivalente ao testar localmente e quando implantado no Azure.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o desenvolvimento de Java de função do Azure, consulte os seguintes recursos:

* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)
- Local de desenvolvimento e depuração com o [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), e [Eclipse](functions-create-maven-eclipse.md). 
* [Azure Functions de Java de Depuração Remota com Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plugin Maven para as Funções do Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) - simplifique a criação de funções por meio da meta `azure-functions:add` e prepare um diretório temporário para a [implantação do arquivo ZIP](deployment-zip-push.md).
