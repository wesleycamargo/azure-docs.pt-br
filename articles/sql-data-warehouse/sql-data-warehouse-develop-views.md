<properties
   pageTitle="Modos de exibição no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para usar exibições Transact-SQL no Azure SQL Data Warehouse para desenvolvimento de soluções."
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
   ms.date="06/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

 
# Modos de exibição no SQL Data Warehouse

Os modos de exibição são particularmente úteis no SQL Data Warehouse. Eles podem ser usados em diversas maneiras diferentes de melhorar a qualidade de sua solução.

Este artigo destaca alguns exemplos de como aprimorar sua solução implementando com modos de exibição. Existem algumas limitações que também precisam ser consideradas.

## Abstração de arquitetura
Um padrão de aplicativo muito comum é recriar tabelas usando CREATE TABLE AS SELECT (CTAS) seguido por um objeto de renomeação do padrão durante o carregamento dos dados.

O exemplo a seguir adiciona novos registros de data para uma dimensão de data. Observe como um novo objeto, DimDate_New, é criado pela primeira vez e, em seguida, renomeado para substituir a versão original do objeto.``` CREATE TABLE dbo.DimDate_New WITH (DISTRIBUTION = REPLICATE , CLUSTERED INDEX (DateKey ASC) ) AS SELECT * FROM dbo.DimDate AS prod UNION ALL SELECT * FROM dbo.DimDate_stg AS stg ;

RENAME OBJECT DimDate TO DimDate_Old; RENAME OBJECT DimDate_New TO DimDate;

``` No entanto, isso pode resultar em objetos de tabela que aparecem e desaparecem da exibição do usuário no SSDT SQL Server Object Explorer. As exibições podem ser usadas para fornecer aos consumidores de data warehouse uma camada de apresentação consistente enquanto os objetos subjacentes são renomeados. O fornecimento de acesso aos dados através de uma exibição significa que os usuários não precisam ter visibilidade das tabelas subjacentes. Isso fornece uma experiência de usuário consistente ao mesmo tempo que garante que os designers do data warehouse possam aprimorar o modelo de dados e também maximizar o desempenho usando CTAS durante o processo de carregamento de dados.

## Otimização do desempenho
Os modos de exibição são uma forma de impor as junções de desempenho otimizado entre as tabelas. Por exemplo, o modo de exibição pode incorporar uma chave de distribuição redundante como parte dos critérios de junção para minimizar a movimentação de dados. Outra razão pode ser forçar uma consulta específica ou dica de junção. Isso garante que a junção seja sempre executada de forma ideal e não seja dependente do usuário, lembre-se de construir a junção corretamente.

## Limitações
Os modos de exibição no SQL Data Warehouse são somente metadados.

Consequentemente, as opções a seguir não estão disponíveis: - não há nenhuma opção de associação de esquema - as tabelas base não podem ser atualizadas por meio da exibição - os modos de exibição não podem ser criados sobre as tabelas temporárias - não há suporte para as dicas EXPAND/NOEXPAND - não há exibições indexadas no SQL Data Warehouse


## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte a [Visão geral de desenvolvimento do SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Visão geral de desenvolvimento do SQL Data Warehouse]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO4-->