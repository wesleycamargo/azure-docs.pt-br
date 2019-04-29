---
title: Apache Sqoop com Apache Hadoop - Azure HDInsight
description: Aprenda a usar o Apache Sqoop para importar e exportar entre o Apache Hadoop no HDInsight e um banco de dados SQL do Azure.
keywords: hadoop sqoop,sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 6dcb6853daf34fede590011d165c0ba9001cbac6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128967"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Use o Apache Sqoop para importar e exportar dados entre o Apache Hadoop no HDInsight e o banco de dados SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar o Apache Sqoop para importar e exportar entre um cluster do Apache Hadoop no HDInsight do Azure e no banco de dados SQL do Azure ou do Microsoft SQL Server. As etapas deste documentam usam o comando `sqoop` diretamente do nó principal do cluster Hadoop. Use o SSH para se conectar ao nó principal e executar os comandos neste documento. Este artigo é uma continuação da [usar o Apache Sqoop com Hadoop no HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da [configurar o ambiente de teste](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) partir [usar o Apache Sqoop com Hadoop no HDInsight](./hdinsight-use-sqoop.md).

* Um cliente para consultar o banco de dados SQL do Azure. Considere o uso de [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) ou [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Exportação do Sqoop

Do Hive para o SQL Server.

1. Use o SSH para conectar ao cluster HDInsight. Substitua `CLUSTERNAME` com o nome do seu cluster, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Substitua `MYSQLSERVER` com o nome do seu SQL Server. Para verificar se o Sqoop pode ver seu banco de dados SQL, digite o comando abaixo na sua conexão de SSH aberta. Insira a senha para o logon do SQL Server quando solicitado. Esse comando retorna uma lista de bancos de dados.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Substitua `MYSQLSERVER` com o nome do SQL Server, e `MYDATABASE` com o nome do banco de dados SQL. Para exportar dados do Hive `hivesampletable` de tabela para o `mobiledata` da tabela no banco de dados SQL, insira o comando abaixo na sua conexão de SSH aberta. Digite a senha do logon do SQL Server, quando solicitado

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Para verificar se os dados foram exportados, use as seguintes consultas de seu cliente SQL para exibir os dados exportados:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Importação do Sqoop

Do SQL Server para o armazenamento do Azure.

1. Substitua `MYSQLSERVER` com o nome do SQL Server, e `MYDATABASE` com o nome do banco de dados SQL. Insira o comando abaixo na sua conexão de SSH aberta para importar dados das `mobiledata` de tabela no banco de dados SQL, como o `wasb:///tutorials/usesqoop/importeddata` do HDInsight. Insira a senha para o logon do SQL Server quando solicitado. Os campos nos dados que são separados por um caractere de tabulação, e as linhas serão encerradas por um caractere de nova linha.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. Depois que a importação for concluída, digite o seguinte comando em sua conexão de SSH aberta para listar os dados no novo diretório:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Limitações

* Exportação em massa — com o HDInsight baseado em Linux, o conector Sqoop usado para exportar dados para o Microsoft SQL Server ou para o Banco de Dados SQL do Azure, não permite inserções em massa.

* Envio em lote — Com HDInsight baseado em Linux, ao usar o comutador `-batch` ao executar inserções, o Sqoop executará várias inserções em vez de operações de inserção em lotes.

## <a name="important-considerations"></a>Considerações importantes

* O HDInsight e o SQL Server devem estar na mesma Rede Virtual do Azure.

    Para obter um exemplo, consulte o documento [Conectar o HDInsight à sua rede local](./../connect-on-premises-network.md).

    Para saber mais sobre como usar o HDInsight com Redes Virtuais do Azure, veja o documento [Estender o HDInsight usando a Rede Virtual do Azure](../hdinsight-extend-hadoop-virtual-network.md). Para saber mais sobre Rede Virtual do Azure, veja o documento [Visão Geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md).

* O SQL Server também deve ser configurado para permitir autenticação do SQL. Para obter mais informações, consulte o documento [Escolher um modo de autenticação](https://msdn.microsoft.com/ms144284.aspx).

* Você precisará configurar o SQL Server para aceitar conexões remotas. Para obter mais informações, consulte o documento [Como solucionar problemas de conexão com o Mecanismo de Banco de Dados do SQL Server](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como usar Sqoop. Para obter mais informações, consulte:

* [Usar o Apache Oozie com o HDInsight](../hdinsight-use-oozie-linux-mac.md): Use a ação Sqoop em um fluxo de trabalho do Oozie.
* [Analisar dados de atraso de voo usando HDInsight](../hdinsight-analyze-flight-delay-data-linux.md): Use o Apache Hive para analisar dados de atraso de voo e, em seguida, use o Sqoop para exportar dados para um banco de dados SQL do Azure.
* [Carregar dados para HDInsight](../hdinsight-upload-data.md): Encontre outros métodos para fazer upload de dados para HDInsight/Azure Storage Blob.
