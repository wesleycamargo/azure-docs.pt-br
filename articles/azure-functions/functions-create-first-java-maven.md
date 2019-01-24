---
title: Criar sua primeira função no Azure com Java e Maven | Microsoft Docs
description: Criar e publicar uma função simples disparada por HTTP no Azure com Java e Maven.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, funções, processamento de eventos, computação, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: java
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: f0dd3b276adb815723673042060b2ad5d54a1ac7
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382641"
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>Criar sua primeira função com Java e Maven (versão prévia)

> [!NOTE] 
> O Java para Azure Functions está atualmente na versão prévia.

Este guia de início rápido fornece orientação para criar um projeto de função [sem servidor](https://azure.microsoft.com/solutions/serverless/) com o Maven, testá-lo localmente e implantá-lo no Azure. Quando terminar, seu código de função Java estará em execução na nuvem e poderá ser disparado a partir de uma solicitação HTTP.

![Acessar uma função Olá, Mundo na linha de comando com cURL](media/functions-create-java-maven/hello-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>pré-requisitos
Para desenvolver aplicativos de funções com Java, você deve ter o seguinte instalado:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), versão 8.
-  [Apache Maven](https://maven.apache.org), versão 3.0 ou posterior.
-  [CLI do Azure](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> A variável de ambiente JAVA_HOME deve ser definida como o local de instalação do JDK para concluir este guia de início rápido.

## <a name="install-the-azure-functions-core-tools"></a>Instalação das ferramentas básicas do Azure Functions

As [Ferramentas Básicas do Azure Functions 2.0](https://www.npmjs.com/package/azure-functions-core-tools) fornecem um ambiente de desenvolvimento local para gravar, executar e depurar funções do Azure. 

Para instalá-las, visite a seção de [Instalação](https://github.com/azure/azure-functions-core-tools#installing) do projeto de Ferramentas principais do Azure Functions para encontrar as instruções específicas para o seu sistema operacional.

Também é possível fazer a instalação manualmente com [npm](https://www.npmjs.com/), incluído com o [Node.js](https://nodejs.org/), depois de instalar os seguintes requisitos:

-  [.NET core](https://www.microsoft.com/net/core), a versão mais recente.
-  [Node.js](https://nodejs.org/download/), versão 8.6 ou superior.

Para continuar com uma instalação baseada em npm, execute:

```
npm install -g azure-functions-core-tools
```

> [!NOTE]
> Se você tiver problemas para instalar as Ferramentas Básicas do Azure Functions 2.0, consulte [Tempo de execução da versão 2.x](/azure/azure-functions/functions-run-local#version-2x-runtime).

## <a name="generate-a-new-functions-project"></a>Gerar um novo projeto do Functions

Em uma pasta vazia, execute o seguinte comando para gerar o projeto do Functions a partir de um [arquétipo Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

### <a name="linuxmacos"></a>Linux/MacOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

### <a name="windows-cmd"></a>Windows (CMD)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

O Maven solicitará os valores necessários para concluir a geração do projeto. Para os valores de _groupId_, _artifactId_ e _versão_, consulte a referência [Convenções de nomenclatura Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). O valor de _appName_ deve ser exclusivo no Azure, portanto, o Maven gera um nome de aplicativo com base no _artifactId_ em inserido anteriormente como padrão. O valor de _packageName_ determina o pacote de Java para o código de função gerado.

Os identificadores `com.fabrikam.functions` e `fabrikam-functions` abaixo são usados como um exemplo e tornam as etapas posteriores deste início rápido mais fáceis de ler. Forneça seus próprios valores para o Maven nesta etapa.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

O Maven cria os arquivos de projeto em uma nova pasta com um nome de _artifactId_ neste exemplo `fabrikam-functions`. O código pronto para executar gerado no projeto é uma simples função [disparada por HTTP](/azure/azure-functions/functions-bindings-http-webhook) que reflete o corpo da solicitação:

```java
public class Function {
    /**
     * This function listens at endpoint "/api/hello". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/hello
     * 2. curl {your host}/api/hello?name=HTTP%20Query
     */
    @FunctionName("hello")
    public HttpResponseMessage<String> hello(
            @HttpTrigger(name = "req", methods = {"get", "post"}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponse(400, "Please pass a name on the query string or in the request body");
        } else {
            return request.createResponse(200, "Hello, " + name);
        }
    }
}

```

## <a name="run-the-function-locally"></a>Executar a função localmente

Altere o diretório para a pasta do projeto recém-criado e compile e execute a função com o Maven:

```
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Caso esteja presenciando esta exceção: `javax.xml.bind.JAXBException`com o Java 9, consulte a solução alternativa no [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Esse resultado é exibido quando a função está sendo executada localmente no seu sistema e está pronta para responder às solicitações HTTP:

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

Dispare a função na linha de comando usando cURL em uma nova janela de terminal:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/hello
```

```Output
Hello LocalFunction!
```

Use `Ctrl-C` no terminal para interromper o código da função.

## <a name="deploy-the-function-to-azure"></a>Implantar a função no Azure

O processo de implantação no Azure Functions usa credenciais de conta da CLI do Azure. [Faça logon com a CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar.

```azurecli
az login
```

Implante seu código em um novo aplicativo de funções usando o destino Maven `azure-functions:deploy`.

```
mvn azure-functions:deploy
```

Quando a implantação for concluída, você verá a URL que pode usar para acessar seu aplicativo de funções do Azure:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

Teste o aplicativo de funções em execução no Azure usando `cURL`. Você precisará alterar a URL do exemplo abaixo para corresponder à URL implantada no seu próprio aplicativo de funções da etapa anterior.

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Fazer alterações e implantar novamente

Edite o arquivo de origem `src/main.../Function.java` no projeto gerado para alterar o texto retornado pelo seu aplicativo de Funções. Altere esta linha:

```java
return request.createResponse(200, "Hello, " + name);
```

Para o seguinte:

```java
return request.createResponse(200, "Hi, " + name);
```

Salve as alterações e reimplante executando `azure-functions:deploy` do terminal como antes. O aplicativo de funções será atualizado e esta solicitação:

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

Terá a saída atualizada:

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>Próximas etapas

Você criou um aplicativo de funções Java com um gatilho HTTP simples e o implantou no Azure Functions.

- Examine o [Guia do desenvolvedor de funções Java](functions-reference-java.md) para saber mais sobre o desenvolvimento de funções Java.
- Adicione outras funções com gatilhos diferentes ao seu projeto usando o destino Maven `azure-functions:add`.
- Escreva e depure funções localmente com [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) e [Eclipse](functions-create-maven-eclipse.md). 
- Depure funções implantadas no Azure com o Visual Studio Code. Veja a documentação de [aplicativos Java sem servidor](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) do Visual Studio Code para obter instruções.
