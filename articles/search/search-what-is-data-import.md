---
title: Upload de dados no Azure Search | Microsoft Docs
description: "Aprenda a carregar dados em um índice na Pesquisa do Azure."
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: aa8d47c1-4ae6-4209-a8ce-48d5a9474707
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/09/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 72cc0d9ff35ff656a6134b52812b64c39a295a6f
ms.openlocfilehash: 53786d60d9971d9f976bf0f3ef4e40346c3101f4


---
# <a name="upload-data-to-azure-search"></a>Carregar dados para a Pesquisa do Azure
> [!div class="op_single_selector"]
> * [Visão geral](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

## <a name="data-upload-models-in-azure-search"></a>Modelos para carregar dados na Pesquisa do Azure
Há duas maneiras de preencher o índice de Pesquisa do Azure com seus dados. A primeira opção é enviar manualmente os dados para o índice usando a [API REST](search-import-data-rest-api.md) ou o [SDK do .NET](search-import-data-dotnet.md) do Azure Search. A segunda opção é [apontar uma fonte de dados com suporte](search-indexer-overview.md) para o índice de Pesquisa do Azure e deixar que a Pesquisa do Azure extraia automaticamente os dados para o serviço de pesquisa.

Este guia abrange apenas instruções sobre como usar o modelo de push de carregamento de dados (que tem suporte apenas na [API REST](search-import-data-rest-api.md) e no [SDK do .NET](search-import-data-dotnet.md)), mas você ainda pode aprender mais sobre o modelo de pull abaixo.

## <a name="push-data-to-an-index"></a>Enviar por push dados para um índice
Essa abordagem se refere ao envio programático de dados para a Pesquisa do Azure para torná-los disponíveis para a pesquisa. Para os aplicativos com requisitos de latência muito baixos (por exemplo, se você precisar que as operações de pesquisa estejam sincronizadas com os bancos de dados de inventário), um modelo de push será sua única opção.

Você pode usar a [API REST](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) ou o [SDK do .NET](search-import-data-dotnet.md) para enviar dados por push para um índice. Atualmente, não há nenhum suporte de ferramenta para enviar por push dados por meio do portal.

Essa abordagem é mais flexível do que um modelo de pull, pois você pode carregar os documentos individualmente ou em lotes (até 1.000 por lote ou 16 MB, o limite que vier em primeiro lugar). O modelo de push também permite que você carregue documentos para a Pesquisa do Azure, independentemente de onde os dados estão.

## <a name="pull-data-into-an-index"></a>Extrair dados para um índice
O modelo de pull rastreia uma fonte de dados com suporte e carrega automaticamente os dados no índice de Pesquisa do Azure para você. Rastreando as alterações e as exclusões para os documentos existentes, além de reconhecer novos documentos, os indexadores dispensam a necessidade de gerenciar ativamente os dados no índice.

No Azure Search, esse recurso é implementado por meio de *indexadores*, disponíveis no momento para o [armazenamento de Blobs (visualização)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [banco de dados SQL do Azure e SQL Server nas VMs do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).

A funcionalidade do indexador é mostrada no [portal do Azure](search-import-data-portal.md), bem como na [API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations).




<!--HONumber=Dec16_HO2-->


