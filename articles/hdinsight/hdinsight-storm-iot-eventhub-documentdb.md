---
title: "Processar dados de sensor de veículo com o Apache Storm no HDInsight | Microsoft Docs"
description: "Saiba como processar dados de sensor de veículo nos Hubs de Eventos usando o Apache Storm no HDInsight. Adicione dados de modelo do Azure Cosmos DB e armazenar a saída no armazenamento."
services: hdinsight,documentdb,notification-hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 78980635-8bef-4c33-96c3-fae50e932e31
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8e8ebc724e1c70e8fcd56312adef5da2342373ea
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>Processar dados de sensor de veículo nos Hubs de Eventos do Azure usando o Apache Storm no HDInsight

Saiba como processar dados dos sensores do veículo a partir dos Hubs de Evento do Azure usando o Apache Storm no HDInsight. Este exemplo lê os dados de sensor dos Hubs de Eventos do Azure e enriquece os dados referenciando os dados armazenados no Azure Cosmos DB. Os dados são armazenados no Armazenamento do Azure usando o HDFS (Sistema de Arquivos Hadoop).

![Diagrama da arquitetura do HDInsight e da IoT (Internet das Coisas)](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

## <a name="overview"></a>Visão geral

A adição de sensores a veículos permite prever problemas de equipamentos de acordo com as tendências de dados históricos. Ela também permite fazer melhorias em versões futuras, com base na análise de padrão de uso. Você deve conseguir carregar os dados de forma rápida e eficiente de todos os veículos no Hadoop antes que ocorra o processamento do MapReduce. Além disso, você pode querer fazer uma análise dos caminhos de falha críticos (temperatura do mecanismo, freios, etc.) em tempo real.

Os Hubs de Eventos do Azure foram desenvolvidos para manipular o grande volume de dados gerados por sensores. O Apache Storm pode ser usado para carregar e processar os dados antes de armazená-los no HDFS.

## <a name="solution"></a>Solução

Os dados telemétricos de temperatura do mecanismo, temperatura ambiente e velocidade do veículo são registrados pelos sensores. Depois, os dados são enviados para os Hubs de Eventos juntamente com o VIN (Número de Identificação do Veículo) do carro e um carimbo de data/hora. A partir daí, uma Storm Topology em execução em um cluster do Apache Storm no HDInsight lê os dados, processa-os e armazena-os no HDFS.

Durante o processamento, o VIN é usado para recuperar informações sobre o modelo do Cosmos DB. Esses dados são adicionados ao fluxo de dados antes de serem armazenados.

Os componentes usados na Storm Topology são:

* **EventHubSpout** - lê dados de Hubs de Evento do Azure
* **TypeConversionBolt** –converte a cadeia de caracteres JSON dos Hubs de Eventos em uma tupla que contém os seguintes dados de sensor:
    * Temperatura do motor
    * Temperatura ambiente
    * Velocidade
    * VIN
    * Timestamp
* **DataReferencBolt** – pesquisa o modelo do veículo no Cosmos DB usando o VIN
* **WasbStoreBolt** - armazena os dados para o HDFS (Armazenamento do Azure)

A seguinte imagem é um diagrama dessa solução:

![topologia Storm](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

## <a name="implementation"></a>Implementação

Há uma solução completa e automatizada para esse cenário disponível como parte do repositório [HDInsight-Storm-Examples](https://github.com/hdinsight/hdinsight-storm-examples) no GitHub. Para usar esse exemplo, siga as etapas no [IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais topologias do Storm, consulte [Exemplo de topologias para Storm em HDInsight](hdinsight-storm-example-topology.md).


