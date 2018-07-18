---
title: Integrando o Apache Spark com Hubs de Eventos do Azure | Microsoft Docs
description: Integrar com o Apache Spark para habilitar o Streaming estruturado com Hubs de Eventos
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: shvija
ms.openlocfilehash: 09790852d28f2f68e854b37256ca1c8edba992e6
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311287"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrando o Apache Spark com Hubs de Eventos

O Hubs de Eventos do Azure integra-se perfeitamente com [Apache Spark](https://spark.apache.org/) para habilitar a compilação de aplicativos streaming distribuídos. Essa integração oferece suporte [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html), [Streaming estruturado](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). O conector do Hubs de Eventos para o Apache Spark está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-spark). Esta biblioteca também está disponível para uso em projetos Maven no [Repositório Central do Maven](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Este artigo mostra como criar um aplicativo contínuo no [Azure Databricks](https://azure.microsoft.com/services/databricks/). Embora este artigo use o [Azure Databricks](https://azure.microsoft.com/services/databricks/), clusters do Spark também estão disponíveis com [HDInsight](../hdinsight/spark/apache-spark-overview.md).

O exemplo a seguir usa dois blocos de anotações Scala, um para transmitir eventos um Hub de Eventos e outro para enviar eventos de volta para ele.

## <a name="prerequisites"></a>pré-requisitos

* Uma assinatura do Azure. Se você não tiver um, [crie uma conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Uma instância de Hubs de Eventos. Se você não tiver uma, [crie uma](event-hubs-create.md)
* Uma instância do [Azure Databricks](https://azure.microsoft.com/services/databricks/). Se você não tiver uma, [crie uma](../azure-databricks/quickstart-create-databricks-workspace-portal.md)
* [Crie uma biblioteca usando coordenadas maven](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`

Eventos de fluxo de seu Hub de Eventos usando o seguinte código:

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
O código de exemplo a seguir envia eventos para o Hub de Eventos com APIs de lote Spark. Você também pode escrever uma consulta de fluxo para enviar eventos para o Hub de Eventos.

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Próximas etapas

Agora você sabe configurar um fluxo dimensionável e tolerante a falhas usando o conector de Hubs de Eventos para o Apache Spark. Saiba mais sobre usar os Hubs de Eventos com Fluxo Estruturado e Fluxo Spark seguindo os links a seguir:

* [Streaming estruturado + guia de integração de Hubs de Eventos do Azure](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Spark Streaming + guia de integração de Hubs de Eventos](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
