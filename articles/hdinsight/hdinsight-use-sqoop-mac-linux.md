<properties
	pageTitle="Usar o Sqoop do Hadoop no HDInsight | Microsoft Azure"
	description="Aprenda a usar o Sqoop para importar e exportar entre um Hadoop baseado em Linux no cluster HDInsight e um banco de dados SQL do Azure."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2015"
	ms.author="larryfr"/>

#Usar o Sqoop com Hadoop no HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar o Sqoop para importar e exportar entre um cluster HDInsight baseado em Linux e o banco de dados SQL Server ou Banco de Dados SQL Server do Azure.

> [AZURE.NOTE]As etapas deste artigo usam o SSH para se conectar a um cluster HDInsight baseado em Linux. Os clientes Windows também podem usar o PowerShell do Azure para trabalhar com Sqoop em clusters baseados em Linux, conforme documentado em [Usar o Sqoop com o Hadoop no HDInsight (PowerShell)](hdinsight-use-sqoop.md).

##O que é o Sqoop?

Embora o Hadoop seja uma opção natural para o processamento de dados semiestruturados e não estruturados, como logs e arquivos, também pode ser necessário processar dados estruturados armazenados em bancos de dados relacionais.

O [Sqoop][sqoop-user-guide-1.4.4] é uma ferramenta desenvolvida para transferir dados entre clusters Hadoop e bancos de dados relacionais. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacional), como SQL ou MySQL ou Oracle para o HDFS (Sistema de Arquivos Distribuído) do Hadoop, transformar os dados no Hadoop com MapReduce ou Hive e, em seguida, exportar os dados de volta para um RDBMS. Neste tutorial, você está usando um Banco de Dados SQL como seu banco de dados relacional.

Para as versões do Sqoop com suporte em clusters HDInsight, confira [Novidades nas versões de clusters fornecidas pelo HDInsight][hdinsight-versions].


##Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Estação de trabalho**: um computador com um cliente SSH.

- **CLI do Azure**: para obter mais informações, consulte [Instalar e configurar a CLI do Azure](../xplat-cli.md)

- **Cluster HDInsight baseado no Linux**: para obter instruções sobre como provisionar um cluster, consulte [Introdução ao uso do HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) ou [Provisionar clusters HDInsight][hdinsight-provision].

- **Banco de Dados SQL do Azure**: este documento fornece instruções para criar um Banco de Dados SQL. Para obter mais informações sobre o Banco de Dados SQL, consulte [Introdução ao uso do banco de dados SQL do Azure][sqldatabase-get-started].

* **SQL Server**: as etapas deste documento também podem ser usadas, com algumas modificações, com o SQL Server. Para obter mais informações sobre requisitos específicos para usar este artigo com o SQL Server, consulte a seção [Usando o SQL Server](#using-sql-server).

##Compreender o cenário

O cluster HDInsight é fornecido com alguns dados de exemplo. Você usará uma tabela Hive chamada **hivesampletable** que faz referência ao arquivo de dados localizado em **wasb:///hive/warehouse/hivesampletable**. A tabela contém alguns dados de dispositivo móvel. O esquema da tabela Hive é:

| Campo | Tipo de dados |
| ----- | --------- |
| clientid | cadeia de caracteres |
| querytime | cadeia de caracteres |
| market | cadeia de caracteres |
| deviceplatform | cadeia de caracteres |
| devicemake | cadeia de caracteres |
| devicemodel | cadeia de caracteres |
| state | cadeia de caracteres |
| country | cadeia de caracteres |
| querydwelltime | double |
| sessionid | bigint |
| sessionpagevieworder | bigint |

Você primeiro exportará **hivesampletable** para o banco de dados SQL do Azure ou SQL Server em uma tabela chamada **mobiledata** e, em seguida, importará a tabela de volta para o HDInsight em **wasb:///tutorials/usesqoop/importeddata**.

##Criar um banco de dados

1. Abra um terminal ou prompt de comando e use o seguinte comando para criar um novo Banco de Dados SQL Server:

        azure sql server create <adminLogin> <adminPassword> <region>

    Por exemplo, `azure sql server create admin password "West US"`.

    > [AZURE.NOTE]Se você receber um erro indicando que não tem, talvez seja necessário adicionar o endereço IP da estação de trabalho cliente ao firewall do Banco de Dados SQL usando o seguinte comando:
    >
    > `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

    Quando o comando for concluído, você receberá uma resposta semelhante à seguinte:

        info:    Executing command sql server create
        + Creating SQL Server
        data:    Server Name i1qwc540ts
        info:    sql server create command OK

    > [AZURE.IMPORTANT]Observe o nome do servidor retornado por este comando. Esse é o nome curto do Banco de Dados SQL Server que foi criado. O nome de domínio totalmente qualificado (FQDN) é **&lt;shortname&gt;.database.windows.net**.

2. Use o seguinte comando para criar um banco de dados denominado **sqooptest** no Banco de Dados SQL Server:

        sql db create [options] <serverName> sqooptest <adminLogin> <adminPassword>

    Isso retornará uma mensagem "OK" quando terminar.


##Criar uma tabela

> [AZURE.NOTE]Há várias maneiras para se conectar ao Banco de Dados SQL para criar uma tabela. As seguintes etapas usam [FreeTDS](http://www.freetds.org/) do cluster HDInsight.

1. Utilize o SSH para se conectar ao cluster HDInsight com base em Linux. O endereço a ser usado ao conectar-se é `CLUSTERNAME-ssh.azurehdinsight.net` e a porta é `22`.

	Para obter mais informações sobre como usar o SSH para se conectar ao HDInsight, consulte os seguintes documentos:

    * **Clientes Linux, Unix ou OS X**: consulte [Conectar-se a um cluster HDInsight com base no Linux do Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Clientes Windows**: consulte [Conectar-se a um cluster HDInsight com base no Linux do Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

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

        CREATE TABLE [dbo].[mobiledata](
		[clientid] [nvarchar](50),
		[querytime] [nvarchar](50),
		[market] [nvarchar](50),
		[deviceplatform] [nvarchar](50),
		[devicemake] [nvarchar](50),
		[devicemodel] [nvarchar](50),
		[state] [nvarchar](50),
		[country] [nvarchar](50),
		[querydwelltime] [float],
		[sessionid] [bigint],
		[sessionpagevieworder] [bigint])
        GO
		CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    Quando a instrução `GO` for inserida, as instruções anteriores serão avaliadas. Primeiro, a tabela **mobiledata** é criada e, em seguida, um índice de cluster é adicionado a ela (exigido pelo Banco de Dados SQL).

    Use o seguinte para verificar se a tabela foi criada:

        SELECT * FROM information_schema.tables
        GO

    Você deverá ver uma saída semelhante ao seguinte:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. Para sair do utilitário tsql, insira `exit` no prompt `1>`.

##Exportação do Sqoop

2. Use o seguinte comando para criar um link para o driver JDBC do SQL Server do diretório lib Sqoop. Isso permite que o Sqoop use esse driver para se comunicar com o Banco de Dados SQL:

        sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /usr/hdp/current/sqoop-client/lib/sqljdbc4.jar

3. Use o comando a seguir para verificar se o Sqoop pode ver seu Banco de Dados SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Isso deve retornar uma lista de bancos de dados, incluindo o banco de dados **sqooptest** que você criou anteriormente.

4. Use o seguinte comando para exportar dados de **hivesampletable** para a tabela **mobiledata**:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Isso instrui o Sqoop a se conectar ao Banco de Dados SQL, ao banco de dados **sqooptest** e exportar os dados do **wasb:///hive/warehouse/hivesampletable** (arquivos físico para o *hivesampletable*) à tabela **mobiledata**.

5. Depois de concluir o comando, use o seguinte para se conectar ao banco de dados usando TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Uma vez conectado, use as instruções a seguir para verificar que os dados foram exportados para a tabela **mobiledata**:

        SELECT * FROM mobiledata
        GO

    Você deve ver uma listagem dos dados na tabela. Digite `exit` para sair do utilitário tsql.

##Importação do Sqoop

1. Use o seguinte para importar dados da tabela **mobiledata** no Banco de Dados SQL para o diretório **wasb:///tutorials/usesqoop/importeddata** do HDInsight: 
        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    Os dados importados terão campos que são separados por um caractere de tabulação, e as linhas serão encerradas por um caractere de nova linha.

2. Quando a importação for concluída, use o seguinte comando para listar os dados no novo diretório:

        hadoop fs -text wasb:///tutorials/usesqoop/importeddata/part-m-00000

##Usar o SQL Server

Você também pode usar o Sqoop para importar e exportar dados do SQL Server, seja no seu data center ou em uma máquina virtual hospedada no Azure. As diferenças entre o uso do Banco de Dados SQL e SQL Server são:

* O HDInsight e o SQL Server devem estar na mesma rede virtual do Azure

    > [AZURE.NOTE]O Azure HDInsight oferece suporte somente a redes virtuais baseadas no local, e não trabalha atualmente com redes virtuais baseadas em grupos de afinidade.

    Ao usar o SQL Server no datacenter, você deve configurar a rede virtual como *site a site* ou *ponto a site*.

    > [AZURE.NOTE]Para redes virtuais **ponto a site**, o SQL Server deve estar executando o aplicativo de configuração de cliente VPN, que está disponível no **Painel** de configuração da rede virtual do Azure.

    Para obter informações sobre como criar e configurar uma rede virtual, consulte [Tarefas de configuração de rede virtual](http://msdn.microsoft.com/library/azure/jj156206.aspx).

* O SQL Server também deve ser configurado para permitir autenticação do SQL. Para obter mais informações, consulte [Escolher um modo de autenticação](https://msdn.microsoft.com/ms144284.aspx)

* Você precisará configurar o SQL Server para aceitar conexões remotas. Para obter mais informações, consulte [Como solucionar problemas de conexão com o mecanismo de banco de dados SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)

* Você deve criar o banco de dados **sqooptest** no SQL Server usando um utilitário como **SQL Server Management Studio** ou **tsql**. As etapas para usar a CLI do Azure só funcionam para o Banco de Dados SQL do Azure

    As instruções TSQL para criar a tabela **mobiledata** são semelhantes às utilizadas para o Banco de Dados SQL, com exceção da criação de um índice de cluster; isso não é necessário para o SQL Server:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* Ao conectar-se ao SQL Server no HDInsight, você terá que usar o endereço IP do SQL Server, a menos que tenha configurado um Sistema de Nome de Domínio (DNS) para resolver nomes na Rede Virtual do Azure. Por exemplo:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##Próximas etapas

Você aprendeu como usar Sqoop. Para obter mais informações, consulte:

- [Usar o Oozie com o HDInsight][hdinsight-use-oozie]: use a ação do Sqoop no fluxo de trabalho do Oozie.
- [Analisar dados de atraso de voos usando o HDInsight][hdinsight-analyze-flight-data]: use o Hive para analisar dados de atraso de voos e o Sqoop para exportar dados para o banco de dados SQL do Azure.
- [Carregar dados no HDInsight][hdinsight-upload-data]: localize outros métodos de carregamento de dados no HDInsight/Armazenamento de Blob do Azure.




[azure-management-portal]: https://manage.windowsazure.com/

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
 

<!---HONumber=62-->