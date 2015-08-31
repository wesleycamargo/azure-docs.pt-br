<properties 
	pageTitle="Analisar dados de atraso de voo com Hadoop no HDInsight | Microsoft Azure" 
	description="Saiba como usar um script do Windows PowerShell para provisionar um cluster HDInsight, executar um trabalho do Hive, executar um trabalho do Sqoop e excluir o cluster." 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="larryfr"/>

#Analisar dados de atraso de voo usando o Hadoop no HDInsight

Saiba como analisar dados de atraso de voos usando o Hive no HDInsight baseado em Linux (visualização) e exporte os dados para um Banco de Dados SQL do Azure usando o Sqoop.

> [AZURE.NOTE]Embora diversas partes deste documento possam ser usadas com clusters HDInsight baseados no Windows (Python e Hive, por exemplo), muitas etapas deste documento são específicas de clusters baseados em Linux. Para conhecer as etapas que funcionarão em um cluster baseado no Windows, confira [Analisar dados de atraso de voos usando o Hive no HDInsight](hdinsight-analyze-flight-delay-data.md)

###Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Um cluster HDInsight__. Confira [Introdução ao uso do Hadoop com o Hive no HDInsight em Linux](hdinsight-hadoop-linux-tutorial-get-started.md) para obter as etapas da criação de um novo cluster HDInsight baseado em Linux.

- __Banco de dados SQL do Azure__. Você usará um banco de dados SQL do Azure como um repositório de dados de destino. Se você ainda não tiver um Banco de Dados SQL, confira [Como criar e configurar um Banco de Dados SQL do Azure](../sql-database/sql-database-create-configure.md) para criar um.

- __CLI do Azure__. Se você não tiver instalado a CLI do Azure, confira [Instalar e configurar a CLI do Azure](../xplat-cli.md) para obter mais etapas.


##Baixar os dados de voos

1. Vá para [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].
2. Na página, selecione os valores a seguir:

	| Name | Value | | Filter Year | 2013 | | Filter Period | January | | Fields | Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Limpe todos os outros campos |

3. Clique em **Download**.

##Carregar os dados

1. Use o comando a seguir para carregar o arquivo zip no nó principal do cluster HDInsight:

		scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	Substitua __NOMEDOARQUIVO__ pelo nome do arquivo zip. Substitua __NOMEDEUSUÁRIO__ pelo logon SSH para o cluster HDInsight. Substitua NOMEDOCLUSTER pelo nome do seu cluster HDInsight.
	
	> [AZURE.NOTE]Se você usar uma senha para autenticar seu logon SSH, a senha será solicitada. Se você tiver usado uma chave pública, talvez precise usar o parâmetro `-i` e especificar a chave privada correspondente. Por exemplo, `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Quando o upload for concluído, conecte-se ao cluster usando SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Para obter mais informações sobre como usar SSH com o HDInsight baseado em Linux, confira os seguintes artigos:
	
	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows)
	
3. Uma vez conectado, use o seguinte para descompactar o arquivo .zip:

		unzip FILENAME.zip
	
	Isso extrairá um arquivo .csv com aproximadamente 60 MB de tamanho.
	
4. Use o seguinte para criar um novo diretório no WASB (o repositório de dados distribuídos usado pelo HDInsight) e copie o arquivo:

	hadoop fs -mkdir -p /tutorials/flightdelays/data hadoop fs -copyFromLocal NOMEDOARQUIVO.csv /tutorials/flightdelays/data/NOMEDOARQUIVO.csv
	
##Criar e executar o HiveQL

Use as etapas a seguir para importar dados do arquivo CSV para uma tabela do Hive chamada __Delays__.

1. Use a instrução a seguir para criar e editar um novo arquivo chamado __flightdelays.hql__:

		nano flightdelays.hql
		
	Use o seguinte como conteúdo deste arquivo:
	
		DROP TABLE delays_raw;
		-- Creates an external table over the csv file
		CREATE EXTERNAL TABLE delays_raw (
			YEAR string,
			FL_DATE string,
			UNIQUE_CARRIER string,
			CARRIER string,
			FL_NUM string,
			ORIGIN_AIRPORT_ID string,
			ORIGIN string,
			ORIGIN_CITY_NAME string,
			ORIGIN_CITY_NAME_TEMP string,
			ORIGIN_STATE_ABR string,
			DEST_AIRPORT_ID string,
			DEST string,
			DEST_CITY_NAME string,
			DEST_CITY_NAME_TEMP string,
			DEST_STATE_ABR string,
			DEP_DELAY_NEW float,
			ARR_DELAY_NEW float,
			CARRIER_DELAY float,
			WEATHER_DELAY float,
			NAS_DELAY float,
			SECURITY_DELAY float,
			LATE_AIRCRAFT_DELAY float)
		-- The following lines describe the format and location of the file
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE
		LOCATION '/tutorials/flightdelays/data';
		
		-- Drop the delays table if it exists
		DROP TABLE delays;
		-- Create the delays table and populate it with data
		-- pulled in from the CSV file (via the external table defined previously)
		CREATE TABLE delays AS
		SELECT YEAR AS year,
		    FL_DATE AS flight_date,
		    substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
		    substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
		    substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
		    ORIGIN_AIRPORT_ID AS origin_airport_id,
		    substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
		    substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
		    substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
		    DEST_AIRPORT_ID AS dest_airport_id,
		    substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
		    substring(DEST_CITY_NAME,2) AS dest_city_name,
		    substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
		    DEP_DELAY_NEW AS dep_delay_new,
		    ARR_DELAY_NEW AS arr_delay_new,
		    CARRIER_DELAY AS carrier_delay,
		    WEATHER_DELAY AS weather_delay,
		    NAS_DELAY AS nas_delay,
		    SECURITY_DELAY AS security_delay,
		    LATE_AIRCRAFT_DELAY AS late_aircraft_delay
		FROM delays_raw;
		
2. Use __Ctrl + X__ e __Y__ para salvar o arquivo.

3. Use o seguinte para iniciar o Hive e executar o arquivo __flightdelays.hql__:

		hive -i flightdelays.hql
		
	Isso executará o arquivo e retornará um prompt do `hive>`.

4. Quando você receber o prompt do `hive>`, use o seguinte para recuperar dados dos dados de atraso de voos importados.

		INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
		SELECT regexp_replace(origin_city_name, '''', ''),
			avg(weather_delay)
		FROM delays
		WHERE weather_delay IS NOT NULL
		GROUP BY origin_city_name;

	Isso irá recuperar uma lista de cidades em que houve atrasos causados pelo clima, além do tempo médio de atrasos, e salve-a em `/tutorials/flightdelays/output`. Posteriormente, o Sqoop lerá os dados desse local e os exportará para o Banco de Dados SQL do Azure.

##Criar um banco de dados SQL

Siga as etapas abaixo para criar um Banco de Dados SQL do Azure. Isso será usado para manter os dados exportados do HDInsight por meio do Sqoop.

1. Do seu ambiente de desenvolvimento onde a CLI do Azure está instalada, use o comando a seguir para criar um novo Banco de Dados SQL do Azure:

		azure sql server create <adminLogin> <adminPassword> <region>

	Por exemplo, `azure sql server create admin password "West US"`.

	Quando o comando for concluído, você receberá uma resposta semelhante à seguinte:

		info:    Executing command sql server create
		+ Creating SQL Server
		data:    Server Name i1qwc540ts
		info:    sql server create command OK

> [AZURE.IMPORTANT]Observe o nome do servidor retornado por este comando. Esse é o nome curto do Banco de Dados SQL Server que foi criado. O FQDN (nome de domínio totalmente qualificado) é `<shortname>.database.windows.net`.

2. Use o seguinte comando para criar um banco de dados denominado **sqooptest** no Banco de Dados SQL Server:

        azure sql db create [options] <serverName> sqooptest <adminLogin> <adminPassword>

    Isso retornará uma mensagem "OK" quando terminar.

	> [AZURE.NOTE]Se você receber um erro indicando que não tem, talvez seja necessário adicionar o endereço IP da estação de trabalho cliente ao firewall do Banco de Dados SQL usando o seguinte comando:
	>
	> `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

##Criar uma tabela do Banco de Dados SQL

> [AZURE.NOTE]Há várias maneiras para se conectar ao Banco de Dados SQL para criar uma tabela. As seguintes etapas usam [FreeTDS](http://www.freetds.org/) do cluster HDInsight.

1. Use o SSH para conectar-se ao cluster HDInsight baseado em Linux e execute as etapas a seguir na sessão SSH.

3. Use o seguinte comando para instalar o FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Uma vez que o FreeTDS tiver sido instalado, use o seguinte comando para conectar-se ao Banco de Dados SQL Server criado anteriormente:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Você receberá saídas semelhantes ao seguinte:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. Ao prompt `1>`, insira o seguinte:

        CREATE TABLE [dbo].[delays](
		[origin_city_name] [nvarchar](50) NOT NULL,
		[weather_delay] float,
		CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
		([origin_city_name] ASC))
		GO

    Quando a instrução `GO` for inserida, as instruções anteriores serão avaliadas. Isso criará uma nova tabela chamada __delays__ com um índice clusterizado (exigido pelo Banco de Dados SQL).

    Use o seguinte para verificar se a tabela foi criada:

        SELECT * FROM information_schema.tables
        GO

    Você deverá ver uma saída semelhante ao seguinte:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     delays      BASE TABLE

8. Para sair do utilitário tsql, insira `exit` no prompt `1>`.
	
##Exportar dados com o Sqoop

1. Use o seguinte comando para criar um link para o driver JDBC do SQL Server do diretório lib Sqoop. Isso permite que o Sqoop use esse driver para se comunicar com o Banco de Dados SQL:

		sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /usr/hdp/current/sqoop-client/lib/sqljdbc4.jar

2. Use o comando a seguir para verificar se o Sqoop pode ver seu Banco de Dados SQL:

		sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

	Isso deve retornar uma lista de bancos de dados, incluindo o banco de dados sqooptest que você criou anteriormente.

3. Use o comando a seguir para exportar dados de hivesampletable para a tabela mobiledata:

		sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasb:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

	Isso instrui o Sqoop a se conectar ao Banco de Dados SQL, ao banco de dados sqooptest e a exportar os dados do wasb:///tutorials/flightdelays/output (onde armazenamos a saída da consulta do hive) para a tabela de atrasos.

4. Depois de concluir o comando, use o seguinte para se conectar ao banco de dados usando TSQL:

		TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

	Uma vez conectado, use as instruções a seguir para verificar se os dados foram exportados para a tabela mobiledata:
	
		SELECT * FROM delays
		GO

	Você deve ver uma listagem dos dados na tabela. Digite `exit` para sair do utilitário tsql.

##<a id="nextsteps"></a> Próximas etapas
Agora você compreende como carregar um arquivo para o armazenamento de Blob do Azure, como preencher uma tabela Hive usando os dados do armazenamento de Blob do Azure, como executar consultas do Hive e como usar o Sqoop para exportar dados do HDFS para o banco de dados SQL do Azure. Para saber mais, consulte os seguintes artigos:

* [Introdução ao HDInsight][hdinsight-get-started]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Oozie com o HDInsight][hdinsight-use-oozie]
* [Usar o Sqoop com o HDInsight][hdinsight-use-sqoop]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Desenvolver programas MapReduce em Java para HDInsight][hdinsight-develop-mapreduce]
* [Desenvolver programas de streaming do Hadoop em Python para o HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 

<!---HONumber=August15_HO8-->