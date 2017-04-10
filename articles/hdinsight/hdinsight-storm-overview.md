---
title: "Introdução ao Apache Storm no HDInsight | Microsoft Docs"
description: "Obtenha uma introdução ao Apache Storm no HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/31/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1155f534869f240c1567fa8b791dbcb7750c8a40
ms.lasthandoff: 03/31/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>Introdução ao Apache Storm no HDInsight: análise em tempo real para o Hadoop

O Apache Storm no HDInsight permite que você crie soluções de análise em tempo real distribuídas no Azure.

O Apache Storm é um sistema de computação distribuído e tolerante a falhas, sendo um software livre, que permite processar dados em tempo real com o Hadoop. As soluções do Storm também podem oferecer um processamento de dados garantido, com a capacidade de reproduzir dados que não tenham sido processados com sucesso da primeira vez.

## <a name="how-does-storm-work"></a>Como funciona o Storm

O Apache Storm processa **topologias** em vez dos trabalhos do MapReduce com que você pode estar familiarizado no HDInsight ou no Hadoop. Topologias são compostas por vários componentes organizados em um DAG (gráfico acíclico direcionado). O diagrama a seguir ilustra como os dados fluem entre componentes em uma topologia de contagem de palavras básica

![Exemplo de como os componentes são organizados em uma topologia do Storm](./media/hdinsight-storm-overview/wordcount-topology.png)

* Os componentes __spout__ trazem dados para uma topologia. Eles emitem um ou mais fluxos na topologia

* Os componentes __bolt__ consomem fluxos emitidos de spouts ou de outros bolts. Os bolts podem emitir, como opção, novos fluxos na topologia. Os bolts também são responsáveis por gravar dados no armazenamento persistente, como HDFS ou HBase.

## <a name="why-use-storm-on-hdinsight"></a>Por que usar o Storm no HDInsight

O Apache Storm no HDInsight oferece os seguintes benefícios:

* Executa como um serviço gerenciado com um SLA de 99,9% de tempo de atividade.

* Personalização fácil executando scripts em relação ao cluster durante ou após a criação. Para obter mais informações, confira [Personalizar clusters HDInsight usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

* Use a linguagem de sua escolha: os componentes do Storm podem ser escritos em várias linguagens, como **Java**, **C#** e **Python**.

    * Integração do Visual Studio ao HDInsight para desenvolvimento, gerenciamento e monitoramento de topologias em C#. Para obter mais informações, confira [Desenvolver topologias Storm C# com as Ferramentas do HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

    * Dá suporte à interface Java **Trident**. Essa interface permite que você crie topologias Storm que dão suporte ao processamento de mensagens exatamente uma vez, à persistência de armazenamento de dados transacional e a um conjunto de operações de análise de fluxo comuns.

* Fácil ampliação e redução do cluster: adicione ou remova nós de trabalho sem afetar a execução de topologias Storm.

* Integre com os seguintes serviços do Azure:

    * Hubs de evento

    * Rede Virtual

    * Banco de Dados SQL

    * Armazenamento do Azure

    * do Azure.

    * Combine de forma segura as funcionalidades de vários clusters HDInsight usando a Rede Virtual do Azure: crie pipelines analíticas que usam clusters Hadoop, HBase ou HDInsight.

Para obter uma lista de empresas que estão usando o Apache Storm em suas soluções de análise em tempo real, consulte [Empresas que estão usando o Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Para começar a usar o Storm, consulte [Introdução ao uso do Storm no HDInsight][gettingstarted].

## <a name="ease-of-creation"></a>Fácil de criar

Você pode provisionar um novo Storm no cluster HDInsight em minutos. Para saber mais sobre como criar um cluster Storm, confira [Introdução ao Storm no HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Fácil de uso

* __Proteger a conectividade do Shell__: você pode acessar os nós principais do cluster HDInsight pela Internet usando o SSH. O SSH permite que você execute comandos diretamente no cluster.

  Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Conectividade da Web__: clusters HDInsight fornecem a interface da Web do Ambari. A interface de usuário da Web do Ambari permite que você monitore, configure e gerencie facilmente serviços no cluster. O Storm no HDInsight também fornece a interface do usuário do Storm, que permite que você monitore e gerencie topologias Storm em execução no navegador.

  Para obter mais informações, confira [Administrar o HDInsight usando a interface do usuário do Ambari](hdinsight-hadoop-manage-ambari.md) e [Monitorar e gerenciar usando a interface do usuário do Storm](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell e CLI__: o Azure PowerShell e a CLI do Azure fornecem utilitários de linha de comando que você pode usar no sistema cliente para trabalhar com o HDInsight e outros serviços do Azure.

* __Integração do Visual Studio__: o Data Lake Tools para Visual Studio inclui modelos de projeto para criar topologias Storm em C# usando a estrutura SCP.Net. O Data Lake Tools também fornece ferramentas para implantar, monitorar e gerenciar soluções com o Storm no HDInsight.

  Para obter mais informações, confira [Desenvolver topologias Storm C# com as Ferramentas do HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integração a outros serviços do Azure

* __Azure Data Lake Store__: para obter um exemplo de como usar o Data Lake Store com Storm, confira [Usar o Azure Data Lake Store com Apache Storm no HDInsight](hdinsight-storm-write-data-lake-store.md).

* __Hubs de Evento do Azure__: para obter um exemplo de como usar os Hubs de Eventos do Azure com o Storm, consulte os seguintes documentos:

    * [Desenvolver uma topologia baseada em Java para Storm no HDInsight](hdinsight-storm-develop-java-topology.md)

    * [Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)

* Exemplos para trabalhar com __banco de dados SQL__, __DocumentDB__, __EventHub__ e __HBase__ são incluídos como modelos no Azure Data Lake Tools for Visual Studio. Para saber mais,confira [Desenvolver uma topologia em C# para Storm no HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="reliability"></a>Confiabilidade

O Apache Storm sempre faz com que cada mensagem de entrada seja totalmente processada, mesmo quando a análise de dados é difundida por centenas de nós.

O **nó Nimbus** fornece uma funcionalidade semelhante ao Hadoop JobTracker e atribui tarefas a outros nós no cluster por meio do **Zookeeper**. Os Nós do Zookeeper fazem a coordenação do cluster e facilitam a comunicação entre o Nimbus e o processo **Supervisor** nos nós de trabalho. Quando um nó de processamento falha, o nó Nimbus é informado e atribui a tarefa e os dados associados a outro nó.

A configuração padrão do Apache Storm é ter apenas um nó Nimbus. O Storm no HDInsight executa dois nós Nimbus. Se o nó primário falhar, o cluster HDInsight alternará para o nó secundário enquanto o nó primário é recuperado.

![Diagrama do Nimbus, Zookeeper e Supervisor](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Escala

Embora você possa especificar o número de nós no cluster durante a criação, convém aumentar ou reduzir o cluster de acordo com a carga de trabalho. Todos os clusters HDInsight permitem que você altere o número de nós no cluster, mesmo durante o processamento de dados.

> [!NOTE]
> Para tirar proveito dos novos nós adicionados por meio do dimensionamento, você precisa balancear novamente as topologias iniciadas antes do tamanho do cluster ser aumentado.

## <a name="support"></a>Suporte

O Storm no HDInsight é fornecido com suporte completo de nível empresarial 24 horas por dia, 7 dias por semana. O Storm no HDInsight também tem um SLA de 99,9%. Isso significa que garantimos que o cluster terá conectividade externa em, no mínimo, 99,9% do tempo.

Para saber mais, confira o [Suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="common-use-cases"></a>Casos de uso comuns

Abaixo estão alguns cenários comuns em que você pode usar o Apache Storm no HDInsight. 

* Internet das coisas (IoT)
* Detecção de fraude
* Análise das redes sociais
* ETL (extrair, transformar e carregar)
* Monitoramento de rede
* Pesquisar
* Mobile Engagement

Para saber mais sobre cenários reais, confira o documento [Como as empresas estão usando o Storm](https://storm.apache.org/documentation/Powered-By.html).

## <a name="development"></a>Desenvolvimento

O __Data Lake Tools para Visual Studio__ permite que os desenvolvedores de .NET projetem e implementem uma topologia em __C#__. Você também pode criar topologias híbridas que usam componentes Java e C#.

Para obter mais informações, consulte [Desenvolver topologias C# para o Storm no HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Você também pode desenvolver soluções __Java__ usando o IDE de sua escolha. Para saber mais, confira [Desenvolver topologias em Java para o Storm no HDInsight](hdinsight-storm-develop-java-topology.md).

Também é possível usar Python para desenvolver componentes do Storm. Para saber mais, confira [Desenvolver topologias Storm usando Python no HDInsight](hdinsight-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Padrões de desenvolvimento comuns

### <a name="guaranteed-message-processing"></a>Processamento de mensagem garantido

O Storm pode oferecer diferentes níveis de processamento de mensagem garantido. Por exemplo, um aplicativo básico Storm pode garantir um processamento pelo menos uma vez e o Trident pode garantir o processamento exatamente uma vez.

Para obter mais informações, consulte [Garantias do processamento de dados](https://storm.apache.org/about/guarantees-data-processing.html) em apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

O padrão de leitura de uma tupla de entrada, emitindo zero ou mais tuplas, e depois confirmando a tupla de entrada imediatamente no final do método de execução é comum. O Storm oferece a interface [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) para automatizar esse padrão.

### <a name="joins"></a>Junções

O modo como os fluxos de dados são unidos varia entre aplicativos. Por exemplo, você pode juntar cada tupla de vários fluxos em um novo fluxo ou pode juntar somente lotes de tuplas para uma janela específica. De qualquer forma, a junção pode ser realizada usando [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), que é uma maneira de definir como as tuplas são roteadas aos bolts.

No exemplo de Java a seguir, fieldsGrouping é usado para rotear tuplas provenientes dos componentes "1", "2" e "3" para o bolt **MyJoiner** .

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>Envio em lote

O Storm oferece um mecanismo de cronometragem interno conhecido como "tupla em escala", que pode ser usado para emitir um lote a cada X segundos.

Para obter um exemplo de como usar uma tupla de escala de um componente em C#, confira [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

Se estiver usando o Trident, ele se baseia no processamento de lotes de tuplas.

### <a name="caching"></a>Cache

O cache na memória geralmente é usado como um mecanismo para acelerar o processamento, uma vez que mantém os ativos usados com mais frequência na memória. Como uma topologia é distribuída entre vários nós, e entre vários processos dentro de cada nó, considere o uso de [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29). `fieldsGrouping` garante que as tuplas contendo os campos que são usados para pesquisa em cache sejam sempre roteadas para o mesmo processo. Essa funcionalidade de agrupamento evita a duplicação de entradas de cache entre os processos.

### <a name="streaming-top-n"></a>Transmitindo os principais valores N

Quando sua topologia depende do cálculo de um valor de "N principal", você deve calcular o valor N principal paralelamente. Em seguida, mescle o resultado desses cálculos em um valor global. Essa operação pode ser feita usando [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para rotear por campo para processamento em paralelo e depois rotear para um bolt que determina globalmente o valor N principal.

Para obter um exemplo do cálculo de um valor de "N principal", consulte o exemplo [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) .

## <a name="logging"></a>Registro em log

O Storm usa o Apache Log4j para registrar informações em log. Por padrão, uma grande quantidade de dados é registrada e pode ser difícil classificar as informações. Você pode incluir um arquivo de configuração de log como parte de sua topologia do Storm para controlar o comportamento de log.

Para uma topologia de exemplo que demonstra como configurar o log, veja o exemplo [WordCount baseado em Java](hdinsight-storm-develop-java-topology.md) para Storm no HDInsight.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as soluções de análise em tempo real com o Apache Storm no HDInsight:

* [Introdução ao Storm no HDInsight][gettingstarted]
* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

