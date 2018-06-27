---
title: Criar gatilhos baseados em evento no Azure Data Factory | Microsoft Docs
description: Saiba como criar um gatilho no Azure Data Factory que executa um pipeline em resposta a um evento.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: douglasl
ms.openlocfilehash: 457983021034d83e0eed05bd91eae1ac30c046da
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296873"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Criar um gatilho que executa um pipeline em resposta a um evento

Este artigo descreve os gatilhos baseados em evento que você pode criar nos pipelines do Data Factory.

A EDA (arquitetura controlada por evento) é um padrão de integração de dados comum que envolve produção, detecção, consumo e reação a eventos. Os cenários de integração de dados geralmente exigem que os clientes do Data Factory disparem pipelines baseados em eventos.

## <a name="data-factory-ui"></a>IU do Data Factory

### <a name="create-a-new-event-trigger"></a>Criar um novo gatilho de evento

Um evento típico é a chegada de um arquivo ou a exclusão de um arquivo na conta de Armazenamento do Microsoft Azure. É possível criar um gatilho que responda a esse evento no pipeline do Data Factory.

![Criar novo gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="select-the-event-trigger-type"></a>Selecione o tipo de gatilho de evento

Assim que o arquivo chega ao local de armazenamento e o blob correspondente é criado, esse evento dispara e executa o pipeline do Data Factory. É possível criar um gatilho que responda a um evento de criação de blob, um evento de exclusão de blob ou ambos os eventos nos pipelines do Data Factory.

![Selecione o tipo de gatilho como evento](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="configure-the-event-trigger"></a>Configurar o gatilho de evento

Com o **caminho do Blob começa com**  e o **caminho do Blob termina com propriedades** , é possível especificar os contêineres, pastas e nomes de blob para os quais você deseja receber eventos. É possível usar diversos padrões tanto para o **caminho do Blob que começa com** como para o **caminho do Blob que termina com propriedades**, conforme mostrado nos exemplos mais adiante neste artigo. Pelo menos uma dessas propriedades é necessária.

![Configurar o gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image3.png)

## <a name="json-schema"></a>JSON schema

A tabela a seguir fornece uma visão geral dos elementos do esquema relacionados aos gatilhos baseados em eventos:

| **Elemento JSON** | **Descrição** | **Tipo** | **Valores permitidos** | **Obrigatório** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | A ID do recurso do Azure Resource Manager da Conta de Armazenamento. | Cadeia de caracteres | ID do Azure Resource Manager | sim |
| **events** | O tipo de eventos que causam o acionamento desse gatilho. | Matriz    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sim, qualquer combinação. |
| **blobPathBeginsWith** | O caminho do blob deve começar com o padrão fornecido para o gatilho ser acionado. Por exemplo, '/records/blobs/december/' somente irá disparar o gatilho para blobs na pasta de dezembro no container de registros. | Cadeia de caracteres   | | Pelo menos uma dessas propriedades deve ser fornecida: blobPathBeginsWith, blobPathEndsWith. |
| **blobPathEndsWith** | O caminho do blob deve terminar com o padrão fornecido para o gatilho ser acionado. Por exemplo, 'december/boxes.csv' somente irá disparar o gatilho para blobs com nomes de caixas em uma pasta de dezembro. | Cadeia de caracteres   | | Pelo menos uma dessas propriedades deve ser fornecida: blobPathBeginsWith, blobPathEndsWith. |

## <a name="examples-of-event-based-triggers"></a>Exemplos de gatilhos baseados em eventos

Esta seção fornece exemplos de configurações de gatilho baseado em evento.

-   **O caminho do Blob começa com**('/containername/') – Recebe eventos para qualquer blob no contêiner.
-   **O caminho do Blob começa com**('/containername/foldername') – Recebe eventos para quaisquer blobs no contêiner do containername e pasta foldername.
-   **O caminho do Blob começa com**('/containername/foldername/file.txt') – Recebe eventos para um blob nomeado file.txt na pasta foldername no contêiner do containername.
-   **O caminho do Blob termina com**('file.txt') – Recebe eventos para um blob nomeado file.txt em qualquer caminho.
-   **O caminho do blob termina com**('/containername/file.txt') – Recebe eventos para um blob nomeado file.txt no containername do contêiner.
-   **O caminho do blob termina com**('foldername/file.txt') – Recebe eventos para um blob nomeado file.txt na pasta foldername em qualquer contêiner.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações detalhadas sobre gatilhos, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md#triggers).
