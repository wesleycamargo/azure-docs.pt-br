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
   ms.date="03/23/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Renomear no SQL Data Warehouse
Embora o SQL Server dê suporte à renomeação do banco de dados por meio do procedimento armazenado `sp_renamedb`, o SQL Data Warehouse usa a sintaxe DDL para atingir o mesmo objetivo. O comando DDL é `RENAME OBJECT`.

## Renomear tabela

Atualmente, somente as tabelas podem ser renomeadas. A sintaxe para renomear uma tabela é:

```sql
RENAME OBJECT dbo.Customer TO NewCustomer;
```

Ao renomear uma tabela, todos os objetos e propriedades associados à tabela são atualizados para referenciar o novo nome de tabela. Por exemplo, definições, índices, restrições e permissões da tabela são atualizados. Modos de exibição não são atualizados.

## Renomear tabela externa

Renomear uma tabela externa altera o nome da tabela no SQL Data Warehouse. Ele não afeta o local dos dados externos para a tabela.

## Alterar um esquema de tabela
Se a intenção for alterar o esquema ao qual um objeto pertence, isso pode ser alcançado por meio de ALTER SCHEMA:

```sql
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## Renomear uma tabela requer um bloqueio exclusivo na tabela

É importante lembrar-se de que você não pode renomear uma tabela enquanto ela está em uso. A renomeação de uma tabela requer um bloqueio exclusivo na tabela. Se a tabela estiver em uso, talvez seja necessário encerrar a sessão que está usando a tabela. Para encerrar uma sessão, é necessário usar o comando [KILL][]. Tome cuidado ao usar `KILL` já que, quando a sessão for encerrada, e qualquer trabalho não confirmado será revertido. As sessões no SQL Data Warehouse são prefixadas por 'SID'. Você precisará incluir o SID e também o número da sessão ao invocar o comando KILL. Por exemplo, `KILL 'SID1234'`. Consulte o artigo sobre conexões para obter mais informações sobre as [sessões]


## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md
[sessões]: sql-data-warehouse-develop-connections.md


<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/library/ms173730.aspx

<!---HONumber=AcomDC_0330_2016-->