## Receber mensagens com Apache Storm

O [**Apache Storm**](https://storm.incubator.apache.org) é um sistema de computação distribuída em tempo real que simplifica o processamento confiável de fluxos de dados ilimitados. Esta seção mostra como usar um spout de Hubs de Eventos do Storm para receber eventos de Hubs de Eventos. Usando o Apache Storm, você pode dividir eventos em vários processos hospedados em nós diferentes. A integração de Hubs de Eventos com o Storm simplifica o consumo de eventos pela verificação de forma transparente de seu progresso usando a instalação de Zookeeper do Storm, gerenciando pontos de verificação persistentes e recebimentos paralelos de Hubs de Eventos.

Para obter mais informações sobre os padrões de recebimento dos Hubs de Eventos, consulte [Visão geral dos Hubs de Eventos].

Este tutorial usa uma instalação do [HDInsight Storm], que acompanha o spout de Hubs de Eventos já disponível.

1. Siga o procedimento [HDInsight Storm - Get Started](http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-storm-getting-started/) para criar um novo cluster HDInsight e conectá-lo por meio da área de trabalho remota.

2. Copie o arquivo `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` ao seu ambiente de desenvolvimento local. Ele contém o events-storm-spout.

3. Use o seguinte comando para instalar o pacote no armazenamento Maven local. Isso permite que você adicione-o como uma referência no projeto Storm em uma etapa posterior.

		mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

4. No Eclipse, crie um novo projeto Maven (clique em **File**, **Novo** e **Projeto**).

   	![][12]

5. Selecione **Uso do local de espaço de trabalho padrão** e clique em **Avançar**

6. Selecione o arquétipo **maven-archetype-quickstart** e clique em **Avançar**

7. Insira um **GroupId** e **ArtifactId** e clique em **Concluir**

8. Em **pom.xml**, adicione as seguintes dependências no nó `<dependency>`.
		
		<dependency>
			<groupId>org.apache.storm</groupId>
			<artifactId>storm-core</artifactId>
			<version>0.9.2-incubating</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>com.microsoft.eventhubs</groupId>
			<artifactId>eventhubs-storm-spout</artifactId>
			<version>0.9</version>
		</dependency>
		<dependency>
			<groupId>com.netflix.curator</groupId>
			<artifactId>curator-framework</artifactId>
			<version>1.3.3</version>
			<exclusions>
				<exclusion>
					<groupId>log4j</groupId>
					<artifactId>log4j</artifactId>
				</exclusion>
				<exclusion>
					<groupId>org.slf4j</groupId>
					<artifactId>slf4j-log4j12</artifactId>
				</exclusion>
			</exclusions>
			<scope>provided</scope>
		</dependency>

9. Na pasta **src**, crie um arquivo chamado **Config.properties** e copie o conteúdo a seguir, substituindo os seguintes valores:

		eventhubspout.username = ReceiveRule
		
		eventhubspout.password = {receive rule key}
		
		eventhubspout.namespace = ioteventhub-ns
		
		eventhubspout.entitypath = {event hub name}
		
		eventhubspout.partitions.count = 16
		
		# if not provided, will use storm's zookeeper settings
		# zookeeper.connectionstring=localhost:2181
		
		eventhubspout.checkpoint.interval = 10
		
		eventhub.receiver.credits = 10

O valor para **eventhub.receiver.credits** determina quantos eventos são divididos em lotes antes de liberá-los para o pipeline do Storm. Para simplificar, este exemplo define esse valor como 10. Em produção, ele normalmente deve ser definido para valores mais altos. Por exemplo, 1024.

10. Crie uma nova classe chamada **LoggerBolt** com o seguinte código:

		import java.util.Map;
		import org.slf4j.Logger;
		import org.slf4j.LoggerFactory;
		import backtype.storm.task.OutputCollector;
		import backtype.storm.task.TopologyContext;
		import backtype.storm.topology.OutputFieldsDeclarer;
		import backtype.storm.topology.base.BaseRichBolt;
		import backtype.storm.tuple.Tuple;
		
		public class LoggerBolt extends BaseRichBolt {
			private OutputCollector collector;
			private static final Logger logger = LoggerFactory
				      .getLogger(LoggerBolt.class);
		
			@Override
			public void execute(Tuple tuple) {				
				String value = tuple.getString(0);
				logger.info("Tuple value: " + value);
				
				collector.ack(tuple);
			}
		
			@Override
			public void prepare(Map map, TopologyContext context, OutputCollector collector) {
				this.collector = collector;
				this.count = 0;
			}
		
			@Override
			public void declareOutputFields(OutputFieldsDeclarer declarer) {
				// no output fields
			}
		
		}

Este bolt do Storm registra o conteúdo dos eventos recebidos. Isso pode ser estendido facilmente para armazenar tuplas em um serviço de armazenamento. O [tutorial de análise de sensor HDInsight] usa essa mesma abordagem para armazenar dados em HBase.

11. Crie uma classe chamada **LogTopology** com o seguinte código:

		import java.io.FileReader;
		import java.util.Properties;
		import backtype.storm.Config;
		import backtype.storm.LocalCluster;
		import backtype.storm.StormSubmitter;
		import backtype.storm.generated.StormTopology;
		import backtype.storm.topology.TopologyBuilder;
		import com.microsoft.eventhubs.samples.EventCount;
		import com.microsoft.eventhubs.spout.EventHubSpout;
		import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
		
		public class LogTopology {
			protected EventHubSpoutConfig spoutConfig;
			protected int numWorkers;
		
			protected void readEHConfig(String[] args) throws Exception {
				Properties properties = new Properties();
				if (args.length > 1) {
					properties.load(new FileReader(args[1]));
				} else {
					properties.load(EventCount.class.getClassLoader()
							.getResourceAsStream("Config.properties"));
				}
		
				String username = properties.getProperty("eventhubspout.username");
				String password = properties.getProperty("eventhubspout.password");
				String namespaceName = properties
						.getProperty("eventhubspout.namespace");
				String entityPath = properties.getProperty("eventhubspout.entitypath");
				String zkEndpointAddress = properties
						.getProperty("zookeeper.connectionstring"); // opt
				int partitionCount = Integer.parseInt(properties
						.getProperty("eventhubspout.partitions.count"));
				int checkpointIntervalInSeconds = Integer.parseInt(properties
						.getProperty("eventhubspout.checkpoint.interval"));
				int receiverCredits = Integer.parseInt(properties
						.getProperty("eventhub.receiver.credits")); // prefetch count
																	// (opt)
				System.out.println("Eventhub spout config: ");
				System.out.println("  partition count: " + partitionCount);
				System.out.println("  checkpoint interval: "
						+ checkpointIntervalInSeconds);
				System.out.println("  receiver credits: " + receiverCredits);
		
				spoutConfig = new EventHubSpoutConfig(username, password,
						namespaceName, entityPath, partitionCount, zkEndpointAddress,
						checkpointIntervalInSeconds, receiverCredits);
		
				// set the number of workers to be the same as partition number.
				// the idea is to have a spout and a logger bolt co-exist in one
				// worker to avoid shuffling messages across workers in storm cluster.
				numWorkers = spoutConfig.getPartitionCount();
		
				if (args.length > 0) {
					// set topology name so that sample Trident topology can use it as
					// stream name.
					spoutConfig.setTopologyName(args[0]);
				}
			}
		
			protected StormTopology buildTopology() {
				TopologyBuilder topologyBuilder = new TopologyBuilder();
		
				EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
				topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
						spoutConfig.getPartitionCount()).setNumTasks(
						spoutConfig.getPartitionCount());
				topologyBuilder
						.setBolt("LoggerBolt", new LoggerBolt(),
								spoutConfig.getPartitionCount())
						.localOrShuffleGrouping("EventHubsSpout")
						.setNumTasks(spoutConfig.getPartitionCount());
				return topologyBuilder.createTopology();
			}
		
			protected void runScenario(String[] args) throws Exception {
				boolean runLocal = true;
				readEHConfig(args);
				StormTopology topology = buildTopology();
				Config config = new Config();
				config.setDebug(false);
		
				if (runLocal) {
					config.setMaxTaskParallelism(2);
					LocalCluster localCluster = new LocalCluster();
					localCluster.submitTopology("test", config, topology);
					Thread.sleep(5000000);
					localCluster.shutdown();
				} else {
					config.setNumWorkers(numWorkers);
				    StormSubmitter.submitTopology(args[0], config, topology);
				}
			}
		
			public static void main(String[] args) throws Exception {
				LogTopology topology = new LogTopology();
				topology.runScenario(args);
			}
		}


Essa classe cria um novo spout de Hubs de Eventos, usando as propriedades no arquivo de configuração para instanciá-lo. É importante observar que esse exemplo cria quantas tarefas spouts quanto o número de partições no Hub de Eventos, para usar o paralelismo máximo permitido por esse Hub de Eventos.

<!-- Links -->
[Visão geral de Hubs de Evento]: http://msdn.microsoft.com/pt-br/library/azure/dn821413.aspx
[HDInsight Storm]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-storm-overview/
[Tutorial de análise de sensor do HDInsight]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-storm-sensor-data-analysis/

<!-- Images -->

[12]: ./media/service-bus-event-hubs-getstarted/create-storm1.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
