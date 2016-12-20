---
title: "Processar dados de sensor de veículo com o Apache Storm no HDInsight | Microsoft Docs"
description: "Saiba como processar dados de sensor de veículo nos Hubs de Eventos usando o Apache Storm no HDInsight. Adicionar dados de modelo do Banco de Dados de Documentos e armazenar a saída no armazenamento."
services: hdinsight,documentdb,notification-hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 78980635-8bef-4c33-96c3-fae50e932e31
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/08/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 46bc5b3b70120cd631523fd2b27ad8b9a47e3c6d
ms.openlocfilehash: 952480e71dac19c7772198516863b3e64be1a6b3


---
# <a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>Processar dados de sensor de veículo nos Hubs de Eventos do Azure usando o Apache Storm no HDInsight

Saiba como processar dados dos sensores do veículo a partir dos Hubs de Evento do Azure usando o Apache Storm no HDInsight. Este exemplo lê os dados do sensor de Hubs de Evento do Azure, enriquece dados consultando os dados armazenados no Banco de Dados de Documentos do Azure e, finalmente, armazena os dados no Armazenamento do Microsoft Azure usando o sistema de arquivos Hadoop (HDFS).

![Diagrama da arquitetura do HDInsight e da IoT (Internet das Coisas)](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

## <a name="overview"></a>Visão geral

Adicionar sensores em veículos permite prever problemas de equipamentos com base nas tendências de dados históricos, bem como fazer melhorias em versões futuras com base na análise de padrão de uso. Enquanto o processamento de lote tradicional do MapReduce pode ser usado para esta análise, você deve carregar de forma rápida e eficiente os dados de todos os veículos no Hadoop antes que ocorra o processamento de MapReduce. Além disso, você pode querer fazer uma análise dos caminhos de falha críticos (temperatura do mecanismo, freios, etc.) em tempo real.

Os Hubs de Evento do Azure são criados para lidar com o grande volume de dados gerados por sensores e o Apache Storm no HDInsight pode ser usado para carregar e processar os dados antes de armazená-los no HDFS (apoiado pelo Armazenamento do Microsoft Azure) para processamento adicional de MapReduce.

## <a name="solution"></a>Solução

Os dados de telemetria de temperatura do mecanismo, temperatura ambiente e velocidade do veículo são registrados pelos sensores e depois enviados para Hubs de Evento juntamente com o Número de Identificação do Veículo (VIN) do carro e um carimbo de data/hora. A partir daí, uma Storm Topology em execução em um cluster do Apache Storm no HDInsight lê os dados, processa-os e armazena-os no HDFS.

Durante o processamento, o VIN é usado para recuperar informações sobre o modelo do Banco de Dados de Documentos do Azure. Essas informações são adicionadas ao fluxo de dados antes de ele ser armazenado.

Os componentes usados na Storm Topology são:

* **EventHubSpout** - lê dados de Hubs de Evento do Azure
* **TypeConversionBolt** - converte a cadeia de caracteres JSON de Hubs de Evento para uma tupla que contém os valores de dados individuais de temperatura do mecanismo, temperatura ambiente, velocidade, VIN e carimbo de data/hora
* **DataReferencBolt** - pesquisa o modelo do veículo a partir do Banco de Dados de Documentos usando o VIN
* **WasbStoreBolt** - armazena os dados para o HDFS (Armazenamento do Azure)

Segue um diagrama dessa solução:

![topologia Storm](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [!NOTE]
> Esse é um diagrama simplificado e cada componente da solução pode ter várias instâncias. Por exemplo, as várias instâncias de cada componente na topologia são distribuídas entre os nós no Storm no cluster HDInsight.
> 
> 

## <a name="implementation"></a>Implementação

Há uma solução completa e automatizada para esse cenário disponível como parte do repositório [HDInsight-Storm-Examples](https://github.com/hdinsight/hdinsight-storm-examples) no GitHub. Para usar esse exemplo, siga as etapas no [IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais topologias do Storm, consulte [Exemplo de topologias para Storm em HDInsight](hdinsight-storm-example-topology.md).




<!--HONumber=Nov16_HO3-->


