<properties
	pageTitle="Pool de banco de dados elásticos para bancos de dados SQL | Microsoft Azure"
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
	ms.date="04/04/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# O que é um Pool de Banco de Dados Elástico do Azure?

Um desenvolvedor de SaaS cria e gerencia dezenas, centenas ou até milhares de bancos de dados SQL. Um Pool de Banco de Dados Elástico simplifica a criação, manutenção e gerenciamento de desempenho em vários bancos de dados. Adicionar ou subtrair bancos de dados do pool à vontade. Veja [Criar um Pool de Banco de Dados Elástico escalonável para Bancos de Dados SQL no Portal do Azure](sql-database-elastic-pool-create-portal.md), [usando o PowerShell](sql-database-elastic-pool-create-powershell.md) ou [C#](sql-database-elastic-pool-csharp.md).

Para obter detalhes de APIs e erros, consulte [Referência do Pool de Banco de Dados Elástico](sql-database-elastic-pool-reference.md).

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

## Propriedades do pool de bancos de dados elásticos
Limites para pools elásticos e bancos de dados elásticos.

| Propriedade | Descrição |
| :-- | :-- |
| Camada de serviço | Básica, Standard ou Premium. A camada de serviço determina o intervalo em limites de desempenho e armazenamento que podem ser configurados, bem como opções de continuidade dos negócios. Cada banco de dados dentro de um pool tem a mesma camada de serviço que o pool. A "camada de serviço" também é conhecida como "edição".|
| eDtu por pool | Número máximo de eDTUs que podem ser compartilhados por bancos de dados no pool. Os eDTUs totais usados pelos bancos de dados no pool não podem exceder esse limite no mesmo momento. |
| Armazenamento por pool | Quantidade máxima de armazenamento que pode ser compartilhada por bancos de dados no pool. O armazenamento total usado pelos bancos de dados no pool não pode exceder esse limite. Esse limite é determinado pelos eDTUs por pool. Se esse limite for excedido, todos os bancos de dados se tornarão somente leitura. |
| Máximo de eDTUs por banco de dados | Número máximo de eDTUs que qualquer banco de dados no pool pode usar e se aplica a todos os bancos de dados no pool. O máximo de eDTUs por banco de dados não é uma garantia de recursos. |
| Mínimo de eDTUs por banco de dados | Número mínimo de eDTUs garantido para qualquer banco de dados no pool e se aplica a todos os bancos de dados no pool. O mínimo de eDTUs por banco de dados pode ser definido como 0. Observe que o produto do número de bancos de dados no pool e o mínimo de eDTUs por banco de dados não pode exceder o eDTU por pool. |


## Limites de eDTU e armazenamento para pools elásticos e bancos de dados elásticos


[AZURE.INCLUDE [Tabela de níveis de serviço de BD SQL para bancos de dados elásticos](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

## Trabalhos de banco de dados elástico

Com um pool, as tarefas de gerenciamento são simplificadas com a execução de scripts em **[trabalhos elásticos](sql-database-elastic-jobs-overview.md)**. Um Trabalho de Banco de Dados Elástico elimina a maioria do tédio associado a um grande número de bancos de dados. Para começar, veja [Introdução a trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-getting-started.md).

Para obter mais informações sobre outras ferramentas, veja o [Elastic database tools learning map](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) (Mapa de aprendizagem das ferramentas de banco de dados elástico).

## Recursos de continuidade de negócios para bancos de dados em um pool

Atualmente em visualização, os bancos de dados elásticos dão suporte à maioria dos [recursos de continuidade de negócios](sql-database-business-continuity.md) disponíveis para bancos de dados únicos em servidores V12.

### Recuperação Pontual

O sistema realiza backup automático dos bancos de dados em um pool de banco de dados elástico, e a política de retenção de backup é a mesma que a camada de serviços correspondente para bancos de dados únicos. Em suma, os bancos de dados em cada camada têm um intervalo de restauração diferente:

* **Pool Básico**: capaz de fazer a restauração para qualquer ponto nos últimos 7 dias.
* **Pool Standard**: capaz de fazer a restauração para qualquer ponto dentro dos últimos 14 dias.
* **Pool Premium**: capaz de fazer a restauração para qualquer ponto nos últimos 35 dias.

Durante a visualização, os bancos de dados em um pool serão restaurados para um novo banco de dados no mesmo pool. Bancos de dados descartados sempre serão restaurados como um banco de dados autônomo fora do pool para o nível de desempenho mais baixo para essa camada de serviço. Por exemplo, um banco de dados elástico em um pool padrão que é descartado será restaurado como um banco de dados S0. Você pode executar operações de restauração de bancos de dados pelo Portal do Azure ou programaticamente usando a API REST. O suporte ao cmdlet do PowerShell será disponibilizado em breve.

### Restauração geográfica

A Restauração Geográfica permite recuperar o banco de dados de um pool para um servidor em uma região diferente. Durante a visualização, para restaurar um banco de dados de um pool em um servidor diferente, o servidor de destino de ter um pool com o mesmo nome do pool de origem. Se necessário, crie um novo pool no servidor de destino e dê a ele o mesmo nome antes de restaurar o banco de dados. Caso o servidor de destino não contenha um pool com o mesmo nome, a operação de Restauração Geográfica falhará. Para obter detalhes, consulte [Recuperar usando a restauração geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore).


### Replicação Geográfica

A replicação geográfica está disponível para qualquer banco de dados em um pool de banco de dados elástico Standard ou Premium. Um ou todos os bancos de dados em uma parceria de replicação geográfica podem ser em um pool de banco de dados elástico, contanto que as camadas de serviço sejam as mesmas. Você pode configurar a replicação geográfica para pools de bancos de dados elásticos usando o [Portal do Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md) ou [Transact-SQL](sql-database-geo-replication-transact-sql.md).

### Importação e exportação

É possível exportar um banco de dados de dentro de um pool. Atualmente, não há suporte para a importação de um banco de dados diretamente para um pool, mas você pode importar para um único banco de dados e, em seguida, mover o banco de dados para um pool.


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=AcomDC_0413_2016-->