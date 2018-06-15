---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: e6e70da1f939547cdb589ae7bcb6ed1f6148f22e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
ms.locfileid: "31429280"
---
Agora você pode usar consultas no Data Explorer para recuperar e filtrar os dados.

1. Veja que por padrão, a consulta está definida como `SELECT * FROM c`. Essa consulta padrão recupera e exibe todos os documentos na coleção. 

    ![A consulta padrão no Data Explorer é `SELECT * FROM c`](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)

2. Permaneça na guia **Documentos** e altere a consulta clicando no botão **Editar Filtro**, adicionando `ORDER BY c._ts DESC` à caixa de predicado de consulta e depois clicando em **Aplicar Filtro**.

    ![Altere a consulta padrão adicionando ORDER BY c._ts DESC e clicando Aplicar Filtro](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

Essa consulta modificada lista os documentos em ordem decrescente com base em seu carimbo de data/hora, portanto, agora o segundo documento é listado primeiro. Se você estiver familiarizado com a sintaxe do SQL, poderá inserir quaisquer [consultas SQL](../articles/cosmos-db/sql-api-sql-query.md) nesta caixa. 

Isso conclui nosso trabalho no Data Explorer. Antes de prosseguirmos para trabalhar com código, observe que você também pode usar o Data Explorer para criar procedimentos armazenados, UDFs e gatilhos para executar a lógica de negócios do servidor, além de dimensionar a taxa de transferência. O Data Explorer expõe todo o acesso a dados interno via programação disponível nas APIs, mas oferece acesso fácil aos dados no Portal do Azure.