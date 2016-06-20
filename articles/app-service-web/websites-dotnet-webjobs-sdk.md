<properties 
	pageTitle="O que é o SDK de Trabalhos Web do Azure" 
	description="Uma introdução ao SDK de Trabalhos Web do Azure. Explica o que é o SDK, cenários típicos em que ele é útil e exemplos de código." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2016" 
	ms.author="tdykstra"/>

# O que é o SDK de Trabalhos Web do Azure

## <a id="overview"></a>Visão geral

Este artigo explica o que é o SDK de Trabalhos Web, analisa alguns cenários comuns em que ele é útil e oferece uma visão geral de como usá-lo em seu código.

[WebJobs](websites-webjobs-resources.md) é um recurso do Serviço de Aplicativo do Azure que permite que você execute um programa ou script no mesmo contexto que um aplicativo Web, aplicativo de API ou aplicativo móvel. A finalidade do [WebJobs SDK](websites-webjobs-resources.md) é simplificar o código escrito para tarefas comuns que um WebJob pode executar, como o processamento de imagens, o processamento de filas, a agregação de RSS, a manutenção de arquivos e o envio de emails. O SDK do WebJobs tem recursos internos para trabalhar com o Armazenamento do Azure e o Barramento de Serviço, para o agendamento de tarefas e o tratamento de erros e de muitos outros cenários comuns. Além disso, ele foi projetado para ser extensível. O [SDK do Web Jobs é um software livre](https://github.com/Azure/azure-webjobs-sdk/), e há um [repositório de softwares livres para extensões](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

O SDK de Trabalhos Web inclui os seguintes componentes:

* **Pacotes NuGet**. Os pacotes NuGet adicionados a um projeto de Aplicativo de Console do Visual Studio fornecem uma estrutura usada pelo código ao decorar os métodos com atributos do WebJobs SDK.
  
* **Painel**. Parte do SDK de Trabalhos Web está incluído no Serviço de Aplicativo do Azure e oferece monitoramento e diagnóstico avançados para os programas que usam pacotes NuGet. Você não precisa escrever o código para usar esses recursos de monitoramento e diagnóstico.

## <a id="scenarios"></a>Cenários

Aqui estão alguns cenários típicos que é possível processar mais facilmente com o SDK de Trabalhos Web do Azure:

* Processamento de imagens ou outro trabalho com uso intensivo da CPU. Uma característica comum de sites é a capacidade de carregar imagens ou vídeos. Você pode querer manipular o conteúdo após o upload, mas sem fazer o usuário esperar enquanto faz isso.

* Processamento de filas. Uma maneira comum de comunicação de um front-end da Web com um serviço back-end é usar filas. Quando precisa fazer o trabalho, o site envia uma mensagem para uma fila. Um serviço back-end efetua pull das mensagens da fila e faz o trabalho. Você pode usar filas para processamento de imagens: por exemplo, depois que o usuário carregar vários arquivos, coloque os nomes de arquivos em uma mensagem de fila a ser selecionada pelo back-end para processamento. Ou você poderia usar filas para melhorar a resposta do site. Por exemplo, em vez de escrever diretamente para um banco de dados SQL, escreva para uma fila, informe ao usuário quando terminar e deixe o serviço back-end processar o trabalho do banco de dados relacional de alta latência. Para obter um exemplo de processamento de fila com processo de imagens, consulte o tutorial [Introdução ao SDK de Trabalhos Web](websites-dotnet-webjobs-sdk-get-started.md).

* Agregação de RSS. Se tivesse um site que mantivesse uma lista de RSS feeds, você poderia efetuar pull em todos os artigos dos feeds em um processo em segundo plano.

* Manutenção de arquivos, como agregar ou limpar arquivos de log. Você pode ter arquivos de log criados por diversos sites ou para períodos de tempo separados que deseja combinar para executar trabalhos de análise neles. Ou, talvez, você queira agendar uma tarefa a ser executada semanalmente para limpar arquivos de log antigos.

* Ingresso em Tabelas do Azure. Você pode ter arquivos armazenados e blobs, e desejar analisá-los e armazenar os dados nas tabelas. A função de ingresso pode consistir em escrever muitas linhas (milhões, em alguns casos), e o SDK de Trabalhos Web permite implementar facilmente essa função. O SDK também oferece monitoramento em tempo real de indicadores de progresso, como o número de linhas escritas na tabela.

* Outras tarefas de execução longa que você deseja executar em um thread em segundo plano, como [enviar e-mails](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure).

* As tarefas que você deseja executar em uma agenda, como executar uma operação de backup todas as noites.

Em muitos desses cenários, talvez você queira dimensionar um aplicativo Web para execução em várias VMs, o que executaria diversos Trabalhos Web simultaneamente. Em alguns cenários, isso poderia resultar nos mesmos dados sendo processados várias vezes, mas isso não será um problema quando você usar os gatilhos internos de fila, de blob e do Barramento de Serviços do SDK do WebJobs. O SDK garante que suas funções serão processadas apenas uma vez para cada mensagem ou blob.

O WebJobs SDK também facilita lidar com cenários de tratamento de erros comuns. Você pode configurar alertas para enviar notificações quando uma função falhar e pode definir tempos limite para que uma função seja cancelada automaticamente se não for concluída dentro de um limite de tempo especificado.

## <a id="code"></a> Exemplos de código

O código para processar tarefas típicas que funcionam com o Armazenamento do Azure é simples. No método `Main` do Aplicativo de Console, você cria um objeto `JobHost` que coordena as chamadas para métodos que você escreve. A estrutura do SDK de WebJobs sabe quando chamar os métodos e quais valores de parâmetro usar baseada nos atributos do SDK de WebJobs que você usou neles. O SDK fornece *gatilhos*, que especificam que condições causam a função a ser chamada, e *associadores*, que especificam como obter informações dentro e fora dos parâmetros do método.

Por exemplo, o atributo [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) faz com que uma função seja chamada quando uma mensagem é recebida em uma fila, e se o formato de mensagem for JSON para uma matriz de bytes ou um tipo personalizado, a mensagem será desserializada automaticamente. O atributo [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) dispara um processo sempre que um novo blob é criado em uma conta de Armazenamento do Azure.

Este é um programa simples que consulta uma fila e cria um blob para cada mensagem de fila recebida:

		public static void Main()
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
		{
		    writer.WriteLine(inputText);
		}

O objeto `JobHost` é um contêiner de um conjunto de funções em segundo plano. O objeto `JobHost` monitora as funções, observa eventos que as acionam e executa as funções quando ocorrem os eventos de acionamento. É possível chamar um método `JobHost` para indicar se deseja que o processo de contêiner seja executado no thread atual ou em um thread em segundo plano. No exemplo, o método `RunAndBlock` executa continuamente o processo no thread atual.

Como o método `ProcessQueueMessage` neste exemplo tem um atributo `QueueTrigger`, o gatilho dessa função é a recepção de uma nova mensagem da fila. O objeto `JobHost` observa novas mensagens na fila especificada ("webjobsqueue" neste exemplo) e quando uma é encontrada, ele chama `ProcessQueueMessage`.

O atributo `QueueTrigger` associa o parâmetro `inputText` ao valor da mensagem da fila. E o atributo `Blob` associa um objeto `TextWriter` a um blob chamado “blobname” em um contêiner chamado "containername".

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
		    [Blob("containername/blobname")]TextWriter writer)

Em seguida, a função usa esses parâmetros para gravar o valor da mensagem da fila no blob:

		writer.WriteLine(inputText);

Os recursos de gatilho e de associador do WebJobs SDK simplificam muito o código que você precisa escrever. O código de baixo nível necessário para processar filas, blobs ou arquivos, ou para iniciar tarefas agendadas, é feito para você pela estrutura do WebJobs SDK. Por exemplo, a estrutura cria filas que ainda não existem, abre a fila, lê mensagens da fila, exclui mensagens da fila quando o processamento é concluído, cria contêineres de blob que ainda não existem, grava em blobs e assim por diante.

O exemplo de código a seguir mostra uma variedade de gatilhos em um WebJob: `QueueTrigger`, `FileTrigger`, `WebHookTrigger` e `ErrorTrigger`.

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

        public static void ErrorMonitor(
        [ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
        [SendGrid(
            To = "admin@emailaddress.com",
            Subject = "Error!")]
         SendGridMessage message)
        {
            // log last 5 detailed errors to the Dashboard
            log.WriteLine(filter.GetDetailedMessage(5));
            message.Text = filter.GetDetailedMessage(1);
        }
    }
```

## Agendamento <a id="schedule"></a>

O atributo `TimerTrigger` permite disparar funções para execução em um agendamento. Você pode agendar um WebJob como um todo usando o Azure ou agendar funções individuais de um WebJob usando o WebJobs SDK `TimerTrigger`. Aqui está um exemplo de código.

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

Para mais código de exemplo, confira [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) no repositório azure-webjobs-sdk-extensions em GitHub.com.

## Extensibilidade

Você não está limitado à funcionalidade interna; o WebJobs SDK permite que você escreva associadores e gatilhos personalizados. Por exemplo, você pode escrever gatilhos para eventos de cache e agendamentos periódicos. Um [repositório de software livre](https://github.com/Azure/azure-webjobs-sdk-extensions) contém um [guia detalhado sobre extensibilidade do WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) e código de exemplo para ajudar você a escrever seus próprios associadores e gatilhos.

## <a id="workerrole"></a> Usando o SDK de Trabalhos Web fora de Trabalhos Web

Um programa que usa o SDK de Trabalhos Web é um Aplicativo de Console padrão e pode ser executado em qualquer lugar, não precisa ser executado como um Trabalho Web. Você pode testar o programa localmente no seu computador de desenvolvimento. Na produção, você pode executá-lo em uma função de trabalho do Serviço de Nuvem ou em um serviço do Windows, se preferir um desses ambientes.

No entanto, o painel só está disponível como uma extensão para um aplicativo web do Serviço de Aplicativo do Azure. Se desejar a execução fora de um Trabalho Web e ainda usar o Painel, você poderá configurar um aplicativo Web para usar a mesma conta de armazenamento que é referenciada pela cadeia de conexão do Painel do SDK de Trabalhos Web. Assim, o Painel de Trabalhos Web desse aplicativo Web mostrará dados sobre a execução da função do seu programa, que está em operação em outro lugar. Você pode acessar o painel usando a URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions. Para obter mais informações, consulte [Obtendo um painel para desenvolvimento local com o SDK de Trabalhos Web](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), mas observe que o post do blog mostra um nome da cadeia de conexão antigo.

## <a id="nostorage"></a>Recursos do painel de controle

O WebJobs SDK oferece várias vantagens, mesmo se você não usar os disparadores ou associadores do WebJobs SDK:

* Você pode chamar funções por meio do Painel.
* Você pode repetir a reprodução de funções por meio do Painel.
* Você pode exibir logs no Painel, vinculados ao trabalho Web específico (logs de aplicativo, escritos usando Console.Out, Console.Error, rastreamento etc.) ou vinculados à invocação de função específica que os gerou (logs escritos usando um objeto `TextWriter` que o SDK passa para a função como um parâmetro). 

Para obter mais informações, consulte [Como chamar manualmente uma função](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) e [Como escrever logs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)

## <a id="nextsteps"></a>Próximas etapas

Para obter mais informações sobre o SDK de Trabalhos Web, consulte [Recursos recomendados para Trabalhos Web do Azure](http://go.microsoft.com/fwlink/?linkid=390226).

Para saber mais sobre os aprimoramentos mais recentes do WebJobs SDK, confira as [Notas de versão](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes).
 

<!---HONumber=AcomDC_0608_2016-->