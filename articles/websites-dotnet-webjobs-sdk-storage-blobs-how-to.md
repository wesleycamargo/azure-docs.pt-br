<properties 
	pageTitle="Como usar o armazenamento de blobs do Azure com o SDK WebJobs" 
	description="Como usar o armazenamento de blobs do Azure com o SDK WebJobs. Disparar um processo quando um novo blob aparece em um contêiner e o identificador 'poison blobs'." 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Como usar o armazenamento de blobs do Azure com o SDK WebJobs

Este guia fornece exemplos de código C# que mostram como disparar um processo quando um blob do Azure é criado ou atualizado. Os exemplos de código usam [SDK WebJobs](websites-dotnet-webjobs-sdk.md) versão 1. x.

Para obter exemplos de código que mostram como criar blobs, consulte [Como usar armazenamento de fila do Azure com o SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 
		
O guia pressupõe que você saiba [como criar um projeto WebJob no Visual Studio com conexão de cadeia de caracteres que apontam para sua conta de armazenamento](websites-dotnet-webjobs-sdk-get-started.md).

## Sumário

-   [Como disparar uma função quando um blob é criado ou atualizado](#trigger)
	- Espaço reservado único para nome de blob com extensão
	- Espaços reservados de nome e extensão do blob separados
-   [Tipos de BlobTrigger que funcionam com](#types)
-   [Obtendo o conteúdo do blob de texto associando a cadeia de caracteres](#string)
-   [Obtendo conteúdo de blob serializado usando ICloudBlobStreamBinder](#icbsb)
-   [Como manipular blobs suspeitos](#poison)
-   [Algoritmo de sondagem de blob](#polling)
-   [Recebimentos de blob](#receipts)
-   [Tópicos relacionados abordados no artigo de filas](#queues)
-   [Próximas etapas](#nextsteps)

## <a id="trigger"></a>Como disparar uma função quando um blob é criado ou atualizado

Esta seção mostra como usar o atributo `BlobTrigger`. 

### Espaço reservado único para nome de blob com extensão  

O exemplo de código a seguir copia os blobs de texto que aparecem no contêiner *input* para o contêiner *output*:

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("output/{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

O construtor de atributo tem um parâmetro de cadeia de caracteres que especifica o nome do contêiner e um espaço reservado para o nome do blob. Neste exemplo, se um blob denominado *Blob1.txt* é criado no contêiner *input*, a função cria um blob denominado *Blob1.txt* no contêiner *output*. 

Você pode especificar um padrão de nome com o espaço reservado do nome do blob, conforme mostrado no exemplo de código a seguir:

		public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
		    [Blob("output/copy-{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Esse código copia somente os blobs que têm nomes que começam com "original-". Por exemplo, *original-Blob1.txt* no contêiner *input* é copiado para *copy-Blob1.txt* no contêiner *output*.

Se você precisar especificar um padrão de nome para nomes de blob que têm chaves no nome, clique duas vezes nas chaves. Por exemplo, se você deseja localizar blobs no contêiner *images* que têm nomes como este:

		{20140101}-soundfile.mp3

use este para o padrão:

		images/{{20140101}}-{name}

No exemplo, o valor do espaço reservado *name* seria *soundfile.mp3*. 

### Espaços reservados de nome e extensão do blob separados

O exemplo de código a seguir altera a extensão de arquivo à medida que ele copia blobs que aparecem no contêiner *input* para o contêiner *output*. O código registra a extensão do blob *input* e define a extensão do blob *output* para *.txt*.

		public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
		    [Blob("output/{name}.txt")] out string output,
		    string name,
		    string ext,
		    TextWriter logger)
		{
		    logger.WriteLine("Blob name:" + name);
		    logger.WriteLine("Blob extension:" + ext);
		    output = input.ReadToEnd();
		}

## <a id="types"></a>Tipos que você pode associar aos blobs

Você pode usar o atributo `BlobTrigger` nos seguintes tipos:

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* Outros tipos desserializados pelo [ICloudBlobStreamBinder](#icbsb) 

Se você quiser trabalhar diretamente com a conta de armazenamento do Azure, você também pode adicionar um parâmetro `CloudStorageAccount` à assinatura do método.

## <a id="string"></a>Obtendo o conteúdo do blob de texto associando a cadeia de caracteres

Se os blobs de texto são esperados, `BlobTrigger` pode ser aplicado a um parâmetro `string`. O exemplo de código a seguir associa um blob de texto para um parâmetro `string` denominado `logMessage`. A função usa esse parâmetro para gravar os conteúdos do blob no painel SDK WebJobs. 
 
		public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
		    string name, 
		    TextWriter logger)
		{
		     logger.WriteLine("Blob name: {0}", name);
		     logger.WriteLine("Content:");
		     logger.WriteLine(logMessage);
		}

## <a id="icbsb"></a>Obtendo conteúdo de blob serializado usando ICloudBlobStreamBinder

O exemplo de código a seguir usa uma classe que implementa `ICloudBlobStreamBinder` para habilitar o atributo `BlobTrigger` para associar um blob para o tipo `WebImage`.

		public static void WaterMark(
		    [BlobTrigger("images3/{name}")] WebImage input,
		    [Blob("images3-watermarked/{name}")] out WebImage output)
		{
		    output = input.AddTextWatermark("WebJobs SDK", 
		        horizontalAlign: "Center", verticalAlign: "Middle",
		        fontSize: 48, opacity: 50);
		}
		public static void Resize(
		    [BlobTrigger("images3-watermarked/{name}")] WebImage input,
		    [Blob("images3-resized/{name}")] out WebImage output)
		{
		    var width = 180;
		    var height = Convert.ToInt32(input.Height * 180 / input.Width);
		    output = input.Resize(width, height);
		}

O código de ligação `WebImage` é fornecido em uma classe `WebImageBinder` que deriva de `ICloudBlobStreamBinder`.

		public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
		{
		    public Task<WebImage> ReadFromStreamAsync(Stream input, 
		        System.Threading.CancellationToken cancellationToken)
		    {
		        return Task.FromResult<WebImage>(new WebImage(input));
		    }
		    public Task WriteToStreamAsync(WebImage value, Stream output,
		        System.Threading.CancellationToken cancellationToken)
		    {
		        var bytes = value.GetBytes();
		        return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
		    }
		}

## <a id="poison"></a>Como manipular blobs suspeitos

Quando uma função `BlobTrigger` falhar, o SDK chama novamente, caso a falha for causada por um erro transitório. Se a falha é causada pelo conteúdo do blob, a função falhará toda vez que ele tentar processar o blob. Por padrão, o SDK chama uma função até 5 vezes para um determinado blob. Se a quinta vez falhar, o SDK adiciona uma mensagem para uma fila denominada *webjobs-blobtrigger-poison*.

O número máximo de novas tentativas é configurável. A mesma configuração [MaxDequeueCounté](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#configqueue) usada para tratamento de blob suspeitos e manipulação de mensagens em fila suspeitas. 

A mensagem da fila para blobs suspeitos é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *{WebJob name}*.Funções.*{Function name}*, por exemplo: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

No exemplo de código a seguir, a função `CopyBlob` tem código que faz com que falhe sempre que for chamado. Depois que o SDK faz a chamada para o número máximo de tentativas, será criada uma mensagem na fila de blob suspeito e essa mensagem é processada pela função `LogPoisonBlob`. 

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("textblobs/output-{name}")] out string output)
		{
		    throw new Exception("Exception for testing poison blob handling");
		    output = input.ReadToEnd();
		}
		
		public static void LogPoisonBlob(
		[QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
		    TextWriter logger)
		{
		    logger.WriteLine("FunctionId: {0}", message.FunctionId);
		    logger.WriteLine("BlobType: {0}", message.BlobType);
		    logger.WriteLine("ContainerName: {0}", message.ContainerName);
		    logger.WriteLine("BlobName: {0}", message.BlobName);
		    logger.WriteLine("ETag: {0}", message.ETag);
		}

O SDK automaticamente desserializa a mensagem JSON. Aqui está a classe `PoisonBlobMessage`: 

		public class PoisonBlobMessage
		{
		    public string FunctionId { get; set; }
		    public string BlobType { get; set; }
		    public string ContainerName { get; set; }
		    public string BlobName { get; set; }
		    public string ETag { get; set; }
		}

### <a id="polling"></a>Algoritmo de sondagem de blob

O SDK WebJobs examina todos os contêineres especificados pelos atributos `BlobTrigger` ao iniciar o aplicativo. Em uma conta de armazenamento grande essa verificação pode levar algum tempo, portanto, pode ser um pouco antes de novos blobs serem encontrados e as funções `BlobTrigger` serem executadas.

Para detectar blobs novos ou alterados após a inicialização do aplicativo, o SDK lê periodicamente a partir dos logs de armazenamento de blob. Os logs de blob são armazenados em buffer e apenas fisicamente gravados a cada 10 minutos ou mais, então pode haver atraso significativo após um blob ser criado ou atualizado antes da função correspondente `BlobTrigger` ser executada. 

Há uma exceção para blobs que você cria usando o atributo `Blob`. Quando o SDK WebJobs cria um novo blob, ele passa o novo blob imediatamente para qualquer função `BlobTrigger` correspondente. Portanto, se você tiver uma cadeia de entradas e saídas de blob, o SDK pode processá-la com eficiência. Mas se você quiser executar em baixa latência as funções de processamento do blob para blobs que são criados ou atualizados por outros meios, é recomendável usar `QueueTrigger` em vez de `BlobTrigger`.

### <a id="receipts"></a>Recebimentos de blob

O SDK WebJobs garante que nenhuma função `BlobTrigger` é chamada mais de uma vez para o mesmo blob novo ou atualizado. Ele faz isso mantendo *blob receipts* para determinar se uma determinada versão de blob foi processada.

Os recebimentos de blob são armazenados em um contêiner denominado *azure-webjobs-hosts* na conta de armazenamento do Azure especificada pela cadeia de caracteres de conexão AzureWebJobsStorage. Um recebimento de blob tem as seguintes informações:

* A função que foi chamada para o blob ("*{WebJob name}*.Funções.*{Function name}*", por exemplo: "WebJob1.Functions.CopyBlob")
* O nome do contêiner
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome de blob
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Se quiser forçar o reprocessamento de um blob, você poderá excluir manualmente o recebimento de blob para esse blob a partir do contêiner *azure-webjobs-hosts*.

## <a id="queues"></a>Tópicos relacionados abordados no artigo de filas

Para obter informações sobre como lidar com processamento de blob disparado por uma mensagem da fila, ou cenários SDK WebJobs não específicos para o processamento de blob, consulte [Como usar o armazenamento de fila do Azure com o SDK WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

Tópicos relacionados abordados nesse artigo incluem o seguinte:

* Funções assíncronas
* Várias instâncias
* Desligamento normal
* Use atributos de SDK WebJobs no corpo de uma função
* Defina as cadeias de conexão do SDK no código.
* Defina valores para parâmetros do construtor do SDK WebJobs no código
* Configure `MaxDequeueCount` para manipulação de blob suspeitos.
* Dispare uma função manualmente
* Grave logs

## <a id="nextsteps"></a>Próximas etapas

Este guia fornece exemplos de código que mostram como lidar com cenários comuns para trabalhar com blobs do Azure. Para obter mais informações sobre como usar os Trabalhos Web do Azure e o SDK de Trabalhos Web, consulte [Trabalhos Web do Azure - Recursos recomendados](http://go.microsoft.com/fwlink/?linkid=390226).



<!--HONumber=42-->
