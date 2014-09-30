<properties linkid="manage-services-hdinsight-hbase-overview" urlDisplayName="HDInsight HBase overview" pageTitle="An overview of HBase in HDInsight | Azure" metaKeywords="" description="An introduction to HBase in HDInsight, use-cases and a comparison with other database solutions ." metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight HBase overview" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev"></tags>

# Visão geral do HBase do HDInsight

## O que é o HBase?

O HBase é um banco de dados NoSQL de código aberto Apache baseado no Hadoop que fornece acesso aleatório e uma sólida consistência para grandes quantidades de dados não estruturados e semiestruturados. Ele foi criado com base no BigTable do Google e é um banco de dados de coluna agrupado em famílias. Os dados são armazenados nas linhas de uma tabela e os dados contidos nas linhas são agrupados por famílias de colunas. O HBase é um banco de dados sem esquema, no aspecto em que nem as colunas nem os tipos de dados armazenados nelas precisam ser definidos antes da utilização. O código aberto foi lançado pela primeira vez por Mike Cafarella em 2007. Ele é dimensionado de maneira linear para lidar com petabytes de dados em milhares de nós. Ele pode fazer uso da redundância de dados, do processamento em lote e de outros recursos que são fornecidos por aplicativos distribuídos no ecossistema do Hadoop.

## O que é o HBase do HDInsight do Azure?

O HBase do HDInsight é oferecido como um cluster gerenciado integrado ao ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no armazenamento de blobs do Azure, que proporciona baixa latência e maior flexibilidade para escolhas relacionadas a desempenho/custos. Isso permite que os clientes criem sites interativos que trabalham com grandes conjuntos de dados, de modo a criar serviços que armazenam dados de sensor e telemetria de milhões de pontos de extremidade e analisar esses dados por meio de trabalhos do Hadoop. O HBase e o Hadoop são bons pontos iniciais para projetos de big data no Azure e, especificamente, podem possibilitar que aplicativos em tempo real trabalhem com grandes conjuntos de dados.

A implementação do HDInsight utiliza a arquitetura de expansão do HBase para fornecer o compartilhamento automático de tabelas, uma sólida consistência para leituras e gravações e failover automático. O desempenho é aprimorado pelo caching na memória para leituras e gravações de streaming de alta produtividade. O provisionamento de rede virtual também está disponível para o HBase no HDInsight. Para obter detalhes, consulte [Provisionar clusters do HDInsight na Rede Virtual do Azure][].

## Como os dados no HBase do HDInsight são gerenciados?

Os dados podem ser gerenciados no HBase usando os comandos `create` `get`, `put` e `scan` do shell do HBase. Os dados são gravados no banco de dados usando `put` e lidos usando `get`. O comando `scan` é utilizado para obter dados de múltiplas linhas em uma tabela. Os dados também podem ser gerenciados utilizando a API C\# do HBase, que oferece uma biblioteca de cliente sobre a API REST do HBase. Um banco de dados do HBase também pode ser consultado usando o Hive. Para ver uma introdução a esses modelos de programação, consulte [Introdução ao HBase com o Hadoop no HDInsight][]. Coprocessadores também estão disponíveis para permitir o processamento de dados nos nós que hospedam o banco de dados.

## Cenários: Quais são os casos de uso do HBase?

O caso de uso canônico para o qual o BigTable e, por extensão, o HBase foi criado era a pesquisa na web. Os mecanismos de pesquisa criam índices que mapeiam termos nas páginas da web que os contêm. Mas há muitos outros casos de uso aos quais o HBase se adéqua, muitos dos quais são descritos nesta seção.

### Caso de uso núm. 1: Repositório de valor-chave

O HBase pode ser usado como um repositório de valor-chave e é adequado para gerenciar sistemas de mensagens. O Facebook utiliza o HBase para seu sistema de mensagens, e ele é ideal para armazenar e gerenciar comunicações pela internet. O WebTable utiliza o HBase para pesquisar e gerenciar tabelas extraídas de páginas da web.

### Caso de uso núm. 2: Dados de sensor

O Hase é útil para capturar dados que são coletados gradativamente de várias fontes. Isso inclui análise de dados sociais, séries temporais, manter painéis interativos atualizados com tendências e contadores e gerenciar sistemas de logs de auditorias. Os exemplos incluem o terminal de comerciantes Bloomberg
e o Open Time Series Database (OpenTSDB), que armazena e oferece acesso a métricas coletadas sobre a integridade dos sistemas de servidores.

### Caso de uso núm. 3: Consulta em tempo real

O [Phoenix][] é um mecanismo de consulta SQL para o HBase no Apache. Ele é acessado como um driver JDBC e permite consultar e gerenciar tabelas do HBase utilizando o SQL.

### Caso de uso núm. 4: HBase como uma plataforma

Os aplicativos podem ser executados sobre o HBase utilizando-o como um armazenamento de dados. Exemplos incluem Phoenix, OpenTSDB, Kiji e Titan. Os aplicativos também podem ser integrados ao HBase. Exemplos incluem Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia e Drill.

## <a name="next-steps"></a>Próximas etapas

[Introdução ao uso do HBase com o Hadoop no HDInsight][Introdução ao HBase com o Hadoop no HDInsight]

[Provisionar clusters do HDInsight na Rede Virtual do Azure][]

[Analisar dados de sentimento no Twitter com o HBase no HDInsight][]

[Utilizar o Maven para criar aplicativos Java que usam o HBase com o HDInsight (Hadoop)][]

[SDK do HBase com C\#][]

## <a name="see-also"></a>Confira também

[HBase no Apache][]

[Bigtable: Um Sistema de Armazenamento Distribuído para Dados Estruturados][]

  [Provisionar clusters do HDInsight na Rede Virtual do Azure]: ../hdinsight-hbase-provision-vnet/
  [Introdução ao HBase com o Hadoop no HDInsight]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/
  [Phoenix]: http://phoenix.apache.org/
  [Analisar dados de sentimento no Twitter com o HBase no HDInsight]: ../hdinsight-hbase-analyze-twitter-sentiment/
  [Utilizar o Maven para criar aplicativos Java que usam o HBase com o HDInsight (Hadoop)]: ../hdinsight-hbase-build-java-maven/
  [SDK do HBase com C\#]: https://github.com/hdinsight/hbase-sdk-for-net
  [HBase no Apache]: https://hbase.apache.org/
  [Bigtable: Um Sistema de Armazenamento Distribuído para Dados Estruturados]: http://research.google.com/archive/bigtable.html
