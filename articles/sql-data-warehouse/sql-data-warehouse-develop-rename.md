<properties
   pageTitle="Renomear no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para renomear tabelas no Azure SQL Data Warehouse para desenvolvimento de soluções."
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
   ms.date="05/28/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Renomear no SQL Data Warehouse
SQL Data Warehouse usa a instrução [RENAME][] para renomear tabelas. Isso é diferente do SQL Server, que usa `sp_rename`. Atualmente, somente as tabelas do usuário podem ser renomeadas. Bancos de dados e tabelas externas não podem ser renomeados.

## Renomear tabela

Ao renomear uma tabela, todos os objetos e propriedades associados à tabela são atualizados para referenciar o novo nome de tabela. Por exemplo, definições, índices, restrições e permissões da tabela são atualizados. Modos de exibição não são atualizados.

A sintaxe para renomear uma tabela é:

```sql
RENAME OBJECT dbo.Customer TO NewCustomer;
```

## Alterar um esquema de tabela

Para alterar o esquema ao qual um objeto pertence, use ALTER SCHEMA:

```sql
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## Renomear uma tabela requer um bloqueio exclusivo na tabela

Você não pode renomear uma tabela enquanto ela estiver em uso. A renomeação de uma tabela requer um bloqueio exclusivo na tabela. Se a tabela estiver em uso, talvez seja necessário encerrar a sessão que está usando a tabela. Use o comando [KILL][] para finalizar uma sessão. Por exemplo, `KILL 'SID1234'`. Tenha cuidado ao usar KILL, uma vez que qualquer trabalho transacional não confirmado será revertido quando a sessão for encerrada. Consulte o artigo sobre conexões para obter mais informações sobre as [sessões e solicitações][]. Consulte [Otimização de transações para o SQL Data Warehouse][] para obter mais informações sobre o impacto de eliminar uma consulta transacional e o efeito de uma reversão.


## Próximas etapas
Para mais referências de T-SQL, consulte [referências de T-SQL][].

<!--Image references-->

<!--Article references-->
[development overview]: ./sql-data-warehouse-overview-develop.md
[sessões e solicitações]: ./sql-data-warehouse-develop-connections.md#sessions-and-requests
[referências de T-SQL]: ./sql-data-warehouse-reference-tsql-statements.md
[Otimização de transações para o SQL Data Warehouse]: ./sql-data-warehouse-develop-best-practices-transactions.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/library/ms173730.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx

<!---HONumber=AcomDC_0601_2016-->