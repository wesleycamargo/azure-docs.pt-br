<properties
	pageTitle="Introdução às consultas elásticas para fragmentação (particionamento horizontal) | Microsoft Azure"
	description="como usar consultas de banco de dados entre bancos de dados"
	services="sql-database"
	documentationCenter=""  
	manager="jeffreyg"
	authors="sidneyh"/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="SilviaDoomra" />

# Introdução às consultas elásticas para fragmentação (particionamento horizontal)

A consulta de banco de dados elástico (visualização) para o Banco de Dados SQL do Azure permite executar consultas T-SQL que abrangem vários bancos de dados usando um único ponto de conexão. Para obter mais informações sobre o recurso de consulta de Banco de Dados Elástico, consulte a [página de visão geral do recurso](sql-database-elastic-query-overview.md).

Este tópico estende o exemplo encontrado na [Introdução às ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-get-started.md). Quando tiver concluído, você saberá como configurar e usar um Banco de Dados SQL do Azure para executar consultas que abrangem vários bancos de dados relacionados.
## Pré-requisitos

Baixe e execute a [exemplo da Introdução às ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-get-started.md).

## Criar um gerenciador de mapa de fragmentos usando o aplicativo de exemplo

Aqui você vai criar um gerenciador de mapa de fragmentos juntamente com vários fragmentos, seguido pela inserção de dados nos fragmentos. Se você já tem fragmentos configurados com dados fragmentados, poderá ignorar as etapas a seguir e mover para a próxima seção.

1. Compile e execute o aplicativo de exemplo da **Introdução às ferramentas de Banco de Dados Elástico**. Siga as etapas até a etapa 7 na seção [Baixe e execute o aplicativo de exemplo](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). No final da etapa 7, você verá o seguinte prompt de comando:

	![prompt de comando][1]

2.  Na janela Comando, digite "1" e pressione **Enter**. Isso cria o gerenciador de mapa de fragmentos e adiciona dois fragmentos ao servidor. Em seguida, digite "3" e pressione **Enter**. Repita a ação quatro vezes. Isso insere linhas de dados de exemplo no seus fragmentos.
3.  O [portal do Azure](https://portal.azure.com) deve mostrar três novos bancos de dados em seu servidor v12:

	![Confirmação do Visual Studio][2]

	Neste ponto, consultas entre bancos de dados têm suporte por meio de bibliotecas de cliente do Banco de Dados Elástico. Por exemplo, use a opção 4 na janela Comando. Os resultados de uma consulta de vários fragmento são sempre um **UNION ALL** dos resultados de todos os fragmentos.

	Na próxima seção, criaremos um ponto de extremidade de banco de dados de exemplo que dá suporte a consultas mais avançadas de dados entre fragmentos.

## Criar um banco de dados de consulta elástico

1. Abra o [Portal do Azure](https://portal.azure.com) e faça logon.
2. Crie um novo Banco de Dados SQL do Azure no mesmo servidor que a instalação do fragmento. Nomeie o banco de dados como "ElasticDBQuery". 

	![Portal do Azure e camada de preços][3]

	Observação: você pode usar um banco de dados existente. Se fizer isso, ele não deve ser um dos fragmentos aonde você deseja executar suas consultas. Esse banco de dados será usado para criar os objetos de metadados para uma consulta de Banco de Dados Elástico.


## Criar objetos de banco de dados

### Chave mestra do escopo do banco de dados e credenciais

Eles são usados para conectar ao gerenciador de mapa de fragmentos e aos fragmentos:

1. Abra o SQL Server Management Studio ou o SQL Server Data Tools no Visual Studio.
2. Conecte-se ao banco de dados ElasticDBQuery e execute os seguintes comandos T-SQL:

		CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

		CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
		WITH IDENTITY = '<username>',
		SECRET = '<password>';

	"username" e "password" devem ser o mesmo que as informações de logon usadas na etapa 6 do [Baixar e executar o aplicativo de exemplo](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools) na [Introdução às ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md).

### Fontes de dados externas

Para criar uma fonte de dados externa, execute o seguinte comando no banco de dados ElasticDBQuery:

	CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
	  CREDENTIAL = ElasticDBQueryCred,
 	  SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" é o nome do mapa de fragmentos, se você tiver criado um mapa de fragmentos e o gerenciador de mapa de fragmentos usando as ferramentas de banco de dados de exemplo. No entanto, se você usou a configuração personalizada para este exemplo, ele deve ter o nome do mapa de fragmento escolhido no seu aplicativo.

### Tabelas externas

Crie uma tabela externa que corresponda à tabela de Clientes nos fragmentos executando o comando a seguir no banco de dados ElasticDBQuery:

	CREATE EXTERNAL TABLE [dbo].[Customers]
	( [CustomerId] [int] NOT NULL,
	  [Name] [nvarchar](256) NOT NULL,
	  [RegionId] [int] NOT NULL)
	WITH
	( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
	) ;

## Executar uma consulta T-SQL no banco de dados elástico de exemplo

Depois que tiver definido sua fonte de dados e tabelas externas, agora você poderá usar o T-SQL completo nas tabelas externas.

Execute esta consulta no banco de dados ElasticDBQuery:

	select count(CustomerId) from [dbo].[Customers]

Você observará que a consulta agrega os resultados de todos os fragmentos e fornece a seguinte saída:

![Detalhes de saída][4]

## Importar resultados de consulta de banco de dados elástico para o Excel

 Você pode importar os resultados de uma consulta para um arquivo do Excel.

1. Inicie o Excel 2013.
2. 	Navegue até a faixa de opções **Dados**.
3. 	Clique em **De Outras Fontes** e em **Do SQL Server**.

	![Importação de outras fontes para o Excel][5]
4. 	No **Assistente para conexão de dados**, digite as credenciais de logon e nome do servidor. Em seguida, clique em **Próximo**.
5. 	Na caixa de diálogo **Selecione o banco de dados que contém os dados que você deseja**, selecione o banco de dados **ElasticDBQuery**.
6. 	Selecione a tabela **Clientes** na exibição de lista e clique em **Avançar**. Em seguida, clique em **Concluir**.
7. 	No formulário **Importar dados** em **Selecione como deseja exibir esses dados na sua pasta de trabalho**, selecione **Tabela** e clique em **OK**.

Todas as linhas da tabela **Clientes**, armazenada em fragmentos diferentes, populam a planilha do Excel.

## Próximas etapas
Agora você pode usar funções avançadas de visualização de dados do Excel. Você pode usar a cadeia de conexão com o nome do servidor, nome do banco de dados e credenciais para conectar suas ferramentas de integração de dados e BI ao banco de dados de consulta elástico. Certifique-se de que o SQL Server tem suporte como uma fonte de dados para a ferramenta. Você pode consultar o banco de dados de consulta elástico e tabelas externas como qualquer outro banco de dados e tabela do SQL Server que se conectariam à sua ferramenta.

### Custo
Não há nenhum custo adicional para usar o recurso de consulta de Banco de Dados Elástico.

Para obter informações sobre os preços, consulte [Detalhes de preços do Banco de Dados SQL](http://azure.microsoft.com/pricing/details/sql-database/).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->

<!---HONumber=AcomDC_0107_2016-->