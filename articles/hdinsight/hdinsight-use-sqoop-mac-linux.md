---
title: Usar o Sqoop do Hadoop no HDInsight baseado em Linux | Microsoft Docs
description: Aprenda a usar o Sqoop para importar e exportar entre um Hadoop baseado em Linux no cluster HDInsight e um banco de dados SQL do Azure.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 6bb8058a74d3417c4972a9010ac9e17739f3e323
ms.lasthandoff: 04/17/2017


---
# <a name="use-sqoop-with-hadoop-in-hdinsight-ssh"></a>Usar o Sqoop com Hadoop no HDInsight (SSH)

[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o Sqoop para importar e exportar entre um cluster HDInsight e o Banco de Dados SQL do Azure ou banco de dados SQL Server.

> [!IMPORTANT]
> As etapas neste documento funcionam somente com clusters HDInsight que usam Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, consulte [Controle de versão do componente do HDInsight](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="install-freetds"></a>Instalar o FreeTDS

1. Utilize SSH para se conectar ao cluster do HDInsight para Linux. O endereço a ser usado ao conectar-se é `CLUSTERNAME-ssh.azurehdinsight.net` e a porta é `22`.

    Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use o seguinte comando para instalar o FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    O FreeTDS é usado em várias etapas para se conectar ao Banco de Dados SQL.

## <a name="create-the-table-in-sql-database"></a>Crie a tabela no banco de dados SQL

> [!IMPORTANT]
> Se você estiver usando o cluster HDInsight e o Banco de Dados SQL criado em [Criar o cluster e o Banco de Dados SQL](hdinsight-use-sqoop.md), ignore as etapas nesta seção. O banco de dados e a tabela foram criados como parte das etapas no documento [Criar o cluster e o Banco de Dados SQL](hdinsight-use-sqoop.md).

1. Da sessão do SSH, use o seguinte comando para se conectar ao servidor do Banco de Dados SQL.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Você receberá saídas semelhantes ao seguinte texto:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

2. No prompt `1>`, insira a seguinte consulta:

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

    Use a seguinte consulta para verificar se a tabela foi criada:

        SELECT * FROM information_schema.tables
        GO

    Você verá uma saída semelhante ao seguinte texto:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

3. Para sair do utilitário tsql, insira `exit` at the `1>` .

## <a name="sqoop-export"></a>Exportação do Sqoop

1. Da conexão SSH para HDInsight, use o comando abaixo para verificar se o Sqoop pode ver seu Banco de Dados SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Esse comando retorna uma lista de bancos de dados, incluindo o banco de dados **sqooptest** que você criou anteriormente.

2. Use o comando a seguir para exportar dados de **hivesampletable** para a tabela **mobiledata**:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    Esse comando instrui o Sqoop a conectar ao banco de dados **sqooptest**. O Sqoop exporta então dados de **wasbs:///hive/warehouse/hivesampletable** para a tabela **mobiledata**.

3. Depois de concluir o comando, use o seguinte comando para se conectar ao banco de dados usando TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Uma vez conectado, use as instruções a seguir para verificar que os dados foram exportados para a tabela **mobiledata** :

        SELECT * FROM mobiledata
        GO

    Você deve ver uma listagem dos dados na tabela. Digite `exit` para sair do utilitário tsql.

## <a name="sqoop-import"></a>Importação do Sqoop

1. Use o seguinte comando para importar dados da tabela **mobiledata** no Banco de Dados SQL para o diretório **wasbs:///tutorials/usesqoop/importeddata** do HDInsight:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    Os campos nos dados que são separados por um caractere de tabulação, e as linhas serão encerradas por um caractere de nova linha.

2. Quando a importação for concluída, use o seguinte comando para listar os dados no novo diretório:

        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

## <a name="using-sql-server"></a>Usar o SQL Server

Você também pode usar o Sqoop para importar e exportar dados do SQL Server, seja no seu data center ou em uma máquina virtual hospedada no Azure. As diferenças entre o uso do Banco de Dados SQL e SQL Server são:

* O HDInsight e o SQL Server devem estar na mesma rede virtual do Azure

  > [!NOTE]
  > O Azure HDInsight oferece suporte somente a redes virtuais baseadas no local, e não trabalha atualmente com redes virtuais baseadas em grupos de afinidade.

    Ao usar o SQL Server no datacenter, você deve configurar a rede virtual como *site a site* ou *ponto a site*.

  > [!NOTE]
  > Ao usar uma rede virtual **ponto a site**, o SQL Server deve executar o aplicativo de configuração do cliente VPN. O cliente VPN está disponível no **Painel** da sua configuração de rede virtual do Azure.

    Para saber mais sobre Rede Virtual do Azure, consulte [Visão Geral da Rede Virtual](../virtual-network/virtual-networks-overview.md).

* O SQL Server também deve ser configurado para permitir autenticação do SQL. Para obter mais informações, consulte o documento [Escolher um modo de autenticação](https://msdn.microsoft.com/ms144284.aspx).

* Você precisará configurar o SQL Server para aceitar conexões remotas. Para obter mais informações, consulte o documento [Como solucionar problemas de conexão com o Mecanismo de Banco de Dados do SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

* Crie o banco de dados **sqooptest** no SQL Server usando um utilitário como **SQL Server Management Studio** ou **tsql**. As etapas para usar a CLI do Azure só funcionam para o Banco de Dados SQL do Azure.

    Use as seguintes instruções do TSQL para criar a tabela **mobiledata**:

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

* Ao conectar-se ao SQL Server no HDInsight, você precisará usar o endereço IP do SQL Server. Por exemplo:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

## <a name="limitations"></a>Limitações

* Exportação em massa — com HDInsight baseado em Linux, o conector Sqoop usado para exportar dados no Microsoft SQL Server ou no Banco de Dados SQL do Azure, atualmente, não permite inserções em massa.

* Envio em lote — Com HDInsight baseado em Linux, ao usar o comutador `-batch` ao executar inserções, o Sqoop executará várias inserções em vez de operações de inserção em lotes.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como usar Sqoop. Para obter mais informações, consulte:

* [Usar o Oozie com o HDInsight][hdinsight-use-oozie]: use a ação do Sqoop no fluxo de trabalho do Oozie.
* [Analisar dados de atraso de voos usando o HDInsight][hdinsight-analyze-flight-data]: use o Hive para analisar dados de atraso de voos e o Sqoop para exportar os dados para o Banco de Dados SQL do Azure.
* [Carregar dados no HDInsight][hdinsight-upload-data]: localize outros métodos de carregamento de dados no HDInsight/Armazenamento de Blobs do Azure.

[hdinsight-versions]:  hdinsight-component-versioning.md
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
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

