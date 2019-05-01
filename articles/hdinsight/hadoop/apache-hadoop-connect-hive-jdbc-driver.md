---
title: Consultar Apache Hive por meio do driver JDBC - Azure HDInsight
description: Use o driver JDBC de um aplicativo Java para enviar consultas do Apache Hive ao Hadoop no HDInsight. Conecte-se de forma programática e do cliente SQL SQuirrel.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: hrasheed
ms.openlocfilehash: aaf54907ca0cf218b126fc9fc74080c02ef6cf6c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721599"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Consultar o Apache Hive por meio do driver JDBC no HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Saiba como usar o driver JDBC de um aplicativo Java para enviar consultas do Apache Hive ao Apache Hadoop no Azure HDInsight. As informações neste documento demonstra como conectar programaticamente e do cliente SQL SQuirreL.

Para obter mais informações sobre a Interface JDBC do Hive, consulte [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Pré-requisitos

* Cluster Hadoop do HDInsight. Para criar um, confira [Introdução ao Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* O [Java Developer Kit (JDK) versão 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) ou superior.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). O SQuirreL é um aplicativo cliente JDBC.


## <a name="jdbc-connection-string"></a>Cadeia de conexão JDBC

Conexões JDBC para um cluster do HDInsight no Azure são feitas pela porta 443, e o tráfego é protegido usando SSL. O gateway público atrás dos quais ficam os clusters redireciona o tráfego para a porta que o HiveServer2 de fato está escutando. A cadeia de conexão a seguir mostra o formato a ser usado para o HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Substitua `CLUSTERNAME` pelo nome do cluster HDInsight.

## <a name="authentication"></a>Authentication

Ao estabelecer a conexão, você deverá usar o nome e a senha do administrador do cluster HDInsight para se autenticar no gateway do cluster. Durante a conexão de clientes JDBC como o SQuirreL SQL, você deve inserir o nome e a senha do administrador nas configurações do cliente.

De um aplicativo Java, use o nome e a senha ao estabelecer uma conexão. Por exemplo, o código Java a seguir abre uma nova conexão usando a cadeia de conexão, o nome do administrador e a senha:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Conectar-se ao cliente do SQuirreL SQL

O SQuirreL SQL é um cliente JDBC que pode ser usado para executar remotamente as consultas do Hive com o cluster HDInsight. As etapas a seguir pressupõem que você já tenha instalado o SQuirreL SQL.

1. Crie um diretório para conter determinados arquivos a serem copiados do seu cluster.

2. No script a seguir, substitua `sshuser` pelo nome da conta de usuário SSH para o cluster.  Substitua `CLUSTERNAME` pelo nome do cluster do HDInsight.  Em uma linha de comando, digite o seguinte comando para copiar arquivos de um cluster HDInsight:

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/hadoop-common.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/lib/log4j-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/lib/slf4j-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/commons-codec*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/commons-logging-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/hive-*-1.2*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/httpclient-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/httpcore-*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/libfb*.jar .
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/libthrift-*.jar .
    ```

3. Inicie o aplicativo SQuirreL SQL. Na parte esquerda da janela, selecione **Drivers**.

    ![Guia Drivers à esquerda da janela](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. Nos ícones na parte superior do diálogo **Drivers**, selecione o ícone **+** para criar um driver.

    ![Ícones de Drivers](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. No diálogo Adicionar Driver, adicione as informações a seguir:

    * **Nome**: Hive
    * **Exemplo de URL**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Caminho da classe extra**: Use o botão Adicionar para adicionar todos os arquivos jar baixados anteriormente
    * **Nome de Classe**: org.apache.hive.jdbc.HiveDriver

   ![diálogo adicionar driver](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Selecione **Okey** para salvar essas configurações.

6. À esquerda da janela do SQuirreL SQL, selecione **Aliases**. Em seguida, selecione a **+** ícone para criar um alias de conexão.

    ![adicionar novo alias](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. Use os valores a seguir para o diálogo **Adicionar Alias**.

    * **Nome**: Hive no HDInsight

    * **Driver**: Use a lista suspensa para selecionar o **Hive** driver

    * **URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight.

    * **Nome de usuário:** O nome da conta de logon do cluster para o cluster HDInsight. O padrão é `admin`.

    * **Senha**: A senha da conta de logon do cluster.

   ![diálogo adicionar alias](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Use o botão **Testar** para verificar se a conexão funciona. Quando a caixa de diálogo **Conectar-se a: Hive no HDInsight** for exibida, selecione **Conectar** para realizar o teste. Se o teste tiver êxito, você verá um diálogo **Conexão bem-sucedida**. Se ocorrer um erro, consulte [Solução de problemas](#troubleshooting).

    Use o botão **Ok** na parte inferior do diálogo **Adicionar Alias** para salvar o alias de conexão.

8. Na lista suspensa **Conectar a**, na parte superior do SQuirreL SQL, selecione **Hive no HDInsight**. Quando solicitado, selecione **Conectar**.

    ![diálogo conexão](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. Uma vez conectado, digite a seguinte consulta na caixa de diálogo de consulta SQL e, em seguida, selecione a **executar** ícone (uma pessoa em execução). A área de resultados deve mostrar os resultados da consulta.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![diálogo consulta sql, incluindo os resultados](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Conectar-se de um aplicativo Java de exemplo

Um exemplo de uso de um cliente Java para consultar o Hive no HDInsight está disponível em [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Siga as instruções no repositório para compilar e executar o exemplo.

## <a name="troubleshooting"></a>solução de problemas

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Ocorreu um erro inesperado ao tentar abrir uma conexão SQL

**Sintomas**: Ao conectar-se a um cluster HDInsight versão 3.3 ou superior, você poderá receber um erro informando que ocorreu um erro inesperado. O rastreamento de pilha para esse erro começa com as seguintes linhas:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Causa**: Esse erro é causado por um arquivo commons-codec.jar de versão mais antiga incluído no SQuirreL.

**Resolução**: Para corrigir esse erro, use as etapas a seguir:

1. Saia do SQuirreL e vá para o diretório em que o SQuirreL está instalado em seu sistema. No diretório do SquirreL, sob o diretório `lib` , substitua o commons-codec.jar existente pelo baixado por meio do cluster HDInsight.

2. Reinicie o SQuirreL. O erro não deverá ocorrer mais nas próximas conexões ao Hive no HDInsight.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o JDBC para trabalhar com Hive, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Visualizar dados do Apache Hive com o Microsoft Power BI no Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizar dados da consulta interativa do Hive com o Power BI no Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use o Apache Zeppelin para executar consultas do Apache Hive no HDInsight do Azure](./../hdinsight-connect-hive-zeppelin.md).
* [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conecte o Excel ao Apache Hadoop usando o Power Query](apache-hadoop-connect-excel-power-query.md).
* [Conecte-se ao Azure HDInsight e execute consultas do Apache Hive usando o Data Lake Tools para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Use a Ferramenta do Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carregar dados no HDInsight](../hdinsight-upload-data.md)
* [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
