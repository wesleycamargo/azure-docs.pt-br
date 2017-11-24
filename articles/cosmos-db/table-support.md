---
title: Suporte de Armazenamento de Tabelas do Azure no banco de dados do Azure Cosmos DB | Microsoft Docs
description: Saiba como a API de Tabela do Azure Cosmos DB e Tabelas de Armazenamento do Azure funcionam juntas.
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: c0c8f1aee75c4ee5cc35758b71ef573637fd3edd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Desenvolver com a API de Tabela do Azure Cosmos DB e o Armazenamento de Tabelas do Azure

A API de Tabela do Azure Cosmos DB e o Armazenamento de Tabelas do Azure compartilham o mesmo modelo de dados de tabela e expõem as mesmas operações de criar, excluir, atualizar e consultar por meio de seus SDKs. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Desenvolver com a API de Tabela do Azure Cosmos DB

Nesse momento, a [API de Tabelas do Azure Cosmos DB](table-introduction.md) tem quatro SDKs disponíveis para desenvolvimento: 
- SDK .NET [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget). Esta biblioteca tem as mesmas classes e assinaturas de método do [SDK do Armazenamento do Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage) público, mas também tem a capacidade de se conectar às contas do Azure Cosmos DB usando a API de Tabela. 
- [SDK do Python](table-sdk-python.md). O novo SDK em Python do Azure Cosmos DB é o único SDK que dá suporte ao Armazenamento de Tabelas do Azure em Python. Esse SDK conecta com o armazenamento de Tabelas do Azure e com a API de Tabelas do Azure Cosmos DB.
- [Java SDK](table-sdk-java.md). Esse SDK do Armazenamento do Azure pode conectar a contas do Azure Cosmos DB com a API de Tabelas.
- [SDK do Node.js](table-sdk-nodejs.md). Esse SDK do Armazenamento do Azure pode conectar a contas do Azure Cosmos DB com a API de Tabelas.

Informações adicionais sobre como trabalhar com a API de Tabela estão disponíveis no artigo [Perguntas frequentes: desenvolver com a API de tabela](faq.md#develop-with-the-table-api).

## <a name="developing-with-the-azure-table-storage"></a>Desenvolvendo com o Armazenamento de Tabelas do Azure

[Armazenamento de Tabelas do Azure](table-storage-overview.md) tem vários SDKs e tutoriais disponíveis, que agora podem ser consultados na seção [Armazenamento de Tabelas do Azure](table-storage-overview.md). Esses artigos estão sendo atualizados conforme a interoperabilidade entre os SDKs de Armazenamento de Tabelas do Azure e APIs de Tabela do Azure Cosmos DB torna-se disponível.  





