---
title: Criar trabalhos de streaming do Spark com processamento de eventos exatamente uma vez - HDInsight do Azure | Microsoft Docs
description: Como configurar o streaming do Spark para processar um evento uma vez e apenas uma vez.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ramoha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: ramoha
ms.openlocfilehash: ebab9ebc92ae1dff8902d618d0a474ce2b2a0af3
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="create-spark-streaming-jobs-with-exactly-once-event-processing"></a>Criar trabalhos de streaming do Spark com o processamento de eventos exatamente uma vez

Os aplicativos de processamento de fluxo adotam abordagens diferentes sobre como manipulam mensagens de re-processamento após alguma falha no sistema:

* Pelo menos uma vez: cada mensagem é garantida para ser processada, mas pode ser processada mais de uma vez.
* No máximo, uma vez: cada mensagem pode ou não ser processada. Se uma mensagem for processada, ela somente será processada uma vez.
* Exatamente uma vez: cada mensagem é garantida para ser processada uma vez e apenas uma vez.

Este artigo mostra como configurar o streaming do Spark para obter um processamento exatamente uma vez.

## <a name="exactly-once-semantics-with-spark-streaming"></a>Semântica exatamente uma vez com streaming do Spark

Primeiro, considere como todos os pontos de falha do sistema são reiniciados após terem um problema e como você poderá evitar a perda de dados. Um aplicativo de streaming do Spark possui:

* Uma fonte de entrada
* Um ou mais processos receptores que efetuam pull de dados da fonte de entrada
* Tarefas que processam os dados
* Um coletor de saída
* Um processo de driver que gerencia o trabalho de longa duração

As semânticas exatamente uma vez exigem que nenhum dado seja perdido em nenhum ponto e que o processamento de mensagens seja reiniciado, independentemente de onde a falha ocorrer.

### <a name="replayable-sources"></a>Fontes reproduzíveis

A fonte em que seu aplicativo de streaming do Spark estiver lendo os eventos deverá ser *reproduzível*. Isso significa que, nos casos em que a mensagem for recuperada, mas o sistema falhar antes que a mensagem possa ser persistente ou processada, a fonte deverá fornecer a mesma mensagem novamente.

No Azure, tanto os Hubs de Eventos do Azure como o Kafka no HDInsight fornecem fontes reproduzíveis. Outro exemplo de uma fonte reproduzível é um sistema de arquivos tolerante a falhas, como HDFS, Azure Storage Blob ou Azure Data Lake Store, onde todos os dados são mantidos para sempre e, em qualquer ponto, você poderá reler os dados na íntegra.

### <a name="reliable-receivers"></a>Receptores confiáveis

No streaming do Spark, fontes como Hubs de Eventos e Kafka possuem *receptores confiáveis*, onde cada receptor rastreia seu progresso pela leitura da fonte. Um receptor confiável persiste seu estado em armazenamento tolerante a falhas, tanto no ZooKeeper quanto nos pontos de verificação do streaming do Spark gravados em HDFS. Se esse receptor falhar e for reiniciado posteriormente, ele poderá recomeçar de onde parou.

### <a name="use-the-write-ahead-log"></a>Usar log write-ahead

O streaming do Spark fornece suporte para uso de um log write-ahead, onde cada evento recebido é gravado primeiro no diretório do ponto de verificação do Spark em armazenamento tolerante a falhas e, em seguida, armazenado em um RDD (Conjunto de dados distribuídos resilientes). No Azure, o armazenamento tolerante a falhas é o HDFS com suporte do Armazenamento do Microsoft Azure ou Azure Data Lake Store. No seu aplicativo de streaming do Spark, o log write-ahead é habilitado para todos os receptores, configurando a `spark.streaming.receiver.writeAheadLog.enable` definição de configuração para `true`. O log write-ahead fornece tolerância a falhas para falhas do driver e dos executores.

Para os trabalhos executando tarefas em dados de eventos, cada RDD é, por definição, replicado e distribuído em vários trabalhos. Se uma tarefa falhar porque o trabalho em execução travou, a tarefa será reiniciada em outro trabalho que tenha uma réplica dos dados de eventos, de modo que o evento não seja perdido.

### <a name="use-checkpoints-for-drivers"></a>Usar pontos de verificação para drivers

Os drivers de trabalho precisam ser reiniciáveis. Se o driver executando seu aplicativo de streaming do Spark falhar, ele tornará todos os receptores em execução, tarefas e quaisquer RDDs armazenando dados de eventos inoperantes. Nesse caso, será necessário salvar o progresso do trabalho para que você possa retomá-lo posteriormente. Isto é conseguido verificando periodicamente o DAG (Grafo Direcionado Acíclico) do DStream para armazenamento tolerante a falhas. Os metadados do DAG incluem a configuração usada para criar o aplicativo de streaming, as operações que definem o aplicativo e os lotes que estão na fila, mas ainda não foram concluídos. Esses metadados permitem que um driver falhado seja reiniciado a partir das informações do ponto de verificação. Quando o driver reiniciar, ele iniciará novos receptores que recuperam os dados de eventos de volta aos RDDs do log write-ahead.

Os pontos de verificação são habilitados no streaming do Spark em duas etapas. 

1. No objeto StreamingContext, configure o caminho de armazenamento para os pontos de verificação:

    val ssc = new StreamingContext(spark, Seconds(1))  ssc.checkpoint("/path/to/checkpoints")

    No HDInsight, esses pontos de verificação devem ser salvos no armazenamento padrão conectado ao seu cluster, seja o Armazenamento do Microsoft Azure ou o Azure Data Lake Store.

2. Em seguida, especifique um intervalo de ponto de verificação (em segundos) no DStream. Em cada intervalo, os dados de estado derivados do evento de entrada persistem ao armazenamento. Os dados de estado persistentes podem reduzir a computação necessária ao recompilar o estado do evento de origem.

    val lines = ssc.socketTextStream("hostname", 9999)  lines.checkpoint(30)  ssc.start()  ssc.awaitTermination()

### <a name="use-idempotent-sinks"></a>Usar coletores idempotent

O coletor de destino ao qual o trabalho grava os resultados deverá ser capaz de lidar com a situação em que o mesmo resultado é fornecido mais de uma vez. O coletor deverá ser capaz de detectar esses resultados duplicados e ignorá-los. Um coletor *idempotent* pode ser chamado várias vezes com os mesmos dados sem mudança de estado.

É possível criar coletores idempotentes implementando a lógica que primeiro verifica a existência do resultado recebido no armazenamento de dados. Se o resultado já existir, a gravação deverá aparecer para ter êxito na perspectiva do trabalho do Spark, mas, na realidade, o armazenamento de dados ignorou os dados duplicados. Se o resultado não existir, então, o coletor deverá inserir esse novo resultado no seu armazenamento. 

Por exemplo, é possível usar um procedimento armazenado com Banco de Dados SQL do Microsoft Azure que insere eventos em uma tabela. Esse procedimento armazenado primeiro procura o evento por campos-chave e somente quando nenhum evento correspondente é encontrado, o registro será inserido na tabela.

Outro exemplo é usar um sistema de arquivos particionado, como Azure Storage Blob ou Azure Data Lake Store. Nesse caso, a lógica do coletor não precisará verificar a existência de um arquivo. Se o arquivo que representa o evento existir, ele simplesmente será substituído pelos mesmos dados. Caso contrário, um novo arquivo será criado no caminho computado.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do streaming do Spark](apache-spark-streaming-overview.md)
* [Criar trabalhos de streaming do Spark altamente disponíveis em YARN](apache-spark-streaming-high-availability.md)
