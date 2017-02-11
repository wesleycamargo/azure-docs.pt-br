---
title: "Limitações do Stretch Database | Microsoft Docs"
description: "Saiba mais sobre as limitações do Stretch Database."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 0a865872-7d42-4873-99b9-cbae45691e54
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: anvang
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3c1599dfc6aee8adf7ec8714c392a7d8d4427d78


---
# <a name="limitations-for-stretch-database"></a>Limitações do Stretch Database
Saiba mais sobre as limitações para tabelas habilitadas para o Stretch e sobre as limitações que no momento impedem você de habilitar o Stretch para uma tabela.

## <a name="a-namecaveatsa-limitations-for-stretch-enabled-tables"></a><a name="Caveats"></a> Limitações para tabelas habilitadas para o Stretch
As tabelas habilitadas para o Stretch têm as limitações a seguir.

### <a name="constraints"></a>Restrições
* A exclusividade não é imposta para as restrições UNIQUE e as restrições PRIMARY KEY na tabela do Azure que contém os dados migrados.

### <a name="dml-operations"></a>Operações DML
* Não é possível ATUALIZAR ou EXCLUIR as linhas que foram migradas ou linhas qualificadas para migração em uma tabela habilitada para o Stretch ou em uma exibição que inclua tabelas habilitadas para o Stretch.
* Você não pode inserir linhas com INSERT em uma tabela habilitada para Stretch em um servidor vinculado.

### <a name="indexes"></a>Índices
* Não é possível criar um índice para uma exibição que inclui tabelas habilitadas para Stretch.
* Os filtros nos índices do SQL Server não são propagados para a tabela remota.

## <a name="a-namelimitationsa-limitations-that-currently-prevent-you-from-enabling-stretch-for-a-table"></a><a name="Limitations"></a> As limitações que atualmente impedem você de habilitar o Stretch para uma tabela
Os itens a seguir atualmente impedem você de habilitar o Stretch para uma tabela.

### <a name="table-properties"></a>Propriedades de tabela
* Tabelas com mais de 1.023 colunas ou com mais de 998 índices
* FileTables ou tabelas com dados FILESTREAM
* Tabelas replicadas ou que estejam usando ativamente o Controle de Alterações ou a Captura de Alteração de Dados
* Tabelas otimizadas para memórias

### <a name="data-types"></a>Tipos de dados
* texto, ntexto e imagem
* timestamp
* sql\_variant
* XML
* Tipos de dados CLR, incluindo geometria, geografia, hierarquia e tipos CLR definidos pelo usuário

### <a name="column-types"></a>Tipos de coluna
* COLUMN\_SET
* Colunas computadas

### <a name="constraints"></a>Restrições
* Restrições padrão e restrições de verificação
* Restrições de chave estrangeira que fazem referência à tabela. Em uma relação pai\-filho \(por exemplo, Order e Order\_Detail\), você pode habilitar o Stretch para a tabela filho \(Order\_Detail\), mas não para a tabela pai \(Order\).

### <a name="indexes"></a>Índices
* Índices de texto completo
* Índices XML
* Índices espaciais
* Exibições indexadas que fazem referência à tabela

## <a name="see-also"></a>Consulte também
[Identificar bancos de dados e tabelas do Banco de Dados de Stretch executando o Supervisor do Banco de Dados de Stretch](sql-server-stretch-database-identify-databases.md)

[Habilitar o Banco de Dados de Stretch para um banco de dados](sql-server-stretch-database-enable-database.md)

[Habilitar o Banco de Dados de Stretch para uma tabela](sql-server-stretch-database-enable-table.md)




<!--HONumber=Nov16_HO3-->


