<properties
   pageTitle="O hash de distribuição e seu efeito no desempenho da consulta no SQL Data Warehouse | Microsoft Azure"
   description="Saiba mais sobre tabelas de hash distribuído e como elas afetam o desempenho da consulta no SQL Data Warehouse Azure para desenvolvimento de soluções."
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

# Distribuição hash e seu efeito no desempenho da consulta no SQL Data Warehouse

Tornar as decisões de distribuição hash inteligentes é uma das maneiras mais importantes para melhorar o desempenho da consulta.

Na verdade, há três fatores principais:

1. Minimizar a Movimentação de Dados
2. Evitar Distorção de Dados
3. Fornecer Execução Equilibrada

## Minimizar a movimentação de dados
A movimentação de dados geralmente surge quando tabelas são unidas ou são executadas agregações em tabelas. As tabelas de distribuição hash em uma chave compartilhada é um dos métodos mais eficientes para minimizar esse movimento.

No entanto, para a distribuição hash ser eficaz para minimizar a movimentação, todos os seguintes critérios devem ser verdadeiros:

1. Ambas as tabelas devem estar distribuídas em hash e estarem unidas na chave compartilhada de distribuição
2. Os tipos de dados de ambas as colunas precisam corresponder
3. A junção de colunas precisa ser junções equitativas (ou seja, os valores na coluna da tabela à esquerda precisam ser iguais aos valores na coluna da tabela à direita)
4. A junção é **não** um `CROSS JOIN`

> [AZURE.NOTE]As colunas usadas nas cláusulas `JOIN`, `GROUP BY`, `DISTINCT` e `HAVING` todas são candidatas para boas colunas HASH. Em outras colunas disponíveis na cláusula `WHERE`, **não** a torna boa candidata para coluna hash . Consulte a seção sobre execução equilibrada abaixo.

A movimentação de dados também pode surgir na sintaxe da consulta (`COUNT DISTINCT` e a cláusula `OVER` as duas sendo ótimos exemplos) quando usada com colunas que não incluem a chave de distribuição hash.

> [AZURE.NOTE]Tabelas de Repetição Alternada normalmente geram movimento de dados. Os dados na tabela foram alocados de maneira não determinística e então os dados devem primeiro ser movidos antes que a maioria das consultas seja concluída.

## Evitar distorção de dados
Para a distribuição hash ser eficaz, é importante que a coluna escolhida exiba as seguintes propriedades:

1. A coluna contém um número significativo de valores distintos.
2. A coluna não sofre de **distorção de dados**.

Cada valor distinto será alocado para uma distribuição. Consequentemente, os dados exigirão um número razoável de valores distintos para garantir que valores de hash exclusivos o suficiente sejam gerados. Caso contrário, poderá obter um hash de baixa qualidade. Se o número de distribuições exceder o número de valores distintos, por exemplo, então algumas distribuições ficarão vazias. Isso prejudicaria o desempenho.

Da mesma forma, se todas as linhas para a coluna na qual foi feito o hash, continham o mesmo valor, então os dados são considerados **distorcidos**. Neste caso extremo, apenas um valor hash teria sido criado, resultando em todas as linhas terminando dentro de uma única distribuição. Teoricamente, cada valor distinto na coluna hash teria o mesmo número de linhas.

> [AZURE.NOTE]As tabelas de repetição alternada não apresentam sinais de distorção. Isso ocorre porque os dados são armazenados uniformemente entre as distribuições.

## Fornecer execução equilibrada
A execução equilibrada é obtida quando cada distribuição possui a mesma quantidade de trabalho a ser executado. O Massively Parallel Processing (MPP - Processamento paralela em larga escala) é um jogo de equipe. Todos tem de cruzar a linha antes que qualquer um possa ser declarado o vencedor. Se cada distribuição possui a mesma quantidade de trabalho (ou seja, dados a serem processados), então todas as consultas serão concluídas aproximadamente ao mesmo tempo. Isso é conhecido como execução equilibrada.

Como foi visto, a distorção de dados pode afetar a execução equilibrada. No entanto, como é possível escolher a chave de distribuição hash. Se uma coluna foi escolhida porque aparece na cláusula `WHERE` de uma consulta, é bastante provável que a consulta não será equilibrada.

> [AZURE.NOTE]A cláusula `WHERE` normalmente ajuda a identificar colunas que são melhor usadas para particionamento.

Um bom exemplo de uma coluna que aparece na cláusula `WHERE` seria um campo de data. Campos de data são exemplos clássicos de excelentes colunas de particionamento, mas são geralmente colunas de distribuição hash ruins. Normalmente, as consultas de data warehouse são durante um período de tempo especificado como dia, semana ou mês. A distribuição de hash por data, pode limitar realmente o dimensionamento e prejudicar o desempenho. Se, por exemplo, o intervalo de datas especificado foi para uma semana, ou seja, 7 dias, o número máximo de hashes seria 7 - um para cada dia. Isso significa que apenas 7 das distribuições conteriam dados. As distribuições restantes não teriam nenhum dado. Isso resultaria em uma execução de consulta desequilibrada, pois apenas 7 distribuições estão processando dados.

> [AZURE.NOTE]Tabelas de repetição alternada normalmente fornecem execução equilibrada. Isso ocorre porque os dados são armazenados uniformemente entre as distribuições.

## Recomendações
Para maximizar o desempenho e o rendimento geral da consulta tente e garanta que suas tabelas distribuídas de hash seguem esse padrão tanto quanto possível:

Chave de distribuição de hash:

1. É usada em cláusulas `JOIN`, `GROUP BY`, `DISTINCT`, ou `HAVING` em suas consultas.
2. Não é usada em cláusulas `WHERE`
3. Possui muitos valores diferentes, pelo menos 1000.
4. Não tem um desproporcionalmente grande número de linhas que serão de hash para um pequeno número de distribuições.
5. É definida como NOT NULL. Linhas NULL serão reunidas em uma única distribuição.

## Resumo

Distribuição de hash pode ser resumida como segue:

- A função de hash é determinística. O mesmo valor sempre é atribuído para a mesma distribuição.
- Uma única coluna é usada como coluna de distribuição. A função de hash usa a coluna indicada para computar as atribuições de linha para as distribuições.
- A função de hash é baseada no tipo de coluna e não nos próprios valores
- Ao distribuir hash em uma tabela, pode resultar em uma tabela distorcida
- Tabelas de hash distribuído normalmente exigem menos movimento de dados ao resolver consultas e, portanto, melhoram o desempenho de grandes tabelas de fatos.
- Observe as recomendações para a seleção de coluna de hash distribuído para aumentar a taxa de transferência de consulta.

> [AZURE.NOTE]Em tipo de dados do SQL Data Warehouse a consistência importa! Verifique se o esquema existente é consistente, usando o mesmo tipo para uma coluna. Isso é especialmente importante para a chave de distribuição. Se os tipos de dados da chave de distribuição não são sincronizados e as tabelas são unidas, ocorrerá a movimentação desnecessária de dados. Isso pode ser caro, se as tabelas são grandes e resultaria em redução do desempenho e da taxa de transferência.


## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Oct15_HO1-->