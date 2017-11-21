---
title: Importar dados para usar com a API de Tabela do Azure Cosmos DB | Microsoft Docs
description: Saiba como importar dados para usar com a API de Tabela do Azure Cosmos DB.
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 984510b2dae99849507953163f94ad2f925651cd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>Importar dados para usar com a API de Tabela do Azure Cosmos DB

Este tutorial fornece instruções sobre como importar dados para usar com o a [API de Tabela](table-introduction.md) do Azure Cosmos DB. Se você tiver dados armazenados no armazenamento de tabelas do Azure, é possível usar tanto a Ferramenta de Migração de Dados quanto a cópia az para importar os dados para o Azure Cosmos DB. Depois que os dados são importados, será possível tirar proveito dos recursos premium das ofertas do Azure Cosmos DB, como distribuição global de turnkey, taxa de transferência dedicada, latências de milissegundo de único dígito no 99º percentil, alta disponibilidade garantida e indexação secundária automática.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Importar dados com a ferramenta de migração de dados
> * Importar dados com o AzCopy

## <a name="data-migration-tool"></a>Ferramenta de migração de dados

A ferramenta de migração de dados do Azure Cosmos DB é uma opção para importar os dados de armazenamento da Tabela do Azure existentes. Para importar dados com a ferramenta, selecione o armazenamento da Tabela do Azure como fonte e a API de Tabela do Azure Cosmos DB como o destino e forneça o restante das informações conforme solicitado na ferramenta. Dados podem ser exportados em massa ou usando a importação de registro sequencial. 

Nas seções a seguir, são fornecidas informações sobre como definir o armazenamento de Tabela do Azure como a fonte e a API de Tabela como o destino:
- [Usar o armazenamento de Tabela do Azure como uma fonte de migração de dados](import-data.md#AzureTableSource). 
- [Exportar para API de Tabela com importação em massa](import-data.md#tableapibulkexport)
- [Exportar para API de Tabela com importação de registros sequenciais](import-data.md#tableapiseqtarget).

## <a name="azcopy-command"></a>Comando AzCopy

Outra opção para migrar dados de armazenamento de tabela do Azure para a API de Tabela do Azure Cosmos DB é usar o utilitário de linha de comando AzCopy. Para usar o AzCopy, primeiro exporte seus dados para um arquivo de manifesto conforme descrito em [Exportar dados do armazenamento de tabela](../storage/common/storage-use-azcopy.md#export-data-from-table-storage), depois importe os dados do arquivo de manifesto para [API de Tabela do Azure Cosmos DB](../storage/common/storage-use-azcopy.md#import-data-into-table-storage).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Importar dados com a ferramenta de migração de dados
> * Importar dados com o AzCopy

Agora você pode seguir para o próximo tutorial e saber como consultar dados usando a API de Tabela do Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Como consultar os dados?](../cosmos-db/tutorial-query-table.md)



