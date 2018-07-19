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
ms.date: 07/11/2018
ms.author: douglasl
ms.openlocfilehash: ecd5f242d2dcb5662376541ac0a9e75ce533b59f
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005825"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Criar um gatilho que executa um pipeline em resposta a um evento

Este artigo descreve os gatilhos baseados em evento que você pode criar nos pipelines do Data Factory.

A EDA (arquitetura controlada por evento) é um padrão de integração de dados comum que envolve produção, detecção, consumo e reação a eventos. Os cenários de integração de dados geralmente exigem que os clientes do Data Factory disparem pipelines baseados em eventos. O Data Factory agora está integrado ao [Azure Event Grid](https://azure.microsoft.com/services/event-grid/), que permite acionar pipelines em um evento.

> [!NOTE]
> A integração descrita neste artigo depende na [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/). Verifique se a assinatura está registrada no provedor de recursos da Grade de Eventos. Para obter mais informações, confira [Provedores e tipos de recursos](../azure-resource-manager/resource-manager-supported-services.md#portal).

## <a name="data-factory-ui"></a>IU do Data Factory

### <a name="create-a-new-event-trigger"></a>Criar um novo gatilho de evento

Um evento típico é a chegada de um arquivo ou a exclusão de um arquivo na conta de Armazenamento do Microsoft Azure. É possível criar um gatilho que responda a esse evento no pipeline do Data Factory.

> [!NOTE]
> Essa integração suporta apenas as contas de armazenamento da versão 2 (finalidade geral).

![Criar novo gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>Configurar o gatilho de evento

Com o **caminho do Blob começa com**  e o **caminho do Blob termina com propriedades** , é possível especificar os contêineres, pastas e nomes de blob para os quais você deseja receber eventos. É possível usar diversos padrões tanto para o **caminho do Blob que começa com** como para o **caminho do Blob que termina com propriedades**, conforme mostrado nos exemplos mais adiante neste artigo. Pelo menos uma dessas propriedades é necessária.

![Configurar o gatilho de evento](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>Selecione o tipo de gatilho de evento

Assim que o arquivo chega ao local de armazenamento e o blob correspondente é criado, esse evento dispara e executa o pipeline do Data Factory. É possível criar um gatilho que responda a um evento de criação de blob, um evento de exclusão de blob ou ambos os eventos nos pipelines do Data Factory.

![Selecione o tipo de gatilho como evento](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>Mapear propriedades de gatilho para parâmetros de pipeline

Quando um gatilho de evento é acionado para um blob específico, o evento captura o nome de arquivo e o caminho de pasta do blob para as propriedades `@triggerBody().folderPath` e `@triggerBody().fileName`. Para usar os valores dessas propriedades em um pipeline, é necessário mapear as propriedades para parâmetros de pipeline. Depois de mapear as propriedades para parâmetros, acesse os valores capturados pelo gatilho por meio da expressão `@pipeline.parameters.parameterName` em todo o pipeline.

![Mapeando propriedades para parâmetros de pipeline](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Por exemplo, na captura de tela anterior, o gatilho está configurado para ser acionado quando um caminho de blob que termina com `.csv` for criado na Conta de Armazenamento. Como resultado, quando um blob com a extensão `.csv` é criada em qualquer lugar da Conta de Armazenamento, as propriedades `folderPath` e `fileName` capturam o local do novo blob. Por exemplo, `@triggerBody().folderPath` tem um valor como `/containername/foldername/nestedfoldername` e `@triggerBody().fileName` tem um valor como `filename.csv`. Esses valores são mapeados no exemplo para os parâmetros de pipeline `sourceFolder` e `sourceFile`. Você pode usá-los em todo o pipeline como `@pipeline.parameters.sourceFolder` e `@pipeline.parameters.sourceFile`, respectivamente.

## <a name="json-schema"></a>JSON schema

A tabela a seguir fornece uma visão geral dos elementos do esquema relacionados aos gatilhos baseados em eventos:

| **Elemento JSON** | **Descrição** | **Tipo** | **Valores permitidos** | **Obrigatório** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | A ID do recurso do Azure Resource Manager da Conta de Armazenamento. | Cadeia de caracteres | ID do Azure Resource Manager | SIM |
| **events** | O tipo de eventos que causam o acionamento desse gatilho. | Matriz    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sim, qualquer combinação. |
| **blobPathBeginsWith** | O caminho do blob deve começar com o padrão fornecido para o gatilho ser acionado. Por exemplo, '/records/blobs/december/' somente irá disparar o gatilho para blobs na pasta de dezembro no container de registros. | Cadeia de caracteres   | | Pelo menos uma dessas propriedades deve ser fornecida: blobPathBeginsWith, blobPathEndsWith. |
| **blobPathEndsWith** | O caminho do blob deve terminar com o padrão fornecido para o gatilho ser acionado. Por exemplo, 'december/boxes.csv' somente irá disparar o gatilho para blobs com nomes de caixas em uma pasta de dezembro. | Cadeia de caracteres   | | Pelo menos uma dessas propriedades deve ser fornecida: blobPathBeginsWith, blobPathEndsWith. |

## <a name="examples-of-event-based-triggers"></a>Exemplos de gatilhos baseados em eventos

Esta seção fornece exemplos de configurações de gatilho baseado em evento.

-   **O caminho do Blob começa com**('/containername/') – Recebe eventos para qualquer blob no contêiner.
-   **O caminho de blob começa com** ('/containername/blobs/foldername') - Recebe eventos para quaisquer blobs no container containername e na pasta foldername.
-   **O caminho de blob começa com**  ('/containername/blobs/foldername/file.txt') - Recebe eventos para um blob com o nome file.txt na pasta foldername sob o container containername.
-   **O caminho do Blob termina com**('file.txt') – Recebe eventos para um blob nomeado file.txt em qualquer caminho.
-   **O caminho do Blob termina com**  ('/containername/blobs/file.txt') - Recebe eventos para um blob chamado file.txt sob container containername.
-   **O caminho do blob termina com**('foldername/file.txt') – Recebe eventos para um blob nomeado file.txt na pasta foldername em qualquer contêiner.

> [!NOTE]
> Você precisa incluir o segmento `/blobs/`do caminho sempre que especificar container e pasta, container e arquivo ou container, pasta e arquivo.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações detalhadas sobre gatilhos, consulte [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md#triggers).
