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
ms.openlocfilehash: 571a005dd3f50690f291a7ffa3c1174ea15cb0ed
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47045572"
---
# <a name="delete-data-from-azure-data-explorer"></a>Excluir dados do Azure Data Explorer

O Azure Data Explorer dá suporte a várias abordagens de exclusão em massa, que abordamos neste artigo. Ele não dá suporte à exclusão por registro em tempo real, porque é otimizado para acesso rápido para leitura.

* Se o banco de dados não for mais necessário, exclua-o usando o comando “soltar banco de dados”.

    ```Kusto
    .drop database <DatabaseName>
    ```

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

* Você pode excluir registros individuais usando a operação *limpar* com base em um predicado como `where CustomerName == 'contoso'`. Uma limpeza é uma exclusão em massa que não é projetada para exclusão em tempo real. O exemplo a seguir mostra uma limpeza.

    ```Kusto
    .purge table Customer records
    | where CustomerName =='contoso'
    ```

Se você precisar de auxílio com problemas de exclusão de dados, abra uma solicitação de suporte no [portal do Azure](https://portal.azure.com).