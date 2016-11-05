---
title: Nível de compatibilidade, como avaliar | Microsoft Docs
description: Etapas e ferramentas para determinar qual é o melhor nível de compatibilidade para seu banco de dados no Banco de Dados SQL do Azure ou no Microsoft SQL Server
services: sql-database
documentationcenter: ''
author: alainlissoir
manager: jhubbard
editor: ''

ms.service: sql-database
ms.workload: data-management
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.topic: article
ms.date: 08/08/2016
ms.author: alainl

---
# Desempenho aprimorado de consultas com nível de compatibilidade 130 no Banco de Dados SQL do Azure
O Banco de Dados SQL do Azure está executando de modo transparente centenas de milhares de bancos de dados em vários níveis de compatibilidade diferentes, preservando e garantindo a compatibilidade com versões anteriores à versão correspondente do Microsoft SQL Server para todos os seus clientes!

Portanto, nada impede que os clientes que alteram qualquer banco de dados existente para o nível de compatibilidade mais recente aproveitem os novos recursos de otimizador de consulta e processador de consulta. Como um lembrete do histórico, os alinhamentos das versões do SQL com os níveis de compatibilidade padrão são:

* 100: no SQL Server 2008 e no Banco de Dados SQL do Azure V11.
* 110: no SQL Server 2012 e no Banco de Dados SQL do Azure V11.
* 120: no SQL Server 2014 e no Banco de Dados SQL do Azure V12.
* 130: no SQL Server 2016 e no Banco de Dados SQL do Azure V12.

> [!IMPORTANT]
> A partir de **meados de junho de 2016**, no Banco de Dados SQL do Azure, o nível de compatibilidade padrão será de 130, em vez de 120 para bancos de dados **recém-criados**.
> 
> Bancos de dados criados antes de meados de junho de 2016 *não* serão afetados e manterão seu nível de compatibilidade atual (100, 110 ou 120). Bancos de dados que migrem do Banco de Dados SQL do Azure versão V11 para V12 não terão seu nível de compatibilidade alterado.
> 
> 

Neste artigo, exploraremos os benefícios do nível de compatibilidade 130 e como aproveitar esses benefícios. Vamos abordar os possíveis efeitos colaterais sobre o desempenho para os aplicativos SQL existentes.

## Sobre o nível de compatibilidade 130
Primeiro, se você quiser saber o nível de compatibilidade atual do seu banco de dados, execute a instrução Transact-SQL a seguir.

```
SELECT compatibility_level
    FROM sys.databases
    WHERE name = '<YOUR DATABASE_NAME>’;
```


Antes que essa alteração para o nível 130 ocorra para bancos de dados **recém**-criados, vamos examinar do que se trata essa alteração com alguns exemplos de consulta muito básicos e ver como qualquer pessoa pode se beneficiar com ela.

Processamento de consultas em bancos de dados relacionais pode ser muito complexo e pode exigir muita ciência da computação e matemática para entender as opções de design e os comportamentos inerentes. Neste documento, o conteúdo foi intencionalmente simplificado para garantir que qualquer pessoa com algum conhecimento técnico mínimo possa entender o impacto da alteração do nível de compatibilidade e determinar como ele pode beneficiar os aplicativos.

Vamos dar uma olhada rápida em o que o nível de compatibilidade 130 traz para o cenário. Você pode encontrar mais detalhes em [ALTERAR o nível de compatibilidade do BANCO DE DADOS (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx), mas aqui está um breve resumo:

* A operação Insert de uma instrução Insert-select pode ser multi-threaded ou pode ter um plano paralelo, enquanto essa operação antes era de single-thread.
* Tabela com Otimização de Memória e consultas de variáveis de tabela agora podem ter planos paralelos, enquanto antes essa operação também era de single-thread.
* As estatísticas de tabela com Otimização de Memória agora podem ser utilizadas e são atualizadas automaticamente. Consulte [O que há de novo no mecanismo de banco de dados: OLTP na memória](https://msdn.microsoft.com/library/bb510411.aspx#InMemory) para obter mais detalhes.
* Modo de lote vs. Modo de Linha muda com índices de Repositório de Coluna
  * As classificações em uma tabela com um índice de Repositório de Coluna agora estão em modo de lote.
  * As agregações em janela agora operam em modo de lote como instruções TSQL LAG/LEAD.
  * Consultas em tabelas de Repositório de Coluna com Múltiplas cláusulas distintas operam em modo de Lote.
  * Consultas em execução com DOP = 1 ou um plano serial também são executadas em Modo de Lote.
* Por fim, melhorias de Estimativa de Cardinalidade estão, na verdade, vindo com o nível de compatibilidade 120, mas para quem está executando em um nível de Compatibilidade inferior (ou seja, 100 ou 110), a mudança para o nível de compatibilidade 130 também trará esses aprimoramentos, e eles também podem beneficiar o desempenho da consulta de seus aplicativos.

## Praticando o nível de compatibilidade 130
Primeiro, vejamos algumas tabelas, índices e dados aleatórios criados para praticar alguns desses novos recursos. Os exemplos de script TSQL podem ser executados no SQL Server 2016 ou no Banco de Dados SQL do Azure. No entanto, ao criar um Banco de Dados SQL do Azure, escolha pelo menos o banco de dados P2, pois precisará de pelo menos dois núcleos para permitir que multi-threads e, portanto, para aproveitar esses recursos.

```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
    (EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
    (Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
    (c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
    (‘Blue’, RAND() * 100000, RAND() * 100000),
    (‘Yellow’, RAND() * 100000, RAND() * 100000),
    (‘Red’, RAND() * 100000, RAND() * 100000),
    (‘Green’, RAND() * 100000, RAND() * 100000),
    (‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


Agora, vamos dar uma olhada em alguns dos recursos de Processamento de Consulta que estão vindo com o nível de compatibilidade 130.

## INSERT paralela
Executar as instruções TSQL abaixo executa a operação de INSERT no nível de compatibilidade 120 e 130, que executa, respectivamente, a operação INSERT em um modelo com single-thread (120) e em um modelo com multi-thread (130).

```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


Solicitando o plano de consulta real, examinando sua representação gráfica ou seu conteúdo XML, você pode determinar qual função está de Estimativa de Cardinalidade está em jogo. Observando a planos lado a lado na Figura 1, podemos ver claramente que a execução de INSERT do Repositório de Coluna vai de serial em 120 a paralela em 130. Além disso, observe a alteração do ícone do iterador no plano 130 mostrando duas setas paralelas, ilustrando o fato de que agora a execução do iterador é, realmente, paralela. Se você tiver grandes operações de INSERT para concluir, a execução paralela, vinculada ao número de núcleos que você tem à sua disposição para o banco de dados, terá um melhor desempenho; dependendo da sua situação, até 100 vezes mais rápido!

*Figura 1: a operação de INSERT muda de serial para paralela com compatibilidade de nível 130.*

![A figura 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)

## Modo de lote SERIAL
Da mesma forma, passar para o nível de compatibilidade 130 ao processar linhas de dados habilita o processamento no modo de lote. Primeiro, operações no modo de lote só estão disponíveis quando você tem um índice de repositório de coluna em vigor. Em segundo lugar, um lote normalmente representa ~900 linhas e usa uma lógica de código otimizada para vários núcleos de CPU, maior taxa de transferência de memória e aproveita diretamente os dados compactados do Repositório de Coluna, sempre que possível. Nessas condições, o SQL Server 2016 pode processar ~900 linhas ao mesmo tempo, em vez de uma linha por vez, assim, o custo geral de sobrecarga da operação agora é compartilhado por todo o lote, reduzindo o custo geral por linha. Essa quantidade compartilhada de operações combinada com a compactação do repositório de coluna basicamente reduz a latência envolvida em uma operação de modo de lote SELECT. Você pode encontrar mais detalhes sobre o repositório de coluna e o modo de lote no [Guia de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO 

– The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Como visível abaixo, observando os planos de consulta lado a lado na figura 2, podemos ver que o modo de processamento mudou com o nível de compatibilidade e, assim, ao executar as consultas em ambos os nível de compatibilidade de uma única vez, podemos ver que a maior parte do tempo de processamento é gasto no modo de linha (86%) em comparação ao modo em lote (% 14), em que os 2 lotes foram processados. Aumentar o conjunto de dados aumentará o benefício.

*Figura 2: a operação SELECT muda do modo serial para o modo de lote com o nível de compatibilidade 130.*

![Figura 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)

## Modo em lotes em Execução de Classificação
Semelhante ao acima, mas aplicado a uma operação de classificação, a transição do modo de linha (nível de compatibilidade 120) para o modo de lote (nível de compatibilidade 130) melhora o desempenho da operação SORT pelas mesmas razões.

```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Visível lado a lado na figura 3, podemos ver que a operação de classificação no modo linha representa 81% do custo, enquanto o modo em lote representa apenas 19% do custo (respectivamente, 81% e 56% na classificação em si).

*Figura 3: a operação SORT muda do modo de linha para o em lotes com o nível de compatibilidade 130.*

![A figura 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)

Obviamente, esses exemplos contêm apenas dezenas de milhares de linhas, o que não é nada ao examinar os dados disponíveis na maioria dos SQL Servers hoje em dia. Apenas projete isso com relação a milhões de linhas, e isso poderá se converter em vários minutos de execução poupados diariamente dependendo da natureza da sua carga de trabalho.

## Aprimoramentos de CE (Estimativa de Cardinalidade)
Introduzida com o SQL Server 2014, qualquer banco de dados em execução em um nível de compatibilidade 120 ou acima usará a nova funcionalidade de Estimativa de Cardinalidade. Essencialmente, a estimativa de cardinalidade é a lógica usada para determinar como o SQL Server executará uma consulta com base em seu custo estimado. A estimativa é calculada usando dados de estatísticas associadas aos objetos envolvidos na consulta. Na prática, em um alto nível, funções de Estimativa de Cardinalidade são estimativas de contagem de linha juntamente com informações sobre a distribuição de valores, contagens de valores distintos e contagens duplicadas contidas nas tabelas e nos objetos referenciados na consulta. Errar essas estimativas pode levar a E/S de disco desnecessárias devido a concessões de memória insuficientes (ou seja, derramamentos de TempDB) ou a uma seleção de uma execução de plano serial sobre a execução de um plano paralelo, para citar alguns exemplos. Em conclusão, estimativas incorretas podem levar a uma degradação do desempenho geral da execução da consulta. Por outro lado, melhores estimativas, estimativas mais precisas, levam a execuções de consulta melhores!

Como mencionado anteriormente, estimativas e otimizações de consulta são uma questão complexa, mas, se você quiser saber mais sobre planos de consulta e o avaliador de cardinalidade, consulte o documento em [Otimizar seus planos de consulta com o avaliador de cardinalidade do SQL Server 2014](https://msdn.microsoft.com/library/dn673537.aspx) para se aprofundar.

## Que Estimativa de Cardinalidade você usa atualmente?
Para determinar em qual Estimativa de Cardinalidade suas consultas estão sendo executadas, vamos simplesmente usar os exemplos de consulta abaixo. Observe que este primeiro exemplo será executado no nível de compatibilidade 110, indicando o uso das funções de estimativa de cardinalidade antigas.

```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Quando a execução estiver concluída, clique no link de XML e examine as propriedades do primeiro iterador conforme mostrado abaixo. Observe o nome da propriedade chamada CardinalityEstimationModelVersion definido no momento em 70. Isso significa que o nível de compatibilidade do banco de dados está definido para a versão do SQL Server 7.0 (definido em 110 como visíveis nas instruções TSQL acima), mas o valor 70 simplesmente representa a funcionalidade de estimativa de cardinalidade herdada disponível desde o SQL Server 7.0, que não teve revisões importantes até o SQL Server 2014 (que vem com um nível de compatibilidade de 120).

*Figura 4: a CardinalityEstimationModelVersion é definida para 70 ao usar um nível de compatibilidade 110 ou abaixo.*

![Figura 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)

Como alternativa, você pode alterar o nível de compatibilidade para 130 e desabilitar o uso da nova função de Estimativa de Cardinalidade usando o LEGACY\_CARDINALITY\_ESTIMATION definido como ON com [ALTERAR CONFIGURAÇÃO DE ESCOPO DO BANCO DE DADOS](https://msdn.microsoft.com/library/mt629158.aspx). Isso será exatamente o mesmo que usar 110 de um ponto de vista de função de Estimativa de Cardinalidade enquanto se usa o nível de compatibilidade de processamento de consulta mais recente. Ao fazer isso, você pode se beneficiar dos novos recursos de processamento de consulta disponíveis com o nível de compatibilidade mais recente (ou seja, o modo de lote), mas ainda contar com a funcionalidade de Estimativa de Cardinalidade antiga, se necessário.

```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Simplesmente passar para o nível de compatibilidade 120 ou 130 habilita a nova funcionalidade de Estimativa de Cardinalidade. Nesse caso, o padrão CardinalityEstimationModelVersion será definido de acordo para 120 ou 130, como visível a seguir.

```
-- New CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*Figura 5: o CardinalityEstimationModelVersion é definido para 130 ao usar um nível de compatibilidade de 130.*

![Figura 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)

## Testemunhando as diferenças de Estimativa de Cardinalidade
Agora vamos executar uma consulta um pouco mais complexa que envolve uma INNER JOIN com uma cláusula WHERE com alguns predicados e vamos examinar a estimativa de contagem de linha da função de Estimativa de Cardinalidade antiga primeiro.

```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


A execução desta consulta efetivamente retorna 200.704 linhas, enquanto a estimativa de linha com a funcionalidade de Estimativa de Cardinalidade antiga reivindica 194.284 linhas. Obviamente, como dito antes, esses resultados de contagem de linha também dependerão da frequência com que você executou os exemplos anteriores, que preenchem as tabelas de exemplo repetidamente a cada execução. Obviamente, os predicados na consulta também terão uma influência sobre a estimativa real além da forma de tabela, do conteúdo de dados e de como esses dados realmente se correlacionam entre si.

*Figura 6: a estimativa de contagem de linhas é de 194.284 ou 6.000 linhas aquém das 200.704 linhas esperadas.*

![Figura 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)

Da mesma forma, agora vamos executar a mesma consulta com a nova funcionalidade de Estimativa de Cardinalidade.

```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Observando o abaixo, agora vemos que a estimativa de linha é de 202.877, ou muito perto e maior do que a Estimativa de Cardinalidade antiga.

*Figura 7: a estimativa de contagem de linha agora é de 202.877, em vez de 194.284.*

![Figura 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)

Na realidade, o conjunto de resultados é de 200.704 linhas (mas tudo depende de quantas vezes você executou as consultas dos exemplos anteriores; mas, mais importante, porque o TSQL usa a instrução RAND(), os valores reais retornados podem variar de uma execução para a próxima). Portanto, neste exemplo específico, a nova Estimativa de Cardinalidade faz um trabalho melhor de estimar o número de linhas, pois 202.877 está muito mais próximo de 200.704, que 194.284! Por fim, se você alterar os predicados da cláusula WHERE para igualdade (em vez de ">", por exemplo), isso poderá tornar as estimativas entre as funções de Cardinalidade antiga e nova ainda mais diferentes, dependendo de quantas correspondências você pode obter.

Obviamente, nesse caso, estar ~6000 aquém da contagem real não representa muitos dados em algumas situações. Agora, transponha isso para milhões de linhas em várias tabelas e consultas mais complexas e, às vezes, a estimativa poderá ter uma diferença de milhões de linhas, portanto, correndo o risco de selecionar o plano de execução incorreto ou solicitar concessões de memória insuficientes, levando a derramamentos de TempDB e, portanto, as E/S são muito maiores.

Se você tiver a oportunidade, pratique essa comparação com suas consultas e conjuntos de dados mais comuns e veja você mesmo em quanto algumas das estimativas novas e antigas são afetadas, enquanto algumas podem estar mais distantes da realidade, ou outras simplesmente mais perto das contagens de linhas reais de fato retornadas nos conjuntos de resultados. Tudo isso depende da forma de suas consultas, das características do Banco de Dados SQL do Azure, da natureza e do tamanho de seus conjuntos de dados e das estatísticas disponíveis sobre eles. Se você tiver acabado de criar sua instância do Banco de Dados SQL do Azure, o otimizador de consulta precisará criar seu conhecimento do zero, em vez de reutilizar estatísticas criadas com base em execuções de consulta anterior. Portanto, as estimativas são muito contextuais e quase específicas a cada situação de servidor e aplicativo. Ele é um aspecto importante para ter em mente!

## Algumas considerações para levar em conta
Embora a maioria das cargas de trabalho possa se beneficiar do nível de compatibilidade 130, antes de adotar o nível de compatibilidade para o seu ambiente de produção, você tem basicamente 3 opções:

1. Você passa para o nível de compatibilidade 130 e examina como fica o desempenho. Caso você perceba algum regressão, simplesmente define o nível de compatibilidade de volta ao nível original ou mantém 130 e apenas reverte a Estimativa de Cardinalidade de volta para o modo herdado (conforme explicado anteriormente, isso sozinho pode resolver o problema).
2. Você testa seus aplicativos existentes sob uma carga de produção semelhante, ajusta e valida o desempenho antes de entrar em produção. No caso de problemas, da mesma foram que acima, você pode voltar para o nível de compatibilidade original ou simplesmente reverter a Estimativa de Cardinalidade de volta para o modo herdado.
3. Como uma opção final e a maneira mais recente de tratar essas questões, aproveite o Repositório de Consultas. Essa é a opção recomendada de hoje! Para ajudar na análise de suas consultas em compatibilidade de nível 120 ou abaixo versus 130, nunca é suficiente incentivar o uso do Repositório de Consultas. O Repositório de Consultas está disponível com a versão mais recente do Banco de Dados SQL do Azure V12 e foi projetada para ajudá-lo a solucionar problemas de desempenho de consulta. Considere o Repositório de Consultas um gravador de dados de voo para seu banco de dados, coletando e apresentando informações históricas detalhadas sobre todas as consultas. Isso simplifica bastante a forense de desempenho, reduzindo o tempo para diagnosticar e resolver problemas. Você pode encontrar mais informações no artigo sobre [Repositório de Consultas: o gravador de dados de voo para o banco de dados](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/).

No geral, se você já tiver um conjunto de bancos de dados em execução no nível de compatibilidade 120 ou abaixo e planejar mover alguns deles para 130, ou porque sua carga de trabalho provisionará novos bancos de dados que logo serão automaticamente definidos por padrão como 130, considere o seguinte:

* Antes de alterar para o novo nível de compatibilidade em produção, habilite o Repositório de Consultas. Você pode consultar o artigo sobre como [alterar o modo de compatibilidade do banco de dados e usar o armazenamento de consulta](https://msdn.microsoft.com/library/bb895281.aspx) para obter mais informações.
* Em seguida, teste todas as cargas de trabalho críticas usando dados representativos e consultas de um ambiente de produção e compare o desempenho observado e o relatado pelo Repositório de Consulta. Se você sofrer algumas regressões, poderá identificar as consultas retornadas com o Repositório de Consulta e usar a opção de imposição de plano do Repositório de Consultas (também conhecido como fixação de plano). Nesse caso, você definitivamente fica com o nível de compatibilidade 130 e usa o plano de consulta anterior conforme sugerido pelo Repositório de Consultas.
* Se você desejar aproveitar os novos recursos e funcionalidades do Banco de Dados SQL do Azure (que está executando o SQL Server 2016), mas for sensível às mudanças trazidas pelo nível de compatibilidade 130, como último recurso, você poderá considerar forçar o nível de compatibilidade de volta para o nível adequado de sua carga de trabalho usando uma instrução ALTER DATABASE. Mas primeiro, lembre-se de que a opção de fixação de plano do Repositório de Consultas é sua melhor opção, porque não usar 130 basicamente é permanecer no nível de funcionalidade de uma versão mais antiga do SQL Server.
* Se você tiver aplicativos multilocatários abrangendo vários bancos de dados, poderá ser necessário atualizar a lógica de provisionamento dos bancos de dados para garantir um nível de compatibilidade consistente em todos os bancos de dados; tantos nos antigos quanto nos recentemente configurados. O desempenho de carga de trabalho do aplicativo pode ser sensível ao fato de que alguns bancos de dados estão em execução em diferentes níveis de compatibilidade, portanto, pode ser necessária consistência de nível de compatibilidade entre quaisquer bancos de dados de para oferecer a mesma experiência aos clientes de um modo geral. Observe que isso não é uma exigência, realmente depende de como seu aplicativo é afetado pelo nível de compatibilidade.
* Por fim, em relação à Estimativa de Cardinalidade e como alterar o nível de compatibilidade, antes de continuar na produção, é recomendável testar sua carga de trabalho de produção em novas condições para determinar se seu aplicativo se beneficia das melhorias de Estimativa de Cardinalidade.

## Conclusão
Usar o Banco de Dados SQL do Azure para se beneficiar de todos os aprimoramentos do SQL Server 2016 claramente pode melhorar suas execuções de consulta. Exatamente como é! É claro que, como qualquer novo recurso, deve ser feita uma avaliação adequada para determinar as condições exatas em que sua carga de trabalho de banco de dados opera melhor. A experiência mostra que a maioria das cargas de trabalho deve executar pelo menos de maneira transparente no nível de compatibilidade 130, ao mesmo tempo aproveitando as novas funções de processamento de consulta e a nova Estimativa de Cardinalidade. Dito isso, na verdade, sempre há algumas exceções e ter fazer uma avaliação cuidadosa adequada é uma avaliação importante para determinar quanto você pode aproveitar esses aperfeiçoamentos. E, novamente, o Repositório de Consultas pode ser de grande ajuda para fazer esse trabalho!

Conforme o SQL Azure evolui, você pode esperar um nível de compatibilidade 140 no futuro. No momento adequado, começaremos a falar sobre o que esse futuro nível de compatibilidade 140 trará, assim como brevemente discutimos aqui o que o nível de compatibilidade 130 está trazendo hoje.

Por enquanto, não vamos nos esquecer, a partir de junho de 2016, o Banco de Dados SQL do Azure mudará do nível de compatibilidade padrão 120 para o 130 para bancos de dados recém-criados. Lembre-se!

## Referências
* [O que há de novo no mecanismo de banco de dados](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)
* [Blog: Repositório de Consultas: um gravador de dados de voo para o banco de dados, por Borko Novakovic, de 8 de junho de 2016](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)
* [Nível de compatibilidade ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)
* [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)
* [Nível de compatibilidade 130 para Banco de Dados SQL do Azure V12](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)
* [Otimizando seus planos de consulta com o Avaliador de Cardinalidade SQL Server 2014](https://msdn.microsoft.com/library/dn673537.aspx)
* [Guia de índices ColumnStore](https://msdn.microsoft.com/library/gg492088.aspx)
* [Blog: Desempenho aprimorado de consultas com nível de compatibilidade 130 no Banco de Dados SQL do Azure, por Alain Lissoir, 6 de maio de 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)

<!--
Improved Query Performance with Compatibility Level 130 in Azure SQL Database

May 6, 2016 by Alain Lissoir (AlainL), on GitHub 'alainlissoir'.

https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/

..... Now, above.
....................
..... Soon, below?

CAPS / MSDN ideally, but instead on ACom:
.. # Assess effects of latest compatibility level on query performance, how to

sql-database-compatibility-level-query-performance-130.md

genemi = MightyPen , 2016-05-20  Friday  17:00pm
-->

<!---HONumber=AcomDC_0810_2016-->