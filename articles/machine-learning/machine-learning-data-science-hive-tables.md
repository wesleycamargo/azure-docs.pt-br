<properties 
	pageTitle="Criar e carregar dados nas tabelas Hive do armazenamento de blobs | Microsoft Azure" 
	description="Criar tabelas Hive e carregar dados em blobs para tabelas hive" 
	services="machine-learning" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="hangzh;bradsev" />

 
#Criar e carregar dados nas tabelas Hive do armazenamento de blobs do Azure
 
Neste documento, são apresentadas consultas de Hive genéricas que criam tabelas Hive e carregam dados do armazenamento de blobs do Azure. Essas consultas de Hive são compartilhadas no repositório GitHub. [Repositório GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql).


Se você criar uma máquina virtual do Azure seguindo as instruções em “Configurar uma máquina virtual do Azure com o servidor do IPython Notebook", o arquivo de script terá sido baixado no diretório `C:\Users<user name>\Documents\Data Science Scripts` na máquina virtual. Você precisa conectar seu próprio esquema de dados e configuração do armazenamento de blobs do Azure nos campos correspondentes nessas consultas e essas consultas de Hive devem estar prontas para envio.

Supomos que os dados de tabelas Hive estejam em formato de tabela **descompactado** e que os dados foram carregados no contêiner padrão ou adicional da conta de armazenamento usada pelo cluster do Hadoop. Se os usuários desejam praticar no _Dados de Viagens de Táxi em NYC_, é necessário primeiro [baixar todos os 24 arquivos](http://www.andresmh.com/nyctaxitrips/) (12 arquivos Trip e 12 arquivos Fair), **descompactar** todos os arquivos em arquivos CSV e carregá-los no contêiner padrão ou adicional da conta de armazenamento do Azure usada quando o cluster do [Hadoop do Azure HDInsight é personalizado](machine-learning-data-science-customize-hadoop-cluster.html).

Consultas de hive podem ser enviadas na Linha de Comando do Hadoop no nó principal do cluster do Hadoop. Você precisa:

1. [Habilitar o acesso remoto ao nó principal do cluster do Hadoop e fazer logon no nó principal](machine-learning-data-science-customize-hadoop-cluster.md).
2. [Enviar consultas de Hive na Linha de Comando do Hadoop no nó principal](machine-learning-data-science-hive-queries.md).

Os usuários também podem usar o [Console de Consultas (Editor de Hive)] digitando a URL em um navegador da Web “https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor (as credenciais do cluster do Hadoop serão solicitadas para fazer logon) ou podem ainda [enviar trabalhos Hive usando o PowerShell](hdinsight-submit-hadoop-jobs-programmatically.md).

- [Etapa 1: criar tabelas e banco de dados do Hive](#create-tables)
- [Etapa 2: carregar dados para tabelas Hive](#load-data)
- [Tópicos avançados: tabela e repositório de dados Hive particionados no formato ORC](#partition-orc)

## <a name="create-tables"></a>Criar banco de dados e tabelas Hive

    create database if not exists <database name>;
	CREATE EXTERNAL TABLE if not exists <database name>.<table name>
	(
		field1 string, 
		field2 int, 
		field3 float, 
		field4 double, 
		...,
		fieldN string
	) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
	STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Veja aqui as descrições dos campos que os usuários precisam para plug-ins e outras configurações:

- `<database name>`: o nome do banco de dados que os usuários desejam criar. Se os usuários desejam apenas usar o banco de dados padrão, a consulta `create database...` pode ser omitida. 
- `<table name>`: o nome da tabela que os usuários desejam criar no banco de dados especificado. Se os usuários desejam usar o banco de dados padrão, a tabela pode ser referida diretamente por `<table name>` sem `<database name>.`.
- `<field separator>`: o separador que divide os campos no arquivo de dados a serem carregados na tabela Hive. 
- `<line separator>`: o separador que divide as linhas no arquivo de dados. 
- `<storage location>`: o local de armazenamento do Azure para salvar os dados das tabelas Hive. Se os usuários não especificarem `LOCATION '<storage location>'`, por padrão, o banco de dados e as tabelas são armazenadas no diretório `hive/warehouse/` no contêiner padrão do cluster de Hive. Se um usuário deseja especificar o local de armazenamento, este deve estar dentro do contêiner padrão para o banco de dados e tabelas. Esse local precisa ser chamado como um local relativo ao contêiner padrão do cluster no formato de `'wasb:///<directory 1>/'` ou `'wasb:///<directory 1>/<directory 2>/'`, etc. Após a consulta ser executada, os diretórios relativos serão criados no contêiner padrão. 
- `TBLPROPERTIES("skip.header.line.count"="1")`: se o arquivo de dados tiver uma linha de cabeçalho, os usuários precisam adicionar essa propriedade ao **final** da consulta `create table`. Caso contrário, a linha de cabeçalho será carregada como um registro para a tabela. Se o arquivo de dados não tiver uma linha de cabeçalho, essa configuração pode ser omitida na consulta. 

## <a name="load-data"></a>Carregar dados para tabelas Hive

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- `<path to blob data>`: se o arquivo de blob a ser carregado na tabela Hive estiver no contêiner padrão do cluster do Hadoop do HDInsight, o `<path to blob data>` deve estar no formato `'wasb:///<directory in this container>/<blob file name>'`. O arquivo de blob também pode estar no contêiner adicional do cluster do Hadoop do HDInsight. Nesse caso, `<path to blob data>` deve estar no formato `'wasb://<container name>@<storage account name>.blob.windows.core.net/<blob file name>'`.

> [AZURE.NOTE]Os dados blob a serem carregados na tabela Hive deve estar no contêiner padrão ou adicional da conta de armazenamento para o cluster do Hadoop. Caso contrário, a `LOAD DATA` consulta falhará reclamando que ele não pode acessar os dados.


## <a name="partition-orc"></a>Tópicos avançados: tabela e repositório de dados Hive particionados no formato ORC

Se os dados forem grandes, particionar a tabela será útil para consultas que só precisam verificar algumas partições da tabela. Por exemplo, é útil particionar os dados de log de um site da Web por datas.

Além da partição de tabela, também é útil armazenar os dados Hive no formato ORC. ORC significa “Optimized Row Columnar” (Colunar de Linha Otimizado). Consulte _"[Usando arquivos ORC melhora o desempenho quando o Hive lê, grava e processa dados](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles)"_ para obter mais detalhes.

### Tabela particionada

As consultas de criação de uma tabela particionada e carregamento de dados nela são as seguintes:

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Ao consultar tabelas particionadas, é recomendável adicionar a condição de partição no **início** da cláusula `where` para que a eficácia de pesquisa possa ser melhorada significativamente.

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Armazenar dados Hive no formato ORC

Os usuários não podem carregar dados diretamente no blob para tabelas Hive no formato de armazenamento ORC. Veja aqui etapas que os usuários devem executar para carregar dados de blobs do Azure para tabelas Hive armazenadas no formato ORC.

1. Crie uma tabela externa **ARMAZENADA COMO ARQUIVO DE TEXTO** e carregue dados do armazenamento de blob na tabela.

		CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
		(
			field1 string,
			field2 int,
			...
			fieldN date
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
			lines terminated by '<line separator>' STORED AS TEXTFILE 
			LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

		LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

2. Crie uma tabela interna com o mesmo esquema que a tabela externa na etapa 1, e o mesmo delimitador de campo. E armazene os dados Hive no formato ORC

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Selecione os dados da tabela externa da etapa 1 e insira-os na tabela ORC

		INSERT OVERWRITE TABLE <database name>.<ORC table name> SELECT * FROM <database name>.<external textfile table name>;

> [AZURE.NOTE]Se a tabela ARQUIVO DE TEXTO `<database name>.<external textfile table name>` tiver partições, na ETAPA 3, `SELECT * FROM <database name>.<external textfile table name>` selecionará a variável de partição como um campo no conjunto de dados retornado. Inseri-lo em `<database name>.<ORC table name>` falhará, pois `<database name>.<ORC table name>` não tem a variável de partição como um campo no esquema de tabela. Nesse caso, os usuários precisam selecionar especificamente os campos que serão inseridos no `<database name>.<ORC table name>`, como mostrado a seguir:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		      SELECT field1, field2, ..., fieldN
		      FROM <database name>.<external textfile table name> 
		      WHERE <partition variable>=<partition value>;

4. É seguro remover o `<external textfile table name>` usando a consulta a seguir após todos os dados serem inserido no `<database name>.<ORC table name>`:

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Agora temos uma tabela com dados no formato ORC pronta para usar.
 

<!---HONumber=July15_HO2-->