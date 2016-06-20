<properties
 pageTitle="Topologias de exemplo do Apache Storm no HDInsight | Microsoft Azure"
 description="Uma lista das topologias de exemplo do Storm criadas e testadas com o Apache Storm no HDInsight, incluindo topologias básicas C# e Java e trabalho com os Hubs de Eventos."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="06/06/2016"
 ms.author="larryfr"/>

# Exemplo de topologias Storm e componentes para o Apache Storm no HDInsight

Segue uma lista de exemplos criados e mantidos pela Microsoft para uso com o Apache Storm no HDInsight. Esses exemplos abrangem uma variedade de tópicos, desde a criação básica em C# e topologias Java até o trabalho com serviços do Azure como os Hubs de Evento, Banco de Dados de Documentos, Power BI, Banco de Dados SQL, HBase em HDInsight e o Armazenamento do Azure. Alguns exemplos também demonstram como trabalhar com tecnologias não Azure ou até mesmo com tecnologias não Microsoft, como o SignalR e Socket.IO

| Descrição | Demonstra | Estrutura/linguagem |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [Gravar no Azure Data Lake Store desde o Apache Storm](hdinsight-storm-write-data-lake-store.md) | Gravando no Azure Data Lake Store | Java |
| [Origem do Bolt e Spout do Hub de Eventos](https://github.com/apache/storm/tree/master/external/storm-eventhubs) | Fonte para o Bolt e Spout do Hub de Eventos | Java |
| [Desenvolver topologias baseadas em Java para o Apache Storm no HDInsight][5797064f] | Maven | Java |
| [Desenvolver topologias C# para o Apache Storm no HDInsight usando o Visual Studio][16fce2d1] | Ferramentas do HDInsight para Visual Studio | C#, Java |
| [Criar vários fluxos de dados em uma topologia Storm em C#][ec5a4064] | Vários fluxos | C# |
| [Determinar tópicos de tendências do Twitter com o Storm no HDInsight][3c86c7c8] | Trident | Java, Trident |
| [Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight][844d1d81] | Hubs de Evento | C# e Java |
| [Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md) | Hubs de Evento | Java |
| [Usar o Power BI para visualizar dados da topologia Storm][94d15238] | Power BI | C# |
| [Analisando dados do sensor com o Storm e o HBase no HDInsight][ab894747] | Hubs de Evento, HBase, Socket.IO, painel da Web | C#, Java, JavaScript, HTML |
| [Processar dados do sensor do veículo a partir de Hubs de Evento usando o Storm no HDInsight][246ee964] | Hubs de Evento, Banco de Dados de Documentos, Blob de Armazenamento do Azure (WASB) | C#, Java |
| [Extração, Transformação e Carregamento (ETL) de Hubs de Evento do Azure para HBase, usando o Storm no HDInsight][b4b68194] | Hubs de Evento, HBase | C# |
| [Projeto de topologia do Storm em modelo C# para trabalhar com os serviços do Azure a partir do Storm no HDInsight][ce0c02a2] | Hubs de Evento, Banco de Dados de Documentos, Banco de Dados SQL, HBase, SignalR | C#, Java |
| [Parâmetros de comparação de escalabilidade para leitura a partir de Hubs de Evento do Azure usando o Storm no HDInsight][d6c540e3] | Taxa de transferência de mensagem, Hubs de Evento, Banco de Dados SQL | C#, Java |
| [Correlacionar eventos usando Storm e HBase no HDInsight](hdinsight-storm-correlation-topology.md) | HBase | C# |
| [Use o Python com o Storm em HDInsight](hdinsight-storm-develop-python-topology.md) | Componentes de Python com Java e Clojure com base em topologias de Storm | Python |

## Próximas etapas

* [Introdução ao Apache Storm no HDInsight][2b8c3488]

* [Saiba como implantar e gerenciar topologias Storm com o Storm no HDInsight][6eb0d3b8]

  [2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Saiba como criar um Storm em cluster do HDInsight e usar o Painel do Storm para implantar topologias de exemplo."
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Aprenda a implantar e gerenciar topologias usando o Painel do Storm baseado na web e a interface do usuário do Storm ou as ferramentas de HDInsight para o Visual Studio."
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Saiba como criar topologias C# do Storm usando as ferramentas do HDInsight para Visual Studio."
  [5797064f]: hdinsight-storm-develop-java-topology.md "Aprenda a criar topologias do Storm em Java, usando Maven, criando uma topologia básica de contagem de palavras."
  [94d15238]: hdinsight-storm-power-bi-topology.md "Demonstra como gravar dados no Power BI por meio de uma topologia C# e depois criar um gráfico e um painel a partir dos dados."
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Demonstra uma topologia básica do Storm que executa uma contagem de palavras, implementada em C#. Isso também demonstra como criar vários fluxos de dados em uma topologia C#."
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Saiba como ler e gravar dados de Hubs de Evento do Azure com o Storm no HDInsight."
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "Saiba como usar o Apache Storm no HDInsight para processar dados de sensor a partir de Hubs de Evento do Azure, visualizá-los usando D3.js e (opcionalmente) armazená-los em HBase."
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "Saiba como usar o Trident para criar uma topologia Storm que determina os tópicos que são tendências (com base em hashtags) no Twitter."
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Saiba como usar uma topologia Storm para ler mensagens de Hubs de Evento do Azure; leia os documentos no Banco de Dados de Documentos do Azure para fazer referência a dados e salvar dados no Armazenamento do Azure."
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Várias topologias para demonstrar a taxa de transferência ao ler a partir dos Hubs de Evento do Azure e armazenar para o Banco de Dados SQL usando o Apache Storm no HDInsight."
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Saiba como ler dados de Hubs de Evento do Azure, agregar e transformar os dados e armazená-los em HBase no HDInsight."
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Este projeto contém modelos para spouts, bolts e topologias para interagir com vários serviços do Azure como os Hubs de Evento, Banco de Dados de Documentos e Banco de Dados SQL."
 

<!---HONumber=AcomDC_0608_2016-->