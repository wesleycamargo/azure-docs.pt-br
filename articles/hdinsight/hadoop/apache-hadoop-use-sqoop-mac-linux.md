---
title: "Apache Sqoop com Hadoop – Azure HDInsight | Microsoft Docs"
description: Saiba como usar o Apache Sqoop para importar e exportar entre o HDInsight e o Banco de Dados SQL do Azure.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop,sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: larryfr
ms.openlocfilehash: a0a63c414bc68f5125b65e288d78fb546c376c04
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2018
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Usar o Apache Sqoop para importar e exportar dados entre o Hadoop no HDInsight e o Banco de Dados SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar o Apache Sqoop para importar e exportar entre um cluster Hadoop no Azure HDInsight e o Banco de Dados SQL do Azure ou um banco de dados do Microsoft SQL Server. As etapas deste documentam usam o comando `sqoop` diretamente do nó principal do cluster Hadoop. Use o SSH para se conectar ao nó principal e executar os comandos neste documento.

> [!IMPORTANT]
> As etapas neste documento funcionam somente com clusters HDInsight que usam Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!WARNING]
> As etapas neste documento pressupõem que você já criou um Banco de Dados SQL do Azure denominado `sqooptest`.
>
> Este documento fornece instruções T-SQL que são usadas para criar e consultar uma tabela no Banco de Dados SQL. Há muitos clientes com os quais você pode usar essas instruções com o Banco de Dados SQL. Recomendamos os seguintes clientes:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * O utilitário [sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)

## <a name="create-the-table-in-sql-database"></a>Crie a tabela no banco de dados SQL

> [!IMPORTANT]
> Se você estiver usando o cluster HDInsight e o Banco de Dados SQL criado em [Criar o cluster e o Banco de Dados SQL](hdinsight-use-sqoop.md), ignore as etapas nesta seção. O banco de dados e a tabela foram criados como parte das etapas no documento [Criar o cluster e o Banco de Dados SQL](hdinsight-use-sqoop.md).

Use um cliente SQL para conexão ao banco de dados `sqooptest` no seu Banco de Dados SQL. Em seguida, use o T-SQL a seguir para criar uma tabela denominada `mobiledata`:

```sql
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
```

## <a name="sqoop-export"></a>Exportação do Sqoop

1. Use o SSH para conectar ao cluster HDInsight. Por exemplo, o comando a seguir se conecta ao nó principal de um cluster chamado `mycluster`:

    ```bash
    ssh CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Para verificar se o Sqoop pode ver o seu Banco de Dados SQL, use o seguinte comando:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Quando solicitado, insira a senha para o logon no Banco de Dados SQL.

    Esse comando retorna uma lista de bancos de dados, incluindo o banco de dados **sqooptest** usado anteriormente.

3. Para exportar dados da tabela **hivesampletable** do Hive para a tabela **mobiledata** no Banco de Dados SQL, use o seguinte comando:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Para verificar se os dados foram exportados, use a seguinte consulta de seu cliente SQL para exibir os dados exportados:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    Este comando lista 50 linhas que foram importadas para a tabela.

## <a name="sqoop-import"></a>Importação do Sqoop

1. Use o seguinte comando para importar dados da tabela **mobiledata** no Banco de Dados SQL para o diretório **wasb:///tutorials/usesqoop/importeddata** do HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    Os campos nos dados que são separados por um caractere de tabulação, e as linhas serão encerradas por um caractere de nova linha.

    > [!IMPORTANT]
    > O caminho `wasb:///` funciona com clusters que usam o Armazenamento do Azure como o armazenamento de cluster padrão. Para clusters que usam o Azure Data Lake Store, use `adl:///` em vez disso.

2. Quando a importação for concluída, use o seguinte comando para listar os dados no novo diretório:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Usar o SQL Server

Você também pode usar o Sqoop para importar e exportar dados do SQL Server. As diferenças entre o uso do Banco de Dados SQL e SQL Server são:

* O HDInsight e o SQL Server devem estar na mesma Rede Virtual do Azure.

    Para obter um exemplo, consulte o documento [Conectar o HDInsight à sua rede local](./../connect-on-premises-network.md).

    Para saber mais sobre como usar o HDInsight com Redes Virtuais do Azure, veja o documento [Estender o HDInsight usando a Rede Virtual do Azure](../hdinsight-extend-hadoop-virtual-network.md). Para saber mais sobre Rede Virtual do Azure, veja o documento [Visão Geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md).

* O SQL Server também deve ser configurado para permitir autenticação do SQL. Para obter mais informações, consulte o documento [Escolher um modo de autenticação](https://msdn.microsoft.com/ms144284.aspx).

* Você precisará configurar o SQL Server para aceitar conexões remotas. Para obter mais informações, consulte o documento [Como solucionar problemas de conexão com o Mecanismo de Banco de Dados do SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

* Use as seguintes instruções do Transact-SQL para criar a tabela **mobiledata**:

    ```sql
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
    ```

* Ao conectar-se ao SQL Server no HDInsight, você precisará usar o endereço IP do SQL Server. Por exemplo: 

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Limitações

* Exportação em massa — com o HDInsight baseado em Linux, o conector Sqoop usado para exportar dados para o Microsoft SQL Server ou para o Banco de Dados SQL do Azure, não permite inserções em massa.

* Envio em lote — Com HDInsight baseado em Linux, ao usar o comutador `-batch` ao executar inserções, o Sqoop executará várias inserções em vez de operações de inserção em lotes.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como usar Sqoop. Para obter mais informações, consulte:

* [Usar o Oozie com o HDInsight](../hdinsight-use-oozie.md): use a ação do Sqoop no fluxo de trabalho do Oozie.
* [Analisar dados de atraso de voos usando o HDInsight](../hdinsight-analyze-flight-delay-data.md): use o Hive para analisar dados de atraso de voos e o Sqoop para exportar dados para o banco de dados SQL do Azure.
* [Carregar dados no HDInsight](../hdinsight-upload-data.md): localize outros métodos de carregamento de dados no HDInsight/Armazenamento de Blob do Azure.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
