---
title: Excluir dados do Azure Data Explorer
description: Este artigo descreve os cenários de exclusão em massa no Azure Data Explorer, incluindo limpeza e exclusões com base em retenção.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 485ee48bf197ad996bba8a516b80882f44ba623a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212906"
---
# <a name="delete-data-from-azure-data-explorer"></a>Excluir dados do Azure Data Explorer

O Azure Data Explorer dá suporte a várias abordagens de exclusão em massa, que abordamos neste artigo. Ele não dá suporte à exclusão por registro em tempo real, porque é otimizado para acesso rápido para leitura.

* Se uma ou mais tabelas não forem necessárias, exclua-as usando os comandos “soltar tabela” ou “soltar tabelas”.

    ```Kusto
    .drop table <TableName>

    .drop tables (<TableName1>, <TableName2>,...)
    ```

* Se os dados antigos não forem mais necessários, exclua-os alterando o período de retenção no nível do banco de dados ou tabela.

    Considere um banco de dados ou tabela que é definida para 90 dias de retenção. As necessidades de negócios mudam, então agora somente 60 dias de dados são necessários. Nesse caso, exclua os dados mais antigos de uma das seguintes maneiras.

    ```Kusto
    .alter-merge database <DatabaseName> policy retention softdelete = 60d

    .alter-merge table <TableName> policy retention softdelete = 60d
    ```

    Para obter mais informações, consulte [Política de retenção](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy).

Se você precisar de auxílio com problemas de exclusão de dados, abra uma solicitação de suporte no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
