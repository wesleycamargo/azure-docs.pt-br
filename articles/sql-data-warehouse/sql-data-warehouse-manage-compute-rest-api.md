---
title: Pausar, retomar, dimensionar com o REST no Azure SQL Data Warehouse | Microsoft Docs
description: Gerenciar potência de computação no SQL Data Warehouse através de APIs REST.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b1763bf08b15a5b40619522ee212eefe3a72ee76
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55455655"
---
# <a name="rest-apis-for-azure-sql-data-warehouse"></a>APIs REST para SQL Data Warehouse do Azure
APIs REST para gerenciar computação no SQL Data Warehouse do Azure.

## <a name="scale-compute"></a>Computação de escala
Para alterar as unidades do data warehouse, use a API REST [Criar ou Atualizar Banco de Dados](/rest/api/sql/databases/createorupdate). O exemplo a seguir configura as unidades do data warehouse para DW1000 para o banco de dados MySQLDW, que está hospedado no servidor MyServer. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```
PATCH https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

## <a name="pause-compute"></a>Pausar computação

Para pausar um banco de dados, use a API REST [Pausar o banco de dados](/rest/api/sql/databases/pause) . O exemplo a seguir pausa um banco de dados chamado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/pause?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="resume-compute"></a>Retomar a computação

Para iniciar um banco de dados, use a API REST [Retomar o banco de dados](/rest/api/sql/databases/resume) . O exemplo a seguir inicia um banco de dados chamado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1. 

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}/resume?api-version=2014-04-01-preview HTTP/1.1
```

## <a name="check-database-state"></a>Verificar estado do banco de dados

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01 HTTP/1.1
```


## <a name="next-steps"></a>Próximas etapas
Para saber mais, veja [Gerenciar computação](sql-data-warehouse-manage-compute-overview.md).

