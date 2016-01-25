<properties
    pageTitle="Consultas do banco de dados elástico para fragmentação (particionamento horizontal) | Microsoft Azure"
    description="como configurar consultas elásticas em partições horizontais"    
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/06/2016"
    ms.author="sidneyh;torsteng" />

# Consultas do banco de dados elástico para fragmentação (particionamento horizontal)

Este documento explica como configurar consultas de banco de dados elástico para cenários de particionamento horizontal e como executar as consultas. Para obter uma definição do cenário de particionamento horizontal, confira a [Visão geral da consulta de banco de dados elástico (visualização)](sql-database-elastic-query-overview.md).

![Consultar em fragmentos][1]

A funcionalidade faz parte do [conjunto de recursos do Banco de Dados Elástico do Banco de Dados](sql-database-elastic-scale-introduction.md) SQL do Azure.
 
## Criando objetos de banco de dados

A consulta de banco de dados elástico estende a sintaxe do T-SQL para fazer referência a camadas de dados que usam a fragmentação (ou o particionamento horizontal) para distribuir os dados entre vários bancos de dados. Esta seção fornece uma visão geral das instruções DDL associadas à consulta elástica em tabelas fragmentadas. Essas instruções criam a representação de metadados de sua camada de dados fragmentados no banco de dados de consulta elástico. Um pré-requisito para executar essas instruções é criar um mapa de fragmentos usando a biblioteca de clientes do banco de dados elástico. Para obter mais informações, veja [Gerenciamento do mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md) ou use o exemplo no seguinte tópico para criar um: [Introdução às ferramentas do banco de dados elástico](sql-database-elastic-scale-get-started.md).

A definição de objetos do banco de dados para a consulta de banco de dados elástico depende das seguintes instruções T-SQL, que são explicadas mais detalhadamente para o cenário de particionamento horizontal abaixo:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) 

* [CRIAR UMA CREDENCIAL NO ESCOPO DO BANCO DE DADOS](https://msdn.microsoft.com/library/mt270260.aspx)

* [CRIAR/REMOVER FONTE DE DADOS EXTERNA](https://msdn.microsoft.com/library/dn935022.aspx)

* [CRIAR/REMOVER TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx)

### 1\.1 Chave mestra do escopo do banco de dados e credenciais 

Uma credencial representa a ID de usuário e a senha que a consulta elástica usará para se conectar aos bancos de dados remotos no Banco de Dados SQL do Azure. Para criar a chave mestra necessária e uma credencial, use a seguinte sintaxe:

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = ’password’;
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = ‘<username>’,  
    SECRET = ‘<password>’
    [;]

Ou para remover a credencial e chave:

    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
**Observação** Verifique se o *< username>* não inclui nenhum sufixo *“@servername”*.

### 1\.2 Fontes de dados externas

Forneça as informações sobre o mapa de fragmentos e sua camada de dados definindo uma fonte de dados externa. A fonte de dados externa faz referência ao seu mapa de fragmentos. Uma consulta elástica então usa a fonte de dados externa e o mapa de fragmentos subjacente para enumerar os bancos de dados que participam da camada de dados. A sintaxe usada para criar uma fonte de dados externa é definida da seguinte maneira:

	<External_Data_Source> ::=    
	CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                               	           
			(TYPE = SHARD_MAP_MANAGER,
                   	LOCATION = '<fully_qualified_server_name>',
			DATABASE_NAME = ‘<shardmap_database_name>',
			CREDENTIAL = <credential_name>, 
			SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 
 
ou para remover uma fonte de dados externa:

	DROP EXTERNAL DATA SOURCE <data_source_name>[;] 

#### Permissões para CREATE/DROP EXTERNAL DATA SOURCE 

O usuário deve ter a permissão para ALTERAR QUALQUER FONTE DE DADOS EXTERNA. Essa permissão está incluída na permissão ALTERAR BANCO DE DADOS.

**Exemplo**

O exemplo a seguir ilustra o uso da instrução CRIAR para fontes de dados externas.

	CREATE EXTERNAL DATA SOURCE MyExtSrc 
	WITH 
	( 
		TYPE=SHARD_MAP_MANAGER,
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ShardMapDatabase', 
		CREDENTIAL= SMMUser, 
		SHARD_MAP_NAME='ShardMap' 
	);
 
Você pode recuperar a lista de fontes de dados externas atual na exibição do catálogo a seguir:

	select * from sys.external_data_sources; 

Observe que as mesmas credenciais são usadas para ler o mapa de fragmentos e acessar os dados nos fragmentos durante o processamento de uma consulta elástica.

### 1\.3 Tabelas externas 
 
A consulta elástica estende a DDL da tabela externa para fazer referência a tabelas externas que são particionadas horizontalmente em vários bancos de dados. A definição da tabela externa abrange os seguintes aspectos:

* **Esquema**: a DDL da tabela externa define um esquema que pode ser usado pelas consultas. O esquema fornecido na definição da tabela externa precisa corresponder ao esquema das tabelas nos fragmentos em que os dados reais são armazenados. 

* **Distribuição de dados**: a DDL da tabela externa define a distribuição de dados usada para distribuir os dados em sua camada de dados. Observe que o Banco de Dados SQL do Azure não valida a distribuição definida na tabela externa em relação à distribuição real nos fragmentos. Você é responsável por garantir que a distribuição de dados real nos fragmentos corresponda à definição de tabela externa.

* **Referência de camada de dados**: a DDL da tabela externa faz referência a uma fonte de dados externa. A fonte de dados externa especifica um mapa de fragmentos que fornece à tabela externa as informações necessárias para localizar todos os bancos de dados em sua camada de dados.

Com uma fonte de dados externa, como descrito na seção anterior, a sintaxe para criar e remover tabelas externas é a seguinte:

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
	    { WITH ( <sharded_external_table_options> ) }
	) [;]  
	
	<sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
	  [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

A cláusula DATA\_SOURCE define a fonte de dados externa (um mapa de fragmentos, no caso do particionamento horizontal) que é usada para a tabela externa.

As cláusulas SCHEMA\_NAME e OBJECT\_NAME fornecem a capacidade de mapear a definição da tabela externa para uma tabela em um esquema diferente no fragmento ou para uma tabela com um nome diferente, respectivamente. Se for omitido, o esquema do objeto remoto será considerado “dbo” e seu nome será considerado como sendo idêntico ao nome da tabela externa que está sendo definido.

As cláusulas SCHEMA\_NAME e OBJECT\_NAME são especialmente úteis se o nome da tabela remota já existir no banco de dados em que você deseja criar a tabela externa. Um exemplo desse problema é quando você deseja definir uma tabela externa para obter uma exibição agregada de exibições de catálogo ou de DMVs em sua camada de dados escalada horizontalmente. Como as exibições de catálogo e as DMVs já existem localmente, você não pode usar seus nomes para a definição da tabela externa. Em vez disso, use um nome diferente e use a exibição do catálogo ou o nome de DMV nas cláusulas SCHEMA\_NAME e/ou OBJECT\_NAME. (Veja o exemplo abaixo.)

A cláusula DISTRIBUTION especifica a distribuição de dados usada para esta tabela:

* SHARDED significa que os dados desta tabela são particionados horizontalmente entre os bancos de dados em seu mapa de fragmentos. A chave de particionamento para a distribuição de dados é capturada no parâmetro <sharding_column_name>.  

* REPLICATED significa que cópias idênticas da tabela estão presentes em cada banco de dados no mapa de fragmentos. O Banco de Dados SQL do Azure não mantém cópias da tabela. É sua responsabilidade assegurar que a réplica seja idêntica entre os bancos de dados.

* ROUND\_ROBIN significa que a tabela é distribuída por meio do particionamento horizontal. No entanto, foi usada uma distribuição dependente do aplicativo.

O processador de consultas utiliza as informações fornecidas na cláusula DISTRIBUTION para criar planos de consulta mais eficientes.

Use a seguinte instrução para remover tabelas externas:

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

**Permissões para CREATE/DROP EXTERNAL TABLE**: as permissões ALTER ANY EXTERNAL DATA SOURCE são necessárias, o que também é necessário para fazer referência à fonte de dados subjacente.

**Considerações sobre segurança:** os usuários com acesso à tabela externa automaticamente têm acesso a tabelas remotas subjacentes com a credencial fornecida na definição de fonte de dados externa. Você deve gerenciar cuidadosamente o acesso à tabela externa para evitar a elevação indesejada de privilégios por meio da credencial da fonte de dados externa. Permissões de SQL regulares podem ser usadas para o acesso de GRANT ou REVOKE a uma tabela externa como se ela fosse uma tabela normal.

**Exemplo**: o seguinte exemplo ilustra como criar uma tabela externa:

	CREATE EXTERNAL TABLE [dbo].[order_line]( 
		 [ol_o_id] int NOT NULL, 
		 [ol_d_id] tinyint NOT NULL,
		 [ol_w_id] int NOT NULL, 
		 [ol_number] tinyint NOT NULL, 
		 [ol_i_id] int NOT NULL, 
		 [ol_delivery_d] datetime NOT NULL, 
		 [ol_amount] smallmoney NOT NULL, 
		 [ol_supply_w_id] int NOT NULL, 
		 [ol_quantity] smallint NOT NULL, 
		 [ol_dist_info] char(24) NOT NULL 
	) 
	
	WITH 
	( 
		DATA_SOURCE = MyExtSrc, 
	 	SCHEMA_NAME = 'orders', 
	 	OBJECT_NAME = 'order_details', 
		DISTRIBUTION=SHARDED(ol_w_id)
	); 

O exemplo a seguir mostra como recuperar a lista de tabelas externas do banco de dados atual:

	select * from sys.external_tables; 

## Consultas 

### 2\.1 Consultas T-SQL de alta fidelidade 

Depois de definir a fonte de dados externa e as tabelas externas, agora você poderá usar o T-SQL completo nas tabelas externas.

**Exemplo de particionamento horizontal:** a consulta a seguir executa uma junção de três vias entre depósitos, pedidos e linhas de pedido e usa várias agregações e um filtro seletivo. Ele pressupõe que o (1) particionamento horizontal (fragmentação) e (2) esses depósitos, pedidos e linhas de pedido são fragmentados pela coluna de ID de depósito, e que a consulta elástica pode colocar as junções nos fragmentos e processar a parte cara da consulta nos fragmentos de modo paralelo.

	select  
		 w_id as warehouse,
		 o_c_id as customer,
		 count(*) as cnt_orderline,
		 max(ol_quantity) as max_quantity,
		 avg(ol_amount) as avg_amount, 
		 min(ol_delivery_d) as min_deliv_date
	from warehouse 
	join orders 
	on w_id = o_w_id
	join order_line 
	on o_id = ol_o_id and o_w_id = ol_w_id 
	where w_id > 100 and w_id < 200 
	group by w_id, o_c_id 
 
### 2\.2 Procedimento armazenado SP\_EXECUTE\_FANOUT 

A consulta elástica também apresenta um procedimento armazenado que fornece acesso direto aos fragmentos. O procedimento armazenado é chamado sp\_execute\_fanout e usa os seguintes parâmetros:

* Nome do servidor (nvarchar): nome totalmente qualificado do servidor lógico que hospeda o mapa de fragmentos. 
* Nome do banco de dados do mapa de fragmentos (nvarchar): o nome do banco de dados do mapa de fragmentos. 
* Nome de usuário (nvarchar): o nome de usuário para fazer logon no banco de dados do mapa de fragmentos. 
* Senha (nvarchar): senha do usuário. 
* Nome do mapa de fragmentos (nvarchar): o nome do mapa de fragmentos a ser usado para a consulta. 
*  Consulta: a consulta T-SQL a ser executada em cada fragmento. 
*  Declaração de parâmetro (nvarchar) - opcional: cadeia de caracteres com definições de tipo de dados para os parâmetros usados no parâmetro Query (como sp\_executesql). 
*  Lista de valores de parâmetro - opcional: lista separada por vírgulas de valores de parâmetro (como sp\_executesql)  

sp\_execute\_fanout usa as informações do mapa de fragmentos fornecidas nos parâmetros de invocação para executar a instrução T-SQL indicada em todos os fragmentos registrados no mapa de fragmentos. Os resultados são mesclados com semânticas UNION ALL. O resultado também inclui a coluna adicional “virtual” com o nome do fragmento.

Observe que as mesmas credenciais são usadas para conectar ao banco de dados do mapa de fragmentos e aos fragmentos.

Exemplo:

	sp_execute_fanout 
		’myserver.database.windows.net', 
		N'ShardMapDb', 
		N'myuser', 
		N'MyPwd', 
		N'ShardMap', 
		N'select count(w_id) as foo from warehouse' 

## Conectividade de ferramentas  

Use cadeias de conexão regulares do SQL Server para conectar seu aplicativo e suas ferramentas de BI e de integração de dados ao banco de dados com as definições da tabela externa. Certifique-se de que o SQL Server tem suporte como uma fonte de dados para a ferramenta. Em seguida, faça referência ao banco de dados de consulta elástica como qualquer outro banco de dados do SQL Server conectado à ferramenta, e use tabelas externas de sua ferramenta ou aplicativo como se elas fossem tabelas locais.

## Práticas recomendadas 

* Certifique-se de que o banco de dados do ponto de extremidade da consulta elástica recebeu acesso ao banco de dados do mapa de fragmentos e a todos os fragmentos por meio dos firewalls do Banco de Dados SQL.  

* A consulta elástica não valida nem impõe a distribuição de dados definida pela tabela externa. Se a distribuição de dados real for diferente da distribuição especificada na definição de tabela, as consultas podem gerar resultados inesperados.

* A consulta elástica atualmente não executa a eliminação de fragmentos quando predicados da chave de fragmentação permitem excluir com segurança determinados fragmentos do processamento.

* A consulta elástica funciona melhor para consultas em que a maior parte da computação pode ser realizada nos fragmentos. Normalmente o melhor desempenho de consulta é obtido com predicados de filtro seletivo que podem ser avaliados nos fragmentos ou junções sobre as chaves de particionamento que podem ser executadas de forma alinhada por partição em todos os fragmentos. Outros padrões de consulta podem precisar carregar grandes quantidades de dados dos fragmentos para o nó de cabeçalho e podem ter um desempenho insatisfatório


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->

<!---HONumber=AcomDC_0114_2016-->