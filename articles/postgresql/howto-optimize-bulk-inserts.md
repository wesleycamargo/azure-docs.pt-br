---
title: Otimizar inserções em massa no Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve como você pode otimizar operações de inserção em massa no Banco de Dados do Azure para PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 9d2bfcddc649e4fff68bdba49df0945e88067036
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545222"
---
# <a name="optimizing-bulk-inserts-and-use-of-transient-data-on-azure-database-for-postgresql-server"></a>Otimizando inserções em massa e uso de dados temporários no banco de dados do Azure para PostgreSQL 
Este artigo descreve como você pode otimizar operações de inserção em massa e o uso de dados temporários em um banco de dados do Azure para PostgreSQL.

## <a name="using-unlogged-tables"></a>Usar tabelas não registradas
Para clientes que têm operações de carga de trabalho que envolvem dados temporários ou que inserem grandes conjuntos de dados em massa, considere o uso de tabelas não registradas.

Tabelas sem registro é um recurso do PostgreSQL que pode ser usado efetivamente para otimizar inserções em massa. O PostgreSQL usa Write-Ahead Logging (WAL), que fornece atomicidade e durabilidade a duas das propriedades ACID por padrão. Inserir em uma tabela não registrada significaria que o PostgreSQL faria inserções sem gravar no log de transações, que em si é uma operação de I/O, tornando essas tabelas consideravelmente mais rápidas do que as tabelas comuns.

Abaixo estão as opções para criar uma tabela não registrada:
- Crie uma nova tabela não registrada usando a sintaxe: `CREATE UNLOGGED TABLE <tableName>`
- Converter um existente conectado a tabela a uma tabela não registrada usando a sintaxe: `ALTER <tableName> SET UNLOGGED`.  Isso pode ser revertido usando a sintaxe: `ALTER <tableName> SET LOGGED`

## <a name="unlogged-table-tradeoff"></a>Troca de tabela sem registro
Tabelas não lidas não são seguras contra falhas. Uma tabela não registrada é automaticamente truncada após uma falha ou sujeita a um desligamento não limpo. O conteúdo de uma tabela não registrada também não é replicado para servidores em espera. Quaisquer índices criados em uma tabela não registrada também são descompactados automaticamente.  Após a conclusão da operação de inserção, você pode converter a tabela em log para que a inserção seja durável.

No entanto, em algumas cargas de trabalho de clientes, experimentamos uma melhoria de desempenho de aproximadamente 15% a 20% ao usar tabelas não registradas.

## <a name="next-steps"></a>Próximas etapas
Analise sua carga de trabalho para usos de dados transitórios e inserções em massa grandes.  

Examine a seguinte documentação do PostgreSQL - [criar comandos de SQL de tabela](https://www.postgresql.org/docs/current/static/sql-createtable.html)