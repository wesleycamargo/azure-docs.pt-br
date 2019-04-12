---
title: Trabalhar com Azure Functions Core Tools | Microsoft Docs
description: Saiba como codificar e testar o Azure Functions do prompt de comando ou terminal no computador local antes de executá-las no Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: glenga
ms.openlocfilehash: 84c578fdbcfb555bde23d6a9e6f8258259cff8de
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59497093"
---
# <a name="work-with-azure-functions-core-tools"></a>Trabalhar com o Azure Functions Core Tools

Azure Functions Core Tools permite desenvolver e testar as funções em seu computador local a partir do prompt de comando ou terminal. Suas funções locais podem se conectar a serviços do Azure em tempo real e você pode depurar as suas funções em seu computador local usando o tempo de execução total do Functions. Você ainda pode implantar um aplicativo de funções para sua assinatura do Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Desenvolvimento de funções em seu computador local e publicá-los para o Azure usando ferramentas básicas segue estas etapas básicas:

> [!div class="checklist"]
> * [Instale as ferramentas básicas e as dependências.](#v2)
> * [Crie um projeto de aplicativo de função de um modelo de idioma específico.](#create-a-local-functions-project)
> * [Registre as extensões de gatilho e associação.](#register-extensions)
> * [Defina o armazenamento e outras conexões.](#local-settings-file)
> * [Crie uma função de um gatilho e o modelo de idioma específico.](#create-func)
> * [Executar a função localmente](#start)
> * [Publicar o projeto no Azure](#publish)

## <a name="core-tools-versions"></a>Versões de Core Tools

Há duas versões do Azure Functions Core Tools. A versão que você usa depende do ambiente de desenvolvimento local, da [escolha da linguagem](supported-languages.md) e do nível de suporte necessário:

+ Versão 1.x: oferece suporte à versão 1.x do tempo de execução. Esta versão das ferramentas só tem suporte em computadores Windows e é instalada por meio de um [pacote npm](https://docs.npmjs.com/getting-started/what-is-npm). Com esta versão, você pode criar funções em linguagens experimentais que não são suportadas oficialmente. Para obter mais informações, consulte [línguas suportadas no Azure Functions](supported-languages.md)

+ [Versão 2.x](#v2): oferece suporte à [versão 2.x do tempo de execução](functions-versions.md). Esta versão oferece suporte a [Windows](#windows-npm), [macOS](#brew), e [Linux](#linux). Usa gerenciadores de pacotes específicos da plataforma ou npm para instalação.

A menos que indicado o contrário, os exemplos neste artigo são para a versão 2.x.

## <a name="install-the-azure-functions-core-tools"></a>Instalação das ferramentas básicas do Azure Functions

O [Azure Functions Core Tools] é uma versão local do tempo de execução do Azure Functions que pode ser executada no computador local de desenvolvimento. Ele também fornece comandos para criar funções, se conectar ao Azure e implantar projetos de função.

### <a name="v2"></a>Versão 2.x

A versão 2.x das ferramentas usa o tempo de execução 2.x do Azure Functions que se baseia em .NET Core. Esta versão tem suporte em todas as plataformas que o .NET Core 2.x oferece suporte, incluindo [Windows](#windows-npm), [macOS](#brew), e [Linux](#linux). Você deve primeiro instalar o .NET Core 2.x SDK.

#### <a name="windows-npm"></a>Windows

As etapas a seguir usam npm para instalar ferramentas principais no Windows. Você também pode usar [Chocolatey](https://chocolatey.org/). Para obter mais informações, confira o [arquivo Leiame das Ferramentas Principais](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Instalar o [.NET Core 2.x SDK para Windows](https://www.microsoft.com/net/download/windows).

2. Instale o [Node.js], que inclui npm. Para a versão 2.x das ferramentas, somente Node.js 8.5 e versões posteriores têm suporte.

3. Instale o pacote de ferramentas principais:

    ```bash
    npm install -g azure-functions-core-tools
    ```

#### <a name="brew"></a>MacOS com o Homebrew

As etapas a seguir usam o Homebrew para instalar as ferramentas principais em macOS.

1. Instalar o [.NET Core 2.x SDK para macOS](https://www.microsoft.com/net/download/macos).

2. Instale o [Homebrew](https://brew.sh/), se ele ainda não estiver instalado.

3. Instale o pacote de ferramentas principais:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

#### <a name="linux"></a> Linux (Ubuntu/Debian) com APT

As etapas a seguir usma [APT](https://wiki.debian.org/Apt) para instalar as ferramentas principais em sua distribuição Ubuntu/Debian Linux. Para outras distribuições do Linux, confira o [arquivo Leiame das ferramentas principais](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Instalar o [.NET Core 2.x SDK para Linux](https://www.microsoft.com/net/download/linux).

2. Registre a chave do produto da Microsoft como confiável:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Verifique se seu servidor Ubuntu está executando uma das versões adequadas da tabela abaixo. Para adicionar a fonte apt, execute:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Distribuição Linux | Versão |
    | --------------- | ----------- |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

4. Instale o pacote de ferramentas principais:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

## <a name="create-a-local-functions-project"></a>Criar um projeto de funções local

Um diretório de projeto de funções contém os arquivos [host. JSON](functions-host-json.md) e [local.settings.json](#local-settings-file), ao longo de subpastas que contêm o código para funções individuais. Esse diretório é o equivalente ao de um aplicativo de funções no Azure. Para saber mais sobre a estrutura de pastas do Functions, consulte o [guia de desenvolvedores do Azure Functions](functions-reference.md#folder-structure).

Versão 2.x exige que você selecione um idioma padrão para seu projeto quando ele é inicializado, e todas as funções adicionadas usam modelos de idioma padrão. Na versão 1.x, você especifica a linguagem cada vez que você criar uma função.

Na janela do terminal ou em um prompt de comando, execute o seguinte comando para criar o projeto e o repositório Git local:

```bash
func init MyFunctionProj
```

Quando você fornece um nome de projeto, uma nova pasta com esse nome é criada e inicializada. Caso contrário, a pasta atual é inicializada.  
Na versão 2.x, quando você executar o comando você deve escolher um tempo de execução para o seu projeto. Se você pretende desenvolver funções do JavaScript, escolha **nó**:

```output
Select a worker runtime:
dotnet
node
```

Use cima/para baixo de teclas de direção para escolher um idioma, em seguida, pressione Enter. A saída se parece com o seguinte exemplo para um projeto JavaScript:

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init` suporta as seguintes opções, que são a versão 2. x somente, a menos que indicado o contrário:

| Opção     | DESCRIÇÃO                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Inicializa um projeto de script (.csx) C#. Você precisam especificar `--csx` nos próximos comandos. |
| **`--docker`** | Crie um Dockerfile para um contêiner usando uma imagem base que se baseia no `--worker-runtime` escolhido. Use esta opção quando planejar publicar em um contêiner do Linux personalizado. |
| **`--force`** | Inicializa o projeto mesmo quando há arquivos existentes no projeto. Essa configuração substitui os arquivos existentes pelo mesmo nome. Os outros arquivos na pasta do projeto não serão afetados. |
| **`--no-source-control -n`** | Impede a criação padrão de um repositório Git na versão 1.x. Na versão 2.x, o repositório git não será criado por padrão. |
| **`--source-control`** | Controla se um repositório git é criado. Por padrão, não é criado um repositório. Quando `true`, um repositório é criado. |
| **`--worker-runtime`** | Define o tempo de execução de linguagem para o projeto. Os valores com suporte são `dotnet`, `node` (JavaScript) `java` e `python`. Quando não estiver definido, será solicitado que você escolha seu tempo de execução durante a inicialização. |

> [!IMPORTANT]
> Por padrão, a versão 2.x das ferramentas do Core cria a função de projetos de aplicativo para o tempo de execução do .NET como [projetos de classes C#](functions-dotnet-class-library.md) (. csproj). Esses projetos C#, que podem ser usados com o Visual Studio ou com Visual Studio Code, são compilados durante o teste e ao publicar no Azure. Se você deseja criar e trabalhar com o os mesmos arquivos de script C# (. CSx) criados na versão 1. x e no portal, você deve incluir o `--csx` parâmetro ao criar e implantar funções.

## <a name="register-extensions"></a>Extensões de registro

Na versão 2.x do Azure Functions Runtime, é necessário registrar explicitamente as extensões de associação (tipos de associação) usadas em seu aplicativo de funções.

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Para obter mais informações, consulte [Gatilhos e conceitos de associações do Azure Functions](./functions-bindings-expressions-patterns.md).

## <a name="local-settings-file"></a>Arquivo de configurações local

O arquivo local.settings.json armazena as configurações do aplicativo, as cadeias de conexão e as configurações para as Ferramentas básicas do Azure Functions. As configurações no arquivo local.settings.json só são usadas pelas ferramentas do Functions quando são executadas localmente. Por padrão, essas configurações não são migradas automaticamente quando o projeto é publicado no Azure. Use a opção `--publish-local-settings` [quando publicar](#publish) para se certificar de que essas configurações serão adicionadas ao aplicativo de funções no Azure. Observe que os valores em **ConnectionStrings** nunca são publicados. O arquivo tem a seguinte estrutura:

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*"
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

| Configuração      | DESCRIÇÃO                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Quando definido como `true`, todos os valores são criptografados usando uma chave do computador local. Usado com `func settings` comandos. O valor padrão é `false`. |
| **`Values`** | Coleção de configurações de aplicativo e cadeias de conexão usadas ao executar localmente. Esses valores correspondem às configurações de aplicativo em seu aplicativo de função no Azure, tais como [ `AzureWebJobsStorage` ]. Vários gatilhos e associações têm uma propriedade que se refere a uma configuração de aplicativo de cadeia de caracteres de conexão, como `Connection` para o [gatilho do armazenamento de Blob](functions-bindings-storage-blob.md#trigger---configuration). Para essas propriedades, você precisa de uma configuração de aplicativo definida na `Values` matriz. <br/>[`AzureWebJobsStorage`] um aplicativo necessário é a configuração de gatilhos diferentes de HTTP. <br/>Versão 2.x do tempo de execução de funções requer o [ `FUNCTIONS_WORKER_RUNTIME` ] configuração, que é gerada para seu projeto pelas ferramentas básicas. <br/> Quando você tiver o [emulador de armazenamento do Azure](../storage/common/storage-use-emulator.md) instalado localmente, você pode definir [ `AzureWebJobsStorage` ] para `UseDevelopmentStorage=true` e ferramentas principais usa o emulador. Isso é útil durante o desenvolvimento, mas você deve testar com uma conexão de armazenamento real antes da implantação. |
| **`Host`** | As configurações nesta seção personalizam o processo de host do Functions quando executadas localmente. |
| **`LocalHttpPort`** | Define a porta padrão usada ao executar o host local do Functions (`func host start` e `func run`). A opção de linha de comando `--port` tem precedência sobre esse valor. |
| **`CORS`** | Define as origens permitidas para [CORS (Compartilhamento de recurso entre origens)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). As origens são fornecidas como uma lista separada por vírgulas, sem espaços. Há suporte para o valor do caractere curinga (\*), que permite solicitações de qualquer origem. |
| **`ConnectionStrings`** | Não use esta coleção para as cadeias de conexão usadas por suas associações de função. Essa coleção é usada somente por estruturas que normalmente obtêm cadeias de caracteres de conexão das `ConnectionStrings` seção de configuração do arquivo, como [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). As cadeias de caracteres de conexão neste objeto são adicionadas ao ambiente com o tipo de provedor de [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Os itens nesta coleção não são publicados no Azure com outras configurações de aplicativo. Você deve adicionar explicitamente esses valores para o `Connection strings` coleção de configurações do aplicativo de função. Se você estiver criando um [ `SqlConnection` ](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) em seu código de função, você deve armazenar o valor de cadeia de caracteres de conexão na **configurações do aplicativo** no portal com as outras conexões. |

Os valores de configuração do aplicativo de funções também podem ser lidos em seu código como variáveis de ambiente. Para obter mais informações, confira a seção de variáveis de Ambiente desses tópicos de referência específicos de linguagem:

* [C#pré-compilado](functions-dotnet-class-library.md#environment-variables)
* [Script do C# (.csx)](functions-reference-csharp.md#environment-variables)
* [F#script (. fsx)](functions-reference-fsharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

Quando nenhuma cadeia de caracteres de conexão de armazenamento válido é definida para [ `AzureWebJobsStorage` ] e o emulador não está sendo usado, a seguinte mensagem de erro é mostrada:

> Valor ausente para AzureWebJobsStorage em local.settings.json. Isso é necessário para todos os gatilhos diferentes de HTTP. É possível executar o func azure functionapp fetch-app-settings \<functionAppName\>' ou especificar uma cadeia de conexão em local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Obter suas cadeias de conexão de armazenamento

Mesmo usando o emulador de armazenamento para desenvolvimento, convém testar com uma conexão de armazenamento real. Supondo que você já tenha [criado uma conta de armazenamento](../storage/common/storage-create-storage-account.md), será possível obter uma cadeia de conexão de armazenamento válida de uma destas maneiras:

+ No [portal do Azure]. Navegue até sua conta de armazenamento, selecione **Chaves de acesso** em **Configurações** e copie um dos valores da **Cadeia de conexão**.

  ![Copiar cadeia de conexão do portal do Azure](./media/functions-run-local/copy-storage-connection-portal.png)

+ Use o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para conectar-se à sua conta do Azure. No **Gerenciador**, expanda sua assinatura, selecione sua conta de armazenamento e copie a cadeia de conexão primária ou secundária.

  ![Copiar cadeia de conexão do Gerenciador de Armazenamento](./media/functions-run-local/storage-explorer.png)

+ Use as Ferramentas Básicas para baixar a cadeia de conexão do Azure com um dos seguintes comandos:

  + Baixe todas as configurações de um aplicativo de funções existente:

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Obtenha a cadeia de conexão para uma conta de armazenamento específica:

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Enquanto ainda não tiver se conectado ao Azure, será solicitado que você faça isso.

## <a name="create-func"></a>Criar uma função

Para criar uma função, execute o seguinte comando:

```bash
func new
```

Na versão 2.x, quando você executa `func new` você é solicitado a escolher um modelo no idioma padrão do seu aplicativo de funções, em seguida, você também precisará escolher um nome para sua função. Na versão 1.x, você também precisará escolher o idioma.

```output
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
```

Código de função é gerado em uma subpasta com o nome da função fornecido, como você pode ver na seguinte saída do gatilho de fila:

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Você também pode especificar essas opções no comando usando os seguintes argumentos:

| Argumento     | DESCRIÇÃO                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Versão 2. x) Gera os mesmos modelos C# (. CSx) de script usados na versão 1. x e no portal. |
| **`--language -l`**| A linguagem de programação modelo, como C#, F# ou JavaScript. Essa opção é necessária na versão 1.x. Na versão 2.x, não use essa opção nem escolha uma linguagem que corresponda ao tempo de execução do trabalho. |
| **`--name -n`** | O nome da função. |
| **`--template -t`** | Use o `func templates list` comando para ver a lista completa de modelos disponíveis para cada idioma com suporte.   |

Por exemplo, para criar um gatilho de HTTP de JavaScript em um único comando, execute:

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Para criar uma função ativada por fila em um único comando, execute:

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Executar funções localmente

Para executar um projeto de funções, execute o host de funções. O host permite os gatilhos para todas as funções no projeto:

```bash
func host start
```

O `host` comando apenas é necessário na versão 1. x.

`func host start` suporta as seguintes opções:

| Opção     | DESCRIÇÃO                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Não compile o projeto atual antes da execução. Somente para projetos dotnet. O padrão é definido como false. Versão 2.x somente. |
| **`--cert`** | O caminho para um arquivo .pfx que contém uma chave privada. Usado somente com `--useHttps`. Versão 2.x somente. |
| **`--cors-credentials`** | Permita solicitações autenticadas de origem cruzada (ou seja, cookies e cabeçalho de autenticação) somente na versão 2.x. |
| **`--cors`** | Uma lista separada por vírgulas de origens CORS, sem espaços. |
| **`--language-worker`** | Argumentos para configurar o trabalhador de idioma. Versão 2.x somente. |
| **`--nodeDebugPort -n`** | A porta para usar o depurador de nós. Padrão: Um valor de launch.json ou 5858. Versão 1.x somente. |
| **`--password`** | A senha ou um arquivo que contém a senha para um arquivo .pfx. Usado somente com `--cert`. Versão 2.x somente. |
| **`--port -p`** | A porta local na qual escutar. Valor padrão: 7071. |
| **`--pause-on-error`** | Pausar para entrada adicional antes de encerrar o processo. Usado apenas ao iniciar as Core Tools de um IDE (ambiente de desenvolvimento integrado).|
| **`--script-root --prefix`** | Usado para especificar o caminho para a raiz do aplicativo de funções que deve ser executado ou implantado. Usado para projetos compilados que geram arquivos de projeto para uma subpasta. Por exemplo, quando você cria um projeto de biblioteca de classes C#, os arquivos host.json, local.settings.json e function.json são gerados em uma subpasta *raiz* com um caminho como `MyProject/bin/Debug/netstandard2.0`. Nesse caso, defina o prefixo como `--script-root MyProject/bin/Debug/netstandard2.0`. Essa é a raiz do aplicativo de funções durante a execução no Azure. |
| **`--timeout -t`** | O tempo limite para o host de funções ser iniciado, em segundos. Padrão: 20 segundos.|
| **`--useHttps`** | Associar a `https://localhost:{port}` em vez de `http://localhost:{port}`. Por padrão, essa opção cria um certificado confiável no computador.|

Para um C# projeto biblioteca de classes (. csproj), você deve incluir o `--build` opção para gerar o arquivo. dll de biblioteca.

Quando o host de funções é iniciado, ele gera as funções acionadas por URL de HTTP:

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>Ao executar localmente, a autenticação não é imposta para pontos de extremidade HTTP. Isso significa que todas as solicitações HTTP locais são tratadas como `authLevel = "anonymous"`. Para obter mais informações, confira o [artigo de associação HTTP](functions-bindings-http-webhook.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Transferência dos dados de teste para uma função

Para testar as funções localmente, [inicie o host do Functions](#start) e chame os pontos de extremidade no servidor local usando solicitações HTTP. O ponto de extremidade que você chama depende do tipo de função.

>[!NOTE]
> Os exemplos neste tópico usam a ferramenta cURL para enviar solicitações HTTP do terminal ou de um prompt de comando. Você pode usar uma ferramenta de sua escolha para enviar solicitações HTTP para o servidor local. Observe que a ferramenta cURL está disponível por padrão nos sistemas baseados em Linux. No Windows, você deve primeiro baixar e instalar o [cURL ferramenta](https://curl.haxx.se/).

Para ter informações mais gerais sobre o teste de funções, confira [Estratégias para testar seu código no Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funções disparadas por HTTP e webhook

Chame o seguinte ponto de extremidade para executar localmente funções disparadas por HTTP e webhook:

    http://localhost:{port}/api/{function_name}

Use o mesmo nome de servidor e porta no qual o host do Functions está escutando. Veja isso na saída gerada ao iniciar o host do Function. Chame essa URL usando qualquer método HTTP com suporte do disparador.

O seguinte comando cURL dispara a função de início rápido `MyHttpTrigger` de uma solicitação GET com o parâmetro _name_ transmitido na cadeia de consulta.

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

O exemplo a seguir é a mesma função chamada a partir de uma solicitação POST passando _name_ no corpo da solicitação:

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Você pode fazer solicitações GET de um navegador passando dados na cadeia de consulta. Para todos os outros métodos HTTP, você deve usar cURL, Fiddler, Postman ou uma ferramenta de teste HTTP semelhante.

#### <a name="non-http-triggered-functions"></a>Funções disparadas por algo diferente de HTTP

Para todos os tipos de funções que não sejam gatilhos HTTP e webhooks, você pode testar suas funções localmente chamando um ponto de extremidade de administração. Chamar esse ponto de extremidade com uma solicitação HTTP POST no servidor local dispara a função. Como alternativa, é possível passar dados de teste para a execução no corpo da solicitação POST. Essa funcionalidade é semelhante á guia **Teste** no Portal do Azure.

Chame o seguinte ponto de extremidade de administrador para disparar funções que não são HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Para passar dados de teste para o ponto de extremidade administrador de uma função, é necessário fornecer os dados no corpo de uma mensagem de solicitação POST. O corpo da mensagem deve ter o seguinte formato JSON:

```JSON
{
    "input": "<trigger_input>"
}
```

O valor `<trigger_input>` contém dados em um formato esperado pela função. O exemplo de cURL a seguir é um POST para uma função `QueueTriggerJS`. Nesse caso, a entrada é uma cadeia de caracteres equivalente à mensagem esperada na fila.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Usar o comando `func run` na versão 1.x

>[!IMPORTANT]
> O comando `func run` não tem suporte na versão 2.x das ferramentas. Para saber mais, consulte o tópico [Como direcionar para versões de tempo de execução do Azure Functions](set-runtime-version.md).

Você também pode invocar uma função diretamente usando `func run <FunctionName>` e fornecer dados de entrada para a função. Esse comando é semelhante à execução de uma função usando a guia **Testar** no Portal do Azure.

`func run` suporta as seguintes opções:

| Opção     | DESCRIÇÃO                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Conteúdo embutido. |
| **`--debug -d`** | Anexe um depurador ao processo de host antes de executar a função.|
| **`--timeout -t`** | Tempo de espera (em segundos) até que o host local de funções esteja pronto.|
| **`--file -f`** | O nome do arquivo a ser usado como conteúdo.|
| **`--no-interactive`** | Não solicitará a entrada. Útil para cenários de automação.|

Por exemplo, para chamar uma função ativada por HTTP e passar o corpo do conteúdo, execute o seguinte comando:

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Publicar no Azure

O Core Tools dá suporte a dois tipos de implantação: a implantação de arquivos de projeto de função diretamente no aplicativo de funções e a implantação de um contêiner do Linux personalizado, com suporte somente na versão 2.x. É necessário já ter [criado um aplicativo de funções em sua assinatura do Azure](functions-cli-samples.md#create).

Na versão 2.x, você precisa ter [registrado suas extensões](#register-extensions) no projeto antes da publicação. Os projetos que exigem build devem ser compilados para que os binários possam ser implantados.

### <a name="project-file-deployment"></a>Implantação de arquivo de projeto

O método mais comum de implantação envolve o uso do Core Tools para empacotar o projeto do aplicativo de funções, binários e dependências e implantar o pacote no aplicativo de funções. Você pode opcionalmente [executar suas funções diretamente do pacote de implantação](run-functions-from-deployment-package.md).

Para publicar um projeto de funções em um aplicativo de funções no Azure, use o comando `publish`:

```bash
func azure functionapp publish <FunctionAppName>
```

Esse comando publica a um aplicativo de funções existente no Azure. Um erro ocorre quando `<FunctionAppName>` não existe em sua assinatura. Para saber como criar um aplicativo de funções pelo prompt de comando ou pela janela do terminal usando a CLI do Azure, consulte [Criar um aplicativo de funções para execução sem servidor](./scripts/functions-cli-create-serverless.md).

O comando `publish` faz upload o conteúdo do diretório do projeto de funções. Se você excluir arquivos localmente, o comando `publish` não os excluirá do Azure. Você pode excluir arquivos no Azure usando a [ferramenta Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) no [Portal do Azure].

>[!IMPORTANT]
> Quando você cria um aplicativo de funções no portal do Azure, ele usa a versão 2.x do tempo de execução do Core Tools por padrão. Para que o aplicativo de funções use a versão 1.x do tempo de execução, siga as instruções em [Executar na versão 1.x](functions-versions.md#creating-1x-apps).
> Você não pode alterar a versão do tempo de execução de um aplicativo de funções que tenha funções existentes.

As opções de publicação do projeto a seguir se aplicam a ambas versões 1.x e 2.x:

| Opção     | DESCRIÇÃO                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Configurações de publicação em local.settings.json do Azure, a solicitação para substituir se a configuração já existe. Se você estiver usando o emulador de armazenamento, altere a configuração do aplicativo para uma [conexão de armazenamento real](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Suprima o prompt para substituir as configurações do aplicativo quando `--publish-local-settings -i` for usado.|

As opções de publicação do projeto a seguir têm suporte apenas na versão 2. x:

| Opção     | DESCRIÇÃO                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only -o`** |  Somente publicar as configurações e ignorar o conteúdo. O padrão é solicitado. |
|**`--list-ignored-files`** | Exibe uma lista de arquivos que são ignorados durante a publicação, que é baseada no arquivo .funcignore. |
| **`--list-included-files`** | Exibe uma lista de arquivos que são publicados, que é baseada no arquivo .funcignore. |
| **`--nozip`** | Transforma o padrão de `Run-From-Zip` modo desativado. |
| **`--build-native-deps`** | Ignora a geração da pasta .wheels durante a publicação de python de aplicativos de funções. |
| **`--additional-packages`** | Lista de pacotes para instalar ao compilar dependências nativas. Por exemplo: `python3-dev libevent-dev`. |
| **`--force`** | Ignorar a verificação de pré-publicação em determinados cenários. |
| **`--csx`** | Publicar um projeto de script (.csx) C#. |
| **`--no-build`** | Ignorar a criação de funções do dotnet. |
| **`--dotnet-cli-params`** | Durante a publicação de funções C# (.csproj), o Core Tools chama 'dotnet build --output bin/publish'. Todos os parâmetros passados para isso serão acrescentados à linha de comando. |

### <a name="custom-container-deployment"></a>Implantação de contêiner personalizado

O Functions permite que você implante o projeto de função em um contêiner do Linux personalizado. Para obter mais informações, confira [Criar uma função no Linux usando uma imagem personalizada](functions-create-function-linux-custom-image.md). A versão 2.x do Core Tools dá suporte à implantação de um contêiner personalizado. Os contêineres personalizados precisam ter um Dockerfile. Use a opção –dockerfile em `func init`.

```bash
func deploy
```

As seguintes opções de implantação de contêiner personalizado estão disponíveis:

| Opção     | DESCRIÇÃO                            |
| ------------ | -------------------------------------- |
| **`--registry`** | O nome de um Registro do Docker ao qual o usuário atual está conectado. |
| **`--platform`** | A plataforma de hospedagem do aplicativo de funções. As opções válidas são `kubernetes` |
| **`--name`** | Nome do aplicativo de funções. |
| **`--max`**  | Opcionalmente, define o número máximo de instâncias do aplicativo de funções a serem implantadas. |
| **`--min`**  | Opcionalmente, define o número mínimo de instâncias do aplicativo de funções a serem implantadas. |
| **`--config`** | Define um arquivo de configuração de implantação opcional. |

## <a name="monitoring-functions"></a>Funções de monitoramento

É a maneira recomendada para monitorar a execução de suas funções com a integração com o Azure Application Insights. Ao criar um aplicativo de funções no portal do Azure, essa integração é realizada por padrão. No entanto, quando você cria seu aplicativo de funções usando a CLI do Azure, não é feita a integração no aplicativo de funções no Azure.

Para habilitar o Application Insights para seu aplicativo de funções:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Para saber mais, consulte [Monitorar Azure Functions](functions-monitoring.md).
## <a name="next-steps"></a>Próximos passos

As principais ferramentas do Azure Functions são [Código-fonte aberto e hospedado no GitHub](https://github.com/azure/azure-functions-cli).  
Para arquivar uma solicitação de bug ou recurso, [abra um problema do GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Ferramentas básicas do Azure Functions]: https://www.npmjs.com/package/azure-functions-core-tools
[Portal do Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[`AzureWebJobsStorage`]: functions-app-settings.md#azurewebjobsstorage
