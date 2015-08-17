<properties 
	pageTitle="Como usar o armazenamento de tabela do Azure com o SDK de Trabalhos Web" 
	description="Saiba como usar o armazenamento de tabela do Azure com o SDK de Trabalhos Web. Crie tabelas, adicione entidades a tabelas e leia tabelas existentes." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="tdykstra"/>

# Como usar o armazenamento de tabela do Azure com o SDK de Trabalhos Web

## Visão geral

Este guia fornece exemplos de código em C# que mostram como ler e gravar tabelas de armazenamento do Azure usando o [SDK de Trabalhos Web](websites-dotnet-webjobs-sdk.md) versão 1.x.

O guia pressupõe que você saiba [como criar um projeto de Trabalho Web no Visual Studio com cadeias de conexão que apontam para sua conta de armazenamento](websites-dotnet-webjobs-sdk-get-started.md).
		
Alguns dos trechos de código mostram o atributo `Table` usado nas funções que são [chamadas manualmente](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual), ou seja, que não usam um dos atributos de acionamento.

## <a id="ingress"></a> Como adicionar entidades a uma tabela

Para adicionar entidades em uma tabela, use o atributo `Table` com um parâmetro `ICollector<T>` ou `IAsyncCollector<T>`, no qual `T` especifica o esquema das entidades que você deseja adicionar. O construtor de atributo tem um parâmetro de cadeia que especifica o nome da tabela.

O exemplo de código a seguir adiciona entidades `Person` em uma tabela nomeada *entrada*.

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

Geralmente, o tipo que você usa com `ICollector` deriva de `TableEntity` ou implementa `ITableEntity`, mas ele não precisa fazer isso. Qualquer uma das seguintes classes `Person` funciona com o código mostrado no método `Ingress` anterior.

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

Para trabalhar diretamente com a API de armazenamento do Azure, você pode adicionar um parâmetro `CloudStorageAccount` à assinatura do método.

## <a id="monitor"></a> Monitoramento em tempo real

Como as funções de entrada de dados geralmente processam grandes volumes de dados, o painel do SDK de Trabalhos Web fornece dados de monitoramento em tempo real. A seção **Log de Invocação** informa se a função ainda está em execução.

![Função de entrada em execução](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressrunning.png)

A página **Detalhes de Invocação** relata o progresso da função (número de entidades gravadas) enquanto ela está em execução e lhe dá a oportunidade de anulá-la.

![Função de entrada em execução](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingressprogress.png)

Quando a função é concluída, a página **Detalhes de Invocação** relata o número de linhas gravadas.

![Função de entrada concluída](./media/websites-dotnet-webjobs-sdk-storage-tables-how-to/ingresssuccess.png)

## <a id="multiple"></a> Como ler várias entidades de uma tabela

Para ler uma tabela, use o atributo `Table` com um parâmetro `IQueryable<T>`, em que tipo `T` deriva de `TableEntity` ou implementa `ITableEntity`.

O seguinte exemplo de código lê e registra em log todas as linhas da tabela `Ingress`:
 
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

### <a id="readone"></a> Como ler uma única entidade de uma tabela

Há um construtor de atributo `Table` com dois parâmetros adicionais que permite especificar a chave de partição e a chave de linha quando você deseja associar a uma entidade de tabela única.

O seguinte exemplo de código lê uma linha de tabela para uma entidade `Person` com base nos valores de chave de partição e chave de linha recebidos em uma mensagem de fila:

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


A classe `Person` nesse exemplo não precisa implementar `ITableEntity`.

## <a id="storageapi"></a> Como usar a API de Armazenamento .NET diretamente para trabalhar com uma tabela

Você também pode usar o atributo `Table` com um objeto `CloudTable` para ter mais flexibilidade ao trabalhar com uma tabela.

A seguinte amostra de código usa um objeto `CloudTable` para adicionar uma única entidade de *entrada* à tabela.
 
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

Para obter mais informações sobre como usar o objeto `CloudTable`, consulte [Como usar o Armazenamento de Tabela do .NET](../storage-dotnet-how-to-use-tables.md).

## <a id="queues"></a>Tópicos relacionados abordados no artigo de instruções sobre filas

Para obter informações sobre como lidar com o processamento de tabelas acionado por uma mensagem da fila ou para cenários do SDK de Trabalhos Web não específicos do processamento de tabelas, consulte [Como usar o armazenamento de fila do Azure com o SDK de Trabalhos Web](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

Os tópicos abordados nesse artigo incluem o seguinte:

* Funções assíncronas
* Várias instâncias
* Desligamento normal
* Usar atributos do SDK de Trabalhos Web no corpo de uma função
* Definir as cadeias de conexão do SDK no código
* Definir valores para parâmetros do construtor do SDK WebJobs no código
* Disparar uma função manualmente
* Gravar logs

## <a id="nextsteps"></a> Próximas etapas

Este guia forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com tabelas do Azure. Para obter mais informações sobre como usar os Trabalhos Web do Azure e o SDK de Trabalhos Web, consulte [Trabalhos Web do Azure – Recursos recomendados](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=August15_HO6-->