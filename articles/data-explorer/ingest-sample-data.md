---
title: Ingerir dados de amostra no Azure Data Explorer
description: Saiba como coletar (carregar) dados de amostra relacionados ao clima no Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 7fdd32f9263b4d1694a0516a98b681ba8744ab6b
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394566"
---
# <a name="ingest-sample-data-into-azure-data-explorer"></a>Ingerir dados de amostra no Azure Data Explorer

Este artigo mostra como ingerir (carregar) dados de amostra em um banco de dados do Azure Data Explorer. Existem [várias maneiras de ingerir dados](ingest-data-overview.md); Este artigo enfoca uma abordagem básica adequada para fins de teste.

> [!NOTE]
> Você já tem esses dados se tiver concluído [Início rápido: ingerir dados usando a biblioteca Python do Data Explorer do Azure](python-ingest-data.md).

## <a name="prerequisites"></a>Pré-requisitos

[Um cluster de teste e um banco de dados](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Ingestão de dados

O conjunto de dados de amostra **StormEvents** contém dados relacionados ao clima dos [Centros Nacionais de Informações Ambientais](https://www.ncdc.noaa.gov/stormevents/).

1. Entre em [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. No canto superior esquerdo do aplicativo, selecione **Adicionar cluster**.

1. Na caixa de diálogo **Adicionar cluster**, insira o URL do cluster no formulário e `https://<ClusterName>.<Region>.kusto.windows.net/`, selecione **Adicionar**.

1. Cole o seguinte comando e selecione **Executar**.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)

    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Depois que o processamento for concluído, cole a consulta a seguir, selecione a consulta na janela e selecione **Executar**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    A consulta retorna os seguintes resultados dos dados de amostra ingeridos.

    ![Resultados da consulta](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Próximas etapas

[Gravar consultas](write-queries.md)

[Ingestão de dados de Data Explorer do Azure](ingest-data-overview.md)