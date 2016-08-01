<properties
   pageTitle="Diretriz específica do serviço de repetição | Microsoft Azure"
   description="Diretriz específica de serviço para configurar o mecanismo de repetição."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/13/2016"
   ms.author="masashin"/>

# Diretriz específica do serviço de repetição

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

## Visão geral

A maioria dos serviços do Azure e SDKs do cliente inclui um mecanismo de repetição. No entanto, eles são diferentes porque cada serviço apresenta características e requisitos diferentes, e cada mecanismo de repetição é ajustado para um serviço específico. Este guia resume os recursos do mecanismo de repetição para a maioria dos serviços do Azure, além de incluir informações que ajudam a usar, a adaptar ou a estender o mecanismo de repetição para o serviço em questão.

Para obter orientação geral sobre o tratamento de falhas transitórias e como repetir conexões e operações em serviços e recursos, consulte [Diretriz de repetição](best-practices-retry-general.md).

A tabela a seguir resume os recursos de repetição para os serviços do Azure descritos nesta diretriz.

| **Serviço** | **Recursos de repetição** | **Configuração de política** | **Escopo** | **Recursos de telemetria** |
|---------------------------------------|-----------------------------------------|------------------------------|--------------------------------------------------|------------------------
| **[AzureStorage](#azure-storage-retry-guidelines)** | Nativo no cliente | Programático | Operações individuais e de cliente | TraceSource |
| **[Banco de dados SQL com o Entity Framework](#sql-database-using-entity-framework-6-retry-guidelines)** | Nativo no cliente | Programático | Global por AppDomain | Nenhum |
| **[Banco de dados SQL com ADO.NET](#sql-database-using-ado-net-retry-guidelines)** | Topaz* | Programático e declarativo | Instruções ou blocos de código únicos | Personalizado |
| **[Barramento de Serviço](#service-bus-retry-guidelines)** | Nativo no cliente | Programático | Gerenciador de Namespace, Messaging Factory e Cliente | ETW |
| **[Cache](#cache-redis-retry-guidelines)** | Nativo no cliente | Programático | Cliente | TextWriter |
| **[Banco de Dados de Documentos](#documentdb-pre-release-retry-guidelines)** | Nativo no serviço | Não configurável | Global | TraceSource |
| **[Pesquisa](#search-retry-guidelines)** | Topaz* (com estratégia de detecção personalizada) | Programático e declarativo | Blocos de código | Personalizado |
| **[Active Directory](#azure-active-directory-retry-guidelines)** | Topaz* (com estratégia de detecção personalizada) | Programático e declarativo | Blocos de código | Personalizado |
*Topaz é o nome amigável para o Bloco de Aplicativos para Tratamento de Falhas Transitórias, incluído no <a href="http://msdn.microsoft.com/library/dn440719.aspx">Enterprise Library 6.0</a>. Você pode usar uma estratégia de detecção personalizada com o Topaz para a maioria dos serviços, como descrito nesta diretriz. As estratégias padrão do Topaz são mostradas na seção [Estratégias do Bloco de Aplicativos para Tratamento de Falhas Transitórias (Topaz)](#transient-fault-handling-application-block-topaz-strategies) no fim desta diretriz. Observe que o bloco agora é uma estrutura de software livre e não tem suporte direto da Microsoft.

> [AZURE.NOTE] Para a maioria dos mecanismos de repetição internos do Azure, atualmente não há meios de aplicar uma política de repetição diferente para diferentes tipos de erro ou exceção além da funcionalidade incluída na política de repetição. Portanto, a melhor diretriz disponível neste momento em que este documento está sendo escrito é configurar uma política que forneça uma média ideal de desempenho e disponibilidade. Uma maneira de ajustar a política é analisar arquivos de log para determinar o tipo de falha transitória que está ocorrendo. Por exemplo, se a maioria dos erros estiver relacionada aos problemas de conectividade de rede, você poderá tentar uma repetição imediata em vez de aguardar um longo período para a primeira repetição.

## Diretrizes de repetição para o Armazenamento do Azure

Os serviços de armazenamento do Azure incluem armazenamento de tabela e blob, arquivos e filas de armazenamento.

### Mecanismo de repetição

As repetições ocorrem no nível de operação REST individuais e são uma parte integrante da implementação da API do cliente. O SDK do armazenamento do cliente usa classes que implementam a [Interface IExtendedRetryPolicy](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx).

Há diferentes implementações da interface. Os clientes de armazenamento podem escolher dentre políticas especificamente desenvolvidas para acesso a tabelas, blobs e filas. Cada implementação usa uma estratégia de repetição diferente que, basicamente, define o intervalo da repetição e outros detalhes.

As classes internas fornecem suporte a intervalos lineares (constantes) e exponenciais com intervalos de repetição de aleatoriedade. Também não há uma política de repetição a ser usada quando outro processo está tratando repetições em um nível mais alto. No entanto, você pode implementar suas próprias classes de repetição caso haja requisitos não fornecidos pelas classes internas.

As repetições alternativas serão alternadas entre o local do serviço de armazenamento primário e secundário se você estiver usando o RA-GRS (armazenamento com redundância geográfica com acesso de leitura) e o resultado da solicitação for um erro que pode ser repetido. Consulte [Opções de redundância de armazenamento do Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx) para saber mais.

### Configuração de política (armazenamento do Azure)

As políticas de repetição são configuradas de modo programático. Um procedimento comum é criar e popular uma instância **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions** ou **QueueRequestOptions**.

```csharp
TableRequestOptions interactiveRequestOption = new TableRequestOptions()
{
  RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
  // For Read-access geo-redundant storage, use PrimaryThenSecondary.
  // Otherwise set this to PrimaryOnly.
  LocationMode = LocationMode.PrimaryThenSecondary,
  // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
  MaximumExecutionTime = TimeSpan.FromSeconds(2)
};
```

A instância das opções de solicitação pode ser definida no cliente e todas as operações com o cliente usarão as opções de solicitação especificadas.

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

É possível substituir as opções de solicitação de cliente passando uma instância populada da classe de opções de solicitação como um parâmetro para os métodos de operação.

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

Você usa uma instância **OperationContext** para especificar o código a ser executado quando uma repetição ocorrer e quando uma operação tiver sido concluída. Esse código pode coletar informações sobre a operação para uso em logs e telemetria.

	// Set up notifications for an operation
	var context = new OperationContext();
	context.ClientRequestID = "some request id";
	context.Retrying += (sender, args) =>
	{
	  /* Collect retry information */
	};
	context.RequestCompleted += (sender, args) =>
	{
	  /* Collect operation completion information */
	};
	var stats = await client.GetServiceStatsAsync(null, context);

Além de indicar se uma falha é adequada para repetição, as políticas estendidas de repetição retornam um objeto **RetryContext** que indica o número de repetições, os resultados da última solicitação e se a próxima repetição acontecerá no local primário ou secundário (consulte a tabela abaixo para obter detalhes). As propriedades do objeto **RetryContext** podem ser usadas para decidir se e quando tentar uma repetição. Para obter mais detalhes, consulte [Método IExtendedRetryPolicy.Evaluate](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx).

A tabela a seguir mostra as configurações padrão para as políticas de repetição internas.

| **Contexto** | **Configuração** | **Valor padrão** | **Significado** |
|--------------------------|-------------------------------------------------------------|------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| QueueRequestOptions<br />de tabela/blob/arquivo | MaximumExecutionTime<br /><br />ServerTimeout<br /><br /><br /><br /><br />LocationMode<br /><br /><br /><br /><br /><br /><br />RetryPolicy | 120 segundos<br /><br />Nenhum<br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br />ExponentialPolicy | Tempo máximo de execução para a solicitação, incluindo todas as tentativas de repetição potenciais.<br />O intervalo do tempo limite do servidor para a solicitação (o valor é arredondado para segundos). Se não for especificado, será usado o valor padrão para todas as solicitações ao servidor. Normalmente, a melhor opção é omitir essa configuração para que o padrão de servidor seja usado.<br />Se a conta de armazenamento for criada com a opção de replicação RA-GRS (armazenamento com redundância geográfica com acesso de leitura), você poderá usar o modo de local para indicar qual local deve receber a solicitação. Por exemplo, se **PrimaryThenSecondary** for especificado, as solicitações sempre serão enviadas para o local primário primeiro. Se uma solicitação falhar, ela será enviada para o local secundário.<br />Veja abaixo os detalhes de cada opção. |
| Política exponencial | maxAttempt<br />deltaBackoff<br /><br /><br />MinBackoff<br /><br />MaxBackoff | 3<br />4 segundos<br /><br /><br />3 segundos<br /><br />30 segundos | Número de tentativas de repetição.<br />Intervalo de retirada entre repetições. Múltiplos desse período de tempo, incluindo um elemento aleatório, serão usados para tentativas de repetição subsequentes.<br />Adicionado a todos os intervalos de repetição calculados de deltaBackoff. Esse valor não pode ser alterado.<br />MaxBackoff será usado se o intervalo de repetição calculado for maior que MaxBackoff. Este valor não pode ser alterado. |
| Política linear | maxAttempt<br />deltaBackoff | 3<br />30 segundos | Número de tentativas de repetição.<br />Intervalo de retirada entre repetições. |

### Diretriz de uso de repetição
Considere as seguintes diretrizes ao acessar serviços de armazenamento do Azure usando a API do cliente de armazenamento:

* Use as políticas de repetição internas do namespace Microsoft.WindowsAzure.Storage.RetryPolicies onde elas forem apropriadas para suas necessidades. Na maioria dos casos, essas políticas serão suficientes.
* Use a política **ExponentialRetry** em operações em lote, tarefas em segundo plano ou cenários não interativos. Nesses cenários, geralmente você pode permitir mais tempo para que o serviço se recupere — com uma chance consequentemente aumentada da operação ser bem-sucedida.
* Considere especificar a propriedade **MaximumExecutionTime** do parâmetro **RequestOptions** para limitar o tempo de execução total, mas leve em consideração o tipo e o tamanho da operação ao escolher um valor de tempo limite.
* Se você precisar implementar uma repetição personalizada, evite criar wrappers em torno das classes do cliente de armazenamento. Em vez disso, use os recursos para estender as políticas existentes por meio da interface **IExtendedRetryPolicy**.
* Se você estiver usando RA-GRS (armazenamento com redundância geográfica com acesso de leitura), será possível usar o **LocationMode** para especificar que as tentativas de repetição acessarão a cópia secundária somente leitura do repositório caso o acesso primário falhe. No entanto, ao usar essa opção, você deve garantir que seu aplicativo possa trabalhar perfeitamente com dados que talvez estejam obsoletos, se a replicação do repositório primário ainda não tiver sido concluída.

Considere começar com as seguintes configurações para operações de repetição. Essas são configurações de finalidade geral, por isso, você deve monitorar as operações e ajustar os valores para que atendam ao seu cenário.

| **Contexto** | **Exemplo de latência<br />E2E máxima de destino** | **Política de repetição** | **Configurações** | **Valores** | **Como funciona** |
|----------------------|-----------------------------------|------------------|-------------------------|-------------|-----------------------------------------------------------------------------|
| Interativo, interface de usuário<br />ou primeiro plano | 2 segundos | Linear | maxAttempt<br />deltaBackoff | 3<br />500 ms | 1ª tentativa — intervalo de 500 ms<br />2ª tentativa — intervalo de 500 ms<br />3ª tentativa — intervalo de 500 ms |
| Segundo plano<br />ou lote | 30 segundos | Exponencial | maxAttempt<br />deltaBackoff | 5<br />4 segundos | 1ª tentativa — intervalo de aprox. 3 s<br />2ª tentativa — intervalo de aprox. 7 s<br />3ª tentativa — intervalo de aprox. 15 s |

## Telemetria

As tentativas de repetição são registradas em um **TraceSource**. Você deve configurar um **TraceListener** para capturar os eventos e gravá-los em um log de destino apropriado. É possível usar o **TextWriterTraceListener** ou **XmlWriterTraceListener** para gravar os dados em um arquivo de log, o **EventLogTraceListener** para gravar no Log de Eventos do Windows ou o **EventProviderTraceListener** para gravar dados de rastreamento no subsistema ETW. Você também pode configurar a liberação automática do buffer e o detalhamento dos eventos que serão registrados (por exemplo, Erro, Aviso, Informativo e Detalhado). Para saber mais, consulte [Registro em log no lado do cliente com a biblioteca do cliente de armazenamento para .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx).

As operações podem receber uma instância **OperationContext**, que expõe um evento **Retrying** que pode ser usado para anexar a lógica personalizada de telemetria. Para saber mais, consulte [Evento OperationContext.Retrying](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx).

## Exemplos (armazenamento do Azure)

O exemplo de código a seguir mostra como criar duas instâncias **TableRequestOptions** com diferentes configurações de repetição; uma para solicitações interativas e outra para solicitações em segundo plano. O exemplo define essas duas políticas de repetição no cliente para que elas sejam aplicadas a todas as solicitações, assim como define a estratégia interativa em uma solicitação específica de modo que ela substitua as configurações padrão aplicadas ao cliente.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.RetryPolicies;
using Microsoft.WindowsAzure.Storage.Table;

namespace RetryCodeSamples
{
    class AzureStorageCodeSamples
    {
        private const string connectionString = "UseDevelopmentStorage=true";

        public async static Task Samples()
        {
            var storageAccount = CloudStorageAccount.Parse(connectionString);

            TableRequestOptions interactiveRequestOption = new TableRequestOptions()
            {
                RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
                // For Read-access geo-redundant storage, use PrimaryThenSecondary.
                // Otherwise set this to PrimaryOnly.
                LocationMode = LocationMode.PrimaryThenSecondary,
                // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
                MaximumExecutionTime = TimeSpan.FromSeconds(2)
            };

            TableRequestOptions backgroundRequestOption = new TableRequestOptions()
            {
                // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
                // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
                MaximumExecutionTime = TimeSpan.FromSeconds(30),
                // PrimaryThenSecondary in case of Read-access geo-redundant storage, else set this to PrimaryOnly
                LocationMode = LocationMode.PrimaryThenSecondary
            };

            var client = storageAccount.CreateCloudTableClient();
            // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
            // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
            // ServerTimeout and MaximumExecutionTime are not set

            {
                // Set properties for the client (used on all requests unless overridden)
                // Different exponential policy parameters for background scenarios
                client.DefaultRequestOptions = backgroundRequestOption;
                // Linear policy for interactive scenarios
                client.DefaultRequestOptions = interactiveRequestOption;
            }

            {
                // set properties for a specific request
                var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
            }

            {
                // Set up notifications for an operation
                var context = new OperationContext();
                context.ClientRequestID = "some request id";
                context.Retrying += (sender, args) =>
                {
                    /* Collect retry information */
                };
                context.RequestCompleted += (sender, args) =>
                {
                    /* Collect operation completion information */
                };
                var stats = await client.GetServiceStatsAsync(null, context);
            }
        }
    }
}
```

## Mais informações

- [Recomendações de política de repetição da biblioteca do cliente de armazenamento do Azure](https://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)
- [Biblioteca do cliente de armazenamento 2.0 – implementando políticas de repetição](http://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## Diretrizes de repetição para Banco de Dados SQL usando o Entity Framework 6

O Banco de Dados SQL é um banco de dados SQL disponível em vários tamanhos e como um serviço padrão (compartilhado) e premium (não compartilhado). O Entity Framework é um mapeador relacional de objeto que permite aos desenvolvedores do .NET trabalhar com dados relacionais usando objetos específicos de domínio. Com ele, não há a necessidade da maioria dos códigos de acesso a dados que os desenvolvedores geralmente precisam para escrever.

## Mecanismo de repetição

O suporte à repetição é fornecido durante o acesso ao Banco de Dados SQL usando o Entity Framework 6.0 e versões posteriores por meio de um mecanismo chamado [Lógica de Repetição/Resiliência de Conexão](http://msdn.microsoft.com/data/dn456835.aspx). Uma especificação completa está disponível no [wiki Entity Framework do .NET](https://entityframework.codeplex.com/wikipage?title=Connection%20Resiliency%20Spec) no Codeplex. Os principais recursos do mecanismo de repetição são:

* A abstração primária é a interface **IDbExecutionStrategy**. Essa interface:
  * Define métodos **Execute*** síncronos e assíncronos.
  * Define classes que podem ser usadas diretamente ou podem ser configuradas em um contexto de banco de dados como uma estratégia padrão, mapeadas para nome do provedor ou para um nome de provedor e nome de servidor. Quando configuradas em um contexto, as repetições ocorrem no nível de operações de banco de dados individuais, das quais pode haver várias de uma determinada operação de contexto.
  * Define quando repetir uma conexão com falha, e como.
* Incluem várias implementações internas da interface **IDbExecutionStrategy**:
  * Padrão: sem repetição.
  * Padrão para Banco de Dados SQL(automático): sem repetição, mas inspeciona exceções e as encapsula com sugestão para usar a estratégia Banco de Dados SQL.
  * Padrão para Banco de Dados SQL: exponencial (herdado da classe base) mais lógica de detecção do Banco de Dados SQL.
* Implementam uma estratégia de retirada exponencial que inclui aleatoriedade.
* As características das classes de repetição internas são: com estado e sem thread-safe. No entanto, elas podem ser reutilizadas depois que a operação atual for concluída.
* Se a contagem de repetição especificada for excedida, os resultados serão encapsulados em uma nova exceção. A exceção atual não é exibida.

## Configuração de política (Banco de Dados SQL usando o Entity Framework 6)

O suporte à repetição é fornecido durante o acesso ao Banco de Dados SQL usando o Entity Framework 6.0 e versões superiores. As políticas de repetição são configuradas de modo programático. A configuração não pode ser alterada por operação.

Ao configurar uma estratégia no contexto como o padrão, você especifica uma função que cria uma nova estratégia sob demanda. O código a seguir mostra como você pode criar uma classe de configuração de repetição que estende a classe base **DbConfiguration**.

```csharp
public class BloggingContextConfiguration : DbConfiguration
{
  public BlogConfiguration()
  {
    // Set up the execution strategy for SQL Database (exponential) with 5 retries and 4 sec delay
    this.SetExecutionStrategy(
         "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4)));
  }
}
```

Depois isso pode ser especificado como a estratégia de repetição padrão para todas as operações usando o método **SetConfiguration** da instância **DbConfiguration** quando o aplicativo é iniciado. Por padrão, o EF vai descobrir e usar automaticamente a classe de configuração.

	DbConfiguration.SetConfiguration(new BloggingContextConfiguration());

Você pode especificar a classe de configuração de repetição para um contexto anotando a classe de contexto com um atributo **DbConfigurationType**. No entanto, se você tiver apenas uma classe de configuração, o EF a usará sem a necessidade de anotar o contexto.

	[DbConfigurationType(typeof(BloggingContextConfiguration))]
	public class BloggingContext : DbContext
	{ ...

Se precisar usar estratégias de repetição diferentes para operações específicas ou desabilitar repetições para operações específicas, você poderá criar uma classe de configuração que permita suspender ou trocar estratégias, definindo um sinalizador no **CallContext**. A classe de configuração pode usar esse sinalizador para alternar estratégias ou desabilitar a estratégia fornecida por você e usar uma estratégia padrão. Para saber mais, consulte [Suspender estratégia de execução](http://msdn.microsoft.com/dn307226#transactions_workarounds) na página Limitações com estratégias de execução de repetição (EF6 em diante).

Outra técnica para usar estratégias de repetição específicas para operações individuais é criar uma instância da classe de estratégia necessária e fornecer as configurações desejadas por meio de parâmetros. Você então chama o método **ExecuteAsync**.

	var executionStrategy = new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4));
	var blogs = await executionStrategy.ExecuteAsync(
	    async () =>
	    {
	        using (var db = new BloggingContext("Blogs"))
	        {
	            // Acquire some values asynchronously and return them
	        }
	    },
	    new CancellationToken()
	);

A maneira mais simples de usar uma classe **DbConfiguration** é colocá-la no mesmo assembly da classe **DbContext**. No entanto, isso não é apropriado quando o mesmo contexto é necessário em cenários diferentes, como diferentes estratégias de repetição interativas e em segundo plano. Se os diferentes contextos forem executados em AppDomains separados, você poderá usar o suporte interno para especificar classes de configuração no arquivo de configuração ou defini-las explicitamente usando código. Se os diferentes contextos devem ser executados no mesmo AppDomain, será necessária uma solução personalizada.

Para saber mais, consulte [Configuração baseada em código (EF6 em diante)](http://msdn.microsoft.com/data/jj680699.aspx).

A tabela a seguir mostra as configurações padrão para a política de repetição interna ao usar o EF6.

![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable4.png)
## Diretriz de uso de repetição

Considere as seguintes diretrizes ao acessar o Banco de Dados SQL usando o EF6:

* Escolha a opção de serviço apropriada (compartilhada ou premium). Uma instância compartilhada pode sofrer atrasos e limitação de conexão mais longos que o normal devido ao uso por outros locatários do servidor compartilhado. Se forem necessárias operações confiáveis de baixa latência e desempenho previsível, considere a escolha da opção premium.
* Uma estratégia de intervalo fixo não é recomendada para ser usada com o Banco de Dados SQL do Azure. Em vez disso, use uma estratégia de retirada exponencial, pois o serviço pode estar sobrecarregado e intervalos mais longos permitem mais tempo para a recuperação.
* Escolha um valor adequado para os tempos limite de conexão e comando ao definir conexões. Baseie o tempo limite no seu design de lógica de negócios e por meio de teste. Talvez seja necessário modificar esse valor ao longo do tempo, uma vez que os volumes de dados ou os processos de negócios mudam. Um tempo limite muito curto pode resultar em falhas prematuras de conexões quando o banco de dados estiver ocupado. Um tempo limite muito longo pode impedir que a lógica de repetição funcione corretamente, aguardando tempo demais para detectar uma conexão com falha. O valor do tempo limite é um componente da latência de ponta a ponta, embora você não possa determinar facilmente quantos comandos serão executados ao salvar o contexto. Você pode alterar o tempo limite padrão definindo a propriedade **CommandTimeout** da instância **DbContext**.
* O Entity Framework oferece suporte às configurações de repetição definidas em arquivos de configuração. No entanto, para máxima flexibilidade no Azure, você deve considerar a criação da configuração de modo programático dentro do aplicativo. Os parâmetros específicos das políticas de repetição, como o número de repetições e os intervalos da repetição, podem ser armazenados no arquivo de configuração de serviço e usados no tempo de execução para criar as políticas apropriadas. Isso permite que as configurações sejam alteradas sem exigir que o aplicativo seja reiniciado.

Considere começar com as seguintes configurações para operações de repetição. Você não pode especificar o intervalo entre tentativas de repetição (ele é fixo e gerado como uma sequência exponencial). Você pode especificar apenas os valores máximo, conforme mostrado aqui, a menos que você crie uma estratégia de repetição personalizada. Essas são configurações de finalidade geral, por isso, você deve monitorar as operações e ajustar os valores para que atendam ao seu cenário.

| **Contexto** | **Exemplo de latência<br />E2E máxima de destino** | **Política de repetição** | **Configurações** | **Valores** | **Como funciona** |
|----------------------|-----------------------------------|--------------------|------------------------|--------------|-----------------------------------------------------------------------------------------------------------------------------|
| Interativo, interface de usuário<br />ou primeiro plano | 2 segundos | Exponencial | MaxRetryCount<br />MaxDelay | 3<br />750 ms | 1ª tentativa — intervalo de 0 s<br />2ª tentativa — intervalo de 750 ms<br />3ª tentativa — intervalo de 750 ms |
| Segundo plano<br /> ou lote | 30 segundos | Exponencial | MaxRetryCount<br />MaxDelay | 5<br />12 segundos | 1ª tentativa — intervalo de 0 s<br />2ª tentativa — intervalo de aprox. 1 s<br />3ª tentativa — intervalo de aprox. 3 s<br />4ª tentativa — intervalo de aprox. de 7 s<br />5ª tentativa — intervalo de 12 s |

> [AZURE.NOTE] A latência de ponta a ponta visa o tempo limite padrão de conexões com o serviço. Se você especificar tempos limite de conexão mais longos, a latência de ponta a ponta será estendida por esse tempo adicional para cada tentativa de repetição.

## Exemplos (Banco de Dados SQL usando o Entity Framework 6)

O exemplo de código a seguir define uma solução de acesso a dados simples que usa o Entity Framework. Ele estabelece uma estratégia de repetição específica definindo uma instância de uma classe chamada **BlogConfiguration** que estende **DbConfiguration**.

```csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.SqlServer;
using System.Threading.Tasks;

namespace RetryCodeSamples
{
	public class BlogConfiguration : DbConfiguration
	{
	    public BlogConfiguration()
	    {
	        // Set up the execution strategy for SQL Database (exponential) with 5 retries and 12 sec delay.
	        // These values could be loaded from configuration rather than being hard-coded.
	        this.SetExecutionStrategy(
	                "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(12)));
	    }
	}

	// Specify the configuration type if more than one has been defined.
	// [DbConfigurationType(typeof(BlogConfiguration))]
	public class BloggingContext : DbContext
	{
	    // Definition of content goes here.
	}

	class EF6CodeSamples
	{
	    public async static Task Samples()
	    {
	        // Execution strategy configured by DbConfiguration subclass, discovered automatically or
	        // or explicitly indicated through configuration or with an attribute. Default is no retries.
	        using (var db = new BloggingContext("Blogs"))
	        {
	            // Add, edit, delete blog items here, then:
	            await db.SaveChangesAsync();
	        }
	    }
	}
}
```

Mais exemplos de como usar o mecanismo de repetição do Entity Framework podem ser encontrados em [Lógica de Repetição/Resiliência de Conexão](http://msdn.microsoft.com/data/dn456835.aspx).

## Mais informações

* [Guia de desempenho e elasticidade do Banco de Dados SQL do Azure](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## Diretrizes de repetição para Banco de Dados SQL usando o ADO.NET

O Banco de Dados SQL é um banco de dados SQL disponível em vários tamanhos e como um serviço padrão (compartilhado) e premium (não compartilhado).

### Mecanismo de repetição

O Banco de Dados SQL não tem suporte interno para repetições quando acessado usando o ADO.NET. No entanto, os códigos de retorno das solicitações podem ser usados para determinar por que uma solicitação falhou. A página [Limitação do Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/dn338079.aspx) explica como a limitação pode impedir conexões, os códigos de retorno para situações específicas e como lidar com eles e operações de repetição.

Você pode usar o Bloco de Aplicativos para Tratamento de Falhas Transitórias (Topaz) com o pacote Nuget EnterpriseLibrary.TransientFaultHandling.Data (classe **SqlAzureTransientErrorDetectionStrategy**) para implementar um mecanismo de repetição para o Banco de Dados SQL.

O bloco também fornece a classe **ReliableSqlConnection**, que implementa a API antiga do ADO.NET 1.0 (**IDbConnection**, em vez de **DbConnection**) e executa o gerenciamento de repetições e conexões internamente. Embora seja conveniente, isso exige que você use um conjunto diferente de métodos para invocar operações com repetições, e essa não é uma substituição simples e direta. Ela não oferece suporte à execução assíncrona, que é recomendada ao implementar e usar os serviços do Azure. Além disso, como essa classe usa ADO.NET 1.0, ela não se beneficia das melhorias e atualizações recentes para o ADO.NET.

### Configuração de política (Banco de Dados SQL usando o ADO.NET)

O Bloco de Aplicativos para Tratamento de Falhas Transitórias oferece suporte à configuração programática e baseada em arquivo. Em geral, você deve usar a configuração programática para máxima flexibilidade (consulte as observações na seção a seguir para saber mais). O código a seguir, que seria executado uma vez na inicialização do aplicativo, cria e popula um **RetryManager** com uma lista de quatro estratégias de repetição adequadas para uso com o Banco de Dados SQL do Azure. Ele também define as estratégias padrão para o **RetryManager**. Essas são as estratégias que serão usadas para conexões e comandos se uma alternativa não for especificada durante a criação de uma conexão ou um comando.

```csharp
RetryManager.SetDefault(new RetryManager(
	new List<RetryStrategy> { new ExponentialBackoff(name: "default", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "default sql connection", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "default sql command", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "alt sql", retryCount: 5,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true), },
	"default",
	new Dictionary<string, string> {
	    {
	    RetryManagerSqlExtensions.DefaultStrategyConnectionTechnologyName, "default sql connection"
	    },
	    {
	    RetryManagerSqlExtensions.DefaultStrategyCommandTechnologyName, "default sql command"}
	    }));
```

Para obter informações sobre como você pode usar as políticas de repetição que configurou quando acessa o Banco de Dados SQL do Azure, consulte a seção [Exemplos](#examples-sql-database-using-ado-net-) abaixo.

As estratégias padrão do Bloco de Aplicativos para Tratamento de Falhas Transitórias são mostradas na seção [Estratégias do Bloco de Aplicativos para Tratamento de Falhas Transitórias (Topaz)](#transient-fault-handling-application-block-topaz-strategies) no fim desta diretriz.

### Diretriz de uso de repetição

Considere as seguintes diretrizes ao acessar o Banco de Dados SQL usando o ADO.NET:

* Escolha a opção de serviço apropriada (compartilhada ou premium). Uma instância compartilhada pode sofrer atrasos e limitação de conexão mais longos que o normal devido ao uso por outros locatários do servidor compartilhado. Se forem necessárias mais operações confiáveis de baixa latência e desempenho previsível, considere a escolha da opção premium.
* Garanta a execução de repetições no nível ou escopo apropriado para evitar operações não idempotentes que causem inconsistência nos dados. De modo ideal, todas as operações devem ser idempotentes para que possam ser repetidas sem causar inconsistência. Quando não for esse o caso, a repetição deverá ser realizada em um nível ou escopo que permita que todas as alterações relacionadas sejam desfeitas, caso uma operação falhe; por exemplo, em um escopo transacional. Para saber mais, consulte [Camada de acesso a dados dos conceitos básicos do serviço de nuvem — tratamento de falhas transitórias](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).
* Uma estratégia de intervalo fixo não é recomendada para uso com o Banco de Dados SQL do Azure, exceto em cenários interativos em que há apenas algumas repetições em intervalos muito curtos. Em vez disso, considere usar uma estratégia de retirada exponencial para a maioria dos cenários.
* Escolha um valor adequado para os tempos limite de conexão e comando ao definir conexões. Um tempo limite muito curto pode resultar em falhas prematuras de conexões quando o banco de dados estiver ocupado. Um tempo limite muito longo pode impedir que a lógica de repetição funcione corretamente, aguardando tempo demais para detectar uma conexão com falha. O valor do tempo limite é um componente da latência de ponta a ponta; ele é efetivamente adicionado ao intervalo de repetição especificado na política de repetição para cada tentativa de repetição.
* Feche a conexão após um determinado número de repetições, mesmo ao usar uma lógica de repetição de retirada exponencial, e repita a operação em uma nova conexão. Repetir a mesma operação várias vezes na mesma conexão pode ser um fator que contribui para problemas de conexão. Para obter um exemplo dessa técnica, consulte [Camada de acesso a dados dos conceitos básicos do serviço de nuvem — tratamento de falhas transitórias](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).
* Quando o pool de conexões está em uso (o padrão), é provável que a mesma conexão seja escolhida no pool, mesmo depois de fechar e reabrir uma conexão. Se for esse o caso, uma técnica para resolver isso é chamar o método **ClearPool** da classe **SqlConnection** para marcar a conexão como não reutilizável. No entanto, isso deve ser feito somente depois que várias tentativas de conexão tiverem falhado, e somente ao encontrar a classe específica de falhas transitórias, como tempos limite de SQL (código de erro -2), relacionada às conexões com falha.
* Se o código de acesso a dados usar transações iniciadas como instâncias **TransactionScope**, a lógica de repetição deverá reabrir a conexão e iniciar um novo escopo de transação. Por esse motivo, o bloco de código com nova tentativa deve englobar o escopo inteiro da transação.
* O Bloco de Aplicativos para Tratamento de Falhas Transitórias oferece suporte a configurações de repetição totalmente definidas em arquivos de configuração. No entanto, para máxima flexibilidade no Azure, você deve considerar a criação da configuração de modo programático dentro do aplicativo. Os parâmetros específicos das políticas de repetição, como o número de repetições e os intervalos da repetição, podem ser armazenados no arquivo de configuração de serviço e usados no tempo de execução para criar as políticas apropriadas. Isso permite que as configurações sejam alteradas sem exigir que o aplicativo seja reiniciado.

Considere começar com as seguintes configurações para operações de repetição. Essas são configurações de finalidade geral, por isso, você deve monitorar as operações e ajustar os valores para que atendam ao seu cenário.

| **Contexto** | **Exemplo de latência<br />E2E máxima de destino** | **Estratégia de repetição** | **Configurações** | **Valores** | **Como funciona** |
|----------------------|-----------------------------------|--------------------|-----------------------------------------------------------------------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Interativo, interface de usuário<br />ou primeiro plano | 2 s | FixedInterval | Contagem de repetição<br />Intervalo de repetição<br />Primeira repetição rápida | 3<br />500 ms<br />verdadeiro | 1ª tentativa — intervalo de 0 s<br />2ª tentativa — intervalo de 500 ms<br />3ª tentativa — intervalo de 500 ms |
| Segundo plano<br />ou lote | 30 s | ExponentialBackoff | Contagem de repetição<br />Retirada mín.<br />Retirada máx.<br />Retirada Delta<br />Primeira repetição rápida | 5<br />0 s<br />60 s<br />2 s<br />falso | 1ª tentativa — intervalo de 0 s<br />2ª tentativa — intervalo de aprox. 2 s<br />3ª tentativa — intervalo de aprox. 6 s<br />4ª tentativa — intervalo de aprox. de 14 s<br />5ª tentativa — intervalo de aprox. 30 s |

> [AZURE.NOTE] A latência de ponta a ponta visa o tempo limite padrão de conexões com o serviço. Se você especificar tempos limite de conexão mais longos, a latência de ponta a ponta será estendida por esse tempo adicional para cada tentativa de repetição.

### Exemplos (Banco de Dados SQL usando o ADO.NET)

Esta seção descreve como você pode usar o Bloco de Aplicativos para Tratamento de Falhas Transitórias para acessar o Banco de Dados SQL do Azure usando um conjunto de políticas de repetição que configurou no **RetryManager** (conforme mostrado na seção anterior [Configuração de política](#policy-configuration-sql-database-using-ado-net-)). A abordagem mais simples para usar o bloco é por meio da classe **ReliableSqlConnection** ou chamando os métodos de extensão, como **OpenWithRetry** em uma conexão (consulte [O Bloco de Aplicativos para Tratamento de Falhas Transitórias](http://msdn.microsoft.com/library/hh680934.aspx) para saber mais).

No entanto, na versão atual do Bloco de Aplicativos para Tratamento de Falhas Transitórias, essas abordagens não oferecem suporte de modo inerente a operações assíncronas no Banco de Dados SQL. Uma boa prática exige que você use apenas técnicas assíncronas para acessar serviços do Azure, como Banco de Dados SQL, e você deve considerar as técnicas a seguir para usar o Bloco de Aplicativos para Tratamento de Falhas Transitórias com o Banco de Dados SQL.

Você pode usar o suporte assíncrono simplificado na versão 5 da linguagem C# para criar versões assíncronas dos métodos fornecidos pelo bloco. Por exemplo, o código a seguir mostra como você pode criar uma versão assíncrona do método de extensão **ExecuteReaderWithRetry**. As alterações e adições no código original são realçadas. O código-fonte para Topaz está disponível no Codeplex em [Bloco de Aplicativos para Tratamento de Falhas Transitórias (“Topaz”)](http://topaz.codeplex.com/SourceControl/latest).

```csharp
public async static Task<SqlDataReader> ExecuteReaderWithRetryAsync(this SqlCommand command, RetryPolicy cmdRetryPolicy,
RetryPolicy conRetryPolicy)
{
	GuardConnectionIsNotNull(command);

	// Check if retry policy was specified, if not, use the default retry policy.
	return await (cmdRetryPolicy ?? RetryPolicy.NoRetry).ExecuteAsync(async () =>
	{
	    var hasOpenConnection = await EnsureValidConnectionAsync(command, conRetryPolicy).ConfigureAwait(false);

	    try
	    {
	        return await command.ExecuteReaderAsync().ConfigureAwait(false);
	    }
	    catch (Exception)
	    {
	        if (hasOpenConnection && command.Connection != null && command.Connection.State == ConnectionState.Open)
	        {
	            command.Connection.Close();
	        }

	        throw;
	    }
	}).ConfigureAwait(false);
}
```

Esse novo método de extensão assíncrono pode ser usado da mesma forma que as versões síncronas incluídas no bloco.

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

var retryPolicy =
	RetryManager.Instance.GetRetryPolicy<SqlDatabaseTransientErrorDetectionStrategy>("alt sql");
using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync(retryPolicy))
{
	// Do something with the values
}
```

No entanto, essa abordagem lida apenas com comandos ou operações individuais, e não com blocos de instruções em que pode haver limites transacionais definidos corretamente. Além disso, ela não aborda a questão de remover as conexões com falha do pool de conexões para que elas não sejam selecionadas em tentativas subsequentes. Um exemplo síncrono de resolução desses problemas pode ser encontrado em [Camada de acesso a dados dos conceitos básicos do serviço de nuvem — tratamento de falhas transitórias](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Timeouts_amp_Connection_Management). Além de repetir sequências arbitrárias de instruções de banco de dados, ele limpa o pool de conexões para remover conexões inválidas e instrumentar o processo inteiro. Embora o código mostrado neste exemplo seja síncrono, é relativamente fácil convertê-lo em código assíncrono.

### Mais informações

Para obter informações detalhadas sobre como usar o Bloco de Aplicativos para Tratamento de Falhas Transitórias, consulte:

* [Usando o Bloco de Aplicativos para Tratamento de Falhas Transitórias com o SQL Azure](http://msdn.microsoft.com/library/hh680899.aspx)
* [Perseverança, os segredos de todos os triunfos: usando o Bloco de Aplicativos para Tratamento de Falhas Transitórias](http://msdn.microsoft.com/library/dn440719.aspx)
* [Camada de acesso a dados dos conceitos básicos do serviço de nuvem — tratamento de falhas transitórias](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)

Para obter orientação geral sobre como aproveitar ao máximo o Banco de Dados SQL, consulte:

* [Guia de desempenho e elasticidade do Banco de Dados SQL do Azure](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)
* [Minimizando erros do pool de conexões no SQL Azure](http://blogs.msdn.com/b/adonet/archive/2011/11/05/minimizing-connection-pool-errors-in-sql-azure.aspx)

## Diretrizes de repetição para Barramento de Serviço

O Barramento de Serviço é uma plataforma de mensagens na nuvem que fornece troca de mensagens flexivelmente acoplada com escala e resiliência melhoradas para componentes de um aplicativo, se hospedado na nuvem ou no local.

### Mecanismo de repetição

O Barramento de Serviço implementa repetições usando implementações da classe base [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx). Todos os clientes do Barramento de Serviço expõem uma propriedade **RetryPolicy** que pode ser definida como uma das implementações da classe base **RetryPolicy**. As implementações internas são:

* A [classe RetryExponential](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx). Ela expõe propriedades que controlam o intervalo de retirada, a contagem de repetições e a propriedade **TerminationTimeBuffer** que é usada para limitar o tempo total para a operação ser concluída.
* A [classe NoRetry](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx). É usada quando as repetições no nível de API do Barramento de Serviço não são exigidas, como quando são gerenciadas por outro processo como parte de uma operação em lote ou de várias etapas.

As ações do Barramento de Serviço podem retornar uma gama de exceções, conforme listado no [Apêndice: exceções de mensagens](http://msdn.microsoft.com/library/hh418082.aspx). A lista fornece informações que indicam se repetir a operação é adequado. Por exemplo, um [ServerBusyException](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) indica que o cliente deve esperar um período de tempo, depois repetir a operação. A ocorrência de um **ServerBusyException** também faz com que o Barramento de Serviço alterne para um modo diferente, no qual um intervalo extra de 10 segundos é adicionado aos intervalos de repetição computados. Esse modo é redefinido após um curto período.

As exceções retornadas do Barramento de Serviço expõem a propriedade **IsTransient** que indica se o cliente deve repetir a operação. A política interna **RetryExponential** depende da propriedade **IsTransient** na classe **MessagingException**, que é a classe base para todas as exceções do Barramento de Serviço. Se você criar implementações personalizadas da classe base **RetryPolicy**, será possível usar uma combinação do tipo de exceção e da propriedade **IsTransient** para fornecer um controle mais refinado sobre as ações de repetição. Por exemplo, é possível detectar um **QuotaExceededException** e tomar providências para diminuir a fila antes de tentar enviar novamente uma mensagem para ela.

### Configuração de política (Barramento de Serviço)

As políticas de repetição são definidas de modo programático e como uma política padrão para um **NamespaceManager** e uma **MessagingFactory** ou individualmente para cada cliente de mensagens. Para definir a política de repetição padrão para uma sessão de mensagens, defina **RetryPolicy** do **NamespaceManager**.

	namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                                             maxBackoff: TimeSpan.FromSeconds(30),
	                                                             maxRetryCount: 3);

Observe que esse código usa parâmetros nomeados para maior clareza. Como alternativa, você pode omitir os nomes, pois nenhum dos parâmetros é opcional.

	namespaceManager.Settings.RetryPolicy = new RetryExponential(TimeSpan.FromSeconds(0.1),
	                 TimeSpan.FromSeconds(30), TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(5), 3);

Para definir a política de repetição padrão para todos os clientes criados de uma fábrica de mensagens, defina **RetryPolicy** da **MessagingFactory**.

	messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                                    maxBackoff: TimeSpan.FromSeconds(30),
	                                                    maxRetryCount: 3);

Para definir a política de repetição para um cliente de mensagens, ou para substituir sua política padrão, defina a propriedade **RetryPolicy** usando uma instância da classe de política necessária:

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                        maxBackoff: TimeSpan.FromSeconds(30),
	                                        maxRetryCount: 3);
```

A política de repetição não pode ser definida no nível de operação individual. Ela se aplica a todas as operações do cliente de mensagens. A tabela a seguir mostra as configurações padrão da política de repetição interna.

![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable7.png)

### Diretriz de uso de repetição

Considere as seguintes diretrizes ao usar o Barramento de Serviço:

* Ao usar a implementação **RetryExponential** interna, não implemente uma operação de fallback, pois a política reage às exceções de Servidor Ocupado e alterna automaticamente para um modo de repetição apropriado.
* O Barramento de Serviço oferece suporte a um recurso chamado Namespaces Emparelhados, que implementa failover automático para uma fila de backup em um namespace separado, caso a fila no namespace primário falhe. As mensagens da fila secundária podem ser enviadas de volta para a fila primária quando esta se recuperar. Esse recurso ajuda a corrigir falhas transitórias. Para saber mais, consulte [Padrões de mensagens assíncronas e alta disponibilidade](http://msdn.microsoft.com/library/azure/dn292562.aspx).

Considere começar com as seguintes configurações para operações de repetição. Essas são configurações de finalidade geral, por isso, você deve monitorar as operações e ajustar os valores para que atendam ao seu cenário.


![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable8.png)

### Telemetria

O Barramento de Serviço registra as repetições como eventos ETW usando um **EventSource**. Você deve anexar um **EventListener** à origem do evento para capturar os eventos e exibi-los no Visualizador de Desempenho ou gravá-los em um log de destino apropriado. Você pode usar o [Bloco de Aplicativos para Registro Semântico](http://msdn.microsoft.com/library/dn775006.aspx) para fazer isso. Os eventos de repetição são da seguinte forma:

```text
Microsoft-ServiceBus-Client/RetryPolicyIteration
ThreadID="14,500"
FormattedMessage="[TrackingId:] RetryExponential: Operation Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05 at iteration 0 is retrying after 00:00:00.1000000 sleep because of Microsoft.ServiceBus.Messaging.MessagingCommunicationException: The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3, TimeStamp:9/5/2014 10:00:13 PM."
trackingId=""
policyType="RetryExponential"
operation="Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
iteration="0"
iterationSleep="00:00:00.1000000"
lastExceptionType="Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage="The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"
```

### Exemplos (Barramento de serviço)

O código de exemplo a seguir mostra como definir a política de repetição para:

* Um gerenciador de namespaces. A política se aplica a todas as operações nesse gerenciador e não pode ser substituída para operações individuais.
* Uma fábrica de mensagens. A política se aplica a todos os clientes criados nessa fábrica e não pode ser substituída durante a criação de clientes individuais.
* Um cliente de mensagens individual. Depois que um cliente tiver sido criado, você poderá definir a política de repetição para esse cliente. A política se aplica a todas as operações nesse cliente.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Messaging;

namespace RetryCodeSamples
{
	class ServiceBusCodeSamples
	{
		private const string connectionString =
		    @"Endpoint=sb://[my-namespace].servicebus.windows.net/;
		        SharedAccessKeyName=RootManageSharedAccessKey;
		        SharedAccessKey=C99..........Mk=";

		public async static Task Samples()
		{
		    const string QueueName = "TestQueue";

		    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.Http;

		    var namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);

		    // The namespace manager will have a default exponential policy with 10 retry attempts
		    // and a 3 second delay delta.
		    // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
		    // with an extra 10 sec added when receiving a ServiceBusyException.

		    {
		        // Set different values for the retry policy, used for all operations on the namespace manager.
		        namespaceManager.Settings.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(0),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                maxRetryCount: 3);

		        // Policies cannot be specified on a per-operation basis.
		        if (!await namespaceManager.QueueExistsAsync(QueueName))
		        {
		            await namespaceManager.CreateQueueAsync(QueueName);
		        }
		    }


		    var messagingFactory = MessagingFactory.Create(
		        namespaceManager.Address, namespaceManager.Settings.TokenProvider);
		    // The messaging factory will have a default exponential policy with 10 retry attempts
		    // and a 3 second delay delta.
		    // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
		    // with an extra 10 sec added when receiving a ServiceBusyException.

		    {
		        // Set different values for the retry policy, used for clients created from it.
		        messagingFactory.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(1),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                maxRetryCount: 3);


		        // Policies cannot be specified on a per-operation basis.
		        var session = await messagingFactory.AcceptMessageSessionAsync();
		    }


		    {
		        var client = messagingFactory.CreateQueueClient(QueueName);
		        // The client inherits the policy from the factory that created it.


		        // Set different values for the retry policy on the client.
		        client.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(0.1),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                maxRetryCount: 3);


		        // Policies cannot be specified on a per-operation basis.
		        var session = await client.AcceptMessageSessionAsync();
		    }
		}
	}
}
```

## Mais informações

* [Padrões de mensagens assíncronas e alta disponibilidade](http://msdn.microsoft.com/library/azure/dn292562.aspx)

## Diretrizes de repetição para cache (Redis)

O Cache Redis do Azure é um serviço de cache de baixa latência e acesso a dados rápido com base no Cache Redis de software livre popular. Ele é seguro, gerenciado pela Microsoft e pode ser acessado de qualquer aplicativo no Azure.

A diretriz nesta seção se baseia em como usar o cliente StackExchange.Redis para acessar o cache. Uma lista de outros clientes adequados pode ser encontrada no [site do Redis](http://redis.io/clients), e eles podem ter mecanismos de repetição diferentes.

Observe que o cliente StackExchange.Redis usa multiplexação por meio de uma única conexão. O uso recomendado é criar uma instância do cliente na inicialização do aplicativo e usar essa instância para todas as operações no cache. Por esse motivo, a conexão com o cache é feita apenas uma vez, e toda a orientação desta seção está relacionada à política de repetição para esta conexão inicial, e não para cada operação que acessa o cache.

### Mecanismo de repetição

O cliente StackExchange.Redis usa uma classe de gerenciador de conexões que é configurada por meio de um conjunto de opções. Essas opções incluem uma propriedade **ConnectRetry** que especifica o número de vezes que uma conexão com o cache que falhou será repetida. No entanto, a política de repetição é usada apenas para a ação de conexão inicial, e não espera entre as repetições.

### Configuração de política (Cache Redis do Azure)

As políticas de repetição são configuradas de modo programático, definindo as opções para o cliente antes de se conectar ao cache. Isso pode ser feito criando uma instância da classe **ConfigurationOptions**, populando suas propriedades e passando-a para o método **Connect**.

```csharp
var options = new ConfigurationOptions { EndPoints = { "localhost" },
	                                        ConnectRetry = 3,
	                                        ConnectTimeout = 2000 };
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

Observe que a propriedade **ConnectTimeout** especifica o tempo máximo de espera em milissegundos, e não o intervalo entre as repetições.

Como alternativa, você pode especificar as opções como uma cadeia de caracteres e passá-la ao método **Connect**.

```csharp
	var options = "localhost,connectRetry=3,connectTimeout=2000";
	ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

Também é possível especificar opções diretamente ao se conectar ao cache.

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

A tabela a seguir mostra as configurações padrão da política de repetição interna.

| **Contexto** | **Configuração** | **Valor padrão**<br />(v 1.0.331) | **Significado** |
|----------------------|-----------------------------------------|-----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConfigurationOptions | ConnectRetry<br /><br />ConnectTimeout<br /><br />SyncTimeout | 3<br /><br />Máximo de 5000 ms mais SyncTimeout<br />1000 | O número de vezes para repetir tentativas de conexão durante a operação de conexão inicial.<br />Tempo limite (ms) para operações de conexão. Não é um intervalo entre tentativas de repetição.<br />Tempo (ms) para permitir operações síncronas. |

> [AZURE.NOTE] SyncTimeout contribui com a latência de ponta a ponta de uma operação. No entanto, de modo geral, não é recomendável usar operações síncronas. Para saber mais, consulte [Pipelines e multiplexadores](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md).

## Diretriz de uso de repetição

Considere as seguintes diretrizes ao usar o Cache Redis do Azure:

* O cliente StackExchange Redis gerencia suas próprias repetições, mas apenas ao estabelecer uma conexão com o cache quando o aplicativo é iniciado pela primeira vez. Você pode configurar o tempo limite da conexão e o número de tentativas de repetição para estabelecer essa conexão, mas a política de repetição não se aplica a operações no cache.
* O mecanismo de repetição não tem nenhum intervalo entre as tentativas de repetição. Ele simplesmente repete uma conexão com falha depois que o tempo limite da conexão especificada expira e pelo número de vezes especificado.
* Em vez de usar um grande número de tentativas de repetição, considere fazer fallback acessando a fonte de dados original.

## Telemetria

Você pode coletar informações sobre conexões (mas não outras operações) usando um **TextWriter**.

```csharp
var writer = new StringWriter();
...
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

Um exemplo da saída que isso gera é mostrado abaixo.

```text
localhost:6379,connectTimeout=2000,connectRetry=3
1 unique nodes specified
Requesting tie-break from localhost:6379 > __Booksleeve_TieBreak...
Allowing endpoints 00:00:02 to respond...
localhost:6379 faulted: SocketFailure on PING
localhost:6379 failed to nominate (Faulted)
> UnableToResolvePhysicalConnection on GET
No masters detected
localhost:6379: Standalone v2.0.0, master; keep-alive: 00:01:00; int: Connecting; sub: Connecting; not in use: DidNotRespond
localhost:6379: int ops=0, qu=0, qs=0, qc=1, wr=0, sync=1, socks=2; sub ops=0, qu=0, qs=0, qc=0, wr=0, socks=2
Circular op-count snapshot; int: 0 (0.00 ops/s; spans 10s); sub: 0 (0.00 ops/s; spans 10s)
Sync timeouts: 0; fire and forget: 0; last heartbeat: -1s ago
resetting failing connections to retry...
retrying; attempts left: 2...
...
```

## Exemplos (Cache Redis do Azure)

O exemplo de código a seguir mostra como você pode definir a configuração de tempo limite de conexão e o número de repetições ao inicializar o cliente StackExchange.Redis para acessar o Cache Redis do Azure na inicialização do aplicativo. Observe que o tempo limite da conexão é o período de tempo que você está disposto a esperar para conexão ao cache; não é o intervalo entre tentativas de repetição.

Este exemplo mostra como definir a configuração usando uma instância de **ConfigurationOptions**.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
	class CacheRedisCodeSamples
	{
	    public async static Task Samples()
	    {
	        var writer = new StringWriter();

	        {
	            try
	            {
	                // Using object-based configuration.
	                var options = new ConfigurationOptions
	                                    {
	                                        EndPoints = { "localhost" },
	                                        ConnectRetry = 3,
	                                        ConnectTimeout = 2000  // The maximum waiting time (ms), not the delay for retries.
	                                    };
	                ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

	                // Store a reference to the multiplexer for use in the application.
	            }
	            catch
	            {
	                Console.WriteLine(writer.ToString());
	                throw;
	            }
	        }
	    }
	}
}
```

Este exemplo mostra como definir a configuração especificando as opções como uma cadeia de caracteres.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
	class CacheRedisCodeSamples
	{
	    public async static Task Samples()
	    {
	        var writer = new StringWriter();

	        {
	            try
	            {
	                // Using string-based configuration.
	                var options = "localhost,connectRetry=3,connectTimeout=2000";
	                ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

	                // Store a reference to the multiplexer for use in the application.
	            }
	            catch
	            {
	                Console.WriteLine(writer.ToString());
	                throw;
	            }
	        }
	    }
	}
}
```

Para obter mais exemplos, consulte [Configuração](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration) no site do projeto.

## Mais informações

* [Site do Redis](http://redis.io/)

## Diretrizes de repetição para Banco de Dados de Documentos (pré-lançamento)

O Banco de Dados de Documentos é um serviço como banco de dados de documentos totalmente gerenciado com recursos avançados de consulta e indexação sobre um modelo de dados JSON sem esquema. Ele oferece desempenho configurável e confiável, processamento transacional nativo JavaScript, além de ser criado para a nuvem com escala elástica.

## Mecanismo de repetição

A versão de pré-lançamento do cliente do Banco de Dados de Documentos inclui um mecanismo de repetição interno e não configurável (isso pode mudar em versões subsequentes). As configurações padrão variam de acordo com o contexto de seu uso. Algumas operações usam uma estratégia de retirada exponencial com parâmetros embutidos em código. Outras especificam apenas quantas tentativas de repetição devem ser realizadas e usam o intervalo de repetição na instância [DocumentClientException](http://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx) que é retornada do serviço. Um intervalo de cinco segundos será usado se nenhum intervalo for especificado.

## Configuração de política (Banco de Dados de Documentos)

Nenhuma. Todas as classes usadas para implementar repetições são internas. Os parâmetros de repetição são constantes ou são definidos usando parâmetros para os construtores de classe.

A tabela a seguir mostra as configurações padrão da política de repetição interna.

| **Contexto** | **Configurações** | **Valores** | **Como funciona** |
|------------------------|---------------------------------------------------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RetryPolicy (interno) | MaxRetryAttemptsOnQuery<br /><br />MaxRetryAttemptsOnRequest | 3<br /><br />0 | O número de tentativas de repetição para consultas de documento. Esse valor não pode ser alterado.<br />O número de tentativas de repetição para outras solicitações. Este valor não pode ser alterado. |

## Diretriz de uso de repetição

Considere as seguintes diretrizes ao usar o Banco de Dados de Documentos:

* Você não pode alterar a política de repetição padrão.
* Consulte [a ser definido] para saber mais sobre as configurações padrão.

## Telemetria

As tentativas de repetição são registradas como mensagens de rastreamento não estruturadas por meio de um **TraceSource** do .NET. Você deve configurar um **TraceListener** para capturar os eventos e gravá-los em um log de destino apropriado.

## Diretrizes de repetição para Pesquisa

A Pesquisa do Azure pode ser usada para adicionar recursos potentes e sofisticados a um site ou aplicativo, ajustar os resultados da pesquisa de maneira rápida e fácil, bem como construir modelos de classificação avançados e ajustados.

### Mecanismo de repetição

Não há mecanismo de repetição interno para Pesquisa, uma vez que o uso comum é por solicitações HTTP. Para implementar repetições, você pode usar uma implementação genérica de um cliente REST e tomar decisões sobre quando e se deve repetir a operação com base na resposta do serviço. Para saber mais, consulte a seção [Diretrizes gerais de repetição e REST](#general-rest-and-retry-guidelines) mais adiante neste guia.

### Diretriz de uso de repetição

Considere as seguintes diretrizes ao usar a Pesquisa do Azure:

* Use o código de status retornado pelo serviço para determinar o tipo de falha. Os códigos de status são definidos nos [códigos de status HTTP (Pesquisa do Azure)](http://msdn.microsoft.com/library/dn798925.aspx). O código de status 503 (Serviço Indisponível) indica que o serviço está sob carga pesada e a solicitação não pode ser processada imediatamente. A ação adequada é repetir a operação apenas após o tempo permitido para recuperação do serviço. Repetir após um intervalo muito curto provavelmente prolongará a indisponibilidade.
* Consulte a seção [Diretrizes gerais de repetição e REST](#general-rest-and-retry-guidelines) mais adiante nesta diretriz para obter informações gerais sobre como repetir operações REST.

## Mais informações

* [API REST da Pesquisa do Azure](http://msdn.microsoft.com/library/dn798935.aspx)

## Diretrizes de repetição para o Active Directory do Azure

O AD (Active Directory) do Azure é uma solução abrangente de nuvem para gerenciamento de acesso e identidade que combina serviços principais de diretório, governança avançada de identidade, segurança e gerenciamento de acesso a aplicativos. O AD do Azure também oferece aos desenvolvedores uma plataforma de gerenciamento de identidade para fornecer controle de acesso aos respectivos aplicativos, com base nas regras e políticas centralizadas.

### Mecanismo de repetição

Não há mecanismo de repetição interno para o Active Directory do Azure na ADAL (Biblioteca de Autenticação do Active Directory). Você pode usar o Bloco de Aplicativos para Tratamento de Falhas Transitórias para implementar uma estratégia de repetição que contenha um mecanismo de detecção personalizado para as exceções retornadas pelo Active Directory.

### Configuração de política (Active Directory do Azure)

Ao usar o Bloco de Aplicativos para Tratamento de Falhas Transitórias com o Active Directory do Azure, você cria uma instância **RetryPolicy** com base em uma classe que define a estratégia de detecção que deseja usar.

```csharp
var policy = new RetryPolicy<AdalDetectionStrategy>(new ExponentialBackoff(retryCount: 5,
	                                                                 minBackoff: TimeSpan.FromSeconds(0),
	                                                                 maxBackoff: TimeSpan.FromSeconds(60),
	                                                                 deltaBackoff: TimeSpan.FromSeconds(2)));
```

Em seguida, chame o método **ExecuteAction** ou **ExecuteAsync** da política de repetição, passando-o para a operação que deseja executar.

```csharp
var result = await policy.ExecuteAsync(() => authContext.AcquireTokenAsync(resourceId, clientId, uc));
```

A classe de estratégia de detecção recebe exceções quando uma falha ocorre, devendo detectar a probabilidade de ser uma falha transitória ou uma mais permanente. Geralmente, ela faz isso examinando o tipo de exceção e o código de status. Por exemplo, uma resposta de Serviço Indisponível indica que deve ser feita uma tentativa de repetição. O Bloco de Aplicativos para Tratamento de Falhas Transitórias não inclui uma classe de estratégia de detecção que seja adequada para uso com o cliente ADAL, mas um exemplo de uma estratégia de detecção personalizada é fornecido na seção [Exemplos](#examples-azure-active-directory-) abaixo. Usar uma estratégia de detecção personalizada não é diferente de usar uma fornecida com o bloco.

As estratégias padrão do Bloco de Aplicativos para Tratamento de Falhas Transitórias são mostradas na seção [Estratégias do Bloco de Aplicativos para Tratamento de Falhas Transitórias (Topaz)](#transient-fault-handling-application-block-topaz-strategies) no fim desta diretriz.

## Diretriz de uso de repetição

Considere as seguintes diretrizes ao usar o Active Directory do Azure:

* Se estiver usando a API REST do Active Directory do Azure, você deverá repetir a operação somente se o resultado for um erro no intervalo 5xx (como 500 Erro de Servidor Interno, 502 Gateway Incorreto, 503 Serviço Indisponível e 504 Tempo Limite do Gateway). Não repita para nenhum outro erro.
* Se você estiver usando a ADAL (Biblioteca de Autenticação do Active Directory), os códigos HTTP não estarão prontamente acessíveis. Será preciso criar uma estratégia de detecção personalizada que inclua a lógica para verificar as propriedades das exceções específicas da ADAL. Consulte a seção [Exemplos](#examples-azure-active-directory-) abaixo.
* Uma política de retirada exponencial é recomendada para uso em cenários de lote com o Active Directory do Azure.

Considere começar com as seguintes configurações para operações de repetição. Essas são configurações de finalidade geral, por isso, você deve monitorar as operações e ajustar os valores para que atendam ao seu cenário.


| **Contexto** | **Exemplo de latência<br />E2E máxima de destino** | **Estratégia de repetição** | **Configurações** | **Valores** | **Como funciona** |
|----------------------|----------------------------------------------|--------------------|-----------------------------------------------------------------------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Interativo, interface de usuário<br />ou primeiro plano | 2 s | FixedInterval | Contagem de repetição<br />Intervalo de repetição<br />Primeira repetição rápida | 3<br />500 ms<br />verdadeiro | 1ª tentativa — intervalo de 0 s<br />2ª tentativa — intervalo de 500 ms<br />3ª tentativa — intervalo de 500 ms |
| Segundo plano ou<br />lote | 60 s | ExponentialBackoff | Contagem de repetição<br />Retirada mín.<br />Retirada máx.<br />Retirada Delta<br />Primeira repetição rápida | 5<br />0 s<br />60 s<br />2 s<br />falso | 1ª tentativa — intervalo de 0 s<br />2ª tentativa — intervalo de aprox. 2 s<br />3ª tentativa — intervalo de aprox. 6 s<br />4ª tentativa — intervalo de aprox. de 14 s<br />5ª tentativa — intervalo de aprox. 30 s |

## Exemplos (Active Directory do Azure)

O exemplo de código a seguir mostra como você pode usar o Bloco de Aplicativos para Tratamento de Falhas Transitórias (Topaz) para definir uma estratégia de detecção de erro transitório personalizada adequada para uso com o cliente ADAL. O código cria uma nova instância **RetryPolicy** com base em uma estratégia de detecção personalizada do tipo **AdalDetectionStrategy**, conforme definido na listagem de códigos abaixo. As estratégias de detecção personalizadas para Topaz implementam a interface **ITransientErrorDetectionStrategy** e retornam verdadeiro se uma tentativa de repetição deve ser realizada ou **falso** se a falha parecer ser não transitória e uma tentativa de repetição não deve ser feita.

	using System;
	using System.Linq;
	using System.Net;
	using System.Threading.Tasks;
	using Microsoft.Practices.TransientFaultHandling;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;

	namespace RetryCodeSamples
	{
	    class ActiveDirectoryCodeSamples
	    {
	        public async static Task Samples()
	        {
	            var authority = "[some authority]";
	            var resourceId = “[some resource id]”;
	            var clientId = “[some client id]”;

	            var authContext = new AuthenticationContext(authority);

	            var uc = new UserCredential(“[user]", "[password]");

	            // Use Topaz with a custom detection strategy to manage retries.
	            var policy =
	                new RetryPolicy<AdalDetectionStrategy>(
	                    new ExponentialBackoff(
	                        retryCount: 5,
	                        minBackoff: TimeSpan.FromSeconds(0),
	                        maxBackoff: TimeSpan.FromSeconds(60),
	                        deltaBackoff: TimeSpan.FromSeconds(2)));

	            var result = await policy.ExecuteAsync(() => authContext.AcquireTokenAsync(resourceId, clientId, uc));

	            // Get the access token
	            var accessToken = result.AccessToken;

	            // Use the result, probably to authorize an API call.
	        }
	    }

	    // TODO: This is sample code that needs validation from the WAAD team!
	    // based on existing detection strategies
	    public class AdalDetectionStrategy : ITransientErrorDetectionStrategy
	    {
	        private static readonly WebExceptionStatus[] webExceptionStatus =
	            new[]
	            {
	                WebExceptionStatus.ConnectionClosed,
	                WebExceptionStatus.Timeout,
	                WebExceptionStatus.RequestCanceled
	            };

	        private static readonly HttpStatusCode[] httpStatusCodes =
	            new[]
	            {
	                HttpStatusCode.InternalServerError,
	                HttpStatusCode.GatewayTimeout,
	                HttpStatusCode.ServiceUnavailable,
	                HttpStatusCode.RequestTimeout
	            };

	        public bool IsTransient(Exception ex)
	        {
	            var adalException = ex as AdalException;
	            if (adalException == null)
	            {
	                return false;
	            }

	            if (adalException.ErrorCode == AdalError.ServiceUnavailable)
	            {
	                return true;
	            }

	            var innerWebException = adalException.InnerException as WebException;
	            if (innerWebException != null)
	            {
	                if (webExceptionStatus.Contains(innerWebException.Status))
	                {
	                    return true;
	                }

	                if (innerWebException.Status == WebExceptionStatus.ProtocolError)
	                {
	                    var response = innerWebException.Response as HttpWebResponse;
	                    return response != null && httpStatusCodes.Contains(response.StatusCode);
	                }
	            }

	            return false;
	        }
	    }
	}

Para saber mais sobre como repetir operações de Graph API do Active Directory e sobre os códigos de erro retornados, consulte:

* [Exemplo de código: lógica de repetição](http://msdn.microsoft.com/library/azure/dn448547.aspx)
* [Códigos de erro do Azure AD Graph](http://msdn.microsoft.com/library/azure/hh974480.aspx)

## Mais informações

* [Implementando uma estratégia de detecção personalizada](http://msdn.microsoft.com/library/hh680940.aspx) (Topaz)
* [Implementando uma estratégia de repetição personalizada](http://msdn.microsoft.com/library/hh680943.aspx) (Topaz)
* [Diretrizes de emissão e repetição de tokens](http://msdn.microsoft.com/library/azure/dn168916.aspx)

## Diretrizes gerais de REST e repetição

Considere o seguinte ao acessar os serviços do Azure ou de terceiros:

* Use uma abordagem sistemática para gerenciar repetições, talvez como código reutilizável, para que você possa aplicar uma metodologia consistente em todos os clientes e soluções.
* Considere usar uma estrutura de repetição, como o Bloco de Aplicativos para Tratamento de Falhas Transitórias, para gerenciar repetições se o serviço ou cliente de destino não tiver algum mecanismo de repetição interno. Isso ajudará você a implementar um comportamento de repetição consistente, bem como pode fornecer uma estratégia de repetição padrão adequada para o serviço de destino. No entanto, talvez seja necessário criar código de repetição personalizado para serviços que tenham comportamento não padrão, que não dependem de exceções para indicar falhas transitórias, ou se desejar, use uma resposta **Retry-Response** para gerenciar o comportamento de repetição.
* A lógica de detecção transitória dependerá da API de cliente real que você usa para invocar as chamadas REST. Alguns clientes, como a classe mais recente **HttpClient**, não lançam exceções para solicitações concluídas com um código de status HTTP sem sucesso. Isso melhora o desempenho, mas impede o uso do Bloco de Aplicativos para Tratamento de Falhas Transitórias. Nesse caso, você pode encapsular a chamada à API REST com o código que gera exceções para códigos de status HTTP sem sucesso, que pode então ser processada pelo bloco. Como alternativa, é possível usar um mecanismo diferente para orientar as repetições.
* O código de status HTTP retornado do serviço pode ajudar a indicar se a falha é transitória. Talvez seja necessário examinar as exceções geradas por um cliente ou pela estrutura de repetição para acessar o código de status ou determinar o tipo de exceção equivalente. Os seguintes códigos HTTP geralmente indicam que uma repetição é apropriada:
  * 408 Tempo Limite da Solicitação
  * 500 Erro Interno do Servidor
  * 502 Gateway Incorreto
  * 503 Serviço Indisponível
  * 504 Tempo Limite do Gateway
* Se você basear suas lógica de repetição em exceções, geralmente o que se segue indica uma falha transitória onde nenhuma conexão pode ser estabelecida:
  * WebExceptionStatus.ConnectionClosed
  * WebExceptionStatus.ConnectFailure
  * WebExceptionStatus.Timeout
  * WebExceptionStatus.RequestCanceled
* No caso de um status de serviço indisponível, o serviço pode indicar o intervalo apropriado antes de tentar a repetição no cabeçalho da resposta **Retry-After** ou um cabeçalho personalizado diferente (como no serviço Banco de Dados de Documentos). Os serviços também podem enviar informações adicionais como cabeçalhos personalizados ou inseridos no conteúdo da resposta. O Bloco de Aplicativos para Tratamento de Falhas Transitórias não pode usar os cabeçalhos “retry-after” padrão ou personalizados.
* Não tente a repetição para códigos de status que representam erros de cliente (erros no intervalo 4xx), exceto para um 408 Tempo Limite de Solicitação.
* Teste minuciosamente seus mecanismos e estratégias de repetição sob diversas condições, como estado diferente de rede e cargas variáveis de sistema.

## Estratégias de repetição

Veja a seguir os tipos comuns de intervalo de estratégias de repetição:

* **Exponencial**: uma política de repetição que executa um determinado número de repetições usando uma abordagem de retirada exponencial aleatória para determinar o intervalo entre as repetições. Por exemplo:

		var random = new Random();

		var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
		            random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
		            (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
		var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
		               this.maxBackoff.TotalMilliseconds);
		retryInterval = TimeSpan.FromMilliseconds(interval);

* **Incremental**: uma estratégia de repetição com um número especificado de tentativas de repetição e um intervalo de tempo incremental entre entradas. Por exemplo:

		retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
		               (this.increment.TotalMilliseconds * currentRetryCount));

* **LinearRetry**: uma política de repetição que executa um número especificado de repetições usando um intervalo de tempo fixo especificado entre as repetições. Por exemplo:

		retryInterval = this.deltaBackoff;

## Mais informações

* [Estratégias de disjuntor](http://msdn.microsoft.com/library/dn589784.aspx)

## Estratégias do Bloco de Aplicativos para Tratamento de Falhas Transitórias (Topaz)

O Bloco de Aplicativos para Tratamento de Falhas Transitórias tem as estratégias padrão a seguir.

| **Estratégia** | **Configuração** | **Valor padrão** | **Significado** |
|-------------------------|-----------------------------------------------------|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Exponencial** | retryCount<br />minBackoff<br /><br />maxBackoff<br /><br />deltaBackoff<br /><br />fastFirstRetry | 10<br />1 segundo<br /><br />30 segundos<br /><br />10 segundos<br /><br />verdadeiro | O número de tentativas de repetição.<br />O tempo mínimo de retirada. O número mais alto desse valor ou da retirada calculada será usado como o intervalo de repetição.<br />O tempo mínimo de retirada. O número mais baixo desse valor ou da retirada calculada será usado como o intervalo da repetição.<br />O valor usado para calcular o delta aleatório para o intervalo exponencial entre repetições.<br />Se a primeira tentativa de repetição será feita imediatamente. |
| **Incremental** | retryCount<br />initialInterval<br />increment<br /><br />fastFirstRetry<br />| 10<br />1 segundo<br />1 segundo<br /><br />verdadeiro | O número de tentativas de repetição.<br />O intervalo inicial que será aplicado para a primeira tentativa.<br />O valor de tempo incremental que será usado para calcular o intervalo progressivo entre repetições.<br />Se a primeira tentativa de repetição será feita imediatamente. |
| **Linear (intervalo fixo)** | retryCount<br />retryInterval<br />fastFirstRetry<br /> | 10<br />1 segundo<br />verdadeiro | O número de tentativas de repetição.<br />O intervalo entre repetições.<br />Se a primeira tentativa de repetição será feita imediatamente. |
Para exemplos de como usar o Bloco de Aplicativos para Tratamento de Falhas Transitórias, consulte as seções Exemplos anteriormente nesta diretriz para Banco de Dados SQL usando o ADO.NET e Active Directory do Azure.

<!---HONumber=AcomDC_0720_2016-->