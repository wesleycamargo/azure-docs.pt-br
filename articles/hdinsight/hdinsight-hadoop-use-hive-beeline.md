---
title: Usar o Beeline para trabalhar com o Hive no HDInsight (Hadoop) | Microsoft Docs
description: "Aprenda a usar o SSH para se conectar a um cluster Hadoop no HDInsight e enviar interativamente consultas do Hive usando o Beeline. Beeline é um utilitário para trabalhar com HiveServer2 sobre JDBC."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: b015e3d99c72c1840a31bacee5d9655fa9f48da7
ms.lasthandoff: 03/25/2017


---
# <a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>Usar o Hive com o Hadoop no HDInsight com Beeline
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Neste artigo, você aprenderá a usar o SSH (Secure Shell) para se conectar a um cluster HDInsight baseado em Linux e enviar interativamente consultas Hive usando a ferramenta de linha de comando [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) .

> [!NOTE]
> A Beeline usa JDBC para se conectar ao Hive. Para saber mais sobre como usar o JDBC com o Hive, confira [Conectar ao Hive no Azure HDInsight usando o driver JDBC do Hive](hdinsight-connect-hive-jdbc-driver.md).

## <a id="prereq"></a>Pré-requisitos
Para concluir as etapas neste artigo, você precisará do seguinte:

* Um cluster do Hadoop no HDInsight baseado em Linux.

  > [!IMPORTANT]
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Um cliente SSH. Sistemas operacionais Linux, Unix e Mac devem ser acompanhados de um cliente SSH. Os usuários do Windows devem baixar um cliente, como [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a id="ssh"></a>Conexão com o SSH
Conecte-se com o FQDN (nome de domínio totalmente qualificado) do cluster HDInsight usando o comando SSH. O FQDN será o nome que você atribuiu ao cluster, depois será **.azurehdinsight.net**. Por exemplo, o exibido a seguir se conectaria a um cluster chamado **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se você tiver fornecido uma chave de certificado para autenticação de SSH** ao criar o cluster HDInsight, talvez seja necessário especificar o local da chave privada no sistema cliente:

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se você forneceu uma senha para autenticação SSH** ao criar o cluster HDInsight, você precisará fornecer a senha quando solicitado.

Para saber mais sobre como usar o SSH com HDInsight, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="beeline"></a>Usar o comando Beeline
1. Uma vez conectado, use o seguinte para iniciar a Beeline:
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
   
    Isso vai iniciar o cliente Beeline e conectar à URL JDBC. Aqui, `localhost` é usado, pois HiveServer2 é executado em ambos os nós principais do cluster, e estamos executando Beeline diretamente no nó de cabeçalho primário.
   
    Quando o comando for concluído, você chegará a um prompt `jdbc:hive2://localhost:10001/>`.
2. Os comandos Beeline normalmente começam com um caractere `!`, por exemplo `!help` exibe a ajuda. No entanto, geralmente, o `!` pode ser omitido. Por exemplo, `help` também funcionará.
   
    Se você exibir a ajuda, observará `!sql`, que é usado para executar instruções HiveQL. No entanto, o HiveQL é tão usado que é possível omitir o `!sql`anterior. As duas instruções a seguir têm exatamente os mesmos resultados; a exibição das tabelas atualmente disponíveis por meio do Hive:
   
        !sql show tables;
        show tables;
   
    Em um novo cluster, somente uma tabela deverá ser listada: **hivesampletable**.
3. Use o seguinte para exibir o esquema para a hivesampletable:
   
        describe hivesampletable;
   
    Isso retornará informações semelhantes às seguintes:
   
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
   
    Isso exibe as colunas da tabela. Embora possamos executar algumas consultas nestes dados, vamos criar uma nova tabela para demonstrarmos como carregar os dados no Hive e como aplicar um esquema.
4. Insira as instruções a seguir para criar uma nova tabela chamada **log4jLogs** usando os dados de exemplo fornecidos com o cluster HDInsight:
   
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   
    As instruções executam as seguintes ações:
   
   * **DROP TABLE** - exclui a tabela e o arquivo de dados caso a tabela já exista.
   * **CREATE EXTERNAL TABLE** - cria uma nova tabela “externa" em Hive. Tabelas externas só armazenam a definição da tabela no Hive. Os dados são mantidos no local original.
   * **ROW FORMAT** - informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
   * **STORED AS TEXTFILE LOCATION** - informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto.
   * **SELECT** - Seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[ERROR]**. Isso deve retornar um valor de **3** , já que existem três linhas que contêm esse valor.
   * **INPUT__FILE__NAME LIKE '%.log'** - informa ao Hive que só devemos retornar dados de arquivos que terminam em .log. Normalmente, você teria somente os dados com o mesmo esquema dentro da mesma pasta durante a consulta com o hive, mas este arquivo de log de exemplo é armazenado com outros formatos de dados.
     
     > [!NOTE]
     > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa, como um processo automático de carregamento de dados, ou outra operação MapReduce, mas sempre quer que as consultas Hive utilizem os dados mais recentes.
     > 
     > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.
     > 
     > 
     
     A saída desse comando deve ser semelhante ao seguinte:

     ```
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
     ```
5. Para sair do Beeline, use `!quit`.

## <a id="file"></a>Executar um arquivo HiveQL
O Beeline também pode ser usado para executar um arquivo com instruções HiveQL. Use as etapas a seguir para criar um arquivo e  executá-lo usando o Beeline.

1. Use o comando a seguir para criar um novo arquivo chamado **query.hql**:
   
        nano query.hql
2. Assim que o editor abrir, use o seguinte como o conteúdo do arquivo. Essa consulta criará uma nova tabela 'interna' chamada **errorLogs**:
   
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   
    As instruções executam as seguintes ações:
   
   * **CREATE TABLE IF NOT EXISTS** - cria uma tabela, se ela ainda não existir. Como a palavra-chave **EXTERNAL** não é usada, esta é uma tabela interna, que é armazenada no data warehouse do Hive e totalmente gerenciada pelo Hive.
   * **STORES AS ORC** : armazena os dados no formato ORC (Optimized Row Columnar). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.
   * **INSERT OVERWRITE ... SELECT** - seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]** e insere os dados na tabela **errorLogs**.
     
     > [!NOTE]
     > Ao contrário das tabelas externas, remover uma tabela interna excluirá também os dados subjacentes.
     > 
     > 
3. Para salvar o arquivo, use **Ctrl**+**_X**, insira **Y** e, por fim, **Enter**.
4. Use o seguinte para executar o arquivo usando Beeline. Substitua **HOSTNAME** pelo nome obtido anteriormente para o nó do cabeçalho, e **PASSWORD** pela senha da conta de administrador:
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql
   
   > [!NOTE]
   > O parâmetro `-i` inicia Beeline, executa as instruções no arquivo query.hql e permanece no Beeline no prompt `jdbc:hive2://localhost:10001/>`. Você também pode executar um arquivo usando o parâmetro `-f` , que retorna ao Bash depois que o arquivo foi processado.
   > 
   > 
5. Para verificar se a tabela **errorLogs** foi criada, use a seguinte instrução para retornar todas as linhas de **errorLogs**:
   
        SELECT * from errorLogs;
   
    Três linhas de dados devem ser devolvidas, todas contendo **[ERROR]** na coluna t4:
   
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>Mais sobre a conectividade do Beeline
As etapas deste documento usam `localhost` para se conectar ao HiveServer2 em execução no nó principal do cluster. Embora você também possa usar o nome do host ou o nome de domínio totalmente qualificado do nó principal, são necessárias etapas adicionais para o processo (etapas para localizar o nome de host ou FQDN). Usar `localhost` é suficiente ao usar o Beeline do nó principal.

Caso tenha um nó de borda no seu cluster, com o Beeline instalado, você precisará usar o nome de host ou FQDN do nó principal para se conectar.

Se você tiver o Beeline instalado em um cliente fora do cluster, é possível se conectar usando o comando a seguir. Substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight. Substitua **PASSWORD** pela senha da conta de administrador (logon HTTP).

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

Observe que os parâmetros/URI são diferentes de quando a execução ocorre diretamente em um nó principal ou em um nó de borda no cluster. Isso ocorre porque a conexão com o cluster da internet usa um gateway público que encaminha o tráfego pela porta 443. Além disso, vários outros serviços são expostos por meio do gateway público na porta 443, portanto o URI é diferente de quando a conexão ocorre de forma direta. Ao conectar-se pela internet, que você também deve autenticar a sessão fornecendo a senha.

## <a id="summary"></a><a id="nextsteps"></a>Próximas etapas
Como você pode ver, o comando do Beeline fornece uma maneira fácil de executar consultas Hive interativamente em um cluster HDInsight.

Para informações gerais sobre o Hive no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Se você estiver usando o Tez com o Hive, consulte os seguintes documentos para as informações de depuração:

* [Usar a interface de usuário do Tez no HDInsight baseado em Windows](hdinsight-debug-tez-ui.md)
* [Usar a exibição de Ambari Tez no HDInsight baseado em Linux](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


