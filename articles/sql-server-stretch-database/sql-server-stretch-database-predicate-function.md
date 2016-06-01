<properties
	pageTitle="Usar um predicado de filtro para selecionar linhas para migrar (Stretch Database) | Microsoft Azure"
	description="Saiba como usar um predicado de filtro para selecionar as linhas para migração."
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
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Usar um predicado de filtro para selecionar linhas para migrar (Stretch Database)

Se você armazenar dados do histórico em uma tabela separada, poderá configurar o Banco de Dados de Stretch para migrar toda a tabela. Por outro lado, se sua tabela contiver dados do histórico e atuais, você poderá especificar um predicado de filtro para selecionar as linhas para migração. O predicado de filtro é uma função com valor de tabela embutida. Este tópico descreve como escrever uma Função com valor de tabela embutida a fim de selecionar linhas para migração.

>   [AZURE.NOTE] Se você fornecer um predicado de filtro que apresente um desempenho ruim, a migração de dados também terá um desempenho ruim. O Banco de Dados de Stretch aplica o predicado de filtro à tabela usando o operador CROSS APPLY.

Se você não especificar um predicado de filtro, toda a tabela será migrada.

Quando você executar o Assistente Habilitar Banco de Dados para o Stretch, poderá migrar uma tabela inteira ou especificar um predicado de filtro simples baseado em data no assistente. Se você quiser usar um predicado de filtro diferente para selecionar linhas a serem migradas, siga um destes procedimentos.

-   Saia do assistente e execute a instrução ALTER TABLE para habilitar o Stretch para tabela e especificar um predicado.

-   Execute a instrução ALTER TABLE para especificar um predicado depois que você sair do assistente.

A sintaxe ALTER TABLE para adicionar um predicado é descrita posteriormente neste tópico.

## Requisitos básicos para a função embutida com valor de tabela
A função com valor de tabela embutida necessária para um predicado de filtro do Stretch Database se parece com o exemplo a seguir.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE <predicate>
```
Os parâmetros da função precisam ser identificadores para as colunas provenientes da tabela.

É necessário usar a associação de esquema para evitar que as colunas usadas pelo predicado de filtro sejam descartadas ou alteradas.

### Valor de retorno
Se a função retornar um resultado não vazio, a linha poderá ser qualificada para migração. Caso contrário, ou seja, se a função não retornar um resultado - a linha não estará qualificada para ser migrada.

### Condições
O &lt;*predicado*&gt; pode ser formado por uma condição ou por várias condições unidas pelo operador lógico AND.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
Cada condição pode ser formada por uma condição primitiva ou por várias condições primitivas unidas pelo operador lógico OR.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### Condições primitivas
Uma condição primitiva pode fazer uma das comparações a seguir.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

-   Comparar um parâmetro de função a uma expressão constante. Por exemplo: `@column1 < 1000`.

    Veja um exemplo que verifica se o valor de uma coluna *data* é &lt; 1/1/2016.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
    GO

    ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   Aplique o operador IS NULL ou IS NOT NULL a um parâmetro de função.

-   Use o operador IN para comparar um parâmetro de função a uma lista de valores constantes.

    Veja um exemplo que verifica se o valor de uma coluna *shipment\_status* é `IN (N'Completed', N'Returned', N'Cancelled')`.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

### Operadores de comparação
Há suporte para os seguintes operadores de comparação.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### Expressões constantes
As constantes usadas em um predicado de filtro podem ser qualquer expressão determinística que possa ser avaliada quando você define a função. As expressões constantes podem conter o seguinte.

-   Literais. Por exemplo: `N’abc’, 123`.

-   Expressões algébricas. Por exemplo: `123 + 456`.

-   Funções determinísticas. Por exemplo: `SQRT(900)`.

-   Conversões determinísticas que usam CAST ou CONVERT. Por exemplo: `CONVERT(datetime, '1/1/2016', 101)`.

### Outras expressões
Você pode usar os operadores BETWEEN e NOT BETWEEN se o predicado resultante estiver de acordo com as regras descritas aqui depois de substituir os operadores BETWEEN e NOT BETWEEN pelas expressões equivalentes AND e OR.

Não é possível usar subconsultas ou funções não determinísticas como RAND() ou GETDATE().

## Adicionar um predicado de filtro a uma tabela
Adicione um predicado de filtro a uma tabela executando a instrução ALTERAR TABELA e especificando uma função embutida com valor de tabela existente como o valor do parâmetro FILTER\_PREDICATE. Por exemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
) )
```
Depois de associar a função à tabela como um predicado, ocorre o seguinte.

-   Na próxima migração de dados, apenas as linhas para as quais a função retornar um valor não vazio serão migradas.

-   As colunas usadas pela função serão associadas ao esquema. Você não poderá alterar essas colunas se uma tabela estiver usando a função como seu predicado de filtro.

Você não poderá descartar a função embutida com valor de tabela se uma tabela estiver usando a função como seu predicado de filtro.

## Filtrar linhas por data
O exemplo a seguir migra linhas em que a coluna **data** contém um valor antes de 1º de janeiro de 2016.

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## Filtrar linhas pelo valor em uma coluna de status
O exemplo a seguir migra linhas em que a coluna **status** contém um dos valores especificados.

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## Filtrar linhas usando uma janela deslizante
Para filtrar linhas usando uma janela deslizante, tenha em mente os seguintes requisitos para a função de filtro.

-   A função deve ser determinística. Portanto, você não pode criar uma função que recalcula automaticamente a janela deslizante com o passar do tempo.

-   A função usa a associação do esquema. Portanto, não é possível simplesmente atualizar a função "no local" diariamente chamando ALTER FUNCTION para mover a janela deslizante.

Iniciar com um predicado de filtro como o exemplo a seguir, que migra as linhas em que a coluna **systemEndTime** contém um valor antes de 1º de janeiro de 2016.

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

Aplique o predicado de filtro à tabela.

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

Quando você deseja atualizar a janela deslizante, faça o seguinte.

1.  Crie uma nova função que especifica a nova janela deslizante. O exemplo a seguir seleciona datas anteriores a 2 de janeiro de 2016, em vez de 1º de janeiro de 2016.

2.  Substitua o predicado do filtro anterior pelo novo, chamando ALTER TABLE, conforme mostrado no exemplo a seguir.

3. Opcionalmente, remova a função de filtro anterior que você não usa mais ao chamar DROP FUNCTION. (Essa etapa não é mostrada no exemplo.)

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## Mais exemplos de predicados de filtro válido

-   O exemplo a seguir combina duas condições primitivas usando o operador lógico AND.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
      WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
    GO

    ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
    	FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
    	MIGRATION_STATE = OUTBOUND
    ) )
    ```

-   O exemplo a seguir usa várias condições e uma conversão determinística com CONVERT.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
    GO
    ```

-   O exemplo a seguir usa funções e operadores matemáticos.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < SQRT(400) + 10
    GO
    ```

-   O exemplo a seguir usa os operadores BETWEEN e NOT BETWEEN. Esse uso é válido porque o predicado resultante está de acordo com as regras descritas aqui, depois de substituir os operadores BETWEEN e NOT BETWEEN pelas expressões equivalentes AND e OR.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 BETWEEN 0 AND 100
    			AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
    GO
    ```
    A função anterior é equivalente à função a seguir depois de substituir os operadores BETWEEN e NOT BETWEEN pelas expressões equivalentes AND e OR.

    ```tsql
    CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
    GO
    ```

## Exemplos de predicados de filtro que não são válidos

-   A função a seguir não é válida porque contém uma conversão não determinística.

    ```tsql
    CREATE FUNCTION dbo.fn_example5(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < CONVERT(datetime, '1/1/2016')
    GO
    ```

-   A função a seguir não é válida porque contém uma chamada de função não determinística.

    ```tsql
    CREATE FUNCTION dbo.fn_example6(@column1 datetime)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 < DATEADD(day, -60, GETDATE())
    GO
    ```

-   A função a seguir não é válida porque contém uma subconsulta.

    ```tsql
    CREATE FUNCTION dbo.fn_example7(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
    GO
    ```

-   As funções a seguir não são válidas porque expressões que usam operadores algébricos ou funções internas devem ser avaliadas como uma constante quando você define a função. Você não pode incluir referências da coluna em expressões algébricas ou em chamadas de função.

    ```tsql
    CREATE FUNCTION dbo.fn_example8(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE @column1 % 2 =  0
    GO

    CREATE FUNCTION dbo.fn_example9(@column1 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE SQRT(@column1) = 30
    GO
    ```

-   A função a seguir não é válida porque viola as regras descritas aqui após a substituição do operador BETWEEN pela expressão AND equivalente.

    ```tsql
    CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```
    A função anterior é equivalente à função a seguir depois de substituir o operador BETWEEN pela expressão equivalente AND. Essa função não é válida porque condições primitivas só podem usar o operador lógico OR.

    ```tsql
    CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
    RETURNS TABLE
    WITH SCHEMABINDING
    AS
    RETURN	SELECT 1 AS is_eligible
    		WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
    GO
    ```

## Como o Banco de Dados de Stretch aplica o predicado de filtro
O Banco de Dados de Stretch aplica o predicado de filtro à tabela e determina as linhas qualificadas usando o operador CROSS APPLY. Por exemplo:

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
Se a função retornar um resultado não vazio para a linha, a linha poderá ser migrada.

## Substituir um predicado de filtro existente
Você pode substituir um predicado de filtro especificado anteriormente executando novamente a instrução ALTERAR TABELA e especificando um novo valor para o parâmetro FILTER\_PREDICATE. Por exemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
	MIGRATION_STATE = <desired_migration_state>
```
A nova função embutida com valor de tabela tem os seguintes requisitos.

-   A nova função deve ser menos restritiva do que a função anterior.

-   Todos os operadores que existiam na função antiga devem existir na nova função.

-   A nova função não pode conter operadores inexistentes na função antiga.

-   Não é possível alterar a ordem dos argumentos do operador.

-   Somente os valores constantes que fazem parte de uma comparação `<, <=, >, >=` podem ser alterados de uma maneira a tornar o predicado menos restritivo.

### Exemplo de uma substituição válida
Vamos supor que a função a seguir seja o predicado de filtro atual.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
GO
```
A função a seguir é uma substituição válida porque a nova constante de data (que especifica uma data de corte posterior) torna o predicado menos restritivo.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
			AND (@column2 < -50 OR @column2 > 50)
GO
```

### Exemplos de substituições que não são válidas
A função a seguir não é uma substituição válida porque a nova constante de data (que especifica uma data de corte anterior) não torna o predicado menos restritivo.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
			AND (@column2 < -100 OR @column2 > 100)
GO
```
A função a seguir não é uma substituição válida porque um dos operadores de comparação foi removido.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -50)
GO
```
A função a seguir não é uma substituição válida porque uma nova condição foi adicionada com o operador lógico AND.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN	SELECT 1 AS is_eligible
		WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
			AND (@column2 < -100 OR @column2 > 100)
			AND (@column2 <> 0)
GO
```

## Remover um predicado de filtro de uma tabela
Para migrar toda a tabela em vez das linhas selecionadas, remova o FILTER\_PREDICATE existente definido-o como nulo. Por exemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
	FILTER_PREDICATE = NULL,
	MIGRATION_STATE = <desired_migration_state>
) )
```
Depois de remover o predicado de filtro, todas as linhas na tabela poderão ser migradas. Como resultado, você não pode especificar um predicado de filtro para a mesma tabela posteriormente, a menos que você retorne todos os dados remotos para a tabela do Azure primeiro. Essa restrição existe para evitar a situação em que linhas que não estão qualificadas para a migração quando você fornece um novo predicado de filtro já foram migradas para o Azure.

## Verificar o predicado de filtro aplicado a uma tabela
Para verificar o predicado de filtro aplicado a uma tabela, abra a exibição de catálogo **sys.remote\_data\_archive\_tables** e verifique o valor da coluna **filter\_predicate**. Se o valor for nulo, a tabela inteira poderá ser arquivada. Para obter mais informações, confira [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## Consulte também

[ALTERAR TABELA (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0518_2016-->