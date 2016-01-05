<properties 
	pageTitle="Criar e carregar dados nas tabelas Hive do armazenamento de blobs | Microsoft Azure" 
	description="Criar tabelas Hive e carregar dados em blobs para tabelas hive" 
	services="machine-learning,storage" 
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
	ms.date="10/12/2015" 
	ms.author="hangzh;bradsev" />

 
#Criar e carregar dados nas tabelas Hive do armazenamento de blobs do Azure

Esse **menu** liga os tópicos que descrevem a inclusão de dados em ambientes de destino em que os dados podem ser armazenados e processados durante o Processo de Análise do Cortana (CAPS).

[AZURE.INCLUDE [seletor de dados de ingestão de limite](../../includes/cap-ingest-data-selector.md)]

 
## Introdução
Neste **documento**, são apresentadas consultas de Hive genéricas que criam tabelas Hive e carregam dados do armazenamento de blobs do Azure. Também são fornecida algumas orientações sobre o particionamento de tabelas Hive e sobre como usar a formatação ORC (Colunar de Linha Otimizado) para melhorar o desempenho da consulta.

## Pré-requisitos
Este artigo supõe que você:
 
* Criou uma conta de armazenamento do Azure. Se precisar de instruções, consulte [Criar uma conta de Armazenamento do Azure](../hdinsight-get-started.md#storage) 
* Provisionou um cluster do Hadoop personalizado com o serviço HDInsight. Se precisar de instruções, consulte [Personalizar os clusters do Hadoop do Azure HDInsight para análise avançada](machine-learning-data-science-customize-hadoop-cluster.md).
* Habilitou o acesso remoto para o cluster, conectou-se e abriu o Console de Linha de Comando do Hadoop. Se precisar de instruções, consulte [Acessar o nó principal do Cluster do Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode). 

## Carregar dados no armazenamento de blob do Azure
Se você criou uma máquina virtual do Azure seguindo as instruções fornecidas em [Configurar uma máquina virtual do Azure para análise avançada](machine-learning-data-science-setup-virtual-machine.md), esse arquivo de script deve ter sido baixado no diretório *C:\\Users\\<user name>\\Documents\\Data Science Scripts* na máquina virtual. Essas consultas de Hive exigem somente conectar em seu próprio esquema de dados e que a configuração de armazenamento de blobs do Azure nos campos apropriados esteja pronta para envio.

Supomos que os dados de tabelas Hive estejam em formato de tabela **descompactado** e que os dados foram carregado no contêiner padrão (ou em um adicional) da conta de armazenamento usada pelo cluster do Hadoop.

Se você deseja praticar no _Dados de Viagens de Táxi em NYC_, é necessário primeiro baixar todos os 24 arquivos dos <a href="http://www.andresmh.com/nyctaxitrips/" target="_blank">Dados de Viagens de Táxi em NYC</a> (12 arquivos Trip e 12 arquivos Fare), **descompactar** todos os arquivos em arquivos CSV e carregá-los para o contêiner padrão (ou adicional) da conta de armazenamento do Azure criada pelo procedimento descrito no tópico [Personalizar os clusters do Hadoop do Azure HDInsight para processo e tecnologia de análise avançada](machine-learning-data-science-customize-hadoop-cluster.md). O processo para carregar os arquivos .csv para o contêiner padrão na conta de armazenamento pode ser encontrado nesta [página](machine-learning-data-science-process-hive-walkthrough/#upload).

## <a name="submit"></a>Como enviar consultas de Hive
Consultas de Hive podem ser enviadas usando:

* a Linha de Comando do Hadoop no nó principal do cluster
* o IPython Notebook
* o Editor de Hive
* Scripts do PowerShell do Azure

Consultas de Hive são semelhantes ao SQL. Usuários familiarizados com o SQL podem achar o <a href="http://hortonworks.com/wp-content/uploads/downloads/2013/08/Hortonworks.CheatSheet.SQLtoHive.pdf" target="_blank">Roteiro de SQL para Hive</a> útil.

Ao enviar uma consulta de Hive, você também pode controlar o destino da saída de consultas de Hive, seja na tela, para um arquivo local no nó principal ou para um blob do Azure.

### Por meio do Console de Linha de Comando do Hadoop no nó principal do Cluster do Hadoop

Se a consulta for complexa, enviar de consultas de Hive diretamente do nó principal do cluster do Hadoop normalmente gera resultados mais rápidos do que enviá-las com um Editor de Hive ou usando scripts do PowerShell do Azure.

Faça logon no nó principal do cluster do Hadoop, abra a Linha de Comando do Hadoop na área de trabalho do nó principal e digite o comando

    cd %hive_home%\bin

Os usuários têm três maneiras de enviar consultas de Hive no console de Linha de Comando do Hadoop:

* diretamente da linha de comando do Hadoop
* usando arquivos .hql
* pelo console de comando de Hive

#### Enviar consultas de Hive diretamente da Linha de Comando do Hadoop

Os usuários podem executar um comando como

	hive -e "<your hive query>;

para enviar consultas de Hive simples diretamente pela linha de comando do Hadoop. Veja um exemplo, no qual a caixa vermelha descreve o comando que envia a consulta Hive e a caixa verde descreve a saída da consulta Hive.

![Criar espaço de trabalho](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png)

#### Enviar consultas de Hive em arquivos de .hql

Quando a consulta de Hive é mais complicada e tem várias linhas, não é prático editar consultas na linha de comando ou no console de comando de Hive. Uma alternativa é usar um editor de texto no nó principal do cluster do Hadoop e salvar as consultas de Hive em um arquivo .hql em um diretório local do nó principal. Em seguida, a consulta de Hive no arquivo HQL pode ser enviada usando o argumento `-f` no comando `hive` da seguinte maneira:

	`hive -f "<path to the .hql file>"`


#### Suprimir a impressão da tela de status de progresso de consultas de Hive

Por padrão, após a consulta de Hive ser enviada no console de Linha de Comando do Hadoop, o andamento do trabalho de Mapear/Reduzir será ser impresso na tela. Para suprimir a impressão da tela de andamento do trabalho de Mapear/Reduzir, você pode usar o argumento `-S` (diferencia maiúsculas de minúsculas) na linha de comando da seguinte maneira:

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

#### Enviar consultas de Hive no console de comando de Hive.

Os usuários também podem entrar no console de comando de Hive executando o comando `hive` na Linha de Comando do Hadoop e enviar consultas de Hive no console de comando do Hive no prompt **hive>**. Aqui está um exemplo.

![Criar espaço de trabalho](./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png)

Neste exemplo, as duas caixas vermelhas realçam os comandos usados para inserir o console de comando de Hive e a consulta de Hive enviada no console de comando de Hive, respectivamente. A caixa verde realça a saída da consulta de Hive.

Os exemplos anteriores mostram os resultados da consulta de Hive diretamente na tela. Os usuários também podem gravar a saída em um arquivo local no nó principal ou em um blob do Azure. Em seguida, os usuários podem usar outras ferramentas para analisar com mais detalhes a saída das consultas de Hive.

#### Resultados da consulta de Hive em um arquivo local de saída.

Para exibir os resultados da consulta de Hive em um diretório local no nó principal, os usuários precisam enviar a consulta de Hive na Linha de Comando do Hadoop da seguinte maneira:

	`hive -e "<hive query>" > <local path in the head node>`


#### Exportar a saída de consulta de Hive para um blob do Azure

Os usuários também podem exportar a saída da consulta de Hive para um blob do Azure dentro do contêiner padrão do cluster do Hadoop. A consulta de Hive para fazer isso é semelhante a esta:

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

No exemplo a seguir, a saída da consulta de Hive é gravada em um diretório de blob `queryoutputdir` no contêiner padrão do cluster do Hadoop. Aqui, você deve fornecer somente o nome do diretório, sem o nome do blob. Um erro será gerado se você fornecer os nomes do blob e do diretório, como **wasb:///queryoutputdir/queryoutput.txt*.

![Criar espaço de trabalho](./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png)

A saída da consulta de Hive pode ser vista no armazenamento de blob abrindo o contêiner padrão do cluster do Hadoop usando a ferramenta Gerenciador de Armazenamento do Azure (ou equivalente). Você pode aplicar o filtro (destacado pela caixa vermelha) se quiser recuperar um blob com letras especificadas em nomes.

![Criar espaço de trabalho](./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png)

### Por meio do Editor de Hive ou de comandos do PowerShell do Azure

Os usuários também podem usar o Console de Consulta (Editor de Hive) digitando a URL do formulário

*https://&#60;Hadoop nome do cluster>.azurehdinsight.net/Home/HiveEditor*

em um navegador da Web. Observe que você será solicitado a inserir as credenciais do cluster do Hadoop para fazer logon.

Como alternativa, você pode [Executar consultas Hive usando o PowerShell](../hdinsight/hdinsight-hadoop-use-hive-powershell.md).


## <a name="create-tables"></a>Criar banco de dados e tabelas Hive

As consultas de Hive são compartilhadas no [repositório Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) e podem ser baixadas de lá.

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

- **&#60;nome do banco de dados>**: o nome do banco de dados que os usuários desejam criar. Se os usuários desejam apenas usar o banco de dados padrão, a consulta *create database...* pode ser omitida. 
- **&#60;nome da tabela>**: o nome da tabela que os usuários desejam criar no banco de dados especificado. Se os usuários desejam usar o banco de dados padrão, a tabela pode ser referida diretamente por *&#60;nome da tabela>* sem &#60;nome do banco de dados>.
- **&#60;separador de campo>**: o separador que delimita os campos no arquivo de dados a serem carregados na tabela Hive. 
- **&#60;separador de linha>**: o separador que delimita as linhas no arquivo de dados. 
- **&#60;local de armazenamento>**: o local de armazenamento do Azure para salvar os dados das tabelas Hive. Se os usuários não especificarem *LOCATION &#60;local do armazenamento>*, o banco de dados e as tabelas são armazenadas no diretório *hive/warehouse/* no contêiner padrão do cluster de Hive por padrão. Se um usuário deseja especificar o local de armazenamento, este deve estar dentro do contêiner padrão para o banco de dados e tabelas. Esse local precisa ser chamado como um local relativo ao contêiner padrão do cluster no formato *'wasb:///&#60;diretório 1>/'* ou *'wasb:///&#60;diretório 1>/&#60;diretório 2>/'*, etc. Após a consulta ser executada, os diretórios relativos serão criados no contêiner padrão. 
- **TBLPROPERTIES("skip.header.line.count"="1")**: Se o arquivo de dados tiver uma linha de cabeçalho, os usuários precisam adicionar essa propriedade **ao final** da consulta *create table*. Caso contrário, a linha de cabeçalho será carregada como um registro para a tabela. Se o arquivo de dados não tiver uma linha de cabeçalho, essa configuração pode ser omitida na consulta. 

## <a name="load-data"></a>Carregar dados para tabelas Hive
Veja aqui a consulta Hive que carrega dados em uma tabela Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **&#60;caminho para dados de blob>**: se o arquivo de blob a ser carregado para a tabela Hive estiver no contêiner padrão do cluster do Hadoop do HDInsight, o *&#60;caminho para dados de blob>* deve estar no formato *'wasb:///&#60;diretório neste contêiner>/&#60;nome do arquivo de blob>'*. O arquivo de blob também pode estar em um contêiner adicional do cluster do Hadoop do HDInsight. Nesse caso, *&#60;caminho para dados de blob>* deve estar no formato *'wasb://&#60;nome do contêiner>@&#60;nome da conta de armazenamento>.blob.core.windows.net/&#60;nome do arquivo de blob>'*.

	>[AZURE.NOTE]Os dados blob a serem carregados na tabela Hive deve estar no contêiner padrão ou adicional da conta de armazenamento para o cluster do Hadoop. Caso contrário, a consulta *LOAD DATA* falhará reclamando que não pode acessar os dados.


## <a name="partition-orc"></a>Tópicos avançados: tabela e repositório de dados Hive particionados no formato ORC

Se os dados forem grandes, particionar a tabela é útil para consultas que só precisam verificar algumas partições da tabela. Por exemplo, é útil particionar os dados de log de um site da Web por datas.

Além do particionamento de tabelas Hive, também é útil armazenar os dados Hive no formato ORC (Colunar de Linha Otimizado). Para obter mais informações sobre a formatação ORC, consulte <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">Usando arquivos ORC melhora o desempenho quando o Hive lê, grava e processa dados</a>.

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

Ao consultar tabelas particionadas, é recomendável adicionar a condição de partição no **início** da cláusula `where`, pois isso melhora a eficácia de pesquisa significativamente.

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Armazenar dados Hive no formato ORC

Os usuários não podem carregar dados diretamente do armazenamento de blob em tabelas Hive armazenadas no formato ORC. Veja aqui etapas que os usuários devem executar para carregar dados de blobs do Azure para tabelas Hive armazenadas no formato ORC.

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

		INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

	>[AZURE.NOTE]Se a tabela ARQUIVO DE TEXTO *&#60;nome do banco de dados>.&#60;nome da tabela de arquivo de texto externa>* tiver partições, na ETAPA 3, o comando `SELECT * FROM <database name>.<external textfile table name>` selecionará a variável de partição como um campo no conjunto de dados retornados. Inserir no *&#60;nome do banco de dados>.&#60;nome da tabela ORC>* falhará, uma vez que o *&#60;nome do banco de dados>.&#60;nome da tabela ORC>* não tem a variável de partição como um campo no esquema de tabela. Nesse caso, os usuários precisam selecionar especificamente os campos que serão inseridos ao *&#60;nome do banco de dados>.&#60;nome da tabela ORC>*, como mostrado a seguir:

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		   SELECT field1, field2, ..., fieldN
		   FROM <database name>.<external textfile table name> 
		   WHERE <partition variable>=<partition value>;

4. É seguro remover o *&#60;nome da tabela do arquivo de texto externa>* ao usar a consulta a seguir depois de todos os dados serem inseridos no *&#60;nome do banco de dados>.&#60;nome da tabela ORC>*:

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Depois de seguir esse procedimento, você deve ter uma tabela com dados no formato ORC pronta para uso.


##O ajuste das seções deve ocorrer aqui

Na seção final, são discutidos os parâmetros que os usuários podem ajustar para que o desempenho das consultas do Hive possa ser melhorado.

<!---HONumber=AcomDC_1203_2015-->