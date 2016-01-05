<properties
 pageTitle="Usar JDBC para consultar Hive no Azure HDInsight"
 description="Saiba como usar JDBC para se conectar ao Hive no Azure HDInsight e executar remotamente as consultas nos dados armazenados na nuvem."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="12/04/2015"
 ms.author="larryfr"/>

#Conectar-se ao Hive no Azure HDInsight usando o driver JDBC do Hive

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Neste documento, você aprenderá a usar JDBC por meio de um aplicativo Java para enviar remotamente consultas de Hive para um cluster do HDInsight. Para obter mais informações sobre a Interface JDBC do Hive, consulte [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

##Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop no cluster do HDInsight. Um cluster baseado em Linux ou em Windows funcionará.

* O [Java Developer Kit (JDK) versão 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou superior.

* [Apache Maven](https://maven.apache.org). Maven é um sistema de criação de projeto para projetos Java que é usado pelo projeto associado a este artigo.

##Cadeia de conexão

Conexões JDBC para um cluster HDInsight no Azure são feitas em 443, e o tráfego é protegido usando SSL. O gateway público atrás dos quais ficam os clusters redireciona o tráfego para a porta que o HiveServer2 de fato está escutando. Assim, uma cadeia de conexão típica seria semelhante à seguinte:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

##Autenticação

Ao estabelecer a conexão, você precisa especificar o nome e a senha do administrador do cluster HDInsight. Isso autentica a solicitação para o gateway. Por exemplo, o código Java a seguir abre uma nova conexão usando a cadeia de conexão, o nome do administrador e a senha:

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##Consultas

Quando a conexão for estabelecida, você poderá executar consultas no Hive. Por exemplo, o código Java a seguir executa um __SELECT__ em uma tabela, limitando os resultados a apenas três linhas, e então exibe os resultados:

    sql = "SELECT querytime, market, deviceplatform, devicemodel, state, country from " + tableName + " LIMIT 3";
    stmt2 = conn.createStatement();
    System.out.println("\nRetrieving inserted data:");

    res2 = stmt2.executeQuery(sql);

    while (res2.next()) {
      System.out.println( res2.getString(1) + "\t" + res2.getString(2) + "\t" + res2.getString(3) + "\t" + res2.getString(4) + "\t" + res2.getString(5) + "\t" + res2.getString(6));
    }

##Exemplo de projeto de Java

Um exemplo de como usar um cliente Java para consultar Hive no HDInsight está disponível em [https://github.com/Blackmist/hdinsight-hive-jdbc](https://github.com/Blackmist/hdinsight-hive-jdbc). Siga as instruções no repositório para compilar e executar o exemplo.

##Próximas etapas

Agora que você aprendeu a usar o JDBC para trabalhar com Hive, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Carregar dados no HDInsight](hdinsight-upload-data.md)
* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_1210_2015-->