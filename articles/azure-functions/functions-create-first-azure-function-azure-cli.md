---
title: Criar sua primeira função usando a CLI do Azure
description: Aprenda a criar sua primeira função do Azure para execução sem servidor usando a CLI do Azure e o Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 09/10/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: f65ff3e38e3768dccf04e9eaa4345ffcea4daa9e
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451113"
---
# <a name="create-your-first-function-from-the-command-line"></a>Criar sua primeira função na linha de comando

Este tópico de início rápido explica como criar sua primeira função na linha de comando ou no terminal. É possível usar a CLI do Azure para criar um aplicativo de funções, que é a infraestrutura [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) que hospeda sua função. O projeto de código de função é gerado de um modelo usando o [Azure Functions Core Tools](functions-run-local.md), que também é usado para implantar o projeto de aplicativo de funções no Azure.

Você pode seguir as etapas abaixo usando um computador Mac, Windows ou Linux.

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, você deve ter o seguinte:

+ Instale a [versão do Azure Core Tools 2.x](functions-run-local.md#v2).

+ Instale a [CLI do Azure]( /cli/azure/install-azure-cli). Este artigo requer a CLI do Azure versão 2.0 ou posterior. Execute `az --version` descobrir a versão que você tem. Você também pode usar o [Azure Cloud Shell](https://shell.azure.com/bash).

+ Uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Criar o projeto de aplicativo de funções local

Execute o comando a seguir na linha de comando para criar um projeto de aplicativo de funções na pasta `MyFunctionProj` do diretório local atual. Um repositório GitHub também é criado em `MyFunctionProj`.

```bash
func init MyFunctionProj
```

Quando solicitado, use as teclas de direção para selecionar um tempo de execução do trabalho entre as seguintes opções de linguagem:

+ `dotnet`: cria um projeto de biblioteca de classes do .NET (.csproj).
+ `node`: cria um projeto de JavaScript.

Quando o comando for executado, você verá algo parecido com a seguinte saída:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

## <a name="create-a-function"></a>Criar uma função

O comando a seguir vai para o novo projeto e cria uma função disparada por HTTP denominada `MyHtpTrigger`.

```bash
cd MyFunctionProj
func new --name MyHttpTrigger --template "HttpTrigger"
```

Quando o comando for executado, você verá algo parecido com a seguinte saída, que é uma função de JavaScript:

```output
Writing C:\functions\MyFunctionProj\MyHttpTrigger\index.js
Writing C:\functions\MyFunctionProj\MyHttpTrigger\sample.dat
Writing C:\functions\MyFunctionProj\MyHttpTrigger\function.json
```

## <a name="edit-the-function"></a>Editar a função

Por padrão, o modelo cria uma função que requer uma tecla de função ao fazer solicitações. Para facilitar o teste da função no Azure, você precisará atualizar a função para permitir acesso anônimo. A maneira de fazer essa alteração depende da linguagem do projeto de funções.

### <a name="c"></a>C\#

Abra o arquivo de código MyHttpTrigger.cs que é sua nova função, atualize o atributo **AuthorizationLevel** na definição de função para o valor `anonymous` e salve as alterações.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

Abra o arquivo function.json para a nova função, abra-o em um editor de texto, atualize a propriedade **authLevel** em **bindings.httpTrigger** para `anonymous`e salve as alterações.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

Agora você pode chamar a função no Azure sem ter que fornecer a tecla de função. A tecla de função nunca é necessária na execução local.

## <a name="run-the-function-locally"></a>Executar a função localmente

O comando a seguir inicia o aplicativo de funções. O aplicativo é executado usando o mesmo tempo de execução do Azure Functions no Azure.

```bash
func host start --build
```

A opção `--build` é necessária para compilar projetos em C#. Essa opção não é necessária para um projeto em JavaScript.

Quando o host do Functions é iniciado, ele escreve algo parecido com a seguinte saída, que foi truncada por questões de legibilidade:

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Copie a URL da função `HTTPTrigger` da saída do tempo de execução de função e cole-a na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `?name=<yourname>` a essa URL e execute a solicitação. O exemplo a seguir mostra a resposta no navegador à solicitação GET retornada pela função local:

![Testar localmente no navegador](./media/functions-create-first-azure-function-azure-cli/functions-test-local-browser.png)

Agora que você executou a função localmente, poderá criar o aplicativo de funções e outros recursos necessários no Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de funções para hospedar a execução de suas funções. O aplicativo de funções fornece um ambiente para execução sem servidor do seu código de função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos. Crie um aplicativo de funções ao usar o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

No comando a seguir, substitua um nome de aplicativo de funções exclusivo quando você vir o espaço reservado `<app_name>` e o nome da conta de armazenamento por `<storage_name>`. O `<app_name>` é usado como domínio DNS padrão para o aplicativo de funções, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure. O parâmetro _deployment-source-url_ é um repositório de exemplo no GitHub que contém uma função de gatilho HTTP "Olá, Mundo".

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```

Definir o parâmetro _local do plano de consumo_ significa que o aplicativo da função é hospedado em um plano de hospedagem de consumo. Nesse plano sem servidor, os recursos são adicionados dinamicamente conforme exigido pelas funções, e você paga apenas quando as funções estão em execução. Para obter mais informações, consulte [Escolher o plano de hospedagem correto](functions-scale.md).

Depois que o aplicativo de funções for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

## <a name="configure-the-function-app"></a>Configurar o aplicativo de funções

A versão do Core Tools 2.x cria projetos usando modelos para o tempo de execução do Azure Functions 2.x. Por isso, você precisa ter certeza de que a versão do tempo de execução 2.x está sendo usada no Azure. Definir a configuração de aplicativo`FUNCTIONS_WORKER_RUNTIME` como `~2` fixa o aplicativo de funções na última versão 2.x. Defina as configurações de aplicativo com o comando [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).

O seguinte comando da CLI do Azure, '<nome_aplicativo> é o nome do seu aplicativo de funções.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~2
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
