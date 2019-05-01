---
title: Funções de expressão no recurso de mapeamento de fluxo de dados do Azure Data Factory
description: Saiba mais sobre as funções de expressão no mapeamento de fluxo de dados.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 6b4df70114dd481566ae1a666c91c1c9b5bad86f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717003"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Expressões de transformação de dados no fluxo de dados de mapeamento 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>Funções de expressão

No Data Factory, use a linguagem de expressão do recurso de mapeamento de fluxo de dados para configurar as transformações de dados.

*********************************
<code>abs</code>
==============================
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Esta expressão de marca o módulo positivo de um par de números.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
*********************************
<code>acos</code>
==============================
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão calcula um valor do cosseno inverso.
* ``acos(1) -> 0.0``
*********************************
<code>add</code>
==============================
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Essa expressão adiciona um par de cadeias de caracteres ou números. Ele adiciona uma data para um número de dias. Ele acrescenta uma matriz do tipo semelhante para outro. 
* ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``

O **adicione** operador é igual a **+** operador.
*********************************
<code>addDays</code>
==============================
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Essa expressão adiciona dias para uma data ou carimbo de hora. 
* ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``

O **addDays** operador é igual a **+** operador para datas.
*********************************
<code>addMonths</code>
==============================
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Essa expressão adiciona meses para uma data ou carimbo de hora.
* ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
*********************************
<code>and</code>
==============================
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Esta é uma **e** operador. Isso é igual a **&&** operador.
* ``and(true, false) -> false``
* ``true && false -> false``
*********************************
<code>asin</code>
==============================
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão calcula um valor do seno inverso.
* ``asin(0) -> 0.0``
*********************************
<code>atan</code>
==============================
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão calcula um valor de tangente inverso.
* ``atan(0) -> 0.0``
*********************************
<code>atan2</code>
==============================
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão retorna o ângulo em radianos entre o eixo x positivo de um plano e o ponto que oferecem as coordenadas.
* ``atan2(0, 0) -> 0.0``
*********************************
<code>avg</code>
==============================
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Essa expressão obtém a média dos valores de uma coluna.
* ``avg(sales) -> 7523420.234``
*********************************
<code>avgIf</code>
==============================
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Essa expressão com base em um critério, obtém a média dos valores de uma coluna.
* ``avgIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>byName</code>
==============================
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
Essa expressão seleciona um valor de coluna pelo nome no fluxo. Se houver várias correspondências, a primeira correspondência é retornada. Se não houver nenhuma correspondência, a expressão retorna um valor nulo. O valor retornado deve ser convertido por uma das funções de conversão de tipo (TO_DATE, TO_STRING...). Nomes de colunas que são conhecidos em tempo de design devem ser resolvidos apenas por seu nome. Entradas computadas não têm suporte, mas você pode usar substituições de parâmetro.

* ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
*********************************
<code>byPosition</code>
==============================
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Essa expressão seleciona um valor de coluna por sua posição relativa (baseado em 1) no fluxo. Se a posição está fora dos limites, ele retorna um valor nulo. O valor retornado deve ser convertido por uma das funções de conversão de tipo (TO_DATE, TO_STRING e assim por diante). Entradas computadas não têm suporte, mas você pode usar substituições de parâmetro.

* ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
*********************************
<code>case</code>
==============================
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Com base em condições de alternadas, essa expressão se aplica um valor ou a outra. 
* ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``

Se o número de entradas for par, o outro valor é NULL para a última condição.
*********************************
<code>cbrt</code>
==============================
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão calcula a raiz cúbica de um número.
* ``cbrt(8) -> 2.0``
*********************************
<code>ceil</code>
==============================
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Essa expressão retorna o menor inteiro que não é menor que o número.
* ``ceil(-0.1) -> 0``
*********************************
<code>concat</code>
==============================
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Esta expressão concatena um número variável de cadeias de caracteres. O **concat** operador é igual a **+** operador com cadeias de caracteres.
* ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
*********************************
<code>concatWS</code>
==============================
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Esta expressão concatena um número variável de cadeias de caracteres junto com um separador. O primeiro parâmetro é o separador.
* ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
*********************************
<code>cos</code>
==============================
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão calcula um valor de cosseno.
* ``cos(10) -> -0.83907152907``
*********************************
<code>cosh</code>
==============================
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão calcula um cosseno hiperbólico de um valor.
* ``cosh(0) -> 1.0``
*********************************
<code>count</code>
==============================
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Essa expressão obtém a contagem agregada de valores. Se a coluna opcional ou colunas forem especificado, NULL valores na contagem são ignorados.
* ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
*********************************
<code>countDistinct</code>
==============================
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Essa expressão obtém a contagem agregada de valores distintos de um conjunto de colunas.
* ``countDistinct(custId, custName) -> 60``
*********************************
<code>countIf</code>
==============================
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Essa expressão com base em um critério, obtém a contagem agregada de valores. Se a coluna opcional for especificado, NULL valores na contagem são ignorados.
* ``countIf(state == 'CA' && commission < 10000, name) -> 100``
*********************************
<code>covariancePopulation</code>
==============================
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão obtém a covariância de população entre duas colunas.
* ``covariancePopulation(sales, profit) -> 122.12``
*********************************
<code>covariancePopulationIf</code>
==============================
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão com base em um critério, obtém a covariância de população de duas colunas.
* ``covariancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>covarianceSample</code>
==============================
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão obtém a covariância de exemplo de duas colunas.
* ``covarianceSample(sales, profit) -> 122.12``
*********************************
<code>covarianceSampleIf</code>
==============================
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão com base em um critério, obtém a covariância de exemplo de duas colunas.
* ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
*********************************
<code>crc32</code>
==============================
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Essa expressão calcula o hash CRC32 de um conjunto de colunas de variados tipos de dados primitivos, dado um comprimento de bit cujos valores podem ser somente 0(256), 224, 256, 384, 512. Você pode usar o **crc32** operador para calcular uma impressão digital para uma linha.
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
*********************************
<code>cumeDist</code>
==============================
<code><b>cumeDist() => integer</b></code><br/><br/>
Esta função calcula a posição de um valor em relação a todos os valores na partição. O resultado é o número de linhas anteriores ou iguais à linha atual na ordem da partição, dividida pelo número total de linhas na partição de janela. Quaisquer valores de empate na ordenação retornam a mesma posição.
* ``cumeDist() -> 1``
*********************************
<code>currentDate</code>
==============================
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Essa expressão obtém a data atual quando o trabalho começa a ser executado. 
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``

Você pode passar um fuso horário opcional na forma de `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. O fuso horário local é o padrão.
*********************************
<code>currentTimestamp</code>
==============================
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Essa expressão obtém o carimbo de hora atual quando o trabalho começa a executar com o fuso horário local.
* ``currentTimestamp() -> 12-12-2030T12:12:12``
*********************************
<code>currentUTC</code>
==============================
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Essa expressão obtém o carimbo de hora atual como UTC. 
* ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``

Você pode passar um fuso horário opcional na forma de `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. O fuso horário local é o padrão.
*********************************
<code>dayOfMonth</code>
==============================
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Dada uma data, essa expressão obtém o dia do mês.
* ``dayOfMonth(toDate('2018-06-08')) -> 08``
*********************************
<code>dayOfWeek</code>
==============================
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Essa expressão dada uma data, obtém o dia da semana. 
* ``dayOfWeek(toDate('2018-06-08')) -> 7``

Os valores de dia são da seguinte maneira:
- 1 - domingo
- 2 - segunda-feira 
- ...
- 7 - Sábado
*********************************
<code>dayOfYear</code>
==============================
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Dada uma data, essa expressão obtém o dia do ano.
* ``dayOfYear(toDate('2016-04-09')) -> 100``
*********************************
<code>degrees</code>
==============================
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão Converte radianos em graus.
* ``degrees(3.141592653589793) -> 180``
*********************************
<code>denseRank</code>
==============================
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Essa expressão calcula a classificação de um valor em um grupo de valores. O resultado é 1 mais o número de linhas anteriores ou iguais à linha atual na ordenação da partição. Os valores não produzem intervalos na sequência. 
* ``denseRank(salesQtr, salesAmt) -> 1``

O **denseRank** operador funciona até mesmo quando os dados não estão classificados. Ele procura por uma alteração nos valores.
*********************************
<code>divide</code>
==============================
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Essa expressão divide o par de números. O **dividir** operador é igual a **/** operador.
* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
*********************************
<code>endsWith</code>
==============================
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Essa expressão verifica se a cadeia de caracteres termina com a cadeia de caracteres fornecida.
* ``endsWith('great', 'eat') -> true``
*********************************
<code>equals</code>
==============================
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Isso é um operador de comparação igual a. É igual a **==** operador.
* ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
*********************************
<code>equalsIgnoreCase</code>
==============================
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
O **equalsIgnoreCase** operador é um operador é igual a comparação que diferencia maiusculas e minúsculas. É igual a **<=>** operador.
* ``'abc'=='abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
*********************************
<code>factorial</code>
==============================
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Essa expressão calcula o fatorial de um número.
* ``factorial(5) -> 120``
*********************************
<code>false</code>
==============================
<code><b>false() => boolean</b></code><br/><br/>
Essa expressão sempre retorna um valor falso. 
* ``isDiscounted == false()``
* ``isDiscounted() == false``

Use o `syntax(false())` funcionar se uma coluna for nomeada *falso*.
*********************************
<code>first</code>
==============================
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Essa expressão obtém o primeiro valor de um grupo de colunas. Se você omitir o segundo parâmetro `ignoreNulls`, ele é presumido ser falsa.
* ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
*********************************
<code>floor</code>
==============================
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Essa expressão retorna o maior inteiro que não é maior que o número.
* ``floor(-0.1) -> -1``
*********************************
<code>fromUTC</code>
==============================
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Essa expressão converte o carimbo de hora do UTC. Opcionalmente, você pode passar o fuso horário na forma de `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. O padrão é o fuso horário atual.

* ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>greater</code>
==============================
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Esta é uma comparação **maior** operador. É igual a **>** operador.
* ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
*********************************
<code>greaterOrEqual</code>
==============================
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Isso é um operador de comparação maior-than-or-equal. Esse operador é igual a **>=** operador.
* ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
*********************************
<code>greatest</code>
==============================
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Essa expressão retorna o maior valor na lista de valores como entrada. Retorna NULL se a todas as entradas forem NULL.
* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
*********************************
<code>hour</code>
==============================
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Essa expressão obtém o valor de um carimbo de data hora. 
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``

Você pode passar um fuso horário opcional na forma de `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. O fuso horário local é o padrão.
*********************************
<code>iif</code>
==============================
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Com base em uma condição, essa expressão se aplica um valor ou a outra. Se o outro valor for especificado, ele será considerado NULL. Ambos os valores devem ser compatíveis (numérico ou cadeia de caracteres, por exemplo).
* ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
*********************************
<code>in</code>
==============================
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Essa expressão verifica para um item na matriz.
* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
*********************************
<code>initCap</code>
==============================
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Essa expressão converte a primeira letra de cada palavra em maiuscula. Palavras são identificadas por separações de espaço em branco.
* ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
*********************************
<code>instr</code>
==============================
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Essa expressão localiza a posição da subcadeia de caracteres dentro de uma cadeia de caracteres (com base em 1). Se a posição não for encontrada, 0 será retornado. 
* ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
*********************************
<code>isDelete</code>
==============================
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Essa expressão verifica para linhas que são marcadas para exclusão. 
* ``isDelete() -> true``
* ``isDelete(1) -> false``

Se a sua transformação leva mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1.
*********************************
<code>isError</code>
==============================
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Essa expressão verifica para linhas marcadas como erros.
* ``isError() -> true``
* ``isError(1) -> false``

Se a sua transformação leva mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1.
*********************************
<code>isIgnore</code>
==============================
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Essa expressão verifica para linhas marcadas para ser ignorado.
* ``isIgnore() -> true``
* ``isIgnore(1) -> false``

Se a sua transformação leva mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1.
*********************************
<code>isInsert</code>
==============================
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Essa expressão verifica o marcado para inserção de linhas. 
* ``isInsert() -> true``
* ``isInsert(1) -> false``

Se a sua transformação leva mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1.
*********************************
<code>isMatch</code>
==============================
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Essa expressão verifica para linhas correspondidas na pesquisa. 
* ``isMatch() -> true``
* ``isMatch(1) -> false``

Se a sua transformação leva mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1.
*********************************
<code>isNull</code>
==============================
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Essa expressão verifica um valor NULL.
* ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
*********************************
<code>isUpdate</code>
==============================
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Essa expressão verifica para linhas marcadas para atualização. 
* ``isUpdate() -> true``
* ``isUpdate(1) -> false``

Se a sua transformação leva mais de um fluxo de entrada, você pode passar o índice (baseado em 1) do fluxo. O valor padrão para o índice de fluxo é 1.
*********************************
<code>kurtosis</code>
==============================
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão obtém a curtose de uma coluna.
* ``kurtosis(sales) -> 122.12``
*********************************
<code>kurtosisIf</code>
==============================
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão com base em um critério, obtém a curtose de uma coluna.
* ``kurtosisIf(region == 'West', sales) -> 122.12``
*********************************
<code>lag</code>
==============================
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Essa expressão obtém o valor do primeiro parâmetro avaliado *n* linhas antes da linha atual. O segundo parâmetro é o número de linhas a retroceder. O valor padrão é 1. Se não existem tantas linhas, um valor null é retornado, a menos que um valor padrão é especificado.
* ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
*********************************
<code>last</code>
==============================
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Essa expressão obtém o último valor de um grupo de colunas. Se você omitir o segundo parâmetro `ignoreNulls`, a expressão retorna `false`.
* ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
*********************************
<code>lastDayOfMonth</code>
==============================
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Dada uma data, essa expressão obtém a última data do mês.
* ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
*********************************
<code>lead</code>
==============================
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Essa expressão obtém o valor do primeiro parâmetro avaliado *n* linhas após a linha atual. O segundo parâmetro é o número de linhas a avançar. O valor padrão é 1. Se não existem tantas linhas, um valor null é retornado, a menos que um valor padrão é especificado.
* ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
*********************************
<code>least</code>
==============================
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Isso é um operador de comparação menor-than-or-equal. É igual a **<=** operador.
* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
*********************************
<code>left</code>
==============================
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Essa expressão extrai um início da subcadeia de caracteres no índice 1 e usa o número de caracteres. O **esquerdo** operador é igual a **subcadeia de caracteres (str, 1, n)**.
* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
*********************************
<code>length</code>
==============================
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Essa expressão retorna o comprimento da cadeia de caracteres.
* ``length('kiddo') -> 5``
*********************************
<code>lesser</code>
==============================
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Esta é uma comparação menor-que o operador. É igual a **<** operador.
* ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
*********************************
<code>lesserOrEqual</code>
==============================
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Isso é um operador de comparação menor-than-or-equal. É igual a **<=** operador.
* ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
*********************************
<code>levenshtein</code>
==============================
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Essa expressão obtém a distância de Levenshtein entre duas cadeias de caracteres.
* ``levenshtein('boys', 'girls') -> 4``
*********************************
<code>like</code>
==============================
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Nesta expressão, o padrão é uma cadeia de caracteres que é correspondida literalmente. 
* ``like('icecream', 'ice%') -> true``

As exceções são os seguintes símbolos especiais:  
* `_` Isso corresponde a qualquer caractere na entrada. Ele é semelhante ao `.` em expressões regulares do POSIX.
* `%` Isso corresponde a zero ou mais caracteres na entrada. Esse símbolo é semelhante ao `.*` em expressões regulares do POSIX.
* `''` Isso é o caractere de escape. Se um caractere de escape precede um símbolo especial ou outro caractere de escape, o caractere seguinte será correspondido literalmente. Não é possível substituir qualquer outro caractere.
*********************************
<code>locate</code>
==============================
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Essa expressão localiza a posição da subcadeia de caracteres dentro de uma cadeia de caracteres, começando em uma certa posição (com base em 1). Se você omitir a posição, a avaliação começa no início da cadeia de caracteres. Se a posição não for encontrada, 0 será retornado. 
* ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
*********************************
<code>log</code>
==============================
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Essa expressão calcula o valor de log. Você pode fornecer uma base opcional ou um número de Euler, se ele for usado.
* ``log(100, 10) -> 2``
*********************************
<code>log10</code>
==============================
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão usa uma base de 10 para calcular o valor de log.
* ``log10(100) -> 2``
*********************************
<code>lower</code>
==============================
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Esta expressão define uma cadeia de caracteres em letras minúsculas.
* ``lower('GunChus') -> 'gunchus'``
*********************************
<code>lpad</code>
==============================
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
A cadeia de caracteres até que a cadeia de caracteres usando o preenchimento fornecido, essa expressão preenche à esquerda atinge um determinado período. Se a cadeia de caracteres é igual ou maior que o tamanho, ele será considerado uma nenhuma operação (não operacional).
* ``lpad('great', 10, '-') -> '-----great'``
* ``lpad('great', 4, '-') -> 'great'``
* ``lpad('great', 8, '<>') -> '<><great'``
*********************************
<code>ltrim</code>
==============================
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Essa expressão esquerda-corta uma cadeia de caracteres à esquerda. Se um segundo parâmetro não for especificado, a expressão corta o espaço em branco. Caso contrário, ele corta o caractere que especifica o segundo parâmetro.
* ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
*********************************
<code>max</code>
==============================
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Essa expressão obtém o valor máximo de uma coluna.
* ``MAX(sales) -> 12312131.12``
*********************************
<code>maxIf</code>
==============================
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Essa expressão com base em um critério, obtém o valor máximo de uma coluna.
* ``maxIf(region == 'West', sales) -> 99999.56``
*********************************
<code>md5</code>
==============================
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Essa expressão calcula o resumo de MD5 de um conjunto de colunas de tipos de dados primitivos variados. Ele retorna uma cadeia de caracteres hexadecimal de 32 caracteres. 
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``

Você pode usar o **md5** operador para calcular uma impressão digital para uma linha.
*********************************
<code>mean</code>
==============================
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Essa expressão obtém a média dos valores de uma coluna. O **significam** operador é igual a média
* ``mean(sales) -> 7523420.234``
*********************************
<code>meanIf</code>
==============================
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Essa expressão com base em um critério, obtém a média dos valores de uma coluna. O **meanIf** operador é igual a **avgIf**.
* ``meanIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>min</code>
==============================
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Essa expressão obtém o valor mínimo de uma coluna.
* ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
*********************************
<code>minIf</code>
==============================
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Essa expressão com base em um critério, obtém o valor mínimo de uma coluna.
* ``minIf(region == 'West', sales) -> 00.01``
*********************************
<code>minus</code>
==============================
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Essa expressão subtrai números. Por exemplo, ele pode subtrair um número de dias a partir de uma data. O **menos** operador é igual a **-** operador.
* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
*********************************
<code>minute</code>
==============================
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Essa expressão obtém o valor de minuto de um carimbo de hora. 
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``

Você pode passar um fuso horário opcional na forma de `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. O fuso horário local é o padrão.
*********************************
<code>mod</code>
==============================
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
O **mod** operador marca um par de módulo de números. Isso é igual a **%** operador.
* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
*********************************
<code>month</code>
==============================
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Essa expressão obtém o valor de mês de uma data ou carimbo de hora.
* ``month(toDate('2012-8-8')) -> 8``
*********************************
<code>monthsBetween</code>
==============================
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
Essa expressão obtém o número de meses entre duas datas. 
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``

Você pode passar um fuso horário opcional na forma de `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. O fuso horário local é o padrão.
*********************************
<code>multiply</code>
==============================
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Essa expressão multiplica um par de números. O **multiplicar** operador é igual a * operador.
* ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
*********************************
<code>nTile</code>
==============================
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
O **nTile** função divide as linhas para cada partição de janela em *n* buckets, que variam entre 1 e no máximo *n*. Os valores de bucket são diferentes no máximo 1. Se o número de linhas na partição não dividir uniformemente em um número de buckets, cada valor restante é distribuído em um recipiente, começando com o primeiro bucket. 

* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``

O **nTile** função é útil quando você precisa calcular tertiles, Quartis, decis e outras estatísticas de resumidas comuns. A função calcula duas variáveis durante a inicialização. Uma linha extra é adicionada a um bucket regular. As duas variáveis são com base no tamanho da partição atual. 

Durante o cálculo, a função controla do número da linha atual, o número de bucket atual e o número da linha em que o número de buckets será alterado (bucketThreshold). Quando o número de linha alcança o limite de bucket, o valor do bucket aumenta em um. O limite aumenta o tamanho do bucket, mais um extra se o bucket atual será preenchido.
*********************************
<code>negate</code>
==============================
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Essa expressão nega um número. Na verdade números positivos para números negativos e vice-versa.
* ``negate(13) -> -13``
*********************************
<code>nextSequence</code>
==============================
<code><b>nextSequence() => long</b></code><br/><br/>
Essa expressão retorna a próxima sequência exclusiva. O número é consecutivo dentro de uma partição. Ele será prefixado pelo `partitionId`.
* ``nextSequence() -> 12313112``
*********************************
<code>normalize</code>
==============================
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalizar o valor de cadeia de caracteres para separar os caracteres acentuados unicode * ``normalize('boys') -> 'boys'``
*********************************
<code>not</code>
==============================
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
O **não** operador é um operador de negação lógica.
* ``not(true) -> false``
* ``not(premium)``
*********************************
<code>notEquals</code>
==============================
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
O **notEquals** operador é um operador not-equals de comparação. É igual a **! =** operador.
* ``12!=24 -> true``
* ``'abc'!='abc' -> false``
*********************************
<code>null</code>
==============================
<code><b>null() => null</b></code><br/><br/>
Essa expressão retorna um valor nulo. 
* ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``

Use a função **syntax(null())** se uma coluna for nomeada *nulo*. Qualquer operação que usa o operador nulo resulta em NULL.
*********************************
<code>or</code>
==============================
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
O **ou** operador é uma lógica **OR** operador. É igual a **||** operador.
* ``or(true, false) -> true``
* ``true || false -> true``
*********************************
<code>pMod</code>
==============================
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
O **pMod** operador marca o módulo positivo de um par de números.
* ``pmod(-20, 8) -> 4``
*********************************
<code>power</code>
==============================
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão eleva um número à potência de outro.
* ``power(10, 2) -> 100``
*********************************
<code>rank</code>
==============================
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Essa expressão calcula a classificação de um valor em um grupo de valores. O resultado é 1 mais o número de linhas anteriores ou iguais à linha atual na ordenação da partição. Os valores de produzem intervalos na sequência. 
* ``rank(salesQtr, salesAmt) -> 1``

O **classificação** operador funciona até mesmo quando os dados não estão classificados. Ele procura as alterações nos valores.
*********************************
<code>regexExtract</code>
==============================
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Essa expressão extrai uma subcadeia de caracteres de correspondência para um padrão de regex fornecido. O último parâmetro identifica o grupo de correspondência. Se você omitir o último parâmetro, o padrão é 1. 
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``

Use `<regex>`(acento) para corresponder uma cadeia de caracteres sem escape.
*********************************
<code>regexMatch</code>
==============================
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Essa expressão verifica se a cadeia de caracteres corresponde ao padrão de regex fornecido. 
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``

Use `<regex>`(acento) para corresponder uma cadeia de caracteres sem escape.
*********************************
<code>regexReplace</code>
==============================
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Essa expressão substitui todas as ocorrências de um padrão de regex por outra subcadeia de caracteres na cadeia de caracteres especificada.
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``

Use `<regex>`(acento) para corresponder uma cadeia de caracteres sem escape.
*********************************
<code>regexSplit</code>
==============================
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Essa expressão divide uma cadeia de caracteres com base em um delimitador com base no regex. Ele retorna uma matriz de cadeias de caracteres.
* ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
*********************************
<code>replace</code>
==============================
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Essa expressão substitui todas as ocorrências de uma subcadeia de caracteres por outra subcadeia de caracteres na cadeia de caracteres especificada.
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
*********************************
<code>reverse</code>
==============================
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Essa expressão reverte uma cadeia de caracteres.
* ``reverse('gunchus') -> 'suhcnug'``
*********************************
<code>right</code>
==============================
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Essa expressão extrai uma subcadeia de caracteres com número de caracteres à direita. 
* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``

O **certa** função é a mesma subcadeia de caracteres (str, LENGTH(str) - n, n).
*********************************
<code>rlike</code>
==============================
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Essa expressão verifica se a cadeia de caracteres corresponde ao padrão de regex fornecido.
* ``rlike('200.50', '(\d+).(\d+)') -> true``
*********************************
<code>round</code>
==============================
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Dada uma escala opcional e um modo de arredondamento opcional, essa expressão Arredonda um número. Se você omitir a escala, o padrão é 0. Se você omitir o modo, o padrão é ROUND_HALF_UP(5). 

* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``

Estes são os valores de arredondamento:
* 1 - ROUND_UP
* 2 - ROUND_DOWN
* 3 - ROUND_CEILING
* 4 - ROUND_FLOOR
* 5 - ROUND_HALF_UP
* 6 - ROUND_HALF_DOWN
* 7 - ROUND_HALF_EVEN
* 8 - ROUND_UNNECESSARY

*********************************
<code>rowNumber</code>
==============================
<code><b>rowNumber() => integer</b></code><br/><br/>
Essa expressão atribui uma linha sequencial a numeração de linhas em uma janela, começando com 1.
* ``rowNumber() -> 1``
*********************************
<code>rpad</code>
==============================
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Essa expressão preenche à direita, a cadeia de caracteres, o preenchimento fornecido até que a cadeia de caracteres atinge um determinado período. Se a cadeia de caracteres é igual ou maior que o tamanho, ele será considerado uma nenhuma operação (não operacional).
* ``rpad('great', 10, '-') -> 'great-----'``
* ``rpad('great', 4, '-') -> 'great'``
* ``rpad('great', 8, '<>') -> 'great<><'``
*********************************
<code>rtrim</code>rtrim</code>
==============================
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Essa expressão direita-corta uma cadeia de caracteres à esquerda. Se você não especificar o segundo parâmetro, a expressão corta o espaço em branco. Caso contrário, ele remove qualquer caractere que especifica o segundo parâmetro.
* ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
*********************************
<code>second</code>
==============================
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Essa expressão obtém o segundo valor de uma data. 
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``

Você pode passar um fuso horário opcional na forma de `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. O fuso horário local é o padrão.
*********************************
<code>sha1</code>
==============================
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Essa expressão calcula o resumo de SHA-1 de um conjunto de colunas de tipos de dados primitivos variados. Ele retorna uma cadeia de caracteres hexadecimal de 40 caracteres. 
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``

Você pode usar `sha1` para calcular uma impressão digital para uma linha.
*********************************
<code>sha2</code>
==============================
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Essa expressão calcula o resumo de SHA-2 de um conjunto de colunas de variados tipos de dados primitivos, dado um comprimento de bit cujos valores podem ser apenas 0(256), 224, 256, 384, 512. 
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``

Você pode usar `sha2` para calcular uma impressão digital para uma linha.
*********************************
<code>sin</code>
==============================
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão calcula um valor de seno.
* ``sin(2) -> 0.90929742682``
*********************************
<code>sinh</code>
==============================
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão calcula um valor do seno hiperbólico.
* ``sinh(0) -> 0.0``
*********************************
<code>skewness</code>
==============================
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão obtém a distorção de uma coluna.
* ``skewness(sales) -> 122.12``
*********************************
<code>skewnessIf</code>
==============================
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão com base em um critério, obtém a distorção de uma coluna.
* ``skewnessIf(region == 'West', sales) -> 122.12``
*********************************
<code>slice</code>
==============================
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Essa expressão extrai um subconjunto de uma matriz de uma posição. A posição se baseia em 1. Se você omitir o comprimento, o padrão é o fim da cadeia de caracteres.
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
*********************************
<code>soundex</code>
==============================
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Essa expressão obtém o código soundex para a cadeia de caracteres.
* ``soundex('genius') -> 'G520'``
*********************************
<code>split</code>
==============================
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Essa expressão divide uma cadeia de caracteres com base em um delimitador. Ele retorna uma matriz de cadeias de caracteres.
* ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
*********************************
<code>sqrt</code>
==============================
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão calcula a raiz quadrada de um número.
* ``sqrt(9) -> 3``
*********************************
<code>startsWith</code>
==============================
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Essa expressão verifica se a cadeia de caracteres começa com a cadeia de caracteres fornecida.
* ``startsWith('great', 'gr') -> true``
*********************************
<code>stddev</code>
==============================
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão obtém o desvio padrão de uma coluna.
* ``stdDev(sales) -> 122.12``
*********************************
<code>stddevIf</code>
==============================
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão com base em um critério, obtém o desvio padrão de uma coluna.
* ``stddevIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevPopulation</code>
==============================
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão obtém o desvio padrão da população de uma coluna.
* ``stddevPopulation(sales) -> 122.12``
*********************************
<code>stddevPopulationIf</code>
==============================
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão com base em um critério, obtém o desvio padrão da população de uma coluna.
* ``stddevPopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevSample</code>
==============================
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão obtém o desvio padrão de uma coluna.
* ``stddevSample(sales) -> 122.12``
*********************************
<code>stddevSampleIf</code>
==============================
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão com base em um critério, obtém o desvio padrão de uma coluna.
* ``stddevSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>subDays</code>
==============================
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Essa expressão subtrai meses a partir de uma data. 
* ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
O **subDays** operador é igual a **-** operador para data.
*********************************
<code>subMonths</code>
==============================
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Essa expressão subtrai meses a partir de uma data ou carimbo de hora.
* ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
*********************************
<code>substring</code>
==============================
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Essa expressão extrai uma subcadeia de caracteres de um determinado tamanho de uma posição. A posição se baseia em 1. Se você omitir o comprimento, o padrão é o fim da cadeia de caracteres.
* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
*********************************
<code>sum</code>
==============================
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Essa expressão obtém a soma de agregação de uma coluna numérica.
* ``sum(col) -> value``
*********************************
<code>sumDistinct</code>
==============================
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Essa expressão obtém a soma de agregação de valores distintos de uma coluna numérica.
* ``sumDistinct(col) -> value``
*********************************
<code>sumDistinctIf</code>
==============================
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Essa expressão com base em um critério, obtém a soma de agregação de uma coluna numérica. Você pode basear a condição em qualquer coluna.
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
*********************************
<code>sumIf</code>
==============================
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Essa expressão com base no critério, obtém a soma de agregação de uma coluna numérica. Você pode basear a condição em qualquer coluna.
* ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
*********************************
<code>tan</code>
==============================
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão calcula um valor de tangente.
* ``tan(0) -> 0.0``
*********************************
<code>tanh</code>
==============================
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão calcula um valor de tangente hiperbólico.
* ``tanh(0) -> 0.0``
*********************************
<code>toBoolean</code>
==============================
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Essa expressão converte um valor de `('t', 'true', 'y', 'yes', '1')` como true. Ele converte `('f', 'false', 'n', 'no', '0')` como false. Para qualquer outro valor, ele retorna NULL.
* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
*********************************
<code>toDate</code>
==============================
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Dado um formato de data opcional, essa expressão converte uma cadeia de caracteres em uma data. Consulte Java SimpleDateFormat para todos os formatos de data possível. 
* ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``

Se você omitir o formato de data, as combinações das opções a seguir são aceitas: [yyyy yyyy, yyyy-[M] M, d de-[M] M-[d], yyyy-[M] M-[d] d, AAAA-[M] M-[d] d, AAAA-[M] M-[d] dT *].
*********************************
<code>toDecimal</code>
==============================
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : integral], [<i>&lt;value3&gt;</i> : integral], [<i>&lt;value4&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Essa expressão Converte qualquer numérico ou cadeia de caracteres em um valor decimal. 
* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``

Se você não especificar a precisão e escala, o padrão é (10,2). Você pode usar um formato decimal opcional do Java para a conversão.

*********************************
<code>toDouble</code>
==============================
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => double</b></code><br/><br/>
Essa expressão Converte qualquer numérico ou cadeia de caracteres em um valor duplo. Você pode usar um formato decimal opcional do Java para a conversão.
* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
*********************************
<code>toFloat</code>
==============================
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => float</b></code><br/><br/>
Essa expressão Converte qualquer numérico ou cadeia de caracteres em um valor flutuante. Você pode usar um formato decimal opcional do Java para a conversão. 
* ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``

O **toFloat** função trunca qualquer dupla.
*********************************
<code>toInteger</code>
==============================
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => integer</b></code><br/><br/>
Essa expressão Converte qualquer numérico ou cadeia de caracteres em um valor inteiro. Você pode usar um formato decimal opcional do Java para a conversão. 
* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``

O **toInteger** função trunca qualquer long, float, ou clique duas vezes.
*********************************
<code>toLong</code>
==============================
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => long</b></code><br/><br/>
Essa expressão Converte qualquer numérico ou cadeia de caracteres em um valor longo. Você pode usar um formato decimal opcional do Java para a conversão. 
* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``

O **toLong** função trunca qualquer float ou duplos.
*********************************
<code>toShort</code>
==============================
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => short</b></code><br/><br/>
Essa expressão Converte qualquer numérico ou cadeia de caracteres em um valor resumido. Você pode usar um formato decimal opcional do Java para a conversão. 
* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``

O **toShort** função trunca qualquer inteiro, long, float, ou clique duas vezes.
*********************************
<code>toString</code>
==============================
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Essa expressão converte um tipo de dados primitivos em uma cadeia de caracteres. 
* ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``

Você pode especificar um formato para números e datas. Se você não especificar um formato, o padrão do sistema é usado. O formato padrão é `yyyy-MM-dd`. Os números seguir o formato decimal de Java. Para todos os formatos de data possíveis, consulte Java SimpleDateFormat. 
*********************************
<code>toTimestamp</code>
==============================
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Dado um formato de carimbo de hora opcional, essa expressão converte uma cadeia de caracteres em uma data. 
* ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``

Consulte Java SimpleDateFormat para todos os formatos possíveis. Se você omitir o carimbo de hora, o modelo padrão é `yyyy-[M]M-[d]d hh:mm:ss[.f...]` é usado.
*********************************
<code>toUTC</code>
==============================
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Essa expressão converte o carimbo de hora em UTC. 
* ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``

Você pode passar um fuso horário opcional na forma de `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. O padrão é o fuso horário atual.
*********************************
<code>translate</code>
==============================
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Essa expressão substitui um conjunto de caracteres com outro conjunto de caracteres na cadeia de caracteres. Caracteres têm uma substituição de um para um.
* ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
*********************************
<code>trim</code>
==============================
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Essa expressão corta uma cadeia de caracteres à esquerda e à direita. 
* ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``

Se você não especificar o segundo parâmetro, a função corta o espaço em branco. Caso contrário, ele remove qualquer caractere que especifica o segundo parâmetro.

*********************************
<code>true</code>
==============================
<code><b>true() => boolean</b></code><br/><br/>
Essa expressão sempre retornará uma `true` valor.  
* ``isDiscounted == true()``
* ``isDiscounted() == true``

Se a coluna é nomeada *verdadeira*, use a função **syntax(true())**.
*********************************
<code>typeMatch</code>
==============================
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Essa expressão corresponde ao tipo da coluna. 
* ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``

Use essa função somente em expressões padrão: número corresponde à short, integer, long, double, float ou decimal, integral corresponde resumo, inteiro, longo, fração correspondências double, float, decimal e datetime corresponde ao tipo de data ou carimbo de hora.
*********************************
<code>upper</code>
==============================
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Esta expressão define uma cadeia de caracteres em letras maiusculas.
* ``upper('bojjus') -> 'BOJJUS'``
*********************************
<code>variance</code>
==============================
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão obtém a variância de uma coluna.
* ``variance(sales) -> 122.12``
*********************************
<code>varianceIf</code>
==============================
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão com base em um critério, obtém a variância de uma coluna.
* ``varianceIf(region == 'West', sales) -> 122.12``
*********************************
<code>variancePopulation</code>
==============================
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão obtém a variância da população de uma coluna.
* ``variancePopulation(sales) -> 122.12``
*********************************
<code>variancePopulationIf</code>
==============================
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão com base em um critério, obtém a variância da população de uma coluna.
* ``variancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>varianceSample</code>
==============================
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão obtém a variância não polarizada de uma coluna.
* ``varianceSample(sales) -> 122.12``
*********************************
<code>varianceSampleIf</code>
==============================
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Essa expressão com base em um critério, obtém a variância não polarizada de uma coluna.
* ``varianceSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>weekOfYear</code>
==============================
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Dada uma data, essa expressão obtém a semana do ano.
* ``weekOfYear(toDate('2008-02-20')) -> 8``
*********************************
<code>xor</code>
==============================
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Essa expressão usa a lógica **xor** operador. Esse operador é igual a **^** operador.
* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
*********************************
<code>year</code>
==============================
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Essa expressão dada uma data, obtém o valor de ano.
* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Próximas etapas

[Saiba como usar o construtor de expressões](concepts-data-flow-expression-builder.md).
