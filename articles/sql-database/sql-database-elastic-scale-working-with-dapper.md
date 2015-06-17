<properties 
	pageTitle="Usando a escala elástica com Dapper" 
	description="Usando a escala elástica com Dapper." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="torsteng"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2015" 
	ms.author="torsteng@microsoft.com"/>

# Usando a escala elástica com Dapper 

Este documento é destinado aos desenvolvedores que dependem do Dapper para criação de aplicativos, mas também querem adotar a [Escala elástica do banco de dados SQL Azure](http://azure.microsoft.com/documentation/articles/sql-database-elastic-scale-introduction/) para o crescimento e redução elástica da capacidade por meio de fragmentação e expansão para seus aplicativos. Este documento ilustra as alterações em aplicativos baseados no Dapper que são necessárias para integrar os recursos de escala elástica atuais. Nosso foco está na composição de dados dependentes e gerenciamento de fragmento de Escala elástica roteada com Dapper. 

**Exemplo de código**: [Escala elástica com banco de dados SQL Azure - integração Dapper](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).
 
Integrar o **Dapper** e as **DapperExtensions** com a escala elástica do banco de dados SQL Azure é fácil. Os aplicativos podem usar dados de escala elástica dependentes de roteamento alterando a criação e abertura de novos objetos [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) para usar a chamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) a partir da [biblioteca de Escala elástica](http://msdn.microsoft.com/library/azure/dn765902.aspx). Isso limita as alterações em seu aplicativo apenas quando novas conexões forem criadas e abertas. 

## Visão geral do Dapper
**Dapper** é um mapeador relacional de objetos. Ele mapeia objetos .NET a partir de seu aplicativo para um banco de dados relacional (e vice-versa). A primeira parte do código de exemplo ilustra como você pode integrar a Escala elástica do banco de dados Azure SQL com aplicativos baseados em Dapper. A segunda parte do código de exemplo ilustra como a escala elástica pode integrar ao usar o Dapper e as DapperExtensions. 

A funcionalidade do mapeador em Dapper fornece métodos de extensão em conexões de banco de dados que simplificam o envio de instruções T-SQL para execução ou consulta ao banco de dados. Por exemplo, o Dapper torna mais fácil mapear entre seus objetos .NET e os parâmetros de instruções SQL por chamadas **Execute**, ou para consumir os resultados de suas consultas SQL em objetos .NET usando chamadas **Query** a partir do Dapper. 

Ao usar DapperExtensions, você não precisa fornecer as instruções SQL. Métodos de extensão, como **GetList** ou **Insert** através da conexão de banco de dados cria as instruções SQL nos bastidores.
 
Outro benefício do Dapper e também das DapperExtensions é que o aplicativo controla a criação da conexão de banco de dados. Isso ajuda a interagir com a escala elástica que serve de intermediária das conexões do banco de dados com base no mapeamento de shardlets para bancos de dados.

Para obter os assemblies Dapper, consulte [Dapper dot net](http://www.nuget.org/packages/Dapper/). Para as extensões do Dapper, consulte [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## Uma rápida olhada na Escala elástica

Com a escala elástica do banco de dados SQL Azure, você definir partições dos dados do aplicativo chamados *shardlets* , mapeá-los para bancos de dados e identificá-los por *sharding keys*. Você pode ter quantos bancos de dados conforme necessário e distribuir seu shardlets entre esses bancos de dados. O mapeamento de valores chave de fragmentação para os bancos de dados é armazenado por um mapa do fragmento fornecido pelas APIs de Escala Elástica. Chamamos esse recurso de **gerenciamento de mapa do fragmento**. O mapa do fragmento também serve como o agente de conexões de banco de dados para solicitações que carregam uma chave de fragmentação. Chamamos esse recurso como **roteamento dependente de dados**.

![Shard maps and data dependent routing][1]

O Gerenciador de mapa do fragmento na Escala Elástica protege os usuários contra modos de exibição inconsistentes em dados shardlet que podem ocorrer quando as operações de gerenciamento simultâneo de shardlet estão ocorrendo nos bancos de dados. Para tanto, os mapas do fragmento na Escala Elástica arranjam as conexões de banco de dados para um aplicativo de Escala Elástica. Quando as operações de gerenciamento de fragmentação podem afetar o shardlet, isso permite que a funcionalidade de mapa do fragmento elimine automaticamente uma conexão de banco de dados. 

Em vez de usar a maneira tradicional de criar conexões para Dapper, precisamos usar o [método OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn824099.aspx). Isso garante que todos as validações ocorram e as conexões são gerenciadas corretamente quando os dados se movem entre os fragmentos.

### Requisitos para a integração do Dapper

Ao trabalhar com a escala elástica e as APIs Dapper, queremos manter as seguintes propriedades:

* **Expansão**: Para adicionar ou remover bancos de dados da camada de dados do aplicativo fragmentado, conforme necessário para as demandas de capacidade do aplicativo. 
-    **Consistência**: Como nosso aplicativo é dimensionado com o uso de fragmentação, precisamos executar roteamento dependente de dados. Queremos usar os recursos de roteamento dependentes de dados da escala elástica para fazer isso. Em particular, queremos reter a validação e garantir a consistência fornecida pelas conexões orientadas por meio do Gerenciador de mapa do fragmento de escala elástica para evitar corrupção ou resultados de consulta incorretos. Isso garante que conexões para um determinado shardlet sejam rejeitadas ou interrompidas se (por exemplo) o shardlet é movido num momento para um fragmento diferente usando APIs de divisão/mesclagem.
-    **Mapeamento de objeto**: Queremos manter a conveniência dos mapeamentos fornecidos pelo Dapper para converter entre classes no aplicativo e as estruturas de banco de dados subjacente. 

A seção a seguir fornece orientação para esses requisitos de aplicativos com base em **Dapper** e **DapperExtensions**.

## Orientações técnicas
### Roteamento com Dapper dependentes dos dados 

Com Dapper, o aplicativo é geralmente responsável por criar e abrir as conexões de banco de dados subjacente. Fornecendo um tipo T pelo aplicativo, o Dapper retorna resultados da consulta como coleções .NET do tipo T. O Dapper executa o mapeamento das linhas de resultado do T-SQL para os objetos do tipo T. Da mesma forma, o Dapper mapeia objetos .NET em valores ou parâmetros SQL para instruções de linguagem de manipulação de dados (DML). O Dapper oferece essa funcionalidade por meio de métodos de extensão no objeto [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) normal a partir de bibliotecas do Cliente SQL do ADO .NET. A conexão do SQL retornada por APIs de escala elástica para DDR também são objetos regulares da [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx). Isso nos permite usar extensões Dapper diretamente no tipo retornado pela API de DDR de escala elástica, como ele também é uma conexão de cliente SQL simples.

Essas observações tornam simples usar conexões orientadas por escala elástica para Dapper. 

Este exemplo de código (do exemplo que acompanha este artigo) ilustra a abordagem onde a chave de fragmentação é fornecida pelo aplicativo para a biblioteca de escala elástica para intermediar a conexão para o fragmento correto.  

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

A chamada para a API [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) substitui a criação e a abertura padrão de uma conexão de cliente SQL. A chamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) usa os argumentos que são necessários para dados dependentes de roteamento com escala elástica: 

-    o mapa do fragmento para acessar as interfaces de roteamento dependente de dados
-    a chave de fragmentação para identificar o shardlet
-    as credenciais (nome de usuário e senha) para conectar-se ao fragmento

o objeto do mapa do fragmento cria uma conexão para o fragmento que mantém o shardlet para a chave de fragmentação determinada. as APIs de escala elástica também marcam a conexão para implementar suas garantias de consistência. Uma vez que a chamada para [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) retorna um objeto de conexão do Cliente SQL regular, a chamada subsequente para o método de extensão **Execute** do Dapper segue a prática padrão do Dapper.

As consultas funcionam praticamente da mesma forma, você primeiro abre a conexão usando [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) da API de escala elástica. Em seguida, você deve usar os métodos de extensão Dapper regulares para mapear os resultados da consulta SQL nos objetos .NET:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Observe que o **uso** de bloco com a conexão DDR da escala elástica abrange todas as operações de banco de dados dentro do bloco para o único fragmento onde tenantId1 é mantido. A consulta retorna apenas os blogs armazenados no fragmento atual, mas não os arquivos armazenados em quaisquer outros fragmentos. 

## Roteamento dependente de dados com Dapper e DapperExtensions

O Dapper vem com um ecossistema de extensões adicionais que podem fornecer mais conveniência e abstração do banco de dados ao desenvolver aplicativos de banco de dados. O DapperExtensions é um exemplo. 

O uso do DapperExtensions em seu aplicativo não muda como conexões de banco de dados são criadas e gerenciadas. Ainda é responsabilidade do aplicativo abrir conexões, e objetos de conexão do Cliente SQL regulares são aguardados pelos métodos de extensão. Podemos contar com a [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) conforme descrito acima. Como mostram os exemplos de código a seguir, a única alteração é que não temos de escrever as instruções T-SQL:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

E aqui está o exemplo de código para a consulta: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### Manipulando falhas transitórias

A equipe Microsoft Patterns & Practices publicou o [Bloco de aplicativos de manipulação de falhas transitórias](http://msdn.microsoft.com/library/hh680934.aspx) para ajudar os desenvolvedores de aplicativos a atenuarem condições de falha transitória comuns encontradas durante a execução na nuvem. Para obter mais informações, consulte [Perseverança, o segredo de todos os triunfos: Uso do Bloco de aplicativos de manipulação de falhas transitórias](http://msdn.microsoft.com/library/dn440719.aspx).

O código de exemplo se baseia na biblioteca de falha transitória para proteger contra falhas transitórias. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** no código acima é definido como um **SqlDatabaseTransientErrorDetectionStrategy** com uma contagem de repetição de 10, e um tempo de espera de 5 segundos entre as tentativas. Se você estiver usando transações, certifique-se de que o escopo de repetição volta para o início da transação no caso de uma falha temporária.

## Limitações

As abordagens descritas neste documento envolvem algumas limitações:

* O código de exemplo para este documento demonstra como gerenciar o esquema em fragmentos.
* Recebida uma solicitação, pressupomos que todo o processamento de seu banco de dados está contido em um único fragmento como identificado pela chave de fragmentação fornecida pela solicitação. No entanto, esse pressuposto não é sempre mantido, por exemplo, quando não é possível disponibilizar uma chave de fragmentação. Para resolver isso, as bibliotecas de escala elástica oferecem a [classe MultiShardQuery](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). A classe implementa uma abstração de conexão para consultas em vários fragmentos. O uso da MultiShardQuery em combinação com Dapper está além do escopo deste documento.

## Conclusão

Aplicativos que usam **Dapper** e **DapperExtensions** podem facilmente se beneficiar da escala elástica do banco de dados SQL Azure. Através das etapas descritas neste documento, esses aplicativos podem usar recursos da escala elástica para dados dependentes de roteamento alterando a criação e abertura de novos objetos [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) para usar a chamada [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) da biblioteca de escala elástica. Isso limita as alterações de aplicativo necessárias para os locais onde as novas conexões são criadas e abertas. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png

<!--HONumber=47-->
 