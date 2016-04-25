<properties
	pageTitle="Usar o Sqoop do Hadoop no HDInsight baseado em Linux | Microsoft Azure"
	description="Aprenda a usar o Sqoop para importar e exportar entre um Hadoop baseado em Linux no cluster HDInsight e um banco de dados SQL do Azure."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="larryfr"/>

#Usar o Sqoop com Hadoop no HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar o Sqoop para importar e exportar entre um cluster HDInsight baseado em Linux e o banco de dados SQL Server ou Banco de Dados SQL Server do Azure.

> [AZURE.NOTE] As etapas deste artigo usam o SSH para se conectar a um cluster HDInsight baseado em Linux. Os clientes do Windows também podem usar o Azure PowerShell e o SDK .NET do HDInsight para trabalhar com o Sqoop em clusters baseados em Linux. Use o seletor de tabulação para abrir esses artigos.

##Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:


- **Um cluster Hadoop no HDInsight**. Confira [Create cluster and SQL database](hdinsight-use-sqoop.md#create-cluster-and-sql-database) (Criar o cluster e o Banco de dados SQL).
- **Estação de trabalho**: um computador com um cliente SSH.
- **CLI do Azure**: para obter mais informações, consulte [Instalar e configurar a CLI do Azure](../xplat-cli-install.md)

##Exportação do Sqoop

2. Use o seguinte comando para criar um link para o driver JDBC do SQL Server do diretório lib Sqoop. Isso permite que o Sqoop use esse driver para se comunicar com o Banco de Dados SQL:

        sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /usr/hdp/current/sqoop-client/lib/sqljdbc41.jar

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

    > [AZURE.NOTE] O Azure HDInsight oferece suporte somente a redes virtuais baseadas no local, e não trabalha atualmente com redes virtuais baseadas em grupos de afinidade.

    Ao usar o SQL Server no datacenter, você deve configurar a rede virtual como *site a site* ou *ponto a site*.

    > [AZURE.NOTE] Para redes virtuais **ponto a site**, o SQL Server deve estar executando o aplicativo de configuração de cliente VPN, que está disponível no **Painel** de configuração da rede virtual do Azure.

    Para obter informações sobre como criar e configurar uma rede virtual, consulte [Tarefas de configuração de rede virtual](../services/virtual-machines/).

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

- [Usar o Oozie com o HDInsight][hdinsight-use-oozie]\: use a ação do Sqoop no fluxo de trabalho do Oozie.
- [Analisar dados de atraso de voos usando o HDInsight][hdinsight-analyze-flight-data]\: use o Hive para analisar dados de atraso de voos e o Sqoop para exportar dados para o banco de dados SQL do Azure.
- [Carregar dados no HDInsight][hdinsight-upload-data]\: localize outros métodos de carregamento de dados no HDInsight/Armazenamento de Blob do Azure.



[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=AcomDC_0413_2016-->