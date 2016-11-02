<properties
    pageTitle="O que é um pool elástico do Azure? | Microsoft Azure"
    description="Gerencie centenas ou milhares de bancos de dados usando um pool. Um preço para um conjunto de unidades de desempenho pode ser distribuído pelo pool. Mova bancos de dados para dentro ou para fora à vontade."
    keywords="banco de dados elásticos, bancos de dados sql"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="07/12/2016"
    ms.author="CarlRabeler"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="what-is-an-azure-elastic-pool?"></a>O que é um pool elástico do Azure?

Pools elásticos de bancos de dados SQL fornecem uma solução simples e econômica para gerenciar as metas de desempenho de vários bancos de dados que têm padrões de uso muito variáveis e imprevisíveis.

> [AZURE.NOTE] Os pools elásticos têm uma disponibilidade geral (GA) em todas as regiões do Azure, exceto na Índia Ocidental, onde atualmente estão em preview.  A GA dos pools elásticos nessa região ocorrerá assim que possível.

## <a name="how-it-works"></a>Como ele funciona

Um padrão de aplicativo de SaaS comum é o modelo de banco de dados de locatário único: cada cliente recebe seu próprio banco de dados. Cada cliente (banco de dados) tem requisitos de recursos imprevisíveis para memória, E/S e CPU. Com esses picos e quedas de demanda, como você aloca recursos de forma eficiente e econômica? Tradicionalmente, havia duas opções: (1) provisionar em excesso os recursos com base no uso de pico e pagar a mais ou (2) provisionar de forma insuficiente para economizar, em detrimento do desempenho e satisfação do cliente durante picos. Os pools elásticos resolvem esse problema, garantindo que os bancos de dados recebam os recursos de desempenho que precisam e quando precisam. Eles fornecem um mecanismo de alocação de recursos simples dentro de um orçamento previsível. Para saber mais sobre os padrões de design para aplicativos SaaS usando pools Elásticos, confira [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Padrões de Design para aplicativos de SaaS multilocatários com o Banco de Dados SQL).

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

No Banco de Dados SQL, a medida relativa da capacidade do banco de dados de lidar com as demandas de recursos é expressa em DTUs (Unidades de Transação de Banco de Dados) para bancos de dados individuais e em eDTUs (DTUs elásticos) para bancos de dados elásticos em um pool elástico. Confira a [Introdução ao Banco de Dados SQL](sql-database-technical-overview.md#understand-dtus) para saber mais sobre DTUs e eDTUs.

Um pool é fornecido com um número definido de eDTUs por um preço definido. Dentro do pool, os bancos de dados individuais recebem a flexibilidade para dimensionar automaticamente no conjunto de parâmetros. Sob carga pesada, um banco de dados pode consumir mais eDTUs para atender à demanda. Bancos de dados sob cargas leves consumem menos e bancos de dados sem carga não consomem nenhum eDTU. O provisionamento de recursos para o pool inteiro em vez do bancos de dados único simplifica as tarefas de gerenciamento. Além disso, você tem um orçamento previsível para o pool.

eDTUs adicionais podem ser adicionados a um pool existente sem tempo de inatividade do banco de dados ou impacto sobre os bancos de dados no pool elástico. Da mesma forma, se eDTUs extras não forem mais necessários, eles poderão ser removidos de um pool existente a qualquer momento.

E você pode adicionar ou subtrair bancos de dados para o pool. Se um banco de dados é previsível por estar subutilizando recursos, mova-o.

## <a name="which-databases-go-in-a-pool?"></a>Quais bancos de dados vão em um pool?

![Bancos de dados SQL que compartilham eDTUs em um pool de banco de dados elástico.][1]

Os bancos de dados que são ótimos candidatos a pools de banco de dados elásticos normalmente têm períodos de atividade e períodos de inatividade. No exemplo acima, você pode ver a atividade de um banco de dados individual, de 4 bancos de dados e, finalmente, de um pool elástico com 20 bancos de dados. Bancos de dados com diferentes atividades ao longo do tempo são bons candidatos para pools Elásticos porque eles não estão todos ativos ao mesmo tempo e podem compartilhar eDTUs. Nem todos os bancos de dados se encaixam nesse padrão. Bancos de dados com uma demanda de recursos mais constante são mais adequados para as camadas de serviço Basic, Standard e Premium, em que os recursos são atribuídos individualmente.

[Considerações de preço e desempenho para um pool elástico](sql-database-elastic-pool-guidance.md).

## <a name="edtu-and-storage-limits-for-elastic-pools-and-elastic-databases."></a>Limites de eDTU e armazenamento para pools elásticos e bancos de dados elásticos.

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Se todas as DTUs de um pool elástico forem usadas, cada banco de dados no pool receberá uma quantidade igual de recursos para processar as consultas.  O serviço de Banco de Dados SQL fornece integridade de compartilhamento de recursos entre os bancos de dados ao garantir fatias iguais de tempo de computação. A integridade de compartilhamento de recursos do pool elástico é adicional a qualquer quantidade de recursos garantidos de outra forma a cada banco de dados quando o mínimo de DTUs por banco de dados é definido com um valor diferente de zero.

## <a name="elastic-pool-and-elastic-database-properties"></a>Pool elástico e propriedades de bancos de dados elásticos

### <a name="limits-for-elastic-pools"></a>Limites para pools elásticos

| Propriedade | Descrição |
| :-- | :-- |
| Camada de serviço | Básica, Standard ou Premium. A camada de serviço determina o intervalo em limites de desempenho e armazenamento que podem ser configurados, bem como opções de continuidade dos negócios. Cada banco de dados dentro de um pool tem a mesma camada de serviço que o pool. A "camada de serviço" também é conhecida como "edição". |
| eDTUs por pool | O número máximo de eDTUs que podem ser compartilhados por bancos de dados no pool. Os eDTUs totais usados pelos bancos de dados no pool não podem exceder esse limite no mesmo momento. |
| Armazenamento máx. por pool (GB) | A quantidade máxima de armazenamento em GBs que pode ser compartilhada por bancos de dados no pool. O armazenamento total usado pelos bancos de dados no pool não pode exceder esse limite. Esse limite é determinado pelos eDTUs por pool. Se esse limite for excedido, todos os bancos de dados se tornarão somente leitura. |
| Número máximo de bancos de dados por pool | O número máximo de bancos de dados permitidos por pool. |
| Máximo de trabalhos simultâneos por pool | O número máximo de trabalhos (solicitações) simultâneos disponíveis para todos os bancos de dados no pool. |
| Máximo de logons simultâneos por pool | O número máximo de logons simultâneos para todos os bancos de dados no pool. |
| Máximo de sessões simultâneas por pool | O número máximo de sessões disponíveis para todos os bancos de dados no pool. |


### <a name="limits-for-elastic-databases"></a>Limites para bancos de dados elásticos

| Propriedade | Descrição |
| :-- | :-- |
| Máximo de eDTUs por banco de dados | O número máximo de eDTUs que qualquer banco de dados no pool pode usar, se disponível, com base na utilização por outros bancos de dados no pool.  O máximo de eDTUs por banco de dados não é uma garantia de recursos para um banco de dados.  Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. Defina um valor para o máximo de eDTUs por banco de dados que seja alto o suficiente para lidar com picos de utilização do banco de dados. Espera-se um grau de sobrecarga, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente. Por exemplo, suponha que o pico de utilização por banco de dados seja de 20 eDTUs e apenas 20% dos 100 bancos de dados no pool atinjam o pico simultaneamente.  Se o máximo de eDTUs por banco de dados for definido para 20 eDTUs, será razoável sobrecarregar o pool em 5 vezes e definir os eDTUs por pool como 400. |
| Mínimo de eDTUs por banco de dados | O número mínimo de eDTUs garantido para qualquer banco de dados no pool.  Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. O mínimo de eDTUs por banco de dados pode ser definido como 0 e também é o valor padrão. Essa propriedade é definida entre 0 e a utilização média de eDTUs por banco de dados. O produto do número de bancos de dados no pool e o mínimo de eDTUs por banco de dados não pode exceder os eDTUs por pool.  Por exemplo, se um pool tiver 20 bancos de dados e o mínimo de eDTUs por banco de dados for definido como 10 eDTUs, os eDTUs por pool deverão ser de pelo menos 200 eDTUs. |
| Armazenamento máximo por banco de dados (GB) | O armazenamento máximo para um banco de dados em um pool. Bancos de dados elásticos compartilham o armazenamento de pool, de modo que o armazenamento do banco de dados é limitado ao menor entre o armazenamento de pool restante e o armazenamento máximo por banco de dados.|


## <a name="elastic-database-jobs"></a>Trabalhos de banco de dados elástico

Com um pool, as tarefas de gerenciamento são simplificadas com a execução de scripts em **[trabalhos elásticos](sql-database-elastic-jobs-overview.md)**. Um Trabalho de Banco de Dados Elástico elimina a maioria do tédio associado a um grande número de bancos de dados. Para começar, consulte [Introdução a trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-getting-started.md).

Para obter mais informações sobre outras ferramentas, consulte o [Mapa de aprendizagem das ferramentas de Banco de Dados Elástico](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/).

## <a name="business-continuity-features-for-databases-in-a-pool"></a>Recursos de continuidade de negócios para bancos de dados em um pool

Os Bancos de Dados Elásticos normalmente dão suporte às mesmas [funcionalidades de continuidade dos negócios](sql-database-business-continuity.md) disponíveis para Bancos de Dados Individuais em servidores V12.


### <a name="point-in-time-restore"></a>Recuperação pontual

A restauração pontual usa backups de banco de dados automáticos para recuperar um banco de dados em um pool para um ponto específico no tempo. Confira [Restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>Restauração geográfica

A restauração geográfica fornecerá a opção de recuperação padrão quando um banco de dados estiver indisponível devido a um incidente na região onde está hospedado. Confira [Restaurar um Banco de Dados SQL ou fazer failover para um secundário](sql-database-disaster-recovery.md) 

### <a name="active-geo-replication"></a>Replicação geográfica ativa

Para aplicativos que têm requisitos de restauração mais agressivos do que a Restauração Geográfica pode oferecer, configure a Replicação Geográfica Ativa usando o [Portal do Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md) ou [Transact-SQL](sql-database-geo-replication-transact-sql.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Curso em vídeo da Microsoft Virtual Academy sobre os recursos de banco de dados elástico](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554) 

<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png



<!--HONumber=Oct16_HO2-->


