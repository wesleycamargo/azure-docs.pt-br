---
title: Introdução ao WebJobs SDK - Azure
description: Introdução ao WebJobs SDK para o processamento em segundo plano controlado por evento. Saiba como acessar dados em serviços do Azure e serviços de terceiros.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: glenga
ms.openlocfilehash: 034f182cc282f50eb3a4a1de05331f42957f49fe
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53339800"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Introdução ao SDK do Azure WebJobs para o processamento em segundo plano controlado por evento

Este artigo mostra como criar um projeto do SDK do Azure WebJobs, executá-lo localmente e implantá-lo no Serviço de Aplicativo do Azure.

As instruções são para [Visual Studio 2017](https://www.visualstudio.com/vs/), mas as mesmas tarefas podem ser realizadas com outras ferramentas, como [Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-azure-webjobs-sdk"></a>O que é o SDK de Trabalhos Web do Azure

O Azure WebJobs SDK é uma estrutura que simplifica a tarefa de escrever o código de processamento em segundo plano que acessa dados em serviços do Azure. O SDK apresenta uma sintaxe declarativa para especificar os eventos que devem disparar uma função, como uma nova mensagem adicionada a uma fila. Um sintaxe declarativa semelhante controla a leitura e a gravação dos dados após o disparo de uma função. Este sistema de gatilhos e associações cuida da maioria das tarefas de codificação de nível inferior associadas ao acesso aos serviços de terceiros e do Azure.

### <a name="functions-triggers-and-bindings"></a>Funções, gatilhos e associações do Azure

Um projeto do WebJobs SDK define uma ou mais *funções*. Uma função é um método que tem um atributo de gatilho na sua assinatura de método. Os gatilhos especificam condições para chamar uma função, e as associações especificam o que ler e gravar. Por exemplo, o atributo de gatilho na seguinte função informa o tempo de execução para chamar a função sempre que uma mensagem da fila aparece na fila `items`. O atributo `Blob` informa o tempo de execução para usar a mensagem da fila para ler um blob no contêiner *workitems*. O conteúdo da mensagem da fila &mdash; fornecido no `queueTrigger` parâmetro &mdash; é o nome do blob.

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>Versões 2.x e 3.x

As instruções descrevem como criar um projeto do WebJobs SDK versão 2.x. A versão mais recente do WebJobs SDK é a 3.x, mas ele está atualmente na versão prévia e este artigo ainda não tem instruções para essa versão. A principal mudança introduzida pela versão 3.x é o uso do .NET Core em vez do .NET Framework.

### <a name="azure-functions"></a>Funções do Azure

O [As funções do Azure](../azure-functions/functions-overview.md) se baseia no WebJobs SDK e é uma opção quando você não precisa usar o WebJobs SDK diretamente. O Azure Functions 1.x usa o WebJobs SDK 2.x. Para obter mais informações, consulte [comparação entre Azure Functions e WebJobs SDK](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tenha [uma conta do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) e experiência com [aplicativos no Serviço de Aplicativo do Azure](app-service-web-overview.md). Para completar as etapas neste artigo:

* [Instale o Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/) com a carga de trabalho de **desenvolvimento do Azure**. Se você já tiver o Visual Studio, mas não tiver essa carga de trabalho, adicione a carga de trabalho selecionando **Ferramentas > Obter Ferramentas e Recursos**.
* [Crie um aplicativo do Serviço de Aplicativo](app-service-web-get-started-dotnet-framework.md). Se já tiver um no qual possa implantar um WebJob, você poderá usá-lo em vez de criar um novo.

## <a name="create-a-project"></a>Criar um projeto

1. No Visual Studio, selecione **Arquivo > Novo projeto**.

2. Selecione **Área de Trabalho Clássica do Windows > Aplicativo de Console (.NET Framework)**.

3. Nomeie o projeto *WebJobsSDKSample* e, em seguida, selecione **OK**.

   ![Caixa de diálogo Novo Projeto](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>Adicionar pacote WebJobs NuGet

1. Instale a versão 2.x estável mais recente do pacote NuGet `Microsoft.Azure.WebJobs`.
 
   Aqui está o comando **Console do Gerenciador de Pacotes** para a versão 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>Criar o JobHost

O objeto `JobHost` é o contêiner de tempo de execução de funções: ele escuta gatilhos e chama funções. 

1. Em *Program.cs*, adicione uma instrução `using`:

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. Substitua o método `Main` pelo seguinte código:

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="enable-console-logging"></a>Habilitar o registro em log de console

Há várias opções para fazer logon no projeto WebJobs SDK. A que recomendamos é a [estrutura de registro em log que foi desenvolvida para o ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging). Essa estrutura oferece desempenho melhor e mais flexibilidade em mídia e filtragem de armazenamento. 

Nesta seção, você configura o registro em log do console que usa a nova estrutura.

1. Instale a versão estável mais recente dos seguintes pacotes NuGet:

   * `Microsoft.Extensions.Logging` - A estrutura de registro em log.
   * `Microsoft.Extensions.Logging.Console` - O *provedor* do console. Um provedor envia logs para um destino particular, nesse caso, para o console. 
 
   Estes são os comandos do **Console do Gerenciador de Pacotes** para a versão 2.0.1:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

1. Em *Program.cs*, adicione uma instrução `using`:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. No método `Main`, adicione código para atualizar o `JobHostConfiguration` antes de criar o `JobHost`:
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   Este código faz as seguintes alterações:

   * Desabilita o [registro em log do painel](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). O painel é um ferramenta de monitoramento herdada, e o registro em log do painel não é recomendado para cenários de produção de alta taxa de transferência.
   * Adiciona o provedor de console com [filtragem](webjobs-sdk-how-to.md#log-filtering) padrão. 

   O método `Main` agora tem esta aparência:

   ```
   var config = new JobHostConfiguration();
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   var host = new JobHost(config);
   host.RunAndBlock();
   ```
   
## <a name="create-a-function"></a>Criar uma função

1. Crie *Functions.cs* na pasta do projeto e substitua o código de modelo por este código:

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   O atributo `QueueTrigger` informa o tempo de execução para chamar esta função quando uma nova mensagem é gravada em uma fila de Armazenamento do Microsoft Azure chamada `queue`. O conteúdo da mensagem da fila é fornecido para o código do método no parâmetro `message`. O corpo do método é onde você processa os dados de gatilho. Neste exemplo, o código apenas registra a mensagem.

   O parâmetro `message` não precisa ser uma cadeia de caracteres. Você também pode associar a um objeto JSON, uma matriz de bytes ou um objeto [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage). [Consulte Uso de gatilho de fila](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Cada tipo de associação (como filas, blobs ou tabelas) tem um conjunto diferente de tipos de parâmetros que você pode associar.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

O emulador do Armazenamento do Microsoft Azure executado localmente não tem todos os recursos de que o WebJobs SDK precisa. Portanto, nesta seção, você cria uma conta de Armazenamento no Azure e configura o projeto para usá-la.

1. Abra **Gerenciador de Servidores** no Visual studio e entre no Azure. Clique com o botão direito do mouse no nó do **Azure** e selecione **Conectar à Assinatura do Microsoft Azure**.

   ![Entrar no Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. No nó do **Azure** no **Gerenciador de Servidores**, clique com o botão direito do mouse em **Armazenamento** e, em seguida, selecione **Criar Conta de Armazenamento**.

   ![Menu Criar Conta de Armazenamento](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. Na caixa de diálogo **Criar Conta de Armazenamento**, digite um nome exclusivo para a conta de armazenamento.

1. Escolha a mesma **Região** em que você criou seu aplicativo Serviço de Aplicativo ou uma região perto de você.

1. Selecione **Criar**.

   ![Criar Conta de Armazenamento](./media/webjobs-sdk-get-started/create-storage-account.png)

1. No nó **Armazenamento** no **Gerenciador de Servidores**, selecione a nova conta de armazenamento. Na janela **Propriedades**, selecione as reticências (**...** ) à direita do campo de valor **Cadeia de Conexão**.

   ![Reticências de Cadeia de Conexão](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Copie a cadeia de conexão e salve esse valor em algum lugar no qual você possa copiá-lo de novo imediatamente.

   ![Copiar cadeia de conexão](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-for-running-locally"></a>Configurar armazenamento para execução localmente

O WebJobs SDK procura a cadeia de conexão de Armazenamento na coleção Configurações de Aplicativo. Quando você executa localmente, ele procura esse valor no arquivo *App. config* ou nas variáveis de ambiente.

1. Adicione o seguinte XML ao arquivo *App. config*, imediatamente após a abertura da marca `<configuration>`.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

1. Substitua *{storage connection string}* pela cadeia de conexão que você copiou anteriormente.

   Mais adiante, você usará a cadeia de conexão novamente, quando você configurar o aplicativo Serviço de Aplicativo no Azure.

## <a name="test-locally"></a>Testar localmente

Nesta seção, você compila e executa o projeto localmente e dispara a função criando uma mensagem da fila.

1. Pressione Ctrl+F5 para executar o projeto.

   O console mostra que o tempo de execução encontrou sua função e está aguardando as mensagens de fila o dispararem.

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   ```

   Você poderá ver uma mensagem de aviso sobre uma configuração `ServicePointManager`. Para o teste que fará com este projeto, você poderá ignorar o aviso. Para obter mais informações sobre o aviso, consulte [Como usar o WebJobs SDK](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings).

1. Feche a janela do console.

1. No **Gerenciador de Servidores** no Visual Studio, expanda o nó para sua nova conta de armazenamento e, em seguida, clique com botão direito em **Filas**. 

1. Selecione **Criar fila**. 

1. Digite *queue* como o nome para a fila e selecione **OK**.

   ![Criar fila](./media/webjobs-sdk-get-started/create-queue.png)

1. Clique no botão direito do mouse para a nova fila e selecione **Exibir Fila**.

1. Selecione o ícone **Adicionar Mensagem**.

   ![Criar fila](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Na caixa de diálogo **Adicionar Mensagem**, digite *Olá, Mundo!* como o **Texto da mensagem**e, em seguida, selecione **OK**.

   ![Criar fila](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Execute o projeto novamente.

   Como você usou o atributo `QueueTrigger` na função `ProcessQueueMessage`, o tempo de execução de WeJobs SDK escuta de mensagens da fila quando ele é iniciado. Ele localiza uma nova mensagem de fila na fila denominada *queue* e chama a função.

   Devido à [retirada do exponencial de sondagem de fila](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), o tempo de execução poderá levar até 2 minutos para localizar a mensagem e chamar a função. Esse tempo de espera pode ser reduzido com a execução no [modo de desenvolvimento](webjobs-sdk-how-to.md#jobhost-development-settings).

  A saída do console se assemelha a esta:

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   info: Function[0]
         Hello World!
   info: Host.Results[0]
         Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   ```

1. Feche a janela do console.

## <a name="add-application-insights-logging"></a>Adicionar registro em log do Application Insights

Quando o projeto é executado no Azure, você não pode monitorar a execução da função exibindo a saída do console. A solução de monitoramento que recomendamos é [Application Insights](../application-insights/app-insights-overview.md). Para saber mais, consulte [Monitorar Azure Functions](../azure-functions/functions-monitoring.md).

Nesta seção, execute as seguintes tarefas para configurar o registro em log do Application Insights antes de implantar no Azure:

* Verifique se você tem um aplicativo Serviço de Aplicativo e uma instância do Application Insights para trabalhar.
* Configure o aplicativo Serviço de Aplicativo para usar a instância do Application Insights e a conta de armazenamento que você criou anteriormente.
* Configure o projeto para registro em log no Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Criar aplicativo Serviço de Aplicativo e a instância do Application Insights

1. Se você ainda não tiver um aplicativo Serviço de Aplicativo que você possa usar, [crie um](app-service-web-get-started-dotnet-framework.md).

1. Se você ainda não tiver um aplicativo Serviço de Aplicativo que você possa usar, [crie um](../application-insights/app-insights-create-new-resource.md). Defina **Tipo de Aplicativo** como **Geral**e ignore as seções após **Copiar a chave de instrumentação**.

1. Se você já tiver um recurso do Application Insights que deseja usar, [copie a chave de instrumentação](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Definir configurações de aplicativo 

1. Na **Gerenciador de Servidores** no Visual Studio, expanda o **serviço de aplicativo** nó sob **Azure**.

1. Expanda o grupo de recursos em que o aplicativo Serviço de Aplicativo está e clique com o botão direito do mouse nesse aplicativo.

1. Selecione **Exibir Configurações**.

1. Na caixa **Cadeias de Conexão**, adicione a seguinte entrada.

   |NOME  |Cadeia de conexão  |Tipo de Banco de Dados|
   |---------|---------|------|
   |AzureWebJobsStorage | {a cadeia de conexão de armazenamento que você copiou anteriormente}|Personalizado|
   
1. Se a caixa **Configurações do Aplicativo** não tiver uma chave de instrumentação do Application Insights, adicione uma que você copiou anteriormente. (A chave de instrumentação já pode estar lá, dependendo de como você criou o aplicativo Serviço de Aplicativo.)

   |NOME  |Valor  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {chave de instrumentação} |

1. Substitua *{chave de instrumentação}* pela chave de instrumentação do recurso do Application Insights que você está usando.

1. Clique em **Salvar**.

1. Adicione o seguinte XML ao arquivo *App. config*, imediatamente após a coleta das cadeias de conexão.

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

1. Substitua *{chave de instrumentação}* pela chave de instrumentação do recurso do Application Insights que você está usando.

   Adicionar esses dados ao aplicativo *App. config* permite que você teste a conexão do Application Insights quando executar o projeto localmente. 

1. Salve suas alterações.

### <a name="add-application-insights-logging-provider"></a>Adicionar provedor de registro em log do Application Insights

1. Instale a versão 2.x estável mais recente do pacote NuGet para o provedor de registro em log do Application Insights: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Aqui está o comando **Console do Gerenciador de Pacotes** para a versão 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Instale a versão 4.x estável mais recente do pacote NuGet para o gerenciador de configuração do .NET: `System.Configuration.ConfigurationManager`.

   Eis o comando do **Console do Gerenciador de Pacotes** para a versão 4.4.1:

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

1. Abra *Program.cs* e adicione uma instrução `using` para o Configuration Manager:

   ```csharp
   using System.Configuration;
   ```

1. No método `Main`, substitua o código pelo seguinte:

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

   Este código faz as seguintes alterações:

   * Adiciona um provedor de registro em log do Application Insights com [filtragem](webjobs-sdk-how-to.md#log-filtering) padrão; todas as informações e todos os logs de nível superior agora irão para o console e o Application Insights quando você estiver executando localmente. 
   * Coloca o objeto `LoggerFactory` em um bloco `using` para garantir que a saída do log seja liberada quando o host for encerrado. 

## <a name="test-application-insights-logging"></a>Testar registro em log do Application Insights

Nesta seção, você executa localmente de novo para verificar se os dados de registro em log agora estão indo para o Application Insights, bem como para o console.

1. Use **Gerenciador de Servidores** no Visual Studio para criar uma mensagem da fila, da mesma forma que fez [anteriores](#trigger-the-function-in-azure), exceto insira *Hello App Insights!* como o texto da mensagem.

1. Execute o projeto.

   O WebJobs SDK processa a mensagem da fila e você vê os logs na janela do console.

1. Feche a janela do console.

1. Abra o [portal do Azure](https://portal.azure.com/) e vá para o recurso do Application Insights.

1. Selecione **Pesquisar**.

   ![Selecionar Pesquisar](./media/webjobs-sdk-get-started/select-search.png)

1. Se você não vir a mensagem *Olá, App Insights!*, selecione **Atualizar** periodicamente por vários minutos. (Os logs não aparecem imediatamente porque leva algum tempo para o cliente do Application Insights liberar os logs que processa.)

   ![Logs no Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Feche a janela do console.

## <a name="deploy-as-a-webjob"></a>Implantar como um WebJob

Nesta seção, você implanta o projeto como um WebJob. Você o implanta em um aplicativo Serviço de Aplicativo [criado anteriormente](#create-app-service-app-and-application-insights-instance). Para testar seu código enquanto ele é executado no Azure, você disparará uma invocação de função criando uma mensagem da fila.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Publicar como Azure WebJob**.

1. Na caixa de diálogo **Adicionar Azure WebJob**, selecione **OK**.

   ![Adicionar Azure WebJob](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   O Visual Studio instala automaticamente um pacote NuGet para publicação do WebJob.

1. Na etapa **Perfil** do assistente **Publicar**, selecione **Serviço de Aplicativo do Microsoft Azure**.

   ![Caixa de diálogo Publicar](./media/webjobs-sdk-get-started/publish-dialog.png)

1. Na caixa de diálogo **Serviço de Aplicativo**, selecione **seu grupo de recursos > seu aplicativo Serviço de Aplicativo** e selecione **OK**.

   ![Caixa de diálogo Serviço de Aplicativo](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. Na etapa **Conexão** do assistente, selecione **Publicar**.

## <a name="trigger-the-function-in-azure"></a>Disparar a função no Azure

1. Verifique se você não está executando localmente (feche a janela de console se ainda estiver aberta). Caso contrário, a instância local poderá ser a primeira a processar as mensagens de fila que você criar.


1. Atualize a página **Fila** no Visual Studio, e a nova mensagem desaparecerá porque a função em execução no Serviço de Aplicativo do Azure a processou.

   > [!TIP]
   > Quando você estiver testando no Azure, use o [modo de desenvolvimento](webjobs-sdk-how-to.md#jobhost-development-settings) para garantir que uma função do gatilho de fila seja chamada imediatamente e evitar atrasos devido à [retirada do exponencial de sondagem de fila](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Exibir logs no Application Insights

1. Abra o [portal do Azure](https://portal.azure.com/) e vá para o recurso do Application Insights.

1. Selecione **Pesquisar**.

1. Se você não vir a mensagem *Olá, Azure!*, selecione **Atualizar** periodicamente por vários minutos.

   Você vê os logs da função em execução em um WebJob, incluindo o texto *Hello Azure!* que digitou na seção anterior.

## <a name="add-an-input-binding"></a>Escolher uma associação de entrada

As associações de entrada simplificam o código que lê dados. Neste exemplo, a mensagem da fila será um nome de blob, e você usará o nome do blob para localizar e ler um blob no Armazenamento do Microsoft Azure.

1. Em *Functions.cs*, substitua o método `ProcessQueueMessage` pelo seguinte código:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   Nesse código, `queueTrigger` é uma [expressão de associação](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns), o que significa que ele resolve para um valor diferente no tempo de execução.  No tempo de execução, ele tem o conteúdo da mensagem da fila.

1. Adicione um `using`:

   ```cs
   using System.IO;
   ```

1. Crie um contêiner de blob em sua conta de armazenamento.

    a. No **Gerenciador de Servidores** no Visual Studio, expanda o nó para sua conta de armazenamento, clique com botão direito **Blobs**e, em seguida, selecione **criar contêiner de Blob**.

   b. Na caixa de diálogo **Criar Contêiner de Blob**, digite *container* como o nome do contêiner e clique em **OK**.

1. Carregue o arquivo *Program.cs* para o contêiner de blob. (Esse arquivo é usado aqui como um exemplo; você pode carregar qualquer arquivo de texto e criar uma mensagem da fila com o nome do arquivo.)

    a. No **Gerenciador de Servidores**, clique duas vezes no nó para o contêiner que você acabou de criar.

   b. Na janela **Contêiner**, selecione o botão **Carregar**.

   ![Botão Carregar Blob](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Localize e selecione *Program.cs* e, em seguida, selecione **OK**.

1. Crie uma mensagem da fila na fila criada anteriormente, com *Program.cs* como o texto da mensagem.

   ![Mensagem da fila Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Execute o projeto.

   A mensagem da fila dispara a função, que lê o blob e registra seu comprimento. A saída do console se assemelha a esta:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Adicionar uma associação de saída

As associações de saía simplificam o código que grava dados. Este exemplo modifica o anterior escrevendo uma cópia de blob em vez de registrar em log seu tamanho.

1. Substitua o método `ProcessQueueMessage` pelo seguinte código:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Crie outra mensagem de fila com *Program.cs* como o texto da mensagem.

1. Execute o projeto.

   A mensagem da fila dispara a função, que lê o blob e registra seu comprimento e cria um novo blob. A saída do console é a mesma, mas quando você vai até a janela de contêiner de blob e seleciona **Atualizar**, é exibido um novo blob denominado *copy-Program.cs.*

## <a name="next-steps"></a>Próximas etapas

Este guia mostra como criar, executar e implantar um projeto do WebJobs SDK.

Para mostrar tudo o que entra em um projeto do WebJobs SDK, as instruções precisaram criar um projeto a partir do zero. No entanto, quando você criar seu próximo projeto, considere o uso do modelo **Azure WebJob** na categoria **Nuvem**. Este modelo cria um projeto com pacotes NuGet e o código de exemplo já configurado. Observe que o código de exemplo talvez precise ser alterado para usar a nova estrutura de registro em log.

Para obter mais informações, consulte [Como usar o WebJobs SDK](webjobs-sdk-how-to.md).
