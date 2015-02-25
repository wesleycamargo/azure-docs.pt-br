<properties 
	pageTitle="Como usar o armazenamento de tabela do Azure com o SDK WebJobs" 
	description="Saiba como usar o armazenamento de tabela do Azure com o SDK WebJobs. Criar tabelas, adicionar entidades para tabelas e ler as tabelas existentes." 
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

# Como usar o armazenamento de tabela do Azure com o SDK WebJobs

Este guia fornece exemplos de código C# que mostram como ler e gravar as tabelas de armazenamento do Azure usando [SDK WebJobs](../websites-dotnet-webjobs-sdk/) versão 1. x.

O guia pressupõe que você saiba [como criar um projeto WebJob no Visual Studio com conexão de cadeia de caracteres que apontam para sua conta de armazenamento](../websites-dotnet-webjobs-sdk-get-started/).
		
Alguns dos trechos de código mostram o atributo `Table` usado nas funções que são [chamada manualmente](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#manual), ou seja, não usando um dos atributos de gatilho. 

## Sumário

-   [Como adicionar entidades em uma tabela](#ingress)
-   [Monitoramento em tempo real](#monitor)
-   [Como ler várias entidades de uma tabela](#multiple)
-   [Como ler uma única entidade de uma tabela](#readone)
-   [Como usar a API de armazenamento .NET diretamente para trabalhar com uma tabela](#readone)
-   [Tópicos relacionados abordados no artigo de instruções de filas](#queues)
-   [Próximas etapas](#nextsteps)

## <a id="ingress"></a>Como adicionar entidades em uma tabela

Para adicionar entidades em uma tabela, use o atributo `Table` com um parâmetro `ICollector<T>` ou `IAsyncCollector<T>` onde `T` specifies the schema of the entities you want to add. The attribute constructor takes a string parameter that specifies the name of the table. 

The following code sample adds `Person`entidades em uma tabela nomeada *Ingress*.

		[NoAutomaticTrigger]
		public static void IngressDemo(
		    [Table("Ingress")] ICollector<Person> tableBinding)
		{
		    for (int i = 0; i < 100000; i++)
		    {
		        tableBinding.Add(
		            new Person() { 
		                PartitionKey = "Test", 
		                RowKey = i.ToString(), 
		                Name = "Name" }
		            );
		    }
		}

Geralmente o tipo que você usa com `ICollector` deriva de `TableEntity` ou implementa `ITableEntity`, mas ele não precisa. Ums das seguintes classes `Person` funcionam com o código mostrado no método anterior `Ingress`.

		public class Person : TableEntity
		{
		    public string Name { get; set; }
		}

		public class Person
		{
		    public string PartitionKey { get; set; }
		    public string RowKey { get; set; }
		    public string Name { get; set; }
		}

Se você quiser trabalhar diretamente com a API de armazenamento do Azure, você pode adicionar um parâmetro `CloudStorageAccount` à assinatura do método.

## <a id="monitor"></a>Monitoramento em tempo real

Como as funções de entrada de dados geralmente processam grandes volumes de dados, o painel do SDK WebJobs fornece dados de monitoramento em tempo real. A seção **Log de invocação** informa se a função ainda está em execução.

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

A página **Detalhes de invocação** relata o progresso da função (número de entidades escritas) enquanto ela está sendo executada e lhe dá a oportunidade para anulá-la. 

![Ingress function running](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

Quando a função for concluída, a página **Detalhes de invocação** relata o número de linhas gravadas.

![Ingress function finished](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a>Como ler várias entidades de uma tabela

Para ler uma tabela, use o atributo `Table` com um parâmetro `IQueryable<T>` onde digita-se `T` derives from `TableEntity` ou implementa-se `ITableEntity`.

O exemplo de código a seguir lê e registra todas as linhas da tabela `Ingress`:
 
		public static void ReadTable(
		    [Table("Ingress")] IQueryable<Person> tableBinding,
		    TextWriter logger)
		{
		    var query = from p in tableBinding select p;
		    foreach (Person person in query)
		    {
		        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}", 
		            person.PartitionKey, person.RowKey, person.Name);
		    }
		}

### <a id="readone"></a>Como ler uma única entidade de uma tabela

Há um construtor de atributo `Table` com dois parâmetros adicionais que permitem que você especifique a chave de partição e a chave de linha quando você desejar associar a uma entidade de tabela única.

O exemplo de código a seguir lê uma linha de tabela para uma entidade `Person` com base nos valores da chave de partição e da chave de linha recebidos em uma mensagem da fila:  

		public static void ReadTableEntity(
		    [QueueTrigger("inputqueue")] Person personInQueue,
		    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
		    TextWriter logger)
		{
		    if (personInTable == null)
		    {
		        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
		                personInQueue.PartitionKey, personInQueue.RowKey);
		    }
		    else
		    {
		        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
		                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
		    }
		}


A classe `Person` neste exemplo não tem que implementar `ITableEntity`.

## <a id="storageapi"></a>Como usar a API de armazenamento .NET diretamente para trabalhar com uma tabela

Você também pode usar o atributo `Table` com um objeto `CloudTable` para obter mais flexibilidade ao trabalhar com uma tabela.

O seguinte código exemplo usa um objeto `CloudTable` a ser adicionado a uma única entidade para a tabela *Ingress*. 
 
		public static void UseStorageAPI(
		    [Table("Ingress")] CloudTable tableBinding,
		    TextWriter logger)
		{
		    var person = new Person()
		        {
		            PartitionKey = "Test",
		            RowKey = "100",
		            Name = "Name"
		        };
		    TableOperation insertOperation = TableOperation.Insert(person);
		    tableBinding.Execute(insertOperation);
		}

Para obter mais informações sobre como usar o objeto `CloudTable`, consulte [como usar o armazenamento de tabela no .NET](../storage-dotnet-how-to-use-tables/). 

## <a id="queues"></a>Tópicos relacionados abordados no artigo de instruções de filas

Para obter informações sobre como lidar com o processamento da tabela disparado por uma mensagem da fila, ou para cenários de SDK WebJobs não específicos para o processamento da tabela, consulte [Como usar armazenamento de fila do Azure com o SDK WebJobs](../websites-dotnet-webjobs-sdk-storage-queues-how-to/). 

Tópicos abordados nesse artigo incluem o seguinte:

* Funções assíncronas
* Várias instâncias
* Desligamento normal
* Use atributos de SDK WebJobs no corpo de uma função
* Defina as cadeias de conexão do SDK no código
* Defina valores para parâmetros do construtor do SDK WebJobs no código
* Dispare uma função manualmente
* Grave logs

## <a id="nextsteps"></a>Próximas etapas

Este guia fornece exemplos de código que mostram como lidar com cenários comuns para trabalhar com tabelas do Azure. Para obter mais informações sobre como usar os Trabalhos Web do Azure e o SDK de Trabalhos Web, consulte [Trabalhos Web do Azure - Recursos recomendados](http://go.microsoft.com/fwlink/?linkid=390226).


<!--HONumber=42-->
