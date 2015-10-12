<properties
   pageTitle="Atribução de variáveis no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para atribuir variáveis de Transact-SQL no Azure SQL Data Warehouse para desenvolvimento de soluções."
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
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Atribuir variáveis no SQL Data Warehouse
As variáveis no SQL Data Warehouse são definidas usando a instrução `DECLARE` ou a instrução `SET`.

Todos os itens a seguir são maneiras perfeitamente válidas de definir um valor de variável:

## Definição de variáveis com DECLARE

A inicialização de variáveis com DECLARE é uma das maneiras mais flexíveis de definir um valor de variável no SQL Data Warehouse.

```
DECLARE @v  int = 0
;
```

Você também pode usar DECLARE para definir mais de uma variável por vez. Você não pode usar `SELECT` ou `UPDATE` para fazer isso:

```
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Você não pode inicializar e usar uma variável na mesma instrução DECLARE. Para ilustrar esse ponto, o exemplo a seguir **não** é permitido já que @p1 foi inicializado e usado na mesma instrução DECLARE. Isso resultará em um erro.

```
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## Definição de valores com SET
O SET é um método muito comum para definir uma variável.

Todos os exemplos abaixo são maneiras válidas de definir uma variável com SET:

```
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Você só pode definir uma variável por vez com SET. No entanto, como pode ser visto acima, são permitidos operadores compostos.

## Limitações
Você não pode usar SELECT ou UPDATE para atribuição de variáveis.


## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte a [Visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Oct15_HO1-->