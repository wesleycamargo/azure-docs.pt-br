---
title: Criar sua primeira função no Azure com Java e Maven | Microsoft Docs
description: Criar e publicar uma função simples disparada por HTTP no Azure com Java e Maven.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: azure functions, funções, processamento de eventos, computação, arquitetura sem servidor
ms.service: functions
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/10/2018
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: aeb00bf55c578f61e5e1edbaab11c7773b9eab94
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2018
ms.locfileid: "42022648"
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>Criar sua primeira função com Java e Maven (versão prévia)

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

Este guia de início rápido fornece orientação para a criação de um projeto de função [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) com o Maven, testá-lo localmente e implantá-lo no Azure Functions. Quando terminar, você terá um aplicativo de funções disparado por HTTP em execução no Azure.

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

As Ferramentas Básicas do Azure Functions fornecem um ambiente de desenvolvimento local para gravar, executar e depurar funções do Azure no terminal ou prompt de comando. 

Instale a [versão 2 das Ferramentas Básicas](functions-run-local.md#v2) no computador local antes de continuar.

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

O valor de `appRegion` especifica em qual [região do Azure](https://azure.microsoft.com/global-infrastructure/regions/) você deseja executar o aplicativo Function implantado. Você pode obter uma lista de valores de nome de região por meio do comando `az account list-locations` na CLI do Azure. O valor `resourceGroup` especifica em qual grupo de recursos do Azure o aplicativo de funções será criado.

Os identificadores `com.fabrikam.functions` e `fabrikam-functions` abaixo são usados como um exemplo e tornam as etapas posteriores deste início rápido mais fáceis de ler. Forneça seus próprios valores para o Maven nesta etapa.

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Define value for property 'appRegion' westus : 
Define value for property 'resourceGroup' java-functions-group: 
Confirm properties configuration: Y
```

O Maven cria os arquivos de projeto em uma nova pasta com um nome de _artifactId_ neste exemplo `fabrikam-functions`. O código pronto para executar gerado no projeto é uma simples função [disparada por HTTP](/azure/azure-functions/functions-bindings-http-webhook) que reflete o corpo da solicitação após uma cadeia de caracteres "Olá, ".

```java
public class Function {
    @FunctionName("HttpTrigger-Java")
    public HttpResponseMessage HttpTriggerJava(
    @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST).body("Please pass a name on the query string or in the request body").build();
        } else {
            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        }
    }
}
```

## <a name="run-the-function-locally"></a>Executar a função localmente

Altere o diretório para a pasta do projeto recém-criado e compile e execute a função com o Maven:

```
cd fabrikam-functions
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Caso esteja presenciando esta exceção: `javax.xml.bind.JAXBException`com o Java 9, consulte a solução alternativa no [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Esse resultado é exibido quando a função está sendo executada localmente no seu sistema e está pronta para responder às solicitações HTTP:

```Output
Listening on http://0.0.0.0:7071/
Hit CTRL-C to exit...

Http Functions:

        HttpTrigger-Java: http://localhost:7071/api/HttpTrigger-Java
```

Dispare a função na linha de comando usando cURL em uma nova janela de terminal:

```
curl -w '\n' -d LocalFunctionTest http://localhost:7071/api/HttpTrigger-Java
```

```Output
Hello, LocalFunctionTest
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
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

```Output
Hello, AzureFunctionsTest
```

## <a name="next-steps"></a>Próximas etapas

Você criou um aplicativo de funções Java com um gatilho HTTP simples e o implantou no Azure Functions.

- Examine o [Guia do desenvolvedor de funções Java](functions-reference-java.md) para saber mais sobre o desenvolvimento de funções Java.
- Adicione outras funções com gatilhos diferentes ao seu projeto usando o destino Maven `azure-functions:add`.
- Depure funções localmente com o Visual Studio Code. Com o [pacote de extensão Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) instalado, e com o seu projeto do Functions aberto no Visual Studio Code, [anexe o depurador](https://code.visualstudio.com/Docs/editor/debugging#_launch-configurations) à porta 5005. Em seguida, defina um ponto de interrupção no editor e dispare sua função enquanto ela está em execução localmente: ![Depurar funções no Visual Studio Code](media/functions-create-java-maven/vscode-debug.png)
- Depure funções remotamente com o Visual Studio Code. Verifique a documentação de [Gravação de aplicativos Java sem servidor](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) para obter instruções.
