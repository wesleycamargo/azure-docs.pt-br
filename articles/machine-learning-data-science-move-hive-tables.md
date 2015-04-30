<properties 
	pageTitle="Criar e carregar dados nas tabelas Hive do armazenamento de blobs do Azure | Azure" 
	description="Criar tabelas Hive e carregar dados em blobs para tabelas hive" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="hangzh;bradsev" />

 
# Criar e carregar dados nas tabelas Hive do armazenamento de blobs do Azure
 
Neste documento, são apresentadas consultas de Hive genéricas que criam tabelas Hive e carregam dados do armazenamento de blobs do Azure.  Também são fornecida algumas orientações sobre o particionamento de tabelas Hive e sobre como usar a formatação ORC (Colunar de Linha Otimizado) para melhorar o desempenho da consulta.

As consultas de Hive são compartilhadas no [repositório Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) e pode ser baixadas dele.

Se você criar uma máquina virtual do Azure seguindo as instruções fornecidas em [Configurar uma máquina virtual do Azure para ciência de dados](machine-learning-data-science-setup-virtual-machine.md), esse arquivo de script deve ter sido baixado para o diretório *C:\Users\<user name>\Documents\Data Science Scripts* na máquina virtual.  Essas consultas de Hive exigem somente conectar em seu próprio esquema de dados e que a configuração de armazenamento de blobs do Azure nos campos apropriados esteja pronta para envio.

Supomos que os dados de tabelas Hive estejam em formato de tabela **descompactado** e que os dados foram carregado para o contêiner padrão (ou adicional) da conta de armazenamento usada pelo cluster do Hadoop.  Se os usuários desejam praticar no _Dados de Viagens de Táxi em NYC_, é necessário primeiro [baixar todos os 24 arquivos](http://www.andresmh.com/nyctaxitrips/) (12 arquivos Trip e 12 arquivos Fair), **descompactar** todos os arquivos em arquivos .csv e carregá-los para o contêiner padrão (ou adicional) da conta de armazenamento do Azure usada pelo procedimento descrito no tópico [Personalizar os Clusters de Hadoop do HDInsight do Azure para ciência de dados](machine-learning-data-science-customize-hadoop-cluster.md). 

Consultas de Hive podem ser enviadas do console de Linha de Comando do Hadoop no nó principal do cluster do Hadoop.  Para isso, faça logon no nó principal do cluster do Hadoop, abra o Console de Linha de Comando do Hadoop e envie as consultas de Hive dele.  Para obter instruções sobre como fazer isso, consulte [Enviar consultas de Hive para clusters de Hadoop do HDInsight no processo de ciência de dados de nuvem](machine-learning-data-science-process-hive-tables.md).

Os usuários também podem usar o Console de Consulta (Editor de Hive) digitando a URL 

https://&#60;nome do cluster do Hadoop>.azurehdinsight.net/Home/HiveEditor

em um navegador da Web.  Observe que será solicitado inserir as credenciais de cluster do Hadoop para fazer logon. Como alternativa, você pode [Enviar trabalhos Hive usando o PowerShell](hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell). 

## Pré-requisitos
Este artigo supõe que você:
 
* Criou uma conta de armazenamento do Azure. Se precisar de instruções, consulte [Criar uma conta de Armazenamento do Azure](hdinsight-get-started.md#storage) 
* Provisionou um cluster do Hadoop personalizado com o serviço HDInsight.  Se você precisar de instruções, consulte [Personalizar os Clusters de Hadoop do HDInsight do Azure para ciência de dados](machine-learning-data-science-customize-hadoop-cluster.md).
* Habilitou o acesso remoto para o cluster, conectou-se e abriu o Console de Linha de Comando do Hadoop.  Se precisar de instruções, consulte [Acessar o nó principal do Cluster do Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess). 


## <a name="create-tables"></a>Criar tabelas e banco de dados Hive
Veja aqui a consulta Hive que cria uma tabela Hive.

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

- `<nome do banco de dados>`: o nome do banco de dados que os usuários desejam criar.  Se os usuários desejam apenas usar o banco de dados padrão, a consulta `criar banco de dados...` pode ser omitida. 
- `<table name>`: o nome da tabela que os usuários desejam criar no banco de dados especificado.  Se os usuários desejam usar o banco de dados padrão, a tabela pode ser referida diretamente por `<table name>` sem o `<nome do banco de dados>.`.
- `<separador de campo>`: o separador que delimita os campos no arquivo de dados a serem carregados na tabela Hive. 
- `<line separator>`: o separador que delimita linhas no arquivo de dados. 
- `<local de armazenamento>`: o local de armazenamento do Azure para salvar os dados das tabelas Hive.  Se os usuários não especificarem `LOCATION '<local do armazenamento>'`, o banco de dados e as tabelas são armazenadas no diretório `hive/warehouse/` no contêiner padrão do cluster Hive por padrão.  Se um usuário deseja especificar o local de armazenamento, este deve estar dentro do contêiner padrão para o banco de dados e tabelas.  Esse local precisa ser referido como local relativo ao contêiner padrão do cluster no formato `'wasb:///<diretório 1> /'` ou `'wasb:///<diretório 1>/<diretório 2>/'`, etc. Depois que a consulta é executada, os diretórios relativos serão criados dentro do contêiner padrão. 
- `TBLPROPERTIES("skip.header.line.count"="1")`:  Se o arquivo de dados tiver uma linha de cabeçalho, os usuários precisam adicionar essa propriedade ao **final** da consulta `create table`.  Caso contrário, a linha de cabeçalho será carregada como um registro para a tabela.  Se o arquivo de dados não tiver uma linha de cabeçalho, essa configuração pode ser omitida na consulta. 

## <a name="load-data"></a>Carregar dados para tabelas Hive
Veja aqui a consulta Hive que carrega dados em uma tabela Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- `<caminho para dados blob>`:  Se o arquivo de blob a ser carregado para a tabela Hive estiver no contêiner padrão do cluster do Hadoop do HDInsight, o `<caminho para dados blob>` deve estar no formato `'wasb:///<diretório neste contêiner>/<nome do arquivo de blob >'`.  O arquivo de blob também pode estar em um contêiner adicional do cluster do Hadoop do HDInsight.  Nesse caso, `<caminho para dados blob>` deve estar no formato `'wasb://<nome do contêiner>@<nome da conta de armazenamento>.blob.windows.core.net/<nome do arquivo de blob>'`.

>[AZURE.NOTE] Os dados blob a serem carregados na tabela Hive deve estar no contêiner padrão ou adicional da conta de armazenamento para o cluster do Hadoop.  Caso contrário, a consulta `LOAD DATa` falhará reclamando que ele não pode acessar os dados. 


## <a name="partition-orc"></a>Tópicos avançados: tabela e armazenamento de dados Hive particionados no formato ORC

Se os dados forem grandes, particionar a tabela é útil para consultas que só precisam verificar algumas partições da tabela.  Por exemplo, é útil particionar os dados de log de um site da Web por datas. 

Além do particionamento de tabelas Hive, também é útil armazenar os dados Hive no formato ORC (Colunar de Linha Otimizado).  Para obter mais informações sobre a formatação ORC, consulte [Usar arquivos ORC melhora o desempenho quando o Hive está lendo, gravando e processando dados](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles).

### Tabela particionada
Aqui está a consulta de Hive que cria uma tabela particionada e carrega dados nela.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Ao consultar tabelas particionadas, é recomendável adicionar a condição de partição ao **início** da cláusula `where`, pois isso melhora a eficácia de pesquisa significativamente. 

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Armazenar dados Hive no formato ORC

Os usuários não podem carregar dados diretamente do armazenamento de blob em tabelas Hive armazenadas no formato ORC.  Veja aqui etapas que os usuários devem executar para carregar dados de blobs do Azure para tabelas Hive armazenadas no formato ORC. 

1. Crie uma tabela externa **ARMAZENADA COMO ARQUIVO DE TEXTO** e carregue dados do armazenamento de blob para a tabela.

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

2. Crie uma tabela interna com o mesmo esquema que a tabela externa na etapa 1 e o mesmo delimitador de campo, e armazene dados Hive no formato ORC.

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

[AZURE.NOTE] Se a tabela ARQUIVO DE TEXTO `<nome do banco de dados>.<nome da tabela de arquivo de texto externa>` tiver partições, na ETAPA 3, `SELECT * FROM <database name>.<external textfile table name>` você deverá selecionar a variável de partição como um campo do conjunto de dados retornado.  Inseri-lo no `<nome do banco de dados>.<nome da tabela ORC>` falhará, pois `<nome do banco de dados>.<nome da tabela ORC>` não tem a variável de partição como um campo no esquema de tabela.  Nesse caso, os usuários precisam selecionar especificamente os campos que serão inseridos ao `<nome do banco de dados>.<nome da tabela ORC>`, como mostrado a seguir:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		      SELECT field1, field2, ..., fieldN
		      FROM <database name>.<external textfile table name> 
		      WHERE <partition variable>=<partition value>;

4. É seguro remover o `<nome da tabela do arquivo de texto externa>` ao usar a consulta a seguir depois de todos os dados serem inserido no `<nome do banco de dados>.<nome da tabela ORC>`:

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Depois de seguir esse procedimento, você deve ter uma tabela com dados no formato ORC pronta para uso. 

<!--HONumber=49-->