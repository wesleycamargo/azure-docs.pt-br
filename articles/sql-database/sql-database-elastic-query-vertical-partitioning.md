<properties
    pageTitle="Consulta de banco de dados elástico para consultas entre bancos de dados (particionamento vertical) | Microsoft Azure"
    description="como configurar consultas entre bancos de dados em partições verticais"    
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
    ms.date="11/09/2015"
    ms.author="torsteng;sidneyh" />

# Consulta de banco de dados elástico para consultas entre bancos de dados (particionamento vertical)

Este documento explica como configurar a consulta elástica para cenários de consultas entre bancos de dados (particionamento vertical) e como executar as consultas. Para obter uma definição do cenário de particionamento vertical, confira a [Visão geral da consulta de banco de dados elástico do Banco de Dados SQL do Azure (visualização)](sql-database-elastic-query-overview.md).

## Criando objetos de banco de dados

Para cenários particionados verticalmente, a consulta elástica estende a DDL atual do T-SQL para fazer referência às tabelas que estão armazenadas em bancos de dados remotos. Esta seção fornece uma visão geral das instruções DDL para configurar a consulta elástica para o acesso transparente a tabelas remotas. Essas instruções DDL permitem criar a representação de metadados de suas tabelas remotas no banco de dados local.

**OBSERVAÇÃO**: Ao contrário do particionamento horizontal, essas instruções DDL não dependem da definição de uma camada de dados com um mapa de fragmentos por meio da biblioteca de cliente do banco de dados elástico.

A definição de objetos do banco de dados para a consulta de banco de dados elástico depende das seguintes instruções T-SQL, que são explicadas mais detalhadamente para o cenário de particionamento vertical abaixo:

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
    
Para excluir a credencial:
    
    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
Certifique-se de que o *< username>* não inclui nenhum sufixo *“@servername”*.

### 1\.2 Fontes de dados externas

Forneça as informações sobre seus bancos de dados remotos para a consulta elástica definindo as fontes de dados externas. A sintaxe para criar e remover fontes de dados externas é definida da seguinte maneira:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

Observe o parâmetro TYPE que define esta fonte de dados como RDBMS.

Você pode usar a instrução a seguir para remover uma fonte de dados externa:

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

#### Permissões para CREATE/DROP EXTERNAL DATA SOURCE 

O usuário deve ter a permissão para ALTERAR QUALQUER FONTE DE DADOS EXTERNA. Essa permissão está incluída na permissão ALTERAR BANCO DE DADOS.

**Exemplo**

O exemplo a seguir ilustra o uso da instrução CRIAR para fontes de dados externas.

	CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
	WITH 
	( 
		TYPE=RDBMS, 
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ReferenceData', 
		CREDENTIAL= SqlUser 
	); 
 
Para recuperar a lista das atuais fontes de dados externas por meio da seguinte exibição do catálogo:

    select * from sys.external_data_sources; 

### 1\.3 Tabelas externas 

A consulta elástica estende a sintaxe de tabela externa existente para definir as tabelas externas que usam fontes de dados externas do tipo RDBMS. Uma definição de tabela externa para o particionamento vertical abrange os seguintes aspectos:

* **Esquema**: a DDL da tabela externa define um esquema que pode ser usado pelas consultas. O esquema fornecido na definição da tabela externa precisa corresponder ao esquema das tabelas no banco de dados remoto em que os dados reais são armazenados. 

* **Referência de banco de dados remoto**: a DDL da tabela externa faz referência a uma fonte de dados externa. A fonte de dados externa especifica o nome do servidor lógico e o nome do banco de dados remoto em que os dados da tabela real estão armazenados.

Com uma fonte de dados externa, como descrito na seção anterior, a sintaxe para criar tabelas externas é a seguinte:

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
	{ WITH ( <rdbms_external_table_options> ) } 
	)[;] 
	
	<rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

A cláusula DATA\_SOURCE define a fonte de dados externa (ou seja, o banco de dados remoto, no caso do particionamento vertical) que é usada para a tabela externa.

As cláusulas SCHEMA\_NAME e OBJECT\_NAME fornecem a capacidade de mapear a definição da tabela externa para uma tabela em um esquema diferente no banco de dados remoto ou para uma tabela com um nome diferente, respectivamente. Isso é útil se você deseja definir uma tabela externa para uma exibição de catálogo ou DMV em seu banco de dados remoto – ou em qualquer outra situação em que o nome da tabela remota já esteja sendo usado localmente.

A instrução DDL a seguir remove uma definição existente da tabela externa do catálogo do local. Ela não afeta o banco de dados remoto.

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

**Permissões para CREATE/DROP EXTERNAL TABLE**: as permissões ALTER ANY EXTERNAL DATA SOURCE são necessárias para a DDL da tabela externa, o que também é necessário para fazer referência à fonte de dados subjacente.

**Considerações sobre segurança**: os usuários com acesso à tabela externa automaticamente têm acesso a tabelas remotas subjacentes com a credencial fornecida na definição de fonte de dados externa. Você deve gerenciar cuidadosamente o acesso à tabela externa para evitar a elevação indesejada de privilégios por meio da credencial da fonte de dados externa. Permissões de SQL regulares podem ser usadas para o acesso de GRANT ou REVOKE a uma tabela externa como se ela fosse uma tabela normal.


 **Exemplo**: o seguinte exemplo ilustra como criar uma tabela externa:

	CREATE EXTERNAL TABLE [dbo].[customer]( 
		[c_id] int NOT NULL, 
		[c_firstname] nvarchar(256) NULL, 
		[c_lastname] nvarchar(256) NOT NULL, 
		[street] nvarchar(256) NOT NULL, 
		[city] nvarchar(256) NOT NULL, 
		[state] nvarchar(20) NULL, 
		[country] nvarchar(50) NOT NULL, 
	) 
	WITH 
	( 
	       DATA_SOURCE = RemoteReferenceData 
	); 

O exemplo a seguir mostra como recuperar a lista de tabelas externas do banco de dados atual:

	select * from sys.external_tables; 

## Consultas

### 2\.1 Consultas T-SQL de alta fidelidade 

Depois de definir a fonte de dados externa e as tabelas externas, agora você poderá usar o T-SQL completo nas tabelas externas.

**Exemplo de particionamento vertical**: a consulta a seguir executa uma junção de três vias entre as duas tabelas locais para pedidos e linhas da pedido e a tabela remota para clientes. Este é um exemplo do caso de uso de dados de referência para a consulta elástica:

	SELECT  	
	 c_id as customer,
	 c_lastname as customer_name,
	 count(*) as cnt_orderline, 
	 max(ol_quantity) as max_quantity,
	 avg(ol_amount) as avg_amount,
	 min(ol_delivery_d) as min_deliv_date
	FROM customer 
	JOIN orders 
	ON c_id = o_c_id
	JOIN  order_line 
	ON o_id = ol_o_id and o_c_id = ol_c_id
	WHERE c_id = 100

  
## Conectividade de ferramentas

É possível usar cadeias de conexão regulares do SQL Server para conectar suas ferramentas de BI e de integração de dados a bancos de dados no servidor do Banco de Dados SQL que têm a consulta elástica habilitada e as tabelas externas definidas. Certifique-se de que o SQL Server tem suporte como uma fonte de dados para a ferramenta. Em seguida, consulte o banco de dados de consulta elástica e suas tabelas externas como qualquer outro banco de dados do SQL Server ao qual você se conectaria com a sua ferramenta.

## Práticas recomendadas 
 
* Certifique-se de que o banco de dados do ponto de extremidade da consulta elástica recebeu acesso ao banco de dados remoto, habilitando acesso para os Serviços do Azure em sua configuração de firewall do Banco de Dados SQL. Também verifique se a credencial fornecida na definição da fonte de dados externa pode fazer logon com êxito no banco de dados remoto e se ela tem as permissões para acessar a tabela remota.  

* A consulta elástica funciona melhor para consultas em que a maior parte da computação pode ser realizada nos bancos de dados remotos. Normalmente, você obtém o melhor desempenho de consulta com predicados de filtro seletivo que podem ser avaliados nos bancos de dados remotos ou com junções que podem ser executadas por completo no banco de dados remoto. Outros padrões de consulta podem precisar carregar grandes quantidades de dados do banco de dados remoto e podem ter um desempenho insatisfatório.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
<!--anchors-->

<!---HONumber=Nov15_HO3-->