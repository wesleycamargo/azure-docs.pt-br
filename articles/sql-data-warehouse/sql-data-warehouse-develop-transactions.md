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
   ms.date="05/11/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Transações no SQL Data Warehouse

Como esperado, o SQL Data Warehouse oferece suporte a todas as propriedades de transação. No entanto, para garantir que o desempenho do SQL Data Warehouse seja mantido em grande escala, alguns recursos serão limitados em comparação com o SQL Server. Este artigo realça as diferenças e lista as outras.

## Níveis de isolamento da transação
O SQL Data Warehouse implementa transações ACID. No entanto, o isolamento do suporte transacional é limitado a `READ UNCOMMITTED` e isso não pode ser alterado. Você pode implementar diversos métodos de codificação para impedir leituras sujas de dados, se isso for importante para você. Os métodos mais populares utilizam CTAS e a alternância de partição de tabela (normalmente conhecida como padrão de janela deslizante) para impedir que os usuários consultem dados que ainda estejam sendo preparados. Modos de exibição que filtram previamente os dados também são uma abordagem popular.

## Tamanho da transação
Uma única transação de modificação de dados é limitada em tamanho. Hoje, o limite é aplicado “por distribuição”. Portanto, para obter o valor total, devemos multiplicar o limite pela contagem de distribuição. Para chegar a uma aproximação do número máximo de linhas na transação, divida o limite de distribuição pelo tamanho total de cada coluna. Para colunas de tamanho variável, considere o uso de um tamanho médio de coluna em vez do tamanho máximo.

Na tabela abaixo, foram feitas as seguintes suposições:

* Ocorreu uma distribuição uniforme dos dados 
* O tamanho médio da linha é de 250 bytes

| DWU | Limite por distribuição (GiB) | Número de distribuições | Tamanho máximo de transações (GiB) | Nº de linhas por distribuição | Máximo de linhas por transação |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100 | 1 | 60 | 60 | 4\.000.000 | 240\.000.000 |
| DW200 | 1\.5 | 60 | 90 | 6\.000.000 | 360\.000.000 |
| DW300 | 2\.25 | 60 | 135 | 9\.000.000 | 540\.000.000 |
| DW400 | 3 | 60 | 180 | 12\.000.000 | 720\.000.000 |
| DW500 | 3,75 | 60 | 225 | 15\.000.000 | 900\.000.000 |
| DW600 | 4\.5 | 60 | 270 | 18\.000.000 | 1\.080.000.000 |
| DW1000 | 7\.5 | 60 | 450 | 30\.000.000 | 1\.800.000.000 |
| DW1200 | 9 | 60 | 540 | 36\.000.000 | 2\.160.000.000 |
| DW1500 | 11,25 | 60 | 675 | 45\.000.000 | 2\.700.000.000 |
| DW2000 | 15 | 60 | 900 | 60\.000.000 | 3\.600.000.000 |

O limite de tamanho de transação é aplicado por transação ou operação. Ele não é aplicado em todas as transações simultâneas. Portanto, cada transação tem permissão para gravar essa quantidade de dados no log.

Para otimizar e minimizar a quantidade de dados gravados no log, consulte o artigo [Práticas recomendadas sobre transações][].

> [AZURE.WARNING] O tamanho máximo de transações só pode ser obtido para tabelas distribuídas HASH ou ROUND\_ROBIN nas quais o espalhamento de dados é uniforme. Se a transação estiver gravando dados de maneira distorcida nas distribuições, provavelmente, o limite será alcançado antes do tamanho máximo de transações.
<!--REPLICATED_TABLE-->

## Estado da transação
O SQL Data Warehouse usa a função XACT\_STATE() para relatar uma transação com falha usando o valor -2. Isso significa que a transação falhou e está marcada para reversão somente

> [AZURE.NOTE] O uso de -2 pela função XACT\_STATE para denotar uma transação com falha representa um comportamento diferente para o SQL Server. O SQL Server usa o valor -1 para representar uma transação não confirmável. O SQL Server consegue tolerar alguns erros dentro de uma transação sem precisar ser marcado como não confirmável. Por exemplo, SELECT 1/0 poderia causar um erro mas não forçar uma transação em um estado não confirmável. O SQL Server também permite leituras na transação não confirmável. No entanto, em SQLDW, este não é o caso. Se ocorrer um erro dentro de uma transação SQLDW, ele irá inserir automaticamente o estado de-2: incluindo os erros SELECT 1/0. Portanto, é importante verificar se o código do aplicativo para ver se ele usa XACT\_STATE().

No SQL Server, talvez você veja um fragmento de código com esta aparência:

```sql
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

```sql
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
[Práticas recomendadas sobre transações]: sql-data-warehouse-develop-best-practices-transactions.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0511_2016-->