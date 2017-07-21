---
title: "Desenvolver e executar funções do Azure localmente | Microsoft Docs"
description: "Saiba como codificar e testar o Azure Functions no computador local antes de executá-las no Azure Functions."
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2017
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 478c2ffbb0bfe5b1595bc6ea1bebb2144aebc728
ms.contentlocale: pt-br
ms.lasthandoff: 06/09/2017

---
# <a name="code-and-test-azure-functions-locally"></a>Como codificar e testar o Azure Functions localmente

Você pode usar o editor de código favorito e ferramentas de desenvolvimento locais para executar o tempo de execução do Azure Functions localmente. Disparar eventos no Azure e depure C# e as funções de JavaScript.

Para começar, instale as [Ferramentas básicas do Azure Functions] do npm. As Ferramentas básicas do Azure Functions são uma versão local do tempo de execução do Azure Functions que pode ser executada no computador local do Windows. Não é um simulador ou emulador. Tem o mesmo tempo de execução que é executado no Azure.

[Ferramentas básicas do Azure Functions] adicionam os aliases de comando a seguir:
- `func`
- `azfun`
- `azurefunctions`

As principais ferramentas do Azure Functions são [Código-fonte aberto e hospedado no GitHub](https://github.com/azure/azure-functions-cli). Para arquivar uma solicitação de bug ou recurso, [abra um problema do GitHub](https://github.com/azure/azure-functions-cli/issues).

## <a name="create-a-local-functions-project"></a>Criar um projeto de funções local

Quando estiver em execução localmente, um projeto de funções é um diretório que tem os arquivos host.json e local.settings.json. Esse diretório é o equivalente ao de um aplicativo de funções no Azure. Para saber mais sobre a estrutura de pastas do Azure Functions, consulte o [guia de desenvolvedores do Azure Functions](functions-reference.md#folder-structure).

No prompt de comando, execute o comando a seguir:

```
func init MyFunctionProj
```

A saída se parece com o seguinte exemplo:

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Para recusar a criação de um repositório Git, use a opção `--no-source-control [-n]`.

<a name="local-settings"></a>

## <a name="local-settings-file"></a>Arquivo de configurações local

O arquivo local.settings.json armazena as configurações do aplicativo, as cadeias de conexão e as configurações para as Ferramentas básicas do Azure Functions. Ele contém a seguinte estrutura:

```json
{
  "IsEncrypted": false,   // If set to true, all values are encrypted by using a local machine key. Use with "func settings" commands.
  "Values": {
    "AzureWebJobsStorage": "<connection string>",   // This is required for all triggers except HTTP.
    "AzureWebJobsDashboard": "<connection string>", // Optional, controls whether to log to the Monitor tab in the portal.
  },
  "Host": {
    "LocalHttpPort": 7071, // If specified, this is the default port for "host start" and "run". Can be overridden by using the --port command-line option.
    "CORS": "*"            // Origins to allow in the CORS setting.
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```

Especificar configurações de aplicativo na coleção **Valores**. Essas configurações podem ser lidas como variáveis de ambiente. No C#, use `System.Environment.GetEnvironmentVariable` ou `ConfigurationManager.AppSettings`. No JavaScript, use `process.env`. Se a mesma configuração for especificada como uma variável de ambiente do sistema, ela tem precedência sobre valores em local.settings.json.

A configuração do aplicativo **AzureWebJobsStorage** é uma configuração especial que é exigida pelo tempo de execução do Azure Functions para todos os gatilhos diferente de HTTP. Internamente, o tempo de execução cria filas para gerenciar gatilhos nesta conta de armazenamento. Se um valor para **AzureWebJobsStorage** não for especificado e você usar gatilhos diferentes de HTTP, a seguinte mensagem será exibida:

*Valor ausente para AzureWebJobsStorage em local.settings.json. Isso é necessário para todos os gatilhos diferentes de HTTP. Você pode executar func azure functionapp fetch-app-settings <functionAppName>' ou especifique uma cadeia de conexão em local.settings.json.*

> [!NOTE]
> É possível usar o emulador de armazenamento local, por meio da cadeia de conexão "AzureWebJobsStorage": "UseDevelopmentStorage=true". No entanto, pode haver diferenças de comportamento em comparação com o serviço de armazenamento do Azure.

As seguintes configurações de personalização do host local de funções:
- `LocalHttpPort`. A porta padrão a ser usada para `func host start` `func run`. A opção de linha de comando `--port` tem precedência sobre esse valor.
- `CORS`. As origens permitidas no CORS, como uma lista separada por vírgulas, sem espaços. Use "*" para permitir tudo.

Você pode fornecer cadeias de conexão no objeto `ConnectionStrings`. Elas são adicionadas ao ambiente com o nome do provedor **System.Data.SqlClient**.

A maioria dos gatilhos e associações têm uma propriedade **Conexão** que é o nome de uma configuração de aplicativo ou de variável de ambiente em local.settings.json. Se o valor de configuração do aplicativo estiver ausente, você verá a seguinte mensagem:

*Aviso: não é possível localizar o valor denominado “MyStorageConnection” em local.settings.json que corresponde a propriedade “conexão” definida em”blobTrigger” em “BlobTriggerCSharp\function.json”. Você pode executar func azure functionapp fetch-app-settings <functionAppName>' ou especifique uma cadeia de conexão em local.settings.json.*

O arquivo local.settings.json é usado somente pelas Ferramentas básicas do Azure Functions. Para definir as configurações do aplicativo e as cadeias de conexão no Azure, use a folha **Configurações de Aplicativo**.

### <a name="configure-app-settings"></a>Definir configurações de aplicativo

Para definir um valor para cadeias de conexão, você pode fazer o seguinte:
- Insira manualmente uma cadeia de conexão do [Azure Storage Explorer](http://storageexplorer.com/).
- Use **func azure functionapp fetch-app-settings \<FunctionAppName\>**. Exige **azure login**.
- Use **func azure functionapp storage fetch-connection-string \<StorageAccountName\>**. Exige **azure login**.

## <a name="create-a-function"></a>Criar uma função

Para criar uma função, execute `func new`. Esse comando tem os seguintes argumentos:

- `--language [-l]`. A linguagem de programação modelo, como C#, F# ou JavaScript.
- `--template [-t]`. O nome do modelo.
- `--name [-n]`. O nome da função.

Por exemplo, para criar um gatilho de HTTP de JavaScript, execute:

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

Para criar uma função ativada por fila, execute:

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```

## <a name="run-functions-locally"></a>Executar funções localmente

Para executar um projeto de funções, execute o host de funções. O host permite os gatilhos para todas as funções no projeto:

```
func host start
```

É possível usar as seguintes opções com `func host start`:

- `--port [-p]`. A porta local na qual escutar. Valor Padrão: 7071.
- `--debug <type>`. As opções são VSCode e VS.
- `--cors`. Uma lista separada por vírgulas de origens CORS, sem espaços.
- `--nodeDebugPort [-n]`. A porta para usar o depurador de nós. Padrão: um valor de launch.json ou 5858.
- `--debugLevel [-d]`. O nível de rastreamento do console (desativado, detalhado, informações, aviso ou erro). Padrão: informações.
- `--timeout [-t]`. O tempo limite para o host de funções ser iniciado, em segundos. Padrão: 20 segundos.
- `--useHttps`. Associar a https://localhost:{port} em vez de a http://localhost:{port}. Por padrão, essa opção cria um certificado confiável no computador.
- `--pause-on-error`. Pausar para entrada adicional antes de encerrar o processo. Útil ao iniciar as ferramentas básicas do Azure Functions de um ambiente de desenvolvimento integrado (IDE).

Quando o host de funções é iniciado, ele gera as funções acionadas por URL de HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug"></a>Depurar

Para anexar um depurador, passe o argumento `--debug`. Para depurar funções de JavaScript, use o Visual Studio Code. Para funções C#, use o Visual Studio.

Para depurar funções C#, use `--debug vs`. Como alternativa, use as [ferramentas do Visual Studio 2017 no Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Para iniciar o host e configurar a depuração de JavaScript, execute:

```
func host start --debug vscode
```

Em seguida, no Visual Studio Code, na exibição **Depurar**, selecione **Anexar ao Azure Functions**. Você pode anexar os pontos de interrupção, inspecionar variáveis e o código por etapas.

![Depuração de JavaScript com o Visual Studio Code](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="call-a-function-by-using-test-data"></a>Chamar uma função usando dados de teste

Você também pode chamar uma função diretamente usando `func run <FunctionName>`. Esse comando é semelhante a guia **Teste** no portal do Azure, onde você pode fornecer dados de entrada para a função. Esse comando inicia todo o host de funções.

É possível usar as seguintes opções com `func run`:

- `--content [-c]`. Conteúdo embutido.
- `--debug [-d]`. Anexe um depurador ao processo de host antes de executar a função.
- `--timeout [-t]`. Tempo de espera (em segundos) até que o host local de funções esteja pronto.
- `--file [-f]`. O nome do arquivo a ser usado como conteúdo.
- `--no-interactive`. Não solicitará a entrada. Útil para cenários de automação.

Por exemplo, para chamar uma função ativada por HTTP e passar o corpo do conteúdo, execute o seguinte comando:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-a-function-app"></a>Publicar um aplicativo de funções

Para publicar um projeto de funções em um aplicativo de funções no Azure, use o comando `publish`:

```
func azure functionapp publish <FunctionAppName>
```

É possível usar as seguintes opções:

- `--publish-local-settings [-i]`.  Configurações de publicação em local.settings.json do Azure, a solicitação para substituir se a configuração já existe.
- `--overwrite-settings [-y]`. Deve ser usada com `-i`. Substitui o AppSettings no Azure com o valor local se for diferente. O padrão é solicitado.

O comando `publish` faz upload o conteúdo do diretório do projeto de funções. Se você excluir arquivos localmente, esse comando não os excluirá do Azure. Para excluir esses arquivos, no portal do Azure Functions, use o Kudu. Para iniciar o Kudu, no portal do Azure Functions, selecione **Recursos da Plataforma** > **Ferramentas Avançadas (Kudu)**. 


<!-- LINKS -->

[Ferramentas básicas do Azure Functions]: https://www.npmjs.com/package/azure-functions-core-tools

