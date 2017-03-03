---
title: Indexadores na Azure Search | Microsoft Docs
description: "Rastrear um Banco de Dados SQL do Azure, Banco de Dados de Documentos ou Armazenamento do Azure para extrair dados pesquisáveis e preencher um índice de Pesquisa do Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 34a7694c-8fd9-46b1-8900-cefdd7236323
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 4bcd31a200024a182ee3d5a21bcbcb621fed595f
ms.openlocfilehash: fd46641709d260f8b468556972aae14205fdb515
ms.lasthandoff: 01/19/2017

---

# <a name="indexers-in-azure-search"></a>Indexadores na Pesquisa do Azure
> [!div class="op_single_selector"]
>
> * [Visão geral](search-indexer-overview.md)
> * [Portal](search-import-data-portal.md)
> * [SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
> * [Banco de Dados de Documentos](search-howto-index-documentdb.md)
> * [Armazenamento de blobs (preview)](search-howto-indexing-azure-blob-storage.md)
> * [Armazenamento de Tabelas (visualização)](search-howto-indexing-azure-tables.md)
>
>

Um **indexador** na Pesquisa do Azure é um rastreador que extrai dados pesquisáveis e metadados de uma fonte de dados externa e popula um índice com base nos mapeamentos de campo a campo entre o índice e a fonte de dados. Essa abordagem às vezes é chamada de 'modelo de pull' porque o serviço obtém os dados sem que você precise escrever código que envia dados por push a um índice.

Você pode usar um indexador como o único meio para ingestão de dados ou usar uma combinação de técnicas que incluam o uso de um indexador para carregar apenas alguns dos campos no índice.

Você pode executar os indexadores sob demanda ou em uma agenda de atualização de dados recorrente que é executada a cada quinze minutos. Atualizações mais frequentes exigem um modelo de push que atualiza simultaneamente os dados na Pesquisa do Azure e na fonte de dados externa.

## <a name="approaches-for-creating-and-managing-indexers"></a>Abordagens para criar e gerenciar indexadores
Para indexadores disponíveis, como o Azure SQL ou o Banco de Dados de Documentos, você pode criar e gerenciar índices usando estas abordagens:

* [Portal > Assistente de Dados de Importação ](search-get-started-portal.md)
* [API REST do Serviço](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [SDK .NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

## <a name="basic-configuration-steps"></a>Etapas da configuração básica
Os indexadores podem oferecer recursos que são exclusivos da fonte de dados. Nesse sentido, alguns aspectos de configuração da fonte de dados ou do indexador variam de acordo com o tipo de indexador. No entanto, todos os indexadores compartilham a mesma composição básica e os mesmos requisitos. As etapas que são comuns a todos os indexadores são abordadas a seguir.

### <a name="step-1-create-an-index"></a>Etapa 1: criar um índice
Um indexador irá automatizar algumas tarefas relacionadas a ingestão de dados, mas a criação de um índice não é uma delas. Como pré-requisito, você deve ter um índice predefinido com campos iguais aos da sua fonte de dados externa. Para saber mais sobre como estruturar um índice, confira [Criar um índice (API REST da Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx).

### <a name="step-2-create-a-data-source"></a>Etapa 2: criar uma fonte de dados
Um indexador extrai dados de uma **fonte de dados** que contém informações como uma cadeia de conexão. Atualmente, há suporte às seguintes fontes de dados:

* [Banco de Dados SQL do Azure ou SQL Server em uma máquina virtual do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Banco de Dados de Documentos](search-howto-index-documentdb.md)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md), usado para extrair texto de documentos PDF, do Office, HTML ou XML
* [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)

As fontes de dados são configuradas e gerenciadas independentemente dos indexadores que as utilizam, o que significa que uma fonte de dados pode ser usada por vários indexadores para carregar mais de um índice por vez.

### <a name="step-3create-and-schedule-the-indexer"></a>Etapa 3: criar e agendar o indexador
A definição do indexador é uma construção que especifica o índice, a fonte de dados e uma agenda. Um indexador pode referenciar uma fonte de dados de outro serviço, desde que a fonte de dados seja da mesma assinatura. Para saber maissobre como estruturar um indexador, confira [Criar indexador (API REST da Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn946899.aspx).

## <a name="next-steps"></a>Próximas etapas
Agora que você tem as noções básicas, a próxima etapa é examinar os requisitos e as tarefas específicas para cada tipo de fonte de dados.

* [Banco de Dados SQL do Azure ou SQL Server em uma máquina virtual do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Banco de Dados de Documentos](search-howto-index-documentdb.md)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md), usado para extrair texto de documentos PDF, do Office, HTML ou XML
* [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
* [Indexando blobs CSV usando o indexador de Blobs da Pesquisa do AZURE (Visualização)](search-howto-index-csv-blobs.md)
* [Indexando blobs JSON com o indexador de Blobs da Pesquisa do Azure (Visualização)](search-howto-index-json-blobs.md)

