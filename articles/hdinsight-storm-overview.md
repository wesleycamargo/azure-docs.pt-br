<properties 
	pageTitle="Aprenda sobre o Apache Storm no HDInsight (hadoop)" 
	description="Saiba como você pode usar o Apache Storm no HDInsight (Hadoop)" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# Visão geral do Apache Storm no HDInsight

## O que é o Storm?

[O Apache Storm][apachestorm] é um sistema de computação distribuído e tolerante a falhas, sendo um software livre, que permite processar dados em tempo real. As soluções do Storm também podem oferecer um processamento de dados garantido, com a capacidade de reproduzir dados que não tenham sido processados com sucesso da primeira vez.

## O que é o Storm no HDInsight?

O Storm é oferecido como um cluster gerenciado integrado no ambiente do Azure, onde pode ser usado como parte de uma solução maior do Azure. Por exemplo, o Storm pode consumir dados de serviços como Filas do Barramento de Serviço ou Hub de Evento e usar Websites ou Serviços de Nuvem para oferecer a visualização de dados. Os clusters do Storm também podem ser configurados em uma Rede virtual do Azure, o que reduz a latência ao se comunicar com outros recursos na mesma Rede Virtual, também podendo permitir uma comunicação segura com recursos dentro de um datacenter privado.

Para começar a usar o Storm, consulte [Introdução ao Storm no HDInsight][gettingstarted].

## Como os dados no Storm no HDInsight são processados?

Um cluster Storm processa **topologias** ao invés dos trabalhos do MapReduce com os quais você pode estar familiarizado do HDInsight ou Hadoop. Um cluster Storm contém dois tipos de nós: os nós de cabeça que executam o **Nimbus** e os nós de trabalho que executam o **Supervisor**

* **Nimbus** - Semelhante ao JobTracker no Hadoop, é responsável por distribuir o código no cluster, atribuindo tarefas às máquinas e monitorando falhas. O HDInsight oferece dois nós Nimbus, de forma que não haja um único ponto de falha para um cluster Storm

* **Supervisor** - O supervisor de cada nó de trabalho é responsável por iniciar e interromper **processos de trabalho** no nó

* **Processo de trabalho** -um processo de trabalho executa um subconjunto de uma **topologia**. Uma topologia em execução é distribuída entre vários processos de trabalho no cluster.

* **Topologia** - Define um gráfico de computação que processa **fluxos** de dados. Diferentemente dos trabalhos do MapReduce, as topologias são executadas até que sejam interrompidas

* **Fluxo** - Uma coleção não vinculada de **tuplas**. Fluxos são produzidos por **spouts** e **bolts** e consumidos por **bolts**

* **Tupla** - uma lista nomeada de valores inseridos dinamicamente

* **Spout** - consome dados de uma fonte de dados e emite um ou mais **fluxos**

	> [AZURE.NOTE] Em muitos casos, os dados são lidos de uma fila, como Kafka, Filas de Barramento de Serviço do Azure ou Hubs de Evento. A fila assegura que os dados sejam persistidos em caso de interrupção.

* **Bolt** - consome **fluxos**, realiza o processamento **tuplas** e pode emitir **fluxos**. Bolts também são responsáveis por gravar dados em um armazenamento externo, como uma fila, HBase do HDInsight, um blob ou outros armazenamentos de dados

* **Thrift** - o Apache Thrift é uma estrutura de software para o desenvolvimento de serviço de linguagem cruzada escalável. Ele permite que você compile serviços que funcionam entre C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk e outras linguagens.

	* **O Nimbus** é um serviço do Thrift e uma **topologia** é uma definição do Thrift, portanto, é possível desenvolver topologias usando uma variedade de linguagens de programação 

Para obter mais informações sobre os componentes do Storm, veja o [Tutorial do Storm][apachetutorial] at apache.org.

## Cenários: Quais são os casos de uso do Storm?

Seguem abaixo alguns cenários comuns para os quais você pode usar o Apache Storm. Para obter informações sobre cenários reais, leia [como as empresas estão usando o Storm][poweredby].

### Análise em tempo real

Como o Storm processa fluxos de dados em tempo real, ele é ideal para a análise de dados que envolve a pesquisa e a reação a eventos específicos ou padrões nos fluxos de dados conforme eles são recebidos. Por exemplo, uma topologia do Storm pode monitorar dados do sensor para determinar o funcionamento do sistema e gerar mensagens SMS para alertá-lo quando um padrão específico ocorrer.

### Extract Transform Load (ETL)

ETL pode ser vista quase como um efeito colateral do processamento do Storm. Por exemplo, se você estiver realizando uma detecção de fraudes por meio de uma análise em tempo real, já estará consumindo e transformando dados. Você também pode querer que um bolt armazene os dados no HBase, Hive ou outro armazenamento de dados para uso em uma análise futura.

### RPC distribuída

RPC distribuída é um padrão que pode ser criado usando o Storm. Uma solicitação é transmitida ao Storm, que, então, distribui a computação entre vários nós e, por fim, retorna um fluxo de resultado ao cliente que está aguardando.

Para obter mais informações sobre a RPC Distribuída e o DRPCClient fornecido com o Storm, consulte [RPC Distribuída](https://storm.incubator.apache.org/documentation/Distributed-RPC.html).

### Aprendizado de máquina online

O Storm pode ser usado com uma solução de aprendizado de máquina que tenha sido treinada anteriormente pelo processamento em lote, como uma solução baseada em Mahout. No entanto, seu modelo de computação genérico e distribuído também abre as portas para soluções de aprendizado de máquina baseadas em fluxo. Por exemplo, o [projeto Scalable Advanced Massive Online Analysis (SAMOA)][samoa] é uma biblioteca de aprendizado de máquina que usa o processamento de fluxo e pode funcionar com o Storm.

## Quais linguagens de programação posso usar?

O cluster do Storm no HDInsight oferece suporte pronto para uso ao .NET, Java e Python. Embora o Storm ofereça [suporte a outras linguagens](https://storm.incubator.apache.org/about/multi-language.html), muitas delas exigirão que você instale uma linguagem de programação adicional no cluster do HDInsight, além de outras alterações de configuração. 

### .NET

SCP é um projeto que permite que desenvolvedores de .NET projetem e implementem uma topologia (incluindo spouts e bolts.) O suporte a SCP é fornecido por padrão nos clusters do Storm.

Para obter mais informações sobre o desenvolvimento com SCP, consulte [Desenvolver aplicativos de processamento de dados de streaming com SCP.NET e C# no Storm no HDInsight](/pt-br/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application).

### Java

A maioria dos exemplos com Java encontrados envolvem Java simples ou Trident. Trident é uma abstração de alto nível que torna mais fácil realizar coisas como junções, agregações, agrupamentos e filtragens. No entanto, o Trident age em lotes de tuplas, em que uma solução Java bruta processa um fluxo em uma tupla por vez.

Para obter mais informações sobre o Trident, consulte o [Tutorial do Trident](https://storm.incubator.apache.org/documentation/Trident-tutorial.html) em apache.org.

Para obter exemplos das topologias Java bruta e Trident, consulte o diretório **%storm_home%\contrib\storm-starter** no seu cluster do HDInsight Storm.

## Quais são alguns dos padrões de desenvolvimento comuns?

### Processamento de mensagem garantido

O Storm pode oferecer diferentes níveis de processamento de mensagem garantido. Por exemplo, um aplicativo básico do Storm pode garantir um processamento de pelo menos uma vez, enquanto o Trident pode garantir o processamento de exatamente uma vez.

Para obter mais informações, consulte [Garantias do processamento de dados](https://storm.apache.org/about/guarantees-data-processing.html) em apache.org

### BasicBolt

O padrão de leitura de uma tupla de entrada, emitindo zero ou mais tuplas, e depois confirmando a tupla de entrada imediatamente no final do método de execução, é tão comum que o Storm oferece a interface [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) para automatizar esse padrão.

### Junções

A junção de dois fluxos de dados variará entre aplicativos. Por exemplo, você pode juntar cada tupla de vários fluxos em um novo fluxo, ou pode juntar somente lotes de tuplas para uma janela específica. De qualquer modo, a junção pode ser realizada usando [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), que é uma maneira de definir como as tuplas são roteadas aos bolts.

No exemplo de Java a seguir, fieldsGrouping é usado para rotear tuplas provenientes dos componentes "1", "2" e "3" para o bolt **MyJoiner**.

	builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2")); 

### Envio em lote

O envio em lote pode ser realizado de várias maneiras. Com uma topologia Java básica do Storm, você pode usar um contador simples para enviar em lote um número X de tuplas antes de emiti-las ou usar um mecanismo de cronometragem interno conhecido como Tick Tuple para emitir um lote a cada X segundos.

Para obter um exemplo sobre como usar Tick Tuples, consulte [Analisando dados do sensor com Storm e HDInsight](/pt-br/documentation/articles/hdinsight-storm-sensor-data-analysis.md)

Se estiver usando o Trident, ele se baseia no processamento de lotes de tuplas.

### Caching

O caching na memória geralmente é usado como um mecanismo para acelerar o processamento, uma vez que mantém os ativos usados com mais frequência na memória. Uma vez que uma topologia é distribuída entre vários nós e entre vários processos dentro de cada nó, você deve considerar usar [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para assegurar que as tuplas contendo os campos usados para consulta em cache sejam sempre roteadas para o mesmo processo. Isso evita a duplicação de entradas de cache entre os processos.

### Transmitindo os principais valores N

Quando sua topologia depende do cálculo de um valor N 'top', como os 5 principais tópicos no Twitter, você deve calcular o valor N principal paralelamente e depois mesclar o resultado desses cálculos em um valor global. Isso pode ser feito usando [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para rotear por campo para os bolts paralelos, o que particiona os dados por valor de campo, e depois rotear para um bolt que determina globalmente o valor N principal.

Para obter um exemplo disso, consulte o exemplo [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

## Próximas etapas

* [Introdução ao Storm no HDInsight][gettingstarted]

* [Analisando dados de sensor com Storm e HDInsight](/ pt-br/documentation/articles/hdinsight-storm-sensor-data-analysis)

* [Desenvolver aplicativos de processamento de dados de streaming com SCP.NET e C# em Storm no HDInsight](/pt-br/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application)

[apachestorm]: https://storm.incubator.apache.org
[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[poweredby]: https://storm.incubator.apache.org/documentation/Powered-By.html
[gettingstarted]: /pt-br/documentation/articles/hdinsight-storm-getting-started
<!--HONumber=42-->
