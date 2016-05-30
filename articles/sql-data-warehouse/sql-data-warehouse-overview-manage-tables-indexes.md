<properties
   pageTitle="Gerenciar tabelas e índices no Azure SQL Data Warehouse | Microsoft Azure"
   description="Visão geral sobre considerações, melhores práticas e tarefas para gerenciamento de tabelas e de índices no SQL Data Warehouse do Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/02/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gerenciar tabelas e índices no Azure SQL Data Warehouse

Visão geral sobre considerações, melhores práticas e tarefas para gerenciamento de tabelas e de índices no SQL Data Warehouse.



| Categoria | Tarefa ou consideração | Descrição |
| :-----------------------| :---------------------------------------------- | :----------- |
| Índices ColumnStore | Recriar índices após o carregamento ou a inserção de dados | Por padrão, o SQL Data Warehouse armazena cada tabela como um índice columnstore clusterizado. Isso proporciona melhor desempenho e compactação de dados, especialmente para grandes tabelas. Dependendo de como você fizer a ingestão dos dados no índice columnstore, nem todos os dados poderão ser armazenados com a compactação columnstore. Quando isso acontece, talvez você não obtenha o desempenho que os índices columnstore são projetados para fornecer. <br/><br/>Para verificar se os índices columnstore estão em um estado íntegro, veja [Gerenciar índices columnstore][]. |
| Tabelas de hash distribuídas | Verifique se dados foram distribuídos uniformemente entre os nós | A tabela de hash distribuída quase sempre é a melhor maneira de otimizar junções e agregações em tabelas grandes. SQL Data Warehouse distribuiu as tabelas com base em uma coluna de distribuição especificada. Algumas colunas são boas chaves de distribuição e outras não. Você deseja que as linhas sejam distribuídas uniformemente. Alguma quantidade de desigualdade, ou de distorção de dados, é bom, mas se você tiver muita distorção de dados, perderá os benefícios do MPP (processo extremamente paralelo) que o SQL Data Warehouse foi projetado para fornecer.<br/><br/>Para verificar se sua tabela de hash distribuída não tem muita distorção de dados, veja [Gerenciar a distorção de dados distribuídos][]. |





## Próximas etapas

Para obter mais dicas de gerenciamento, acesse a [Visão geral de gerenciamento][].

<!--Image references-->

<!--Article references-->
[Gerenciar índices columnstore]: sql-data-warehouse-manage-columnstore-indexes.md
[Gerenciar a distorção de dados distribuídos]: sql-data-warehouse-manage-distributed-data-skew.md
[Visão geral de gerenciamento]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->