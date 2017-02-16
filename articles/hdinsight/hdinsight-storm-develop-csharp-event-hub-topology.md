---
title: Processar eventos de Hubs de Eventos com o Storm no HDInsight | Microsoft Docs
description: Saiba como processar dados de Hubs de Eventos com uma topologia Storm C# criada no Visual Studio usando as Ferramentas do HDInsight para Visual Studio.
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 67f9d08c-eea0-401b-952b-db765655dad0
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: c9a5091973395dd888939432292fbd06dcbf0680
ms.openlocfilehash: c0349b5890a75c6ffaa6b7eca93baa3101912cf6


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-c"></a>Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight

Os Hubs de Eventos do Azure permitem processar grandes quantidades de dados de sites, aplicativos e dispositivos. O spout dos Hubs de Eventos facilita o uso do Apache Storm no HDInsight para analisar esses dados em tempo real. Você pode também gravar dados no Hub de Eventos usando o bolt dos Hubs de Eventos.

Neste tutorial, você aprenderá como usar os modelos do Visual Studio instalados com ferramentas do HDInsight para o Visual Studio criar duas topologias que funcionam com os Hubs de Eventos do Azure.

* **EventHubWriter**: gera dados aleatoriamente e grava nos Hubs de Eventos
* **EventHubReader**: lê dados dos Hubs de Eventos e registra os dados nos logs do Storm

> [!NOTE] 
> Para uma versão Java desse projeto, veja [Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## <a name="scpnet"></a>SCP.NET

Esses projetos usam SCP.NET, um pacote NuGet que facilita a criação de componentes e topologias C# para uso com o Storm no HDInsight.

> [!IMPORTANT]
> Embora as etapas neste documento dependam de um ambiente de desenvolvimento do Windows com Visual Studio, o projeto compilado pode ser enviado a um cluster Storm no HDInsight que usa Linux. __Somente os clusters baseados em Linux criados depois de 28/10/2016 dão suporte às topologias do SCP.NET.__

### <a name="cluster-versioning"></a>Controle de versão do cluster

O pacote NuGet Microsoft.SCP.Net.SDK usado pelo seu projeto deve corresponder à versão principal do Storm instalada no HDInsight. As versões 3.3 e 3.4 do Storm no HDInsight usam a versão 0.10.x do Storm, de modo que você deve usar a versão 0.10.x.x do SCP.NET com esses clusters. O HDInsight 3.5 usa o Storm 1.0.x, portanto, você deve usar a versão 1.0.x.x do SCP.NET com essa versão de cluster.

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

O HDInsight 3.4 e superior usam o Mono para executar topologias C#. A maioria das tarefas funcionará com o Mono, no entanto, você deve verificar o documento sobre a [compatibilidade do Mono](http://www.mono-project.com/docs/about-mono/compatibility/) em busca de possíveis incompatibilidades.

As topologias C# também devem ser destinadas ao .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Como trabalhar com Hubs de Eventos

A Microsoft fornece um conjunto de componentes Java que pode ser usado para se comunicar com os Hubs de Eventos do Azure de uma topologia Storm. Você pode encontrar o arquivo jar que contém a versão mais recente desses componentes em [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

> [!IMPORTANT]
> Embora os componentes sejam escritos em Java, você pode usá-los facilmente em uma topologia C#.

Basicamente, os componentes com os quais você trabalhará são:

* __EventHubSpout__: lê dados nos Hubs de Eventos.
* __EventHubBolt__: grava dados nos Hubs de Eventos.
* __EventHubSpoutConfig__: usado para configurar EventHubSpout.
* __EventHubBoltConfig__: usado para configurar EventHubBolt.
* __UnicodeEventDataScheme__: usado na configuração do spout para usar codificação UTF-8 na leitura dos Hubs de Eventos. Se esse componente não for usado, o spout será padronizado para codificação String.

### <a name="example-spout-usage"></a>Exemplo de uso do spout

O SCP.NET fornece métodos especificamente para adição de um EventHubSpout à sua topologia. Esses métodos facilitam a adição de um spout no lugar de métodos genéricos para adição de um componente Java. O exemplo a seguir demonstra como criar um novo spout usando os métodos __SetEventHubSpout__ e EventHubSpoutConfig fornecidos pelo SCP.NET:

```csharp
topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        // the shared access signature name and key used to read the data
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        // The namespace that contains the Event Hub to read from
        ConfigurationManager.AppSettings["EventHubNamespace"],
        // The Event Hub name to read from
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        // The number of partitions in the Event Hub
        eventHubPartitions),
    // Parallelism hint for this component. Should be set to the partition count.
    eventHubPartitions);
```

O exemplo anterior cria um novo componente spout chamado __EventHubSpout__ e o configura para se comunicar com um Hub de Eventos. Observe que a dica de paralelismo do componente é definir para o número de partições no Hub de Eventos. Isso permite que o Storm crie uma instância do componente para cada partição.

> [!WARNING]
> A partir de 1º de janeiro de 2017, o uso dos métodos SetEventHubSpout e EventHubSpoutConfig cria um spout que usa a codificação String durante a leitura nos Hubs de Eventos. Se você precisar usar a codificação UTF-8, consulte o exemplo a seguir.

Você também pode usar o método JavaCompoentConstructor genérico durante a criação de um spout. O exemplo a seguir demonstra como criar um novo spout usando o método JavaComponentConstructor. Ele também demonstra como configurar o spout para ler os dados usando a codificação UTF-8 em vez de String:

```csharp
// Create an instance of UnicodeEventDataScheme
var schemeConstructor = new JavaComponentConstructor("com.microsoft.eventhubs.spout.UnicodeEventDataScheme");
// Create an instance of EventHubSpoutConfig
var eventHubSpoutConfig = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpoutConfig",
    new List<Tuple<string, object>>()
    {
        // the shared access signature name and key used to read the data
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"]),
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubSharedAccessKey"]),
        // The namespace that contains the Event Hub to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubNamespace"]),
        // The Event Hub name to read from
        Tuple.Create<string, object>(JavaComponentConstructor.JAVA_LANG_STRING, ConfigurationManager.AppSettings["EventHubEntityPath"]),
        // The number of partitions in the Event Hub
        Tuple.Create<string, object>("int", eventHubPartitions),
        // The encoding scheme to use when reading
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.IEventDataScheme", schemeConstructor)
    }
    );
// Create an instance of the spout
var eventHubSpout = new JavaComponentConstructor(
    "com.microsoft.eventhubs.spout.EventHubSpout",
    new List<Tuple<string, object>>()
    {
        Tuple.Create<string, object>("com.microsoft.eventhubs.spout.EventHubSpoutConfig", eventHubSpoutConfig)
    }
    );
// Set the spout in the topology
topologyBuilder.SetJavaSpout("EventHubSpout", eventHubSpout, eventHubPartitions);
```

> [!IMPORTANT]
> UnicodeEventDataScheme está disponível apenas na versão 9.5 dos componentes do Hub de Eventos, que está disponível em [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

### <a name="example-bolt-usage"></a>Exemplo de uso de bolt

Você deve usar o método JavaComponmentConstructor para criar uma instância do bolt. O exemplo a seguir demonstra como criar e configurar uma nova instância do EventHubBolt:

```csharp
//Create constructor for the Java bolt
JavaComponentConstructor constructor =
    // Use a Clojure expression to create the EventHubBoltCOnfig
    JavaComponentConstructor.CreateFromClojureExpr(
        String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
    // The policy name and key used to read from Event Hubs
    ConfigurationManager.AppSettings["EventHubPolicyName"],
    ConfigurationManager.AppSettings["EventHubPolicyKey"],
    // The namespace that contains the Event Hub
    ConfigurationManager.AppSettings["EventHubNamespace"],
    "servicebus.windows.net", //suffix for the namespace fqdn
    // The Evetn Hub Name)
    ConfigurationManager.AppSettings["EventHubName"],
    "true"));

//Set the bolt
topologyBuilder.SetJavaBolt(
        "EventHubBolt",
        constructor,
        partitionCount). //Parallelism hint uses partition count
    shuffleGrouping("Spout"); //Consume data from spout
```

> [!NOTE]
> Esse exemplo usa uma expressão Clojure passada como uma cadeia de caracteres, em vez de usar o JavaComponentConstructor para criar um EventHubBoltConfig separado como no exemplo de Spout. Qualquer um dos métodos funciona; use aquele mais adequado para você.

## <a name="download-the-completed-project"></a>Baixar o projeto completo

Você pode baixar uma versão concluída do projeto criado neste tutorial do GitHub: [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). No entanto, você ainda precisa fornecer definições de configuração seguindo as etapas neste tutorial.

### <a name="prerequisites"></a>Pré-requisitos

* Um [Apache Storm no cluster HDInsight versão 3.5](hdinsight-apache-storm-tutorial-get-started.md)

    > [!WARNING]
    > O exemplo usado neste documento requer o Storm no HDInsight versão 3.5. Há alterações nas classnames usadas para os componentes principais do Storm entre as versões em clusters mais antigos e a versão do Storm incluída com o HDInsight 3.5. Para obter uma versão deste exemplo que funciona com os clusters mais antigos, consulte [https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub/releases).

* Um [Hub de Eventos do Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

* O [SDK do .NET do Azure](http://azure.microsoft.com/downloads/)

* As [Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

* Java JDK 1.7 ou posterior em seu ambiente de desenvolvimento. Downloads do JDK estão disponíveis em [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

  * A variável de ambiente **JAVA_HOME** deve apontar ao diretório que contém o Java.
  * O diretório **%JAVA_HOME%/bin** deve estar no caminho

## <a name="download-the-event-hub-components"></a>Baixar os componentes do Hub de Eventos

O bolt e o spout são distribuídos como um único arquivo Java (.jar) denominado **eventhubs-storm-spout-#.#-jar-with-dependencies.jar**, em que #.# é a versão do arquivo.

Uma versão do arquivo jar que funciona com o Storm no HDInsight versão 3.5 pode ser baixada em [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/lib/eventhubs/).

Crie um novo diretório chamado `eventhubspout` e salve o arquivo no diretório.

## <a name="configure-event-hubs"></a>Configurar os Hubs de Eventos

Hubs de Eventos é a fonte de dados para este exemplo. Use as informações da seção **Criar um Hub de Eventos** do documento [Introdução aos Hubs de Eventos](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

1. Depois de criar o hub de eventos, exiba a folha EventHub no portal do Azure e selecione **Políticas de acesso compartilhado**. Use a entrada **+ Adicionar** para incluir as seguintes políticas:
   
   | Nome | Permissões |
   | --- | --- |
   | gravador |Enviar |
   | leitor |Escutar |
   
    ![políticas](./media/hdinsight-storm-develop-csharp-event-hub-topology/sas.png)

2. Selecione as políticas **leitor** e **gravador**. Copie e salve o valor **CHAVE PRIMÁRIA** para ambas as políticas, pois elas serão usadas posteriormente.

## <a name="configure-the-eventhubwriter"></a>Configurar o EventHubWriter

1. Se você ainda não instalou a versão mais recente das Ferramentas do HDInsight para Visual Studio, consulte [Introdução ao uso das Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Baixe a solução de [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). Abra a solução e examine o código do projeto **EventHubWriter**.

3. No projeto **EventHubWriter**, abra o arquivo **App.config**. Use as informações do Hub de Eventos que você configurou anteriormente para preencher o valor das seguintes chaves:
   
   | Chave | Valor |
   | --- | --- |
   | EventHubPolicyName |gravador (se você usou um nome diferente para a política com a permissão *Enviar*, use-o.) |
   | EventHubPolicyKey |A chave para a política de gravador |
   | EventHubNamespace |O namespace que contém o seu Hub de Eventos |
   | EventHubName |Nome do Hub de Eventos |
   | EventHubPartitionCount |O número de partições no seu Hub de Eventos |

4. Salve e feche o arquivo **App.config**.

## <a name="configure-the-eventhubreader"></a>Configurar o EventHubReader

1. Abra o projeto **EventHubReader** e examine o código.

2. Abra o **App.config** do **EventHubWriter**. Use as informações do Hub de Eventos que você configurou anteriormente para preencher o valor das seguintes chaves:
   
   | Chave | Valor |
   | --- | --- |
   | EventHubPolicyName |leitor (se você usou um nome diferente para a política com a permissão *Escutar*, use-o.) |
   | EventHubPolicyKey |A chave para a política de leitor |
   | EventHubNamespace |O namespace que contém o seu Hub de Eventos |
   | EventHubName |Nome do Hub de Eventos |
   | EventHubPartitionCount |O número de partições no seu Hub de Eventos |

3. Salve e feche o arquivo **App.config**.

## <a name="deploy-the-topologies"></a>Implantar as topologias

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EventHubReader** e escolha **Enviar para o Storm no HDInsight**.
   
    ![enviar para o storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Na tela **Enviar Topologia**, selecione seu **Cluster Storm**. Expanda **Configurações Adicionais**, selecione **Caminhos de Arquivo Java**, selecione **...** e selecione o diretório que contém o arquivo JAR baixado anteriormente. Por fim, clique em **Enviar**.
   
    ![Imagem da caixa de diálogo de envio](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Depois que a topologia tiver sido enviada, o **Visualizador de Topologias Storm** será exibido. Selecione a topologia **EventHubReader** do lado esquerdo da caixa de diálogo para exibir estatísticas da topologia. Atualmente, nada deveria estar acontecendo, pois nenhum evento foi gravado no Hub de Eventos ainda.
   
    ![exibição de armazenamento de exemplo](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EventHubWriter** e escolha **Enviar para o Storm no HDInsight**.

5. Na tela **Enviar Topologia**, selecione seu **Cluster Storm**. Expanda **Configurações Adicionais**, selecione **Caminhos de Arquivo Java**, selecione **...** e selecione o diretório que contém o arquivo JAR baixado anteriormente. Por fim, clique em **Enviar**.

6. Depois que a topologia tiver sido enviada, atualize a lista de topologia no **Visualizador de Topologias Storm** para verificar se ambas estão em execução no cluster.

7. Em **Visualizador de Topologias Storm**, escolha a topologia **EventHubReader**.

8. No modo de exibição de gráficos, clique duas vezes no componente **LogBolt**. Isso abrirá a página **Resumo do Componente** para o bolt.

9. Na seção **Executores**, selecione um dos links na coluna **Porta**. Isso exibirá informações registradas no log pelo componente. As informações registradas no log são semelhantes a estas:
   
        2016-10-20 13:26:44.186 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {5769732396213255808=520853934697489134}, [{"deviceId":3,"deviceValue":1379915540}]
        2016-10-20 13:26:44.234 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {7154038361491319965=4543766486572976404}, [{"deviceId":3,"deviceValue":459399321}]
        2016-10-20 13:26:44.335 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:6, stream: default, id: {513308780877039680=-7571211415704099042}, [{"deviceId":5,"deviceValue":845561159}]
        2016-10-20 13:26:44.445 m.s.s.b.ScpNetBolt [INFO] Processing tuple: source: com.microsoft.eventhubs.spout.EventHubSpout:7, stream: default, id: {-2409895457033895206=5479027861202203517}, [{"deviceId":8,"deviceValue":2105860655}]

## <a name="stop-the-topologies"></a>Interromper as topologias

Para interromper as topologias, selecione cada topologia no **Visualizador de Topologia Storm** e clique em **Eliminar**.

![imagem de eliminação de uma topologia](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar o Bolt e o Spout dos Hub de Eventos Java de uma topologia C# para trabalhar com dados no Hub de Eventos do Azure. Para saber mais sobre a criação de topologias de C#, consulte o seguinte.

* [Desenvolver topologias C# para o Apache Storm no HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Guia de programação do SCP](hdinsight-storm-scp-programming-guide.md)
* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)




<!--HONumber=Jan17_HO3-->


