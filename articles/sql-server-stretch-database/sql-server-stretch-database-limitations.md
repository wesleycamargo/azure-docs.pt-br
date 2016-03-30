<properties
	pageTitle="Limitações de área de superfície e problemas de bloqueio do Stretch Database | Microsoft Azure"
	description="Saiba mais sobre problemas de bloqueio que você precisa resolver antes de habilitar o Stretch Database."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Limitações de área de superfície e problemas de bloqueio do Stretch Database

Saiba mais sobre problemas de bloqueio que você precisa resolver antes de habilitar o Stretch Database.

## <a name="Limitations"></a>Problemas de bloqueio
Na versão de teste atual do SQL Server 2016, os itens a seguir tornam uma tabela não qualificada para o Stretch.

**Propriedades de tabela**
-   Mais de 1.023 colunas

-   Mais de 998 índices

-   Tabelas que contêm dados FILESTREAM

-   FileTables

-   Tabelas replicadas

-   Tabelas que estão usando de forma ativa o Controle de Alterações ou a Captura de Alteração de Dados

-   Tabelas com otimização de memória

**Tipos de dados e propriedades de coluna**
-   timestamp

-   sql\_variant

-   XML

-   geometria

-   geografia

-   hierarchyid

-   Tipos de CLR definidos pelo usuário (UDTs)

**Tipos de coluna**
-   COLUMN\_SET

-   Colunas computadas

**Restrições**
-   Restrições de verificação

-   Restrições padrão

-   Restrições de chave estrangeira que fazem referência à tabela

**Índices**
-   Índices de texto completo

-   Índices XML

-   Índices espaciais

-   Exibições indexadas que fazem referência à tabela

## <a name="Caveats"></a>Limitações e advertências de tabelas habilitadas para Stretch
Na versão de teste atual do SQL Server 2016, as tabelas habilitadas para Stretch contêm as limitações ou advertências a seguir.

-   A exclusividade não é imposta para restrições UNIQUE e restrições PRIMARY KEY em uma tabela habilitada para Stretch.

-   Não é possível executar operações UPDATE ou DELETE em uma tabela habilitada para Stretch.

-   Você não pode executar operações INSERT remotamente em uma tabela habilitada para Stretch em um servidor vinculado.

-   Você não pode usar a replicação com uma tabela habilitada para Stretch.

-   Não é possível criar um índice para uma exibição que inclui tabelas habilitadas para Stretch.

-   Não é possível atualizar ou excluir uma exibição que inclui tabelas habilitadas para Stretch. No entanto, é possível inserir em uma exibição que inclui tabelas habilitadas para Stretch.

-   Os filtros nos índices não são propagados para a tabela remota.

## Consulte também

[Identificar bancos de dados e tabelas do Banco de Dados de Stretch executando o Supervisor do Banco de Dados de Stretch](sql-server-stretch-database-identify-databases.md)

[Habilitar o Banco de Dados de Stretch para um banco de dados](sql-server-stretch-database-enable-database.md)

[Habilitar o Banco de Dados de Stretch para uma tabela](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0316_2016-->