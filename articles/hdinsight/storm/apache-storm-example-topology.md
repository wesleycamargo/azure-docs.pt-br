---
title: Topologias de exemplo do Apache Storm no HDInsight | Microsoft Docs
description: "Uma lista das topologias de exemplo do Storm criadas e testadas com o Apache Storm no HDInsight, incluindo topologias básicas C# e Java e trabalho com os Hubs de Eventos."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f9b1bdff-5928-4705-a76d-52fd200917cb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/01/2017
ms.author: larryfr
ms.openlocfilehash: 1a9093255b7f9281afbcca0ea04654780ebf5b89
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="example-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Exemplo de topologias e componentes Storm para o Apache Storm no HDInsight

Segue uma lista de exemplos criados e mantidos pela Microsoft para uso com o Apache Storm no HDInsight. Esses exemplos abrangem uma variedade de tópicos, desde a criação de topologias básicas em C# e Java até o trabalho com serviços do Azure como Hubs de Eventos, Cosmos DB, Banco de Dados SQL, HBase no HDInsight e Armazenamento do Microsoft Azure. Alguns exemplos também demonstram como trabalhar com tecnologias não Azure ou até mesmo com tecnologias não Microsoft, como o SignalR e Socket.IO.

| Descrição | Demonstra | Estrutura/linguagem |
|:--- |:--- |:--- |
| [Gravar no Azure Data Lake Store desde o Apache Storm](apache-storm-write-data-lake-store.md) |Gravando no Azure Data Lake Store |Java |
| [Origem do Bolt e Spout do Hub de Eventos](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Fonte para o Bolt e Spout do Hub de Eventos |Java |
| [Desenvolver topologias baseadas em Java para o Apache Storm no HDInsight][5797064f] |Maven |Java |
| [Desenvolver topologias do C# para o Apache Storm no HDInsight usando o Visual Studio][16fce2d1] |Ferramentas do HDInsight para Visual Studio |C#, Java |
| [Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight (C#)][844d1d81] |Hubs de Eventos |C# e Java |
| [Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Hubs de Eventos |Java |
| [Analisando dados de sensor com o Storm e o HBase no HDInsight][ab894747] |Hubs de Evento, HBase, Socket.IO, painel da Web |C#, Java, JavaScript, HTML |
| [Processar dados de sensor do veículo nos Hubs de Eventos usando o Storm no HDInsight][246ee964] |Hubs de Eventos, Cosmos DB, WASB (Azure Storage Blob) |C#, Java |
| [ETL (Extração, Transformação e Carregamento) de Hubs de Eventos do Azure para o HBase, usando o Storm no HDInsight][b4b68194] |Hubs de Evento, HBase |C# |
| [Projeto de modelo de topologia Storm do C# para trabalhar com os serviços do Azure por meio do Storm no HDInsight][ce0c02a2] |Hubs de Eventos, Cosmos DB, Banco de Dados SQL, HBase, SignalR |C#, Java |
| [Parâmetros de comparação de escalabilidade para leitura nos Hubs de Eventos do Azure usando o Storm no HDInsight][d6c540e3] |Taxa de transferência de mensagem, Hubs de Evento, Banco de Dados SQL |C#, Java |
| [Use o Python com o Storm em HDInsight](apache-storm-develop-python-topology.md) |Componentes do Python com uma topologia Flux |Python |
| [Usar o Kafka com o Storm no HDInsight](../hdinsight-apache-storm-with-kafka.md) | Leitura e gravação em Apache Storm para o Apache Kafka | Java |

### <a name="next-steps"></a>Próximas etapas

* [Introdução ao Apache Storm no HDInsight][2b8c3488]
* [Saiba como implantar e gerenciar topologias Storm com o Storm no HDInsight][6eb0d3b8]

[2b8c3488]:apache-storm-tutorial-get-started-linux.md "Saiba como criar um cluster Storm no HDInsight e usar o Painel do Storm para implantar topologias de exemplo."
[6eb0d3b8]:apache-storm-deploy-monitor-topology.md "Saiba como implantar e gerenciar topologias usando o Painel do Storm baseado na Web e a interface do usuário do Storm ou as Ferramentas do HDInsight para Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Saiba como criar topologias Storm do C# usando as Ferramentas do HDInsight para Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Saiba como criar topologias Storm em Java, usando o Maven, criando uma topologia básica de contagem de palavras."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Saiba como ler e gravar dados dos Hubs de Eventos do Azure com o Storm no HDInsight."
[ab894747]:apache-storm-sensor-data-analysis.md "Saiba como usar o Apache Storm no HDInsight para processar dados de sensor nos Hubs de Eventos do Azure, visualizá-los usando o D3.js e (opcionalmente) armazená-los no HBase."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Saiba como usar uma topologia Storm para ler mensagens dos Hubs de Eventos do Azure, ler documentos do Azure Cosmos DB para referência a dados e salvar os dados no Armazenamento do Azure."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Várias topologias para demonstrar a produtividade ao ler com base nos Hubs de Eventos do Azure e armazenar no Banco de Dados SQL usando o Apache Storm no HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Saiba como ler dados dos Hubs de Eventos do Azure, agregar e transformar os dados e armazená-los no HBase no HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Este projeto contém modelos de spouts, bolts e topologias para interagir com vários serviços do Azure como Hubs de Eventos, Cosmos DB e Banco de Dados SQL."

