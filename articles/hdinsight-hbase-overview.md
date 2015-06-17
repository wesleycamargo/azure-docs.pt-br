<properties 
	pageTitle="O que é o HBase no HDInsight? | Microsoft Azure" 
	description="Uma introdução ao Apache HBase no HDInsight, uma compilação de banco de dados NoSQL no Hadoop. Saiba mais sobre essa tecnologia de Big Data, casos de uso e uma comparação com outros clusters do Hadoop." 
	keywords="bigtable,nosql,what is hbase"
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>



# O que é HBase em HDInsight: um banco de dados NoSQL que fornece capacidades semelhantes a BigTable para Hadoop

O Apache HBase é um banco de dados NoSQL de código-fonte aberto, que é compilado no Hadoop e modelado com base em Google BigTable. O HBase fornece acesso aleatório e uma coerência forte para grandes quantidades de dados não estruturados e semiestruturados em um banco de dados sem esquema, organizado por famílias de colunas.

Os dados são armazenados nas linhas de uma tabela e os dados contidos nas linhas são agrupados por famílias de colunas. O HBase é um banco de dados sem esquema, no aspecto de que nem as colunas nem os tipos de dados armazenados nelas precisam ser definidos antes de serem utilizados. O código-fonte aberto é dimensionado linearmente para lidar com petabytes de dados em milhares de nós. Ele pode fazer uso da redundância de dados, do processamento em lote e de outros recursos que são fornecidos por aplicativos distribuídos no ecossistema do Hadoop.

## Como o HBase é implementado no Azure HDInsight?

O HBase do HDInsight é oferecido como um cluster gerenciado integrado ao ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no armazenamento de blobs do Azure, que proporciona baixa latência e maior flexibilidade para escolhas relacionadas a desempenho e custos. Isso permite que os clientes criem sites interativos que trabalham com grandes conjuntos de dados, de modo a criar serviços que armazenam dados de sensor e telemetria de milhões de pontos de extremidade e analisar esses dados por meio de trabalhos do Hadoop. O HBase e o Hadoop são bons pontos iniciais para projetos de big data no Azure e, especificamente, podem possibilitar que aplicativos em tempo real trabalhem com grandes conjuntos de dados.

A implementação do HDInsight utiliza a arquitetura de expansão do HBase para fornecer o compartilhamento automático de tabelas, uma sólida consistência para leituras e gravações e failover automático. O desempenho é aprimorado pelo cache na memória para leituras e streaming de alta produtividade para gravações. O provisionamento de rede virtual também está disponível para o HBase no HDInsight. Para obter detalhes, consulte [Provisionar clusters do HDInsight na Rede Virtual do Azure][hbase-provision-vnet].


## Como os dados no HBase do HDInsight são gerenciados?

Os dados podem ser gerenciados no HBase usando os comandos `create`, `get`, `put` e `scan` do shell do HBase. Os dados são gravados no banco de dados usando `put` e lidos usando `get`. O comando `scan` é utilizado para obter dados de múltiplas linhas em uma tabela. Os dados também podem ser gerenciados utilizando a API C# do HBase, que oferece uma biblioteca de cliente sobre a API REST do HBase. Um banco de dados HBase também pode ser consultado usando o Hive. Para ver uma introdução a esses modelos de programação, consulte [Introdução ao HBase com o Hadoop no HDInsight][hbase-get-started]. Também estão disponíveis coprocessadores, que permitem o processamento de dados em nós que hospedam o banco de dados.
 

## Cenários: casos de uso para o HBase
O caso de uso canônico para o qual o BigTable (e, por extensão, o HBase) foi criado era a pesquisa na Web. Os mecanismos de pesquisa criam índices que mapeiam termos nas páginas da web que os contêm. Mas há muitos outros casos de uso aos quais o HBase se ajusta, muitos dos quais são descritos nesta seção.

### Repositório de valor-chave
O HBase pode ser usado como um repositório de valor-chave e é adequado para gerenciar sistemas de mensagens. O Facebook utiliza o HBase para seu sistema de mensagens, e ele é ideal para armazenar e gerenciar comunicações pela Internet. O WebTable utiliza o HBase para pesquisar e gerenciar tabelas extraídas de páginas da Web.

### Dados de sensor
O HBase é útil para capturar dados que são coletados gradativamente de várias fontes. Isso inclui análise de dados sociais, séries temporais, manter painéis interativos atualizados com tendências e contadores e gerenciar sistemas de logs de auditorias. Os exemplos incluem o terminal de comerciantes Bloomberg e o OpenTSDB (Open Time Series Database), que armazena e oferece acesso a métricas coletadas sobre a integridade dos sistemas de servidores.

### Consulta em tempo real
O [Phoenix](http://phoenix.apache.org/) é um mecanismo de consulta SQL para o HBase no Apache. Ele é acessado como um driver JDBC e permite consultar e gerenciar tabelas do HBase utilizando o SQL.

### HBase como uma plataforma
Os aplicativos podem ser executados sobre o HBase utilizando-o como um armazenamento de dados. Exemplos incluem Phoenix, OpenTSDB, Kiji e Titan. Os aplicativos também podem ser integrados ao HBase. Exemplos incluem Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia e Drill.


##<a name="next-steps"></a>Próximas etapas

- [Introdução ao uso do HBase com Hadoop no HDInsight][hbase-get-started]
- [Provisionar clusters do HDInsight na Rede Virtual do Azure][hbase-provision-vnet]
- [Configurar a replicação do HBase no HDInsight](hdinsight-hbase-geo-replication.md) 
- [Analisar dados de sentimento no Twitter com o HBase no HDInsight][hbase-twitter-sentiment]
- [Utilizar o Maven para compilar aplicativos Java que usam o HBase com o HDInsight (Hadoop)][hbase-build-java-maven]

##<a name="see-also"></a>Consulte também

- [HBase no Apache](https://hbase.apache.org/)
- [Bigtable: um sistema de armazenamento distribuído para dados estruturados](http://research.google.com/archive/bigtable.html)




[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: hdinsight-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/











<!--HONumber=54-->