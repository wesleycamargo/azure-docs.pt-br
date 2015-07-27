<properties 
	pageTitle="Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha" 
	description="Saiba como usar as ferramentas de banco de dados elástico com segurança em nível de linha para criar um aplicativo com uma camada de dados altamente dimensionável no Banco de Dados SQL que dá suporte a fragmentos multilocatários." 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="tmullaney"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/26/2015" 
	ms.author="thmullan;torsteng;sidneyh" />

# Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha 

[Ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md) e [segurança em nível de linha (RLS)](https://msdn.microsoft.com/library/dn765131) oferecem um poderoso conjunto de recursos para dimensionar a camada de dados de um aplicativo multilocatário com um Banco de Dados SQL do Azure de maneira flexível e eficiente. Este artigo ilustra como usar essas tecnologias em conjunto para criar um aplicativo com uma camada de dados altamente dimensionável e compatível com fragmentos multilocatários usando **ADO.NET SqlClient** e/ou **Entity Framework**.

* **Ferramentas de banco de dados elástico** permitem que os desenvolvedores expandir a camada de dados de um aplicativo por meio de práticas de fragmentação padrão do setor usando um conjunto de bibliotecas .NET e modelos de serviço do Azure. Gerenciar fragmentos usando a Biblioteca Cliente do Banco de Dados Elástico ajuda a automatizar e simplificar muitas das tarefas infraestruturais normalmente associadas à fragmentação. 

* A **segurança em nível de linha (visualização)** permite que os desenvolvedores armazenem dados para vários locatários no mesmo banco de dados usando políticas de segurança para filtrar linhas que não pertencem ao locatário executando uma consulta. Centralizar a lógica de acesso com RLS no banco de dados, em vez de no aplicativo, simplifica a manutenção e reduz o risco de erros conforme a base de código de um aplicativo é expandida. A RLS requer a última [atualização do Banco de Dados SQL (V12) do Azure](sql-database-preview-whats-new.md).

Ao usar esses recursos em conjunto, um aplicativo pode desfrutar de ganhos de eficiência e economia de custo armazenando dados para vários locatários no mesmo banco de dados de fragmentos. Ao mesmo tempo, um aplicativo ainda terá a flexibilidade para oferecer fragmentos isolados de um locatário para locatários "premium" que exigem garantias de desempenho mais rígidas, já que os fragmentos multilocatários não garantem a distribuição uniforme dos recursos entre os locatários.

Em resumo, as APIs de [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) da biblioteca cliente do banco de dados elástico são conectadas automaticamente ao banco de dados de fragmentos correto que contém sua chave de fragmentação (geralmente um "TenantId"). Após o estabelecimento da conexão, uma política de segurança RLS no banco de dados garante que os locatários possam acessar somente as linhas que contêm seu TenantId. Presume-se que todas as tabelas contenham uma coluna TenantId para indicar quais linhas pertencem a cada locatário.

![Arquitetura de aplicativo de blog][1]

## Baixar o projeto de exemplo

### Pré-requisitos
* Usar o Visual Studio (2012 ou superior) 
* Criar três Bancos de Dados SQL do Azure 
* Baixar o projeto de exemplo: [Ferramentas de Banco de Dados Elástico para o SQL do Azure - Fragmentos Multilocatários](http://go.microsoft.com/?linkid=9888163)
  * Preencha as informações dos seus bancos de dados no início do **Program.cs** 

Esse projeto expande o descrito em [Ferramentas de Banco de Dados Elástico para o SQL do Azure - Integração com o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) adicionando suporte para bancos de dados de fragmentos multilocatários. Ele cria um aplicativo de console simples para a criação de blogs e postagens, com quatro locatários e dois bancos de dados de fragmentos multilocatários, conforme é ilustrado no diagrama acima.

Compile e execute o aplicativo. Isso inicializará o gerenciador de mapas de fragmentos das ferramentas de banco de dados elástico e executará os seguintes testes:

1. Usando o Entity Framework e o LINQ, crie um novo blog e exiba todos os blogs para cada locatário
2. Usando o ADO.NET SqlClient, exiba todos os blogs para um locatário
3. Tente inserir um blog para o locatário errado para verificar se um erro será gerado  

Observe que, como a RLS ainda não foi habilitada nos bancos de dados de fragmentos, cada um desses testes revela um problema: locatários podem ver blogs que não pertencem a eles e o aplicativo não é impedido de inserir um blog para o locatário errado. O restante deste artigo descreve como resolver esses problemas impondo o isolamento de locatários com RLS. Há duas etapas:

1. **Camada de aplicativo**: modifique o código do aplicativo para sempre definir CONTEXT_INFO como o TenantId atual após a abertura de uma conexão. O projeto de exemplo já fez isso. 
2. **Camada de dados**: crie uma política de segurança RLS em cada banco de dados de fragmentos para filtrar linhas com base no valor de CONTEXT_INFO. Você precisará fazer isso para cada um dos seus bancos de dados de fragmentos, caso contrário, linhas em fragmentos multilocatários não serão filtradas. 


## Etapa 1) Camada de aplicativo: definir CONTEXT_INFO como TenantId

Depois de se conectar a um banco de dados de fragmentos usando dados da biblioteca cliente do banco de dados elástico que depende de APIs de roteamento, o aplicativo ainda precisa informar o banco de dados qual TenantId está usando essa conexão para que uma política de segurança RLS possa filtrar linhas pertencentes a outros locatários. A maneira recomendada para transmitir essas informações é definir [CONTEXT_INFO](https://msdn.microsoft.com/library/ms180125) como o TenantId atual dessa conexão. Observe que, no Banco de Dados SQL do Azure, CONTEXT_INFO é populado previamente com um GUID específico da sessão, então é *necessário* definir CONTEXT_INFO para a TenantId correta antes de executar qualquer consulta em uma nova conexão para garantir que nenhuma linha seja perdida inadvertidamente.

### Entity Framework

Para aplicativos que usam o Entity Framework, a abordagem mais simples é definir CONTEXT_INFO na substituição ElasticScaleContext descrita em [Roteamento Dependente de Dados usando o EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md/#data-dependent-routing-using-ef-dbcontext). Antes de retornar a conexão negociada por meio do roteamento dependente de dados, crie e execute um SqlCommand que define CONTEXT_INFO como o shardingKey (TenantId) especificado para a conexão. Dessa maneira, só é preciso gravar o código uma vez para definir CONTEXT_INFO.

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data dependent routing. This call will open a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call will fail for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set CONTEXT_INFO to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SET CONTEXT_INFO @shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
} 
// ... 
```

Agora, o CONTEXT_INFO será automaticamente definido como o TenantId especificado sempre que ElasticScaleContext for invocado:

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
	using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
	{     
		var query = from b in db.Blogs
	                orderby b.Name
	                select b;
		
		Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
		foreach (var item in query)     
		{       
			Console.WriteLine(item.Name);     
		}   
	} 
}); 
```

### ADO.NET SqlClient 

Para aplicativos que usam o ADO.NET SqlClient, a abordagem recomendada é criar uma função de wrapper em torno de ShardMap.OpenConnectionForKey() que define automaticamente CONTEXT_INFO para a TenantId correta antes de retornar uma conexão. Para garantir que CONTEXT_INFO seja sempre definido corretamente, você só deve abrir conexões usando essa função de wrapper.

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets CONTEXT_INFO to the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that CONTEXT_INFO is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set CONTEXT_INFO to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SET CONTEXT_INFO @shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs will be listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## Etapa 2) Camada de dados: criar políticas e limitações de segurança em nível de linha 

### Criar uma política de segurança para filtrar consultas SELECT, UPDATE e DELETE 

Agora que o aplicativo está definindo CONTEXT_INFO como o TenantId atual antes de enviar a consulta, uma política de segurança RLS pode filtrar consultas e excluir linhas que tenham um TenantId diferente.

A RLS é implementada no T-SQL: uma função de predicado definida pelo usuário define a lógica de filtragem e uma política de segurança associa essa função a quantas tabelas desejar. Para este projeto, a função de predicado simplesmente verificará se o aplicativo (em vez de outro usuário do SQL) está conectado ao banco de dados, e se o valor de CONTEXT_INFO corresponde ao TenantId de uma determinada linha. As linhas que atendem a essas condições serão permitidas pelo filtro para consultas SELECT, UPDATE e DELETE. Se CONTEXT_INFO não tiver sido definido, nenhuma linha será retornada.

Para habilitar a RLS, execute o seguinte comando do T-SQL em todos os fragmentos usando Visual Studio (SSDT), SSMS ou o script do PowerShell incluído no projeto (ou, se você estiver usando [Trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-overview.md), você poderá usá-lo para automatizar a execução desse T-SQL em todos os fragmentos):

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
	RETURNS TABLE     
	WITH SCHEMABINDING
AS
	RETURN SELECT 1 AS fn_accessResult          
		WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
		AND CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) = @TenantId -- @TenantId (int) is 4 bytes 
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [AZURE.TIP]Para projetos mais complexos que precisam adicionar a função de predicado a centenas de tabelas, você pode usar um procedimento auxiliar armazenado que gera uma política de segurança automaticamente, adicionando um predicado a todas as tabelas em um esquema. Consulte [Aplicar Segurança em Nível de Linha a todas as tabelas – script auxiliar (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script).

Se você adicionar uma nova tabela posteriormente, basta alterar a política de segurança e adicionar um predicado de filtro à nova tabela:

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
	ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable 
GO 
```

Agora, se você executar novamente o aplicativo de exemplo, os locatários não poderão ver linhas que não pertencem a eles.

### Adicionar restrições CHECK para bloquear INSERTs e UPDATEs de locatários errados

No momento, as políticas de segurança RLS não impedem que o aplicativo acidentalmente insira linhas para o TenantId errado ou atualize o TenantId de uma linha visível como um novo valor. Para alguns aplicativos, como aplicativos de geração de relatórios somente leitura, isso não é um problema. No entanto, como esse aplicativo permite que os locatários insiram novos blogs, é recomendável criar uma proteção adicional que gere um erro se o código do aplicativo tentar inserir ou atualizar linhas que violem o predicado do filtro por engano. Conforme é descrito em [Segurança em Nível de Linha: bloqueando INSERTs não autorizados (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/23/row-level-security-blocking-unauthorized-inserts), a solução recomendada é criar uma restrição CHECK em cada tabela para impor o mesmo predicado de filtro RLS para operações de inserção e atualização.

Para adicionar restrições CHECK, execute o seguinte comando T-SQL em todos os fragmentos, usando o SSMS, SSDT ou o script PowerShell incluído (ou Trabalhos de Banco de Dados Elástico) conforme é descrito acima:

```
-- Create a scalar version of the predicate function for use in check constraints 
CREATE FUNCTION rls.fn_tenantAccessPredicateScalar(@TenantId int)     
	RETURNS bit 
AS     
	BEGIN     
		IF EXISTS( SELECT 1 FROM rls.fn_tenantAccessPredicate(@TenantId) )         
			RETURN 1     
		RETURN 0 
	END 
GO 

-- Add the function as a check constraint on all sharded tables 
ALTER TABLE Blogs     
	WITH NOCHECK -- don't check data already in table     
	ADD CONSTRAINT chk_blocking_Blogs -- needs a unique name     
	CHECK( rls.fn_tenantAccessPredicateScalar(TenantId) = 1 ) 
GO

ALTER TABLE Posts     
	WITH NOCHECK     
	ADD CONSTRAINT chk_blocking_Posts     
	CHECK( rls.fn_tenantAccessPredicateScalar(TenantId) = 1 ) 
GO 
```

Agora, o aplicativo não poderá inserir linhas que pertencem a locatários diferentes dos atualmente conectados ao banco de dados de fragmentos. Da mesma forma, o aplicativo não pode atualizar linhas visíveis para atribui-las a um TenantId diferente. Se o aplicativo tentar qualquer uma dessas operações, será gerado um DbUpdateException.


### Adicionar restrições padrão para preencher automaticamente o TenantId para INSERTs 

Além de usar restrições CHECK para bloquear inserções de locatário errado, você pode incluir uma restrição padrão em cada tabela para preencher automaticamente o TenantId com o valor atual de CONTEXT_INFO ao inserir linhas. Por exemplo:

```
-- Create default constraints to auto-populate TenantId with the value of CONTEXT_INFO for inserts 
ALTER TABLE Blogs     
	ADD CONSTRAINT df_TenantId_Blogs      
	DEFAULT CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) FOR TenantId 
GO

ALTER TABLE Posts     
	ADD CONSTRAINT df_TenantId_Posts      
	DEFAULT CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) FOR TenantId 
GO 
```

Agora, o aplicativo não precisa especificar um TenantId ao inserir linhas:

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
	using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
	{
		var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
		db.Blogs.Add(blog);     
		db.SaveChanges();   
	} 
}); 
```

> [AZURE.NOTE]Se você usar restrições padrão para um projeto do Entity Framework, é recomendável não incluir a coluna TenantId em seu modelo de dados do EF. Isso ocorre porque as consultas do Entity Framework fornecem automaticamente os valores padrão que substituirão as restrições padrão criadas no T-SQL que usam CONTEXT_INFO. Para usar restrições padrão no projeto de exemplo, por exemplo, você deve remover TenantId de DataClasses.cs (e executar Add-Migration no Console do Gerenciador de Pacotes) e usar o T-SQL para garantir que o campo só exista nas tabelas do banco de dados. Dessa forma, o EF não fornecerá valores padrão incorretos automaticamente ao inserir dados.

### (Opcional) Habilitar um "superusuário" acessar todas as linhas
Alguns aplicativos talvez queiram criar um "superusuário" que pode acessar todas as linhas, por exemplo, para permitir a emissão de relatórios em todos os locatários em todos os fragmentos ou para executar operações de divisão/mesclagem em fragmentos que envolvem a movimentação de linhas de locatário entre bancos de dados. Para habilitar isso, você deve criar um novo usuário do SQL ("superusuário" neste exemplo) em cada banco de dados do fragmento. Em seguida, altere a política de segurança com uma nova função de predicado que permite que esse usuário acesse todas as linhas:

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CONVERT(int, CONVERT(varbinary(4), CONTEXT_INFO())) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### Manutenção 

* **Adicionando novos fragmentos**: você deve executar o script T-SQL para habilitar a RLS (e adicionar restrições CHECK) em qualquer novo fragmento, caso contrário, consultas sobre esses fragmentos não serão filtradas.

* **Adicionando novas tabelas**: você deve adicionar um predicado de filtro à política de segurança em todos os fragmentos sempre que uma nova tabela for criada, caso contrário, as consultas na nova tabela não serão filtradas. Isso pode ser automatizado usando um gatilho DDL, conforme é descrito em [Aplicar Segurança em Nível de Linha automaticamente a tabelas recém-criadas (blog)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx).


## Resumo 

Ferramentas de banco de dados elástico e segurança em nível de linha podem ser usadas em conjunto para expandir a camada de dados de um aplicativo com suporte para fragmentos multilocatários e de um locatário. Fragmentos multilocatários podem ser usados para armazenar dados com mais eficiência (principalmente em casos em que uma grande quantidade de locatários têm apenas algumas linhas de dados), enquanto fragmentos de um locatário podem ser usados para dar suporte a locatários premium com requisitos de desempenho e isolamento mais rígidos. Para saber mais, confira o [Mapa da documentação das ferramentas de banco de dados elástico](sql-database-elastic-scale-documentation-map.md) ou a [referência da Segurança em Nível de Linha](https://msdn.microsoft.com/library/dn765131) no MSDN.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->

<!---HONumber=July15_HO2-->