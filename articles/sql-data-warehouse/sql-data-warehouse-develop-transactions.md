<properties
   pageTitle="Transações no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para implementar transações no Azure SQL Data Warehouse para o desenvolvimento de soluções."
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
   ms.date="01/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Transações no SQL Data Warehouse

Como esperado, o SQL Data Warehouse oferece suporte a todas as propriedades de transação. No entanto, para garantir que o desempenho do SQL Data Warehouse seja mantido em grande escala, alguns recursos serão limitados em comparação com o SQL Server. Este artigo realça as diferenças e lista os outros.

## Níveis de isolamento da transação
O SQL Data Warehouse implementa transações ACID. No entanto, o isolamento do suporte transacional é limitado a `READ UNCOMMITTED` e isso não pode ser alterado. Você pode implementar diversos métodos de codificação para impedir leituras sujas de dados, se isso for importante para você. Os métodos mais populares utilizam CTAS e a alternância de partição de tabela (normalmente conhecida como padrão de janela deslizante) para impedir que os usuários consultem dados que ainda estejam sendo preparados. Modos de exibição que filtram previamente os dados também são uma abordagem popular.

## Estado da transação
O SQL Data Warehouse usa a função XACT\_STATE() para relatar uma transação com falha usando o valor -2. Isso significa que a transação falhou e está marcada para reversão somente

> [AZURE.NOTE]O uso de -2 pela função XACT\_STATE para denotar uma transação com falha representa um comportamento diferente para o SQL Server. O SQL Server usa o valor -1 para representar uma transação não confirmável. O SQL Server consegue tolerar alguns erros dentro de uma transação sem precisar ser marcado como não confirmável. Por exemplo, SELECT 1/0 poderia causar um erro mas não forçar uma transação em um estado não confirmável. O SQL Server também permite leituras na transação não confirmável. No entanto, em SQLDW, este não é o caso. Se ocorrer um erro dentro de uma transação SQLDW, ele irá inserir automaticamente o estado de-2: incluindo os erros SELECT 1/0. Portanto, é importante verificar se o código do aplicativo para ver se ele usa XACT\_STATE().

No SQL Server, talvez você veja um fragmento de código com esta aparência:

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        ROLLBACK TRAN;

    END CATCH;
```

Observe que a instrução `SELECT` ocorre antes da instrução `ROLLBACK`. Observe também que a configuração da variável `@xact` usa DECLARE e não `SELECT`.

No SQL Data Warehouse, o código precisa ter esta aparência:

```
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        ROLLBACK TRAN;

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

SELECT @xact;
```

Observe que a reversão da transação deve ocorrer antes da leitura das informações de erro no bloco `CATCH`.

## Função Error\_line()
Também vale a pena observar que o SQL Data Warehouse não implementa ou aceita a função ERROR\_LINE(). Se você tiver isso em seu código, você precisará removê-lo para que seja compatível com o SQL Data Warehouse. Em vez disso, use rótulos de consulta em seu código para implementar a funcionalidade equivalente. Consulte o artigo [rótulos de consulta] para obter mais detalhes sobre esse recurso.

## Uso de THROW e RAISERROR
THROW é a implementação mais moderna para lançar exceções no SQL Data Warehouse, mas também há suporte para RAISERROR. No entanto, existem algumas diferenças que valem a pena prestar atenção.

- Os números das mensagens de erro definidas pelo usuário não podem estar no intervalo de 100.000 a 150.000 para THROW
- As mensagens de erro do RAISERROR são fixadas em 50.000
- Não há suporte para o uso de sys.messages

## Limitações
O SQL Data Warehouse tem algumas outras restrições relacionadas a transações.

Elas são as seguintes:

- Sem transações distribuídas
- Não há transações aninhadas permitidas
- Não são permitidos pontos de salvamento

## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->