<properties pageTitle="O que é o SDK de Trabalhos Web do Azure" metaKeywords="Azure Azure WebJobs SDK, Azure storage, Azure queues, Azure tables" description="An introduction to the Azure WebJobs SDK. Explains what the SDK is, typical scenarios it is useful for, and code samples." metaCanonical="" services="web-sites,storage" documentationCenter=".NET" title="What is the Azure WebJobs SDK" authors="tdykstra" solutions="" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/12/2014" ms.author="tdykstra" />

# O que é o SDK de Trabalhos Web do Azure

Este artigo explica o que é o SDK de Trabalhos Web, analisa alguns cenários comuns em que ele é útil e oferece uma visão geral de como usá-lo em seu código.

## Sumário

- [Visão geral](#overview)
- [Cenários](#scenarios)
- [Exemplos de código](#code)
- [Usando o SDK de Trabalhos Web fora de Trabalhos Web](#workerrole)
- [Usando o SDK de Trabalhos Web para chamar qualquer função](#nostorage)
- [Próximas etapas](#nextsteps)

## <a id="overview"></a>Visão geral

[Trabalhos Web](/pt-br/documentation/articles/web-sites-create-web-jobs/) é um recurso dos sites do Azure que permite executar um programa ou um script no mesmo contexto do site. A finalidade do SDK de Trabalhos Web é simplificar a tarefa de escrever um código que seja executado como um Trabalho Web e que funcione com filas, blobs e tabelas do Armazenamento do Azure, além de filas do Barramento de Serviço.

O SDK de Trabalhos Web inclui os seguintes componentes:

* **Pacotes NuGet**. Os pacotes NuGet adicionados a um projeto de Aplicativo de console do Visual Studio fornecem uma estrutura usada pelo código para funcionar com o serviço de Armazenamento do Azure ou as filas do Barramento de Serviço.   
  
* **Painel**. Parte do SDK de Trabalhos Web está incluído em sites Web do Azure e oferece monitoramento e diagnóstico sofisticados para os programas que usam pacotes NuGet. Você não precisa escrever o código para usar esses recursos de monitoramento e diagnóstico.

## <a id="scenarios"></a>Cenários

Aqui estão alguns cenários típicos que é possível processar mais facilmente com o SDK de Trabalhos Web do Azure:

* Processamento de imagens ou outro trabalho com uso intensivo da CPU. Uma característica comum de sites é a capacidade de carregar imagens ou vídeos. Você pode querer manipular o conteúdo após o upload, mas sem fazer o usuário esperar enquanto faz isso.

* Processamento de filas. Uma maneira comum de comunicação de um front-end da Web com um serviço back-end é usar filas. Quando precisa fazer o trabalho, o site envia uma mensagem para uma fila. Um serviço back-end efetua pull das mensagens da fila e faz o trabalho. Você poderia usar filas no processamento de imagens: por exemplo, depois que o usuário carregar vários arquivos, coloque os nomes dos arquivos em uma mensagem na fila a ser retirada pelo back-end para processamento. Ou você poderia usar filas para melhorar a resposta do site. Por exemplo, em vez de escrever diretamente para um banco de dados SQL, escreva para uma fila, informe ao usuário quando terminar e deixe o serviço back-end processar o trabalho do banco de dados relacional de alta latência. Para obter um exemplo de fila de processamento com o processo de imagem, consulte o [tutorial Introdução ao SDK de Trabalhos Web ](../websites-dotnet-webjobs-sdk-get-started/).

* Agregação de RSS. Se tivesse um site que mantivesse uma lista de RSS feeds, você poderia efetuar pull em todos os artigos dos feeds em um processo em segundo plano.

* Manutenção de arquivos, como agregar ou limpar arquivos de log.  Você pode ter arquivos de log criados por diversos sites ou para períodos de tempo separados que deseja combinar para executar trabalhos de análise neles. Ou, talvez, você queira agendar uma tarefa a ser executada semanalmente para limpar arquivos de log antigos.

* Ingresso em Tabelas do Azure. Você pode ter arquivos armazenados e blobs, e desejar analisá-los e armazenar os dados nas tabelas. A função de ingresso pode consistir em escrever muitas linhas (milhões, em alguns casos), e o SDK de Trabalhos Web permite implementar facilmente essa função. O SDK também oferece monitoramento em tempo real de indicadores de progresso, como o número de linhas escritas na tabela.

* Outras tarefas de execução longa que deseja executar em um thread em segundo plano, como [enviar emails](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure). 

## <a id="code"></a> Exemplos de código

O código para processar tarefas típicas que funcionam com o Armazenamento do Azure é simples. Em um Aplicativo do Console, você escreve métodos para as tarefas em segundo plano que deseja executar e as decora com atributos do SDK de Trabalhos Web. O método `Main` cria um objeto `JobHost` que coordena as chamadas para métodos escritos por você. A estrutura do SDK de Trabalhos Web sabe quando chamar os métodos com base nos atributos do SDK de Trabalhos Web que você usou neles. 

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

O objeto `JobHost` é um contêiner de um conjunto de funções em segundo plano. O objeto `JobHost` monitora as funções, observa eventos que as disparam e executa as funções quando ocorrem os eventos de gatilho. É possível chamar um método `JobHost` para indicar se deseja que o processo de contêiner seja executado no thread atual ou em um thread em segundo plano. No exemplo, o método `RunAndBlock` executa continuamente o processo no thread atual.

Como o método `ProcessQueueMessage` neste exemplo tem um atributo `QueueTrigger`, o gatilho dessa função é a recepção de uma nova mensagem da fila. O objeto `JobHost` observa novas mensagens na fila especificada ("webjobsqueue" neste exemplo) e quando uma é encontrada, ele chama `ProcessQueueMessage`. O atributo `QueueTrigger` também notifica a estrutura para associar o parâmetro `inputText` ao valor da mensagem da fila: 

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] <mark>string inputText</mark>, 
    [Blob("containername/blobname")]TextWriter writer)</pre>

A estrutura também associa um objeto `TextWriter` a um blob chamado "blobname" em um contêiner chamado "containername":

<pre class="prettyprint">public static void ProcessQueueMessage([QueueTrigger(&quot;webjobsqueue&quot;)]] string inputText, 
    <mark>[Blob("containername/blobname")]TextWriter writer</mark>)</pre>

Em seguida, a função usa esses parâmetros para gravar o valor da mensagem da fila no blob:

		writer.WriteLine(inputText);

Os recursos de gatilho e de associador do SDK de Trabalhos Web simplificam muito o código que você precisa escrever para trabalhar com objetos do Armazenamento do Azure e filas de Barramento de Serviço. O código de nível inferior obrigatório para processar a fila e o blob é feito por você pela estrutura do SDK de Trabalhos Web. A estrutura que cria filas que ainda não existem, abre a fila, lê as mensagens da fila, exclui mensagens da fila quando o processamento é concluído, cria contêineres de blob que ainda não existem, grava em blobs, etc.

O SDK de Trabalhos Web oferece muitas maneiras de trabalhar com o Armazenamento do Azure. Por exemplo, se o parâmetro decorado por você com o atributo `QueueTrigger` for uma matriz de bytes ou um tipo personalizado, ele será desserializado automaticamente de JSON. É possível usar um atributo `BlobTrigger` para disparar um processo sempre que um novo blob é criado em uma conta do Armazenamento do Azure. (Observe que, embora `QueueTrigger` localize novas mensagens de fila em poucos segundos, `BlobTrigger` pode levar até 20 minutos para detectar um novo blob. `BlobTrigger` procura blobs sempre que `JobHost` inicia e verifica periodicamente os logs de armazenamento do Azure para detectar novos blobs.)

## <a id="workerrole"></a>Usando o SDK de Trabalhos Web fora de Trabalhos Web

Um programa que usa o SDK de Trabalhos Web é um Aplicativo de Console padrão e pode ser executado em qualquer lugar, não precisa ser executado como um Trabalho Web. Você pode testar o programa localmente no seu computador de desenvolvimento. Na produção, você pode executá-lo em uma função de trabalho do Serviço de Nuvem ou em um serviço do Windows, se preferir um desses ambientes. 

Porém, o painel só permanece disponível como uma extensão de site para um Site do Azure. Se você desejar a execução fora de um Trabalho Web e ainda usar o Painel, pode configurar um site da Web do Azure para usar a mesma conta de armazenamento que é referenciada pela cadeia de conexão do Painel do SDK de Trabalhos Web. Assim, o Painel de Trabalhos Web desse site mostrará dados sobre a execução da função do seu programa, que está em operação em outro lugar. Em seguida, você pode chegar ao Painel usando a URL https://*{websitename}*.scm.azurewebsites.net/azurejobs/#/functions. Para obter mais informações, consulte [Obtendo um painel para o desenvolvimento local com o SDK de Trabalhos Web](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), mas observe que a postagem do blog mostra um nome de cadeia de conexão antiga. 

## <a id="nostorage"></a>Usando o SDK de Trabalhos Web para chamar qualquer função

O SDK de Trabalhos Web oferece várias vantagens, mesmo que você não precise trabalhar diretamente com filas, tabelas ou blobs de Armazenamento do Azure ou filas de Barramento de Serviço:

* Você pode chamar funções por meio do Painel.
* Você pode repetir a reprodução de funções por meio do Painel.
* É possível visualizar logs no Painel, vinculados ao Trabalho Web específico (logs do aplicativo) ou vinculados à chamada da função específica que os gerou (logs do parâmetro `TextWriter`). 

* Para obter mais informações, consulte [Como chamar uma função manualmente](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#manual) e [Como gravar logs](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#logs) 

## <a id="nextsteps"></a>Próximas etapas

Para obter mais informações sobre o SDK de Trabalhos Web, consulte [Recursos recomendados de Trabalhos Web do Azure](http://go.microsoft.com/fwlink/?linkid=390226).
