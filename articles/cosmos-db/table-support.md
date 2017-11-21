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
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 1513fc53501f1cfec93134841fbef9a8552dd43c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Desenvolver com a API de Tabela do Azure Cosmos DB e o Armazenamento de Tabelas do Azure

A API de Tabela do Azure Cosmos DB e o Armazenamento de Tabelas do Azure compartilham o mesmo modelo de dados de tabela e expõem as mesmas operações de criar, excluir, atualizar e consultar por meio de seus SDKs. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Desenvolver com a API de Tabela do Azure Cosmos DB

Neste momento, a [API de Tabela Azure Cosmos DB](table-introduction.md) tem um SDK do .NET disponível, a [Tabela Premium do Armazenamento do Microsoft Azure (versão prévia)](https://aka.ms/premiumtablenuget). Esta biblioteca tem as mesmas classes e assinaturas de método do [SDK do Armazenamento do Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage) público, mas também tem a capacidade de se conectar às contas do Azure Cosmos DB usando a API de Tabela (versão prévia). Para um início rápido e um tutorial sobre como usar a API de Tabela do Azure Cosmos DB, consulte os seguintes artigos:
- Início rápido: [Azure Cosmos DB: compilar um aplicativo .NET usando a API de Tabela](create-table-dotnet.md)
- Tutorial: [Azure Cosmos DB: desenvolver com a API de Tabela no .NET](tutorial-develop-table-dotnet.md)

Informações adicionais sobre como trabalhar com a API de Tabela estão disponíveis no artigo [Perguntas frequentes: desenvolver com a API de tabela](faq.md#develop-with-the-table-api-preview).

## <a name="developing-with-the-azure-table-storage"></a>Desenvolvendo com o Armazenamento de Tabelas do Azure

[Armazenamento de Tabelas do Azure](table-storage-overview.md) tem vários SDKs e tutoriais disponíveis, que agora podem ser consultados na seção [Armazenamento de Tabelas do Azure](table-storage-overview.md). Esses artigos estão sendo atualizados conforme a interoperabilidade entre os SDKs de Armazenamento de Tabelas do Azure e APIs de Tabela do Azure Cosmos DB torna-se disponível.  





