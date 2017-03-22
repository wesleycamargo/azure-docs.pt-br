---
title: "Compatibilidade de banco de dados nível 130 - Banco de Dados SQL do Azure | Microsoft Docs"
description: "Neste artigo, exploramos os benefícios da execução do Banco de Dados SQL do Azure no nível de compatibilidade 130 e do uso dos benefícios dos novos recursos de processador de consulta e otimizador de consulta. Também abordamos os possíveis efeitos colaterais sobre o desempenho de consulta dos aplicativos SQL existentes."
services: sql-database
documentationcenter: 
author: alainlissoir
manager: jhubbard
editor: 
ms.assetid: 8619f90b-7516-46dc-9885-98429add0053
ms.service: sql-database
ms.custom: monitor and tune
ms.workload: data-management
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.topic: article
ms.date: 03/03/2017
ms.author: alainl
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 5649895d1ae39d9a7fa863407b5341f1cdf567ee
ms.lasthandoff: 03/04/2017


---
# <a name="improved-query-performance-with-compatibility-level-130-in-azure-sql-database"></a>Desempenho aprimorado de consultas com nível de compatibilidade 130 no Banco de Dados SQL do Azure
O Banco de Dados SQL do Azure está executando de modo transparente centenas de milhares de bancos de dados em vários níveis de compatibilidade diferentes, preservando e garantindo a compatibilidade com versões anteriores à versão correspondente do Microsoft SQL Server para todos os seus clientes!

Neste artigo, exploramos os benefícios da execução do Banco de Dados SQL do Azure no nível de compatibilidade 130 e do uso dos benefícios dos novos recursos de processador de consulta e otimizador de consulta. Também abordamos os possíveis efeitos colaterais sobre o desempenho de consulta dos aplicativos SQL existentes.

Como um lembrete do histórico, os alinhamentos das versões do SQL com os níveis de compatibilidade padrão são:

* 100: no SQL Server 2008 e no Banco de Dados SQL do Azure V11.
* 110: no SQL Server 2012 e no Banco de Dados SQL do Azure V11.
* 120: no SQL Server 2014 e no Banco de Dados SQL do Azure V12.
* 130: no SQL Server 2016 e no Banco de Dados SQL do Azure V12.

> [!IMPORTANT]
> O nível de compatibilidade padrão é 130 para bancos de dados **recém-criados**.
> 
> Bancos de dados criados antes de meados de junho de 2016 têm o nível de compatibilidade em vigor no momento da criação. Bancos de dados que migraram de uma versão anterior do Banco de Dados SQL do Azure têm um nível de compatibilidade 100 ou 110. O nível de compatibilidade do banco de dados mestre de um servidor criado antes de meados de junho de 2016 tem o nível de compatibilidade em vigor no momento da criação do servidor e não pode ser alterado. Um nível de compatibilidade diferente entre o banco de dados mestre e um banco de dados de usuário não é algo que afeta a funcionalidade nem o desempenho de bancos de dados de usuário.
> 

## <a name="about-compatibility-level-130"></a>Sobre o nível de compatibilidade 130
Primeiro, se você quiser saber o nível de compatibilidade atual do seu banco de dados, execute a instrução Transact-SQL a seguir.

```
SELECT compatibility_level
    FROM sys.databases
    WHERE name = '<YOUR DATABASE_NAME>’;
```


Antes que essa alteração para o nível 130 ocorra para bancos de dados **recém**-criados, vamos examinar do que se trata essa alteração com alguns exemplos de consulta básicos e ver como qualquer pessoa pode se beneficiar com ela.

O processamento de consultas em bancos de dados relacionais pode ser muito complexo e exigir um conhecimento profundo em ciência da computação e matemática para entender as escolhas de design e os comportamentos inerentes. Neste documento, o conteúdo foi intencionalmente simplificado para garantir que qualquer pessoa com algum conhecimento técnico mínimo possa entender o impacto da alteração do nível de compatibilidade e determinar como ele pode beneficiar os aplicativos.

Vamos dar uma olhada rápida em o que o nível de compatibilidade 130 traz para o cenário.  Você pode encontrar mais detalhes em [ALTERAR o nível de compatibilidade do BANCO DE DADOS (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx), mas aqui está um breve resumo:

* A operação Insert de uma instrução Insert-select pode ser multi-threaded ou pode ter um plano paralelo, enquanto essa operação antes era de single-thread.
* A tabela com Otimização de Memória e as consultas de variáveis de tabela agora podem ter planos paralelos, embora anteriormente essa operação também fosse single-thread.
* As estatísticas de tabela com Otimização de Memória agora podem ser utilizadas e são atualizadas automaticamente. Consulte [O que há de novo no mecanismo de banco de dados: OLTP na memória](https://msdn.microsoft.com/library/bb510411.aspx#InMemory) para obter mais detalhes.
* Modo de lote vs. Modo de Linha muda com índices de Repositório de Coluna
  * As classificações em uma tabela com um índice de Repositório de Coluna agora estão em modo de lote.
  * As agregações em janela agora operam em modo em lote como instruções T-SQL LAG/LEAD.
  * Consultas em tabelas de Repositório de Coluna com Múltiplas cláusulas distintas operam em modo de Lote.
  * Consultas em execução com DOP =&1; ou um plano serial também são executadas em Modo de Lote.
* Por fim, as melhorias de Estimativa de Cardinalidade são fornecidas com o nível de compatibilidade 120, mas para aqueles que estão executando em um nível de Compatibilidade inferior (ou seja, 100 ou 110), a mudança para o nível de compatibilidade 130 também traz essas melhorias e elas também podem beneficiar o desempenho da consulta dos aplicativos.

## <a name="practicing-compatibility-level-130"></a>Praticando o nível de compatibilidade 130
Primeiro, vamos criar algumas tabelas, índices e dados aleatórios para praticar algumas dessas novas funcionalidades. Os exemplos de script T-SQL podem ser executados no SQL Server 2016 ou no Banco de Dados SQL do Azure. No entanto, ao criar um Banco de Dados SQL do Azure, escolha pelo menos o banco de dados P2, pois precisará de pelo menos dois núcleos para permitir que multi-threads e, portanto, para aproveitar esses recursos.

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

## <a name="parallel-insert"></a>INSERT paralela
A execução das instruções T-SQL a seguir executa a operação INSERT no nível de compatibilidade 120 e 130, o que executa, respectivamente, a operação INSERT em um modelo single-thread (120) e em um modelo multi-thread (130).

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


Solicitando o plano de consulta real, examinando sua representação gráfica ou seu conteúdo XML, você pode determinar qual função está de Estimativa de Cardinalidade está em jogo. Ao observar os planos lado a lado na figura 1, podemos ver claramente que a execução INSERT do Repositório de Coluna vai de serial em 120 a paralela em 130. Além disso, observe a alteração do ícone do iterador no plano 130 mostrando duas setas paralelas, ilustrando o fato de que agora a execução do iterador é, realmente, paralela. Se você tiver operações INSERT grandes a serem concluídas, a execução paralela, vinculada ao número de núcleos que você tem à sua disposição para o banco de dados, terá um melhor desempenho; dependendo da situação, até 100 vezes mais rápido!

*Figura 1: a operação de INSERT muda de serial para paralela com compatibilidade de nível 130.*

![A figura 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)

## <a name="serial-batch-mode"></a>Modo de lote SERIAL
Da mesma forma, passar para o nível de compatibilidade 130 ao processar linhas de dados habilita o processamento no modo de lote. Primeiro, operações no modo de lote só estão disponíveis quando você tem um índice de repositório de coluna em vigor. Em segundo lugar, um lote normalmente representa ~900 linhas e usa uma lógica de código otimizada para vários núcleos de CPU, maior taxa de transferência de memória e aproveita diretamente os dados compactados do Repositório de Coluna, sempre que possível. Nessas condições, o SQL Server 2016 pode processar ~900 linhas ao mesmo tempo, em vez de uma linha por vez, assim, o custo geral de sobrecarga da operação agora é compartilhado por todo o lote, reduzindo o custo geral por linha. Basicamente, esse número compartilhado de operações combinado com a compactação do repositório de coluna reduz a latência envolvida em uma operação SELECT de modo em lote. Você pode encontrar mais detalhes sobre o repositório de coluna e o modo de lote no [Guia de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

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

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Ao observar os planos de consulta lado a lado na figura 2, podemos ver que o modo de processamento mudou com o nível de compatibilidade e, consequentemente, ao executar as consultas em ambos os nível de compatibilidade de uma vez, podemos ver que a maior parte do tempo de processamento é gasto no modo de linha (86%) em comparação com o modo em lote (14%), em que os dois lotes foram processados. Aumente o conjunto de dados, aumente o benefício.

*Figura 2: a operação SELECT muda do modo serial para o modo de lote com o nível de compatibilidade 130.*

![Figura 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)

## <a name="batch-mode-on-sort-execution"></a>Modo em lotes em Execução de Classificação
Semelhante ao exemplo anterior, mas aplicado a uma operação de classificação, a transição do modo de linha (nível de compatibilidade 120) para o modo em lote (nível de compatibilidade 130) melhora o desempenho da operação SORT pelas mesmas razões.

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


Visível lado a lado na figura 3, podemos ver que a operação de classificação no modo de linha representa 81% do custo, enquanto o modo em lote representa apenas 19% do custo (respectivamente, 81% e 56% na classificação em si).

*Figura 3: a operação SORT muda do modo de linha para o em lotes com o nível de compatibilidade 130.*

![A figura 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)

Obviamente, esses exemplos contêm apenas dezenas de milhares de linhas, o que não é nada ao examinar os dados disponíveis na maioria dos SQL Servers hoje em dia. Projete isso em relação a milhões de linhas e isso poderá ser convertido em uma economia diária de vários minutos de execução, dependendo da natureza da carga de trabalho.

## <a name="cardinality-estimation-ce-improvements"></a>Aprimoramentos de CE (Estimativa de Cardinalidade)
Qualquer banco de dados em execução em um nível de compatibilidade 120 ou superior usa a nova funcionalidade de Estimativa de Cardinalidade, introduzida com o SQL Server 2014. Basicamente, a estimativa de cardinalidade é a lógica usada para determinar como o SQL Server executa uma consulta com base no custo estimado. A estimativa é calculada usando dados de estatísticas associadas aos objetos envolvidos na consulta. Na prática, em um alto nível, funções de Estimativa de Cardinalidade são estimativas de contagem de linha juntamente com informações sobre a distribuição de valores, contagens de valores distintos e contagens duplicadas contidas nas tabelas e nos objetos referenciados na consulta. Errar essas estimativas pode levar à E/S de disco desnecessária devido a concessões de memória insuficiente (ou seja, despejos de TempDB) ou a uma seleção de uma execução de plano serial sobre a execução de um plano paralelo, para citar alguns exemplos. Em conclusão, estimativas incorretas podem levar a uma degradação do desempenho geral da execução da consulta. Por outro lado, melhores estimativas, estimativas mais precisas, levam a execuções de consulta melhores!

Como mencionado anteriormente, estimativas e otimizações de consulta são uma questão complexa, mas, se você quiser saber mais sobre planos de consulta e o avaliador de cardinalidade, consulte o documento em [Otimizar seus planos de consulta com o avaliador de cardinalidade do SQL Server 2014](https://msdn.microsoft.com/library/dn673537.aspx) para se aprofundar.

## <a name="which-cardinality-estimation-do-you-currently-use"></a>Que Estimativa de Cardinalidade você usa atualmente?
Para determinar em qual Estimativa de Cardinalidade suas consultas estão sendo executadas, use as amostras de consulta a seguir. O primeiro exemplo é executado no nível de compatibilidade 110, indicando o uso das funções de Estimativa de Cardinalidade antigas.

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


Após a conclusão da execução, clique no link de XML e examine as propriedades do primeiro iterador. Observe o nome da propriedade chamada CardinalityEstimationModelVersion definido no momento em 70. Isso não significa que o nível de compatibilidade do banco de dados está definido como o SQL Server versão 7.0 (definido em 110, conforme visível nas instruções T-SQL acima), mas o valor 70 representa a funcionalidade de Estimativa de Cardinalidade herdada disponível desde o SQL Server 7.0, que não teve revisões principais até o SQL Server 2014 (que é fornecido com um nível de compatibilidade 120).

*Figura 4: A CardinalityEstimationModelVersion é definida como 70 ao usar um nível de compatibilidade 110 ou inferior.*

![Figura 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)

Como alternativa, você pode alterar o nível de compatibilidade para 130 e desabilitar o uso da nova função de Estimativa de Cardinalidade usando o LEGACY_CARDINALITY_ESTIMATION definido como ON com [ALTERAR CONFIGURAÇÃO DE ESCOPO DO BANCO DE DADOS](https://msdn.microsoft.com/library/mt629158.aspx). Isso é o mesmo que usar 110 de um ponto de vista da função de Estimativa de Cardinalidade, enquanto o último nível de compatibilidade do processamento de consulta é usado. Ao fazer isso, é possível se beneficiar dos novos recursos de processamento de consulta disponíveis com o último nível de compatibilidade (ou seja, o modo em lote) e ainda contar com a funcionalidade de Estimativa de Cardinalidade antiga, se necessário.

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


Alterar o nível de compatibilidade 120 ou 130 habilita a nova funcionalidade de Estimativa de Cardinalidade. Nesse caso, a CardinalityEstimationModelVersion padrão é definida como 120 ou 130.

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

## <a name="witnessing-the-cardinality-estimation-differences"></a>Testemunhando as diferenças de Estimativa de Cardinalidade
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


A execução desta consulta efetivamente retorna 200.704 linhas, enquanto a estimativa de linha com a funcionalidade de Estimativa de Cardinalidade antiga reivindica 194.284 linhas. Obviamente, como mencionamos antes, esses resultados de contagem de linha também dependem da frequência com que você executou as amostras anteriores, que populam as tabelas de exemplo repetidamente a cada execução. Obviamente, os predicados na consulta também têm uma influência sobre a estimativa real, além da forma de tabela, do conteúdo de dados e de como esses dados se correlacionam.

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


Agora vemos que a estimativa de linha é de 202.877 ou muito próximo e superior à Estimativa de Cardinalidade antiga.

*Figura 7: a estimativa de contagem de linha agora é de 202.877, em vez de 194.284.*

![Figura 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)

Na realidade, o conjunto de resultados é de 200.704 linhas – mas tudo depende da frequência com que você executou as consultas das amostras anteriores. Porém, mais importante, porque o T-SQL usa a instrução RAND(), os valores reais retornados podem variar de uma execução para a próxima. Portanto, neste exemplo específico, a nova Estimativa de Cardinalidade faz um trabalho melhor de estimar o número de linhas, pois 202.877 está muito mais próximo de 200.704, que 194.284! Por fim, se você alterar os predicados da cláusula WHERE para igualdade (em vez de ">", por exemplo), isso poderá tornar as estimativas entre as funções de Cardinalidade antiga e nova ainda mais diferentes, dependendo de quantas correspondências você pode obter.

Obviamente, nesse caso, estar ~6000 aquém da contagem real não representa muitos dados em algumas situações. Agora, transponha isso para milhões de linhas em várias tabelas e consultas mais complexas e, às vezes, a estimativa pode ter uma diferença de milhões de linhas, portanto, o risco de selecionar o plano de execução incorreto ou solicitar concessões de memória insuficientes que levam a despejos de TempDB e muito mais E/S é muito maior.

Se você tiver a oportunidade, pratique essa comparação com suas consultas e conjuntos de dados mais típicos e veja você mesmo como algumas das estimativas novas e antigas são afetadas, enquanto algumas podem estar mais distantes da realidade ou outras mais próximas às contagens de linhas reais retornadas nos conjuntos de resultados. Tudo isso depende da forma das consultas, das características do banco de dados SQL do Azure, da natureza e do tamanho dos conjuntos de dados e das estatísticas disponíveis sobre eles. Se você tiver acabado de criar a instância do Banco de Dados SQL do Azure, o otimizador de consulta precisará construir seu conhecimento do zero, em vez de reutilizar estatísticas criadas com base em execuções de consulta anteriores. Portanto, as estimativas são muito contextuais e quase específicas a cada situação de servidor e aplicativo. Ele é um aspecto importante para ter em mente!

## <a name="some-considerations-to-take-into-account"></a>Algumas considerações para levar em conta
Embora a maioria das cargas de trabalho se beneficie do nível de compatibilidade 130, antes de adotar o nível de compatibilidade para o ambiente de produção, basicamente, você tem 3 opções:

1. Você passa para o nível de compatibilidade 130 e examina como fica o desempenho. Caso você perceba algumas regressões, defina o nível de compatibilidade de volta ao nível original ou mantenha 130 e apenas reverta a Estimativa de Cardinalidade para o modo herdado (conforme explicado anteriormente, isoladamente, isso poderá resolver o problema).
2. Você testa os aplicativos existentes por completo sob uma carga de produção semelhante, ajusta e valida o desempenho antes de entrar em produção. Em caso de problemas, conforme descrito acima, sempre é possível voltar para o nível de compatibilidade original ou reverter a Estimativa de Cardinalidade para o modo herdado.
3. Como uma opção final e a maneira mais recente de resolver esses problemas, use o Repositório de Consultas. Essa é a opção recomendada de hoje! Para ajudar na análise das consultas no nível de compatibilidade 120 ou inferior versus 130, nunca é suficiente incentivar o uso do Repositório de Consultas. O Repositório de Consultas está disponível com a versão mais recente do Banco de Dados SQL do Azure V12 e foi projetada para ajudá-lo a solucionar problemas de desempenho de consulta. Considere o Repositório de Consultas um gravador de dados de voo para seu banco de dados, coletando e apresentando informações históricas detalhadas sobre todas as consultas. Isso simplifica bastante a forense de desempenho, reduzindo o tempo para diagnosticar e resolver problemas. Você pode encontrar mais informações no artigo sobre [Repositório de Consultas: o gravador de dados de voo para o banco de dados](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/).

No nível superior, se você já tiver um conjunto de bancos de dados em execução no nível de compatibilidade 120 ou inferior e pretender mudar alguns deles para 130, ou porque a carga de trabalho provisiona novos bancos de dados automaticamente que são definidos por padrão como 130, considere o seguinte:

* Antes de alterar para o novo nível de compatibilidade em produção, habilite o Repositório de Consultas. Você pode consultar o artigo sobre como [alterar o modo de compatibilidade do banco de dados e usar o armazenamento de consulta](https://msdn.microsoft.com/library/bb895281.aspx) para obter mais informações.
* Em seguida, teste todas as cargas de trabalho críticas usando dados representativos e consultas de um ambiente de produção e compare o desempenho observado e o relatado pelo Repositório de Consulta. Se você sofrer algumas regressões, poderá identificar as consultas retornadas com o Repositório de Consulta e usar a opção de imposição de plano do Repositório de Consultas (também conhecido como fixação de plano). Nesse caso, você definitivamente fica com o nível de compatibilidade 130 e usa o plano de consulta anterior conforme sugerido pelo Repositório de Consultas.
* Se desejar usar os novos recursos e funcionalidades do Banco de Dados SQL do Azure (que executa o SQL Server 2016), mas for sensível às mudanças trazidas pelo nível de compatibilidade 130, como último recurso, poderá considerar forçar o nível de compatibilidade de volta para o nível adequado da carga de trabalho usando uma instrução ALTER DATABASE. Mas primeiro, lembre-se de que a opção de fixação de plano do Repositório de Consultas é sua melhor opção, porque não usar 130 basicamente é permanecer no nível de funcionalidade de uma versão mais antiga do SQL Server.
* Se você tiver aplicativos multilocatários abrangendo vários bancos de dados, poderá ser necessário atualizar a lógica de provisionamento dos bancos de dados para garantir um nível de compatibilidade consistente em todos os bancos de dados; tantos nos antigos quanto nos recentemente configurados. O desempenho de carga de trabalho do aplicativo pode ser sensível ao fato de que alguns bancos de dados estão em execução em diferentes níveis de compatibilidade, portanto, pode ser necessária consistência de nível de compatibilidade entre quaisquer bancos de dados de para oferecer a mesma experiência aos clientes de um modo geral. Isso não é obrigatório; realmente, depende de como o aplicativo é afetado pelo nível de compatibilidade.
* Por fim, em relação à Estimativa de Cardinalidade, é recomendável testar a carga de trabalho de produção nas novas condições para determinar se o aplicativo se beneficia das melhorias de Estimativa de Cardinalidade.

## <a name="conclusion"></a>Conclusão
Usar o Banco de Dados SQL do Azure para se beneficiar de todos os aprimoramentos do SQL Server 2016 claramente pode melhorar suas execuções de consulta. É claro que, como qualquer novo recurso, deve ser feita uma avaliação adequada para determinar as condições exatas em que sua carga de trabalho de banco de dados opera melhor. A experiência mostra que a maioria das cargas de trabalho deve ser executada, no mínimo, de maneira transparente no nível de compatibilidade 130, ao mesmo tempo que usa as novas funções de processamento de consulta e a nova Estimativa de Cardinalidade. Dito isso, na verdade, sempre há algumas exceções e ter fazer uma avaliação cuidadosa adequada é uma avaliação importante para determinar quanto você pode aproveitar esses aperfeiçoamentos. E, novamente, o Repositório de Consultas pode ser de grande ajuda para fazer esse trabalho!

## <a name="references"></a>Referências
* [O que há de novo no mecanismo de banco de dados](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)
* [Blog: Repositório de Consultas: um gravador de dados de voo para o banco de dados, por Borko Novakovic, de 8 de junho de 2016](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)
* [Nível de compatibilidade ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)
* [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)
* [Nível de compatibilidade 130 para Banco de Dados SQL do Azure V12](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)
* [Otimizando seus planos de consulta com o Avaliador de Cardinalidade SQL Server 2014](https://msdn.microsoft.com/library/dn673537.aspx)
* [Guia de índices ColumnStore](https://msdn.microsoft.com/library/gg492088.aspx)
* [Blog: Desempenho aprimorado de consultas com nível de compatibilidade 130 no Banco de Dados SQL do Azure, por Alain Lissoir, 6 de maio de 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)


