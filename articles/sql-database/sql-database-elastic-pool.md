<properties
	pageTitle="O que é um pool de banco de dados elástico do Azure? | Microsoft Azure"
	description="Gerencie centenas ou milhares de bancos de dados usando um pool. Um preço para um conjunto de unidades de desempenho pode ser distribuído pelo pool. Mova bancos de dados para dentro ou para fora à vontade."
	keywords="banco de dados elásticos, bancos de dados sql"
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/28/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# O que é um Pool de Banco de Dados Elástico do Azure?

Pools elásticos fornecem uma solução simples e econômica para gerenciar as metas de desempenho para vários bancos de dados que têm padrões de uso muito variáveis e imprevisíveis.

## Como ele funciona

Um padrão de aplicativo de SaaS comum é o modelo de banco de dados de locatário único: cada cliente recebe um banco de dados. Cada cliente (banco de dados) tem requisitos de recursos imprevisíveis para memória, E/S e CPU. Com esses picos e quedas de demanda, como você aloca recursos? Tradicionalmente, havia duas opções: (1) provisionar em excesso os recursos com base no uso de pico e pagar a mais ou (2) provisionar de forma insuficiente para economizar, em detrimento do desempenho e satisfação do cliente durante picos. Os pools de banco de dados elástico resolvem esse problema, garantindo que os bancos de dados obtenham os recursos de desempenho que necessitam, quando necessário, fornecendo um mecanismo de alocação de recurso simples dentro de um orçamento previsível.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

No Banco de Dados SQL, a medida relativa da capacidade do banco de dados de lidar com as demandas de recursos é expressa em DTUs (Unidades de Transação de Banco de Dados) para bancos de dados individuais e em eDTUs (DTUs elástico) para pools de banco de dados elástico. Veja a [Introdução ao Banco de Dados SQL](sql-database-technical-overview.md#understand-dtus) para saber mais sobre DTUs e eDTUs.

Um pool é fornecido com um número definido de eDTUs por um preço definido. Dentro do pool, os bancos de dados individuais recebem a flexibilidade para dimensionar automaticamente no conjunto de parâmetros. Sob carga pesada, um banco de dados pode consumir mais eDTUs para atender à demanda. Bancos de dados sob cargas leves consumem menos e bancos de dados sem carga não consomem nenhum eDTU. O provisionamento de recursos para o pool inteiro em vez do bancos de dados único simplifica as tarefas de gerenciamento. Além disso, você tem um orçamento previsível para o pool.

eDTUs adicionais podem ser adicionados a um pool existente sem tempo de inatividade do banco de dados ou impacto negativo sobre os bancos de dados. Da mesma forma, se eDTUs extras não forem mais necessários, eles poderão ser removidos de um pool existente a qualquer momento.

E você pode adicionar ou subtrair bancos de dados para o pool. Se um banco de dados é previsível por estar subutilizando recursos, mova-o.

## Quais bancos de dados vão em um pool?

![Bancos de dados SQL que compartilham eDTUs em um pool de banco de dados elástico.][1]

Bancos de dados que são ótimos candidatos a pools de banco de dados elástico normalmente têm períodos de atividade e períodos de inatividade. No exemplo acima, você vê a atividade de um banco de dados individual, de 4 bancos de dados e, finalmente, um pool de banco de dados elástico com 20 bancos de dados. Bancos de dados com diferentes atividades ao longo do tempo são bons candidatos para pools Elásticos porque eles não estão todos ativos ao mesmo tempo e podem compartilhar eDTUs. Nem todos os bancos de dados se encaixam nesse padrão. Bancos de dados com uma demanda de recursos mais constante são mais adequados para as camadas de serviço Basic, Standard e Premium, em que os recursos são atribuídos individualmente.

[Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md).


> [AZURE.NOTE] No momento, os pools de banco de dados elástico estão em visualização e disponíveis apenas com Servidores V12 do Banco de Dados SQL.

## Limites de eDTU e armazenamento para pools elásticos e bancos de dados elásticos

[AZURE.INCLUDE [Tabela de níveis de serviço de BD SQL para bancos de dados elásticos](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

## Propriedades do pool de bancos de dados elásticos

### Limites para pools elásticos

| Propriedade | Descrição |
| :-- | :-- |
| Camada de serviço | Básica, Standard ou Premium. A camada de serviço determina o intervalo em limites de desempenho e armazenamento que podem ser configurados, bem como opções de continuidade dos negócios. Cada banco de dados dentro de um pool tem a mesma camada de serviço que o pool. A "camada de serviço" também é conhecida como "edição". |
| eDTUs por pool | O número máximo de eDTUs que podem ser compartilhados por bancos de dados no pool. Os eDTUs totais usados pelos bancos de dados no pool não podem exceder esse limite no mesmo momento. |
| Armazenamento máx. por pool (GB) | A quantidade máxima de armazenamento em GBs que pode ser compartilhada por bancos de dados no pool. O armazenamento total usado pelos bancos de dados no pool não pode exceder esse limite. Esse limite é determinado pelos eDTUs por pool. Se esse limite for excedido, todos os bancos de dados se tornarão somente leitura. |
| Número máximo de bancos de dados por pool | O número máximo de bancos de dados permitidos por pool. |
| Máximo de trabalhos simultâneos por pool | O número máximo de trabalhos (solicitações) simultâneos disponíveis para todos os bancos de dados no pool. |
| Máximo de logons simultâneos por pool | O número máximo de logons simultâneos para todos os bancos de dados no pool. |
| Máximo de sessões simultâneas por pool | O número máximo de sessões disponíveis para todos os bancos de dados no pool. |


### Limites para bancos de dados elásticos

| Propriedade | Descrição |
| :-- | :-- |
| Máximo de eDTUs por banco de dados | O número máximo de eDTUs que qualquer banco de dados no pool pode usar, se disponível, com base na utilização por outros bancos de dados no pool. O máximo de eDTUs por banco de dados não é uma garantia de recursos para um banco de dados. Essa é uma configuração global que se aplica a todos os bancos de dados no pool. Defina um valor para o máximo de eDTUs por banco de dados que seja alto o suficiente para lidar com picos de utilização do banco de dados. Espera-se um grau de sobrecarga, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente. Por exemplo, suponha que o pico de utilização por banco de dados seja de 20 eDTUs e apenas 20% dos 100 bancos de dados no pool atinjam o pico simultaneamente. Se o máximo de eDTUs por banco de dados for definido para 20 eDTUs, será razoável sobrecarregar o pool em 5 vezes e definir os eDTUs por pool como 400. |
| Mínimo de eDTUs por banco de dados | O número mínimo de eDTUs garantido para qualquer banco de dados no pool. Essa é uma configuração global que se aplica a todos os bancos de dados no pool. O mínimo de eDTUs por banco de dados pode ser definido como 0 e também é o valor padrão. Essa propriedade normalmente é definida entre 0 e a utilização média de eDTUs por banco de dados. Observe que o produto do número de bancos de dados no pool e o mínimo de eDTUs por banco de dados não pode exceder os eDTUs por pool. Por exemplo, se um pool tiver 20 bancos de dados e o mínimo de eDTUs por banco de dados for definido como 10 eDTUs, os eDTUs por pool deverão ser de pelo menos 200 eDTUs. |
| Armazenamento máximo por banco de dados (GB) | O armazenamento máximo para um banco de dados em um pool. Bancos de dados elásticos compartilham o armazenamento de pool, de modo que o armazenamento do banco de dados é limitado ao menor entre o armazenamento de pool restante e o armazenamento máximo por banco de dados.|


## Trabalhos de banco de dados elástico

Com um pool, as tarefas de gerenciamento são simplificadas com a execução de scripts em **[trabalhos elásticos](sql-database-elastic-jobs-overview.md)**. Um Trabalho de Banco de Dados Elástico elimina a maioria do tédio associado a um grande número de bancos de dados. Para começar, veja [Introdução a trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-getting-started.md).

Para obter mais informações sobre outras ferramentas, veja o [Elastic database tools learning map](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) (Mapa de aprendizagem das ferramentas de banco de dados elástico).

## Recursos de continuidade de negócios para bancos de dados em um pool

Os bancos de dados elásticos normalmente dão suporte aos mesmos [recursos de continuidade de negócios](sql-database-business-continuity.md) disponíveis para bancos de dados únicos em servidores V12.


### Recuperação pontual

A restauração pontual usa backups de banco de dados automáticos para recuperar um banco de dados em um pool para um ponto específico no tempo. Consulte [Recuperar um Banco de Dados SQL do Azure de um erro do usuário](sql-database-user-error-recovery.md)

### Restauração geográfica

A restauração geográfica fornecerá a opção de recuperação padrão quando um banco de dados estiver indisponível devido a um incidente na região onde está hospedado. Consulte [Recuperar um Banco de Dados SQL do Azure de uma interrupção](sql-database-disaster-recovery.md)

### Replicação geográfica ativa

Para aplicativos que têm requisitos de restauração mais agressivos do que a restauração geográfica pode oferecer, configure a Replicação geográfica ativa usando o [Portal do Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md) ou [Transact-SQL](sql-database-geo-replication-transact-sql.md).


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=AcomDC_0504_2016-->