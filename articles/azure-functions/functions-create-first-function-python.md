---
title: Criar sua primeira função Python no Azure
description: Aprenda a criar sua primeira função Python no Azure usando o Azure Functions Core Tools e a CLI do Azure.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: af684a4fcc3a70326c1a57cb10a39204b4fd12dc
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438743"
---
# <a name="create-your-first-python-function-in-azure-preview"></a>Criar sua primeira função Python no Azure (versão prévia)

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Este artigo de início rápido explica como usar a CLI do Azure para criar seu primeiro aplicativo de funções [sem servidor](https://azure.com/serverless) do Python em execução no Linux. O código da função é criado localmente e implantado no Azure usando o [Azure Functions Core Tools](functions-run-local.md). Para saber mais sobre as considerações de versão prévia para executar seus aplicativos de funções no Linux, confira [essas Funções no artigo do Linux](https://aka.ms/funclinux).

As etapas a seguir têm suporte em um computador Mac, Windows ou Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para compilar e testar localmente, você precisará:

+ Instale o [Python 3.6](https://www.python.org/downloads/).

+ Instale [Azure Functions Core Tools](functions-run-local.md#v2) versão 2.2.70 ou posterior (requer o SDK do .NET Core 2.x).

Publicar e executar no Azure:

+ Instalar a [CLI do Azure]( /cli/azure/install-azure-cli) versão 2.x ou posterior.

+ Você precisa de uma assinatura ativa do Azure.
  [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Criar e ativar um ambiente virtual

Para criar um projeto de funções, é necessário que você trabalhe em um ambiente virtual do Python 3.6. Execute os seguintes comandos para criar e ativar um ambiente virtual chamado `.env`.

```bash
# In Bash
python3.6 -m venv .env
source .env/bin/activate

# In PowerShell
py -3.6 -m venv .env
.env\scripts\activate
```

## <a name="create-a-local-functions-project"></a>Criar um projeto de funções local

Agora você pode criar um projeto local do Functions. Esse diretório é o equivalente ao de um aplicativo de funções no Azure. Ele pode conter várias funções que compartilham a mesma configuração de hospedagem e local.

Na janela do terminal ou do prompt de comando, execute o comando a seguir:

```bash
func init MyFunctionProj
```

Escolha **python** como o tempo de execução desejado.

```output
Select a worker runtime:
1. dotnet
2. node
3. python
```

Você verá algo parecido com a saída a seguir.

```output
Installing wheel package
Installing azure-functions package
Installing azure-functions-worker package
Running pip freeze
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing /MyFunctionProj/.vscode/extensions.json
```

Uma nova pasta chamada _MyFunctionProj_ é criada. Para continuar, altere o diretório para essa pasta.

```bash
cd MyFunctionProj
```

## <a name="create-a-function"></a>Criar uma função

Para criar uma função, execute o seguinte comando:

```bash
func new
```

Escolha `HTTP Trigger` como o modelo e forneça um **nome de função** de `HttpTrigger`.

```output
Select a template:
1. Blob trigger
2. Cosmos DB trigger
3. Event Grid trigger
4. Event Hub trigger
5. HTTP trigger
6. Queue trigger
7. Service Bus Queue trigger
8. Service Bus Topic trigger
9. Timer trigger

Choose option: 5
Function name: HttpTrigger
```

Você verá algo parecido com a saída a seguir.

```output
Writing /MyFunctionProj/HttpTrigger/sample.dat
Writing /MyFunctionProj/HttpTrigger/__init__.py
Writing /MyFunctionProj/HttpTrigger/function.json
The function "HttpTrigger" was created successfully from the "HTTP trigger" template.
```

Uma subpasta chamada _HttpTrigger_ é criada. Ela contém `__init__.py`, que é o arquivo de script principal e o arquivo `function.json`, que descreve o gatilho e as associações usadas pela função. Para saber mais sobre o modelo de programação, confira o [guia do desenvolvedor do Python do Azure Functions](functions-reference-python.md).

## <a name="run-the-function-locally"></a>Executar a função localmente

Use o seguinte comando para executar o host de do Functions localmente.

```bash
func host start
```

Quando o host de funções é iniciado, ele gera a função acionada por URL de HTTP. (Observe que a saída inteira foi truncada para facilitar a leitura.)

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
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.
...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger
```

Copie a URL da função da saída e cole-a na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `?name=<yourname>` a essa URL e execute a solicitação.

    http://localhost:7071/api/HttpTrigger?name=<yourname>

A captura de tela a seguir mostra a resposta da função quando é acionada no navegador:

![test](./media/functions-create-first-function-python/function-test-local-browser.png)

Agora você está pronto para criar um aplicativo de funções e outros recursos necessários para publicação no Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Criar um aplicativo de funções Linux no Azure

O aplicativo de funções fornece um ambiente para execução do código de função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos. Crie um **aplicativo Python de funções em execução no Linux** usando o comando [az functionapp create](/cli/azure/functionapp).

Execute o comando a seguir usando um nome de aplicativo de funções exclusivo no lugar do espaço reservado `<app_name>` e o nome da conta de armazenamento de `<storage_name>`. O `<app_name>` também é o domínio do DNS padrão para o aplicativo de funções. O nome precisa ser exclusivo em todos os aplicativos no Azure.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <app_name> --storage-account  <storage_name>
```

> [!NOTE]
> Se você tiver um grupo de recursos existente denominado `myResourceGroup` com aplicativos do Serviço de Aplicativo que não sejam Linux, use um grupo de recursos diferente. Você não pode hospedar aplicativos Windows e Linux no mesmo grupo de recursos.  

Depois que o aplicativo de funções for criado, você verá a seguinte mensagem:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Agora você está pronto para publicar seu projeto de funções locais no aplicativo de funções no Azure.

## <a name="deploy-the-function-app-project-to-azure"></a>Implantar o projeto de aplicativo de funções no Azure

Execute o comando a seguir usando as Ferramentas Básicas do Azure Functions. Substitua `<app_name>` pelo nome do aplicativo da etapa anterior.

```bash
func azure functionapp publish <app_name>
```

Você verá algo semelhante à saída a seguir, que foi truncada para fins de legibilidade.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como o desenvolvimento do Azure Functions usando o Python.

> [!div class="nextstepaction"]
> [Guia do Desenvolvedor do Python do Azure Functions](functions-reference-python.md)
> [Gatilhos e associações do Azure Functions](functions-triggers-bindings.md)
