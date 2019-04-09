---
title: Usar Apache Beeline com Apache Hive - Azure HDInsight
description: Aprenda a usar o cliente Beeline para executar consultas Hive com Hadoop no HDInsight. Beeline é um utilitário para trabalhar com HiveServer2 sobre JDBC.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: 89303e5c827fc24540d345a9a2b9a0743e453a4d
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056837"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Usar o cliente Apache Beeline com Apache Hive

Saiba como usar o [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) para executar consultas do Apache Hive no HDInsight.

O Beeline é um cliente Hive que está incluído em nós principais do cluster HDInsight. O Beeline usa o JDBC para se conectar ao HiveServer2, um serviço hospedado em seu cluster HDInsight. Você também pode usar o Beeline para acessar remotamente o Hive no HDInsight pela internet. Os exemplos a seguir fornecem as cadeias de caracteres de conexão mais comuns usadas para se conectar ao HDInsight do Beeline:

## <a name="types-of-connections"></a>Tipos de conexões

### <a name="from-an-ssh-session"></a>Em uma sessão SSH

Ao conectar-se em uma sessão SSH para um nó de cabeçalho do cluster, você pode, em seguida, conectar-se para o `headnodehost` endereço na porta `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Em uma rede Virtual do Azure

Ao se conectar de um cliente HDInsight em uma rede Virtual do Azure, você deve fornecer o nome de domínio totalmente qualificado (FQDN) de um nó principal do cluster. Desde que essa conexão seja feita diretamente para os nós de cluster, a conexão usa a porta `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Substitua `<headnode-FQDN>` com o nome de domínio totalmente qualificado de um nó de cluster principal. Para localizar o nome de domínio totalmente qualificado de um nó principal, use as informações do documento [Gerenciar HDInsight usando a API de REST do Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>Para o cluster de pacote de segurança de Enterprise do HDInsight (ESP)

Ao conectar-se de um cliente a um cluster de pacote de segurança Enterprise (ESP) Unidas para Azure Active Directory (AAD), você também deve especificar o nome de domínio `<AAD-Domain>` e o nome de uma conta de usuário de domínio com permissões para acessar o cluster `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Substitua `<username>` pelo nome de uma conta no domínio com permissões para acessar o cluster. Substitua `<AAD-DOMAIN>` com o nome do AAD Azure Active Directory () que o cluster é Unido à. Use uma cadeia de caracteres em maiusculas para o `<AAD-DOMAIN>` valor, caso contrário, a credencial não será encontrada. Verificar `/etc/krb5.conf` para os nomes de território, se necessário.

---

### <a name="over-public-internet"></a>Internet pública

Ao conectar-se pela internet pública, você deve fornecer o nome de conta de logon do cluster (padrão `admin`) e a senha. Por exemplo, usando Beeline de um sistema de cliente para conectar-se para o `<clustername>.azurehdinsight.net` endereço. Essa conexão é feita pela porta `443`e é criptografada com SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

Substitua `clustername` pelo nome do cluster HDInsight. Substitua `admin` pela conta de logon do cluster de seu cluster. Substitua `password` pela senha da conta de logon do cluster.

---

### <a id="sparksql"></a>Usar Beeline com Apache Spark

O Apache Spark fornece sua própria implementação de HiveServer2, que, às vezes, é referenciado como o servidor Spark Thrift. Esse serviço usa Spark SQL para resolver consultas em vez de Hive e pode fornecer um desempenho melhor, dependendo da consulta.

#### <a name="over-public-internet-with-apache-spark"></a>Internet pública com o Apache Spark

A cadeia de conexão usada ao conectar-se pela internet é ligeiramente diferente. Em vez de conter `httpPath=/hive2` é `httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>De cluster principal ou dentro do Azure a rede Virtual com o Apache Spark

Ao conectar diretamente do nó principal do cluster ou de um recurso dentro da mesma Rede Virtual do Azure que o cluster HDInsight, a porta `10002` deve ser usada para o servidor do Spark Thrift em vez de `10001`. O exemplo a seguir mostra como conectar-se diretamente ao nó principal:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Pré-requisitos

* Um cluster de Hadoop no HDInsight. Ver [Introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Observe que o [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para armazenamento primário do seu cluster. Por exemplo, `wasb://` para o armazenamento do Azure `abfs://` para o Azure Data Lake armazenamento Gen2, ou `adl://` para Gen1 de armazenamento do Azure Data Lake. Se a transferência segura é habilitada para o armazenamento do Azure ou Data Lake armazenamento Gen2, o URI é `wasbs://` ou `abfss://`, respectivamente. Para obter mais informações, consulte [transferência segura](../../storage/common/storage-require-secure-transfer.md).


* Opção 1: Um cliente SSH. Para saber mais, confira [Conectar-se ao HDInsight (Apache Hadoop) usando SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). A maioria das etapas neste documento pressupõe que você está usando o Beeline em uma sessão SSH para o cluster.

* Opção 2:  Um cliente Beeline local.


## <a id="beeline"></a>Executar um trabalho do Hive

Este exemplo baseia-se sobre como usar o cliente Beeline de uma conexão SSH.

1. Abra uma conexão SSH ao cluster com o código a seguir. Substitua `sshuser` pelo usuário do SSH do cluster e substitua `CLUSTERNAME` pelo nome do cluster. Quando solicitado, insira a senha para a conta de usuário do SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Conecte-se ao HiveServer2 com seu cliente de Beeline da sua sessão SSH aberta digitando o seguinte comando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Os comandos Beeline normalmente começam com um caractere `!`, por exemplo, `!help` exibe a ajuda. No entanto, o `!` pode ser omitido para alguns comandos. Por exemplo, `help` também funciona.

    Há um `!sql`, que é usado para executar instruções HiveQL. No entanto, o HiveQL é tão usado que é possível omitir o `!sql`anterior. As duas instruções a seguir são equivalentes:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    Em um novo cluster, somente uma tabela é listada: **hivesampletable**.

4. Use o comando a seguir para exibir o esquema para a hivesampletable:

    ```hiveql
    describe hivesampletable;
    ```

    Esse comando retorna as informações a seguir:

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Essas informações descrevem as colunas na tabela.

5. Insira as instruções a seguir para criar uma tabela chamada **log4jLogs** usando os dados de exemplo fornecidos com o cluster HDInsight: (Revisar conforme necessário com base em sua [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    Essas instruções executam as seguintes ações:

    * `DROP TABLE` -Se a tabela existir, ele será excluído.

    * `CREATE EXTERNAL TABLE` -Cria um **externo** tabela no Hive. Tabelas externas só armazenam a definição da tabela no Hive. Os dados são mantidos no local original.

    * `ROW FORMAT` – O modo como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

    * `STORED AS TEXTFILE LOCATION` -Onde os dados são armazenados e em qual formato de arquivo.

    * `SELECT` – Seleciona uma contagem de todas as linhas em que coluna **t4** contém o valor **[ERROR]**. Essa consulta deve retornar um valor de **3**, já que existem três linhas que contêm esse valor.

    * `INPUT__FILE__NAME LIKE '%.log'` -Hive tenta aplicar o esquema a todos os arquivos no diretório. Nesse caso, o diretório contém arquivos que não correspondem ao esquema. Para evitar dados incorretos nos resultados, essa instrução informa ao Hive que deve retornar apenas dados de arquivos que terminam em .log.

   > [!NOTE]  
   > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo de upload de dados automatizado ou uma operação MapReduce.
   >
   > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

    A saída desse comando é semelhante ao texto a seguir:

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

6. Para sair do Beeline, use `!exit`.

## <a id="file"></a>Executar um arquivo HiveQL

Esta é uma continuação do exemplo anterior. Use as etapas a seguir para criar um arquivo e  executá-lo usando o Beeline.

1. Use o comando a seguir para criar um novo arquivo chamado **query.hql**:

    ```bash
    nano query.hql
    ```

2. Use o texto a seguir como conteúdo do arquivo. Essa consulta cria uma nova tabela 'interna' chamada **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Essas instruções executam as seguintes ações:

   * **CREATE TABLE IF NOT EXISTS** – criará uma tabela, se ela ainda não existir. Uma vez que a palavra-chave **EXTERNAL** não é usada, essa instrução cria uma tabela interna. As tabelas internas são armazenadas no data warehouse do Hive e totalmente gerenciadas por ele.
   * **STORES AS ORC** : armazena os dados no formato ORC (Optimized Row Columnar). O formato ORC é altamente otimizado e eficiente para o armazenamento de dados do Hive.
   * **INSERT OVERWRITE ... SELECT** - seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]** e insere os dados na tabela **errorLogs**.

    > [!NOTE]  
    > Diferentemente de tabelas externas, o descarte de uma tabela interna excluirá também os dados subjacentes.

3. Para salvar o arquivo, use **Ctrl**+**_X**, insira **Y** e, por fim, **Enter**.

4. Use o seguinte para executar o arquivo usando Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > O parâmetro `-i` inicia o Beeline e executa as instruções no arquivo `query.hql`. Quando a consulta for concluída, você verá um prompt `jdbc:hive2://headnodehost:10001/>`. Você também pode executar um arquivo usando o parâmetro `-f`, que fechará o Beeline após a conclusão da consulta.

5. Para verificar se a tabela **errorLogs** foi criada, use a seguinte instrução para retornar todas as linhas de **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Três linhas de dados devem ser devolvidas, todas contendo **[ERROR]** na coluna t4:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)




## <a id="summary"></a><a id="nextsteps"></a>Próximas etapas

Para obter mais informações gerais sobre como usar o Hive no HDInsight, veja o seguinte documento:

* [Usar o Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para saber mais sobre outras maneiras pelas quais você pode trabalhar com o Hadoop no HDInsight, veja os seguintes documentos:

* [Use o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
