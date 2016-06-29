<properties
	pageTitle="Limitações do Stretch Database | Microsoft Azure"
	description="Saiba mais sobre as limitações do Stretch Database."
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
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Limitações do Stretch Database

Saiba mais sobre as limitações para tabelas habilitadas para o Stretch e sobre as limitações que no momento impedem você de habilitar o Stretch para uma tabela.

##  <a name="Caveats"></a> Limitações para tabelas habilitadas para o Stretch

As tabelas habilitadas para o Stretch têm as limitações a seguir.

### Restrições

-   A exclusividade não é imposta para as restrições UNIQUE e as restrições PRIMARY KEY na tabela do Azure que contém os dados migrados.

### Operações DML

-   Não é possível ATUALIZAR ou EXCLUIR as linhas que foram migradas ou linhas elegíveis para migração em uma tabela habilitada para o Stretch ou em uma exibição que inclua tabelas habilitadas para o Stretch.

-   Você não pode inserir linhas com INSERT em uma tabela habilitada para Stretch em um servidor vinculado.

### Índices

-   Não é possível criar um índice para uma exibição que inclui tabelas habilitadas para Stretch.

-   Os filtros nos índices do SQL Server não são propagados para a tabela remota.

##  <a name="Limitations"></a> As limitações que atualmente impedem você de habilitar o Stretch para uma tabela

Os itens a seguir atualmente impedem você de habilitar o Stretch para uma tabela.

### Propriedades de tabela

-   Tabelas com mais de 1.023 colunas ou com mais de 998 índices

-   FileTables ou tabelas com dados FILESTREAM

-   Tabelas replicadas ou que estejam usando ativamente o Controle de Alterações ou a Captura de Alteração de Dados

-   Tabelas com otimização de memória

### Tipos de dados

-   texto, ntexto e imagem

-   timestamp

-   sql\_variant

-   XML

-   Tipos de dados CLR, incluindo geometria, geografia, hierarchyid e tipos CLR definidos pelo usuário

### Tipos de coluna

-   COLUMN\_SET

-   Colunas computadas

### Restrições

-   Restrições padrão e restrições de verificação

-   Restrições de chave estrangeira que fazem referência à tabela. Em uma relação pai-filho (por exemplo, Order e Order\_Detail), você pode habilitar o Stretch para a tabela filho (Order\_Detail), mas não para a tabela pai (Order).

### Índices

-   Índices de texto completo

-   Índices XML

-   Índices espaciais

-   Exibições indexadas que fazem referência à tabela

## Consulte também

[Identificar bancos de dados e tabelas do Banco de Dados de Stretch executando o Supervisor do Banco de Dados de Stretch](sql-server-stretch-database-identify-databases.md)

[Habilitar o Banco de Dados de Stretch para um banco de dados](sql-server-stretch-database-enable-database.md)

[Habilitar o Banco de Dados de Stretch para uma tabela](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0615_2016-->