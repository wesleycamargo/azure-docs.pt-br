<properties
	pageTitle="Pool de banco de dados elásticos para bancos de dados SQL | Microsoft Azure"
	description="Descubra como você pode controlar o crescimento explosivo em bancos de dados SQL com pools de banco de dados elásticos, uma maneira de compartilhar recursos disponíveis em muitos bancos de dados."
	keywords="banco de dados elásticos, bancos de dados sql"	
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/11/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Controlar crescimento explosivo nos bancos de dados SQL usando pools de banco de dados elástico para compartilhar recursos

Um desenvolvedor de SaaS cria e gerencia dezenas, centenas ou até milhares de bancos de dados SQL. Pools Elásticos simplificam o gerenciamento de criação, manutenção e desempenho entre esses bancos de dados dentro de um orçamento que você controla. Adicionar ou subtrair bancos de dados do pool à vontade. [Criar um pool de banco de dados elástico](sql-database-elastic-pool-portal.md) para os seus bancos de dados SQL em minutos usando o portal do Microsoft Azure, [PowerShell](sql-database-elastic-pool-powershell.md) ou [C#](sql-database-elastic-pool-csharp.md).

Para obter detalhes de APIs e erros, consulte [Referência do Pool de Banco de Dados Elástico](sql-database-elastic-pool-reference.md).

## Como ele funciona

É um padrão comum de aplicativo SaaS para cada cliente a ser atribuído a um banco de dados. Cada cliente (banco de dados) tem requisitos de recurso imprevisível (IO/CPU/memória resumida com DTU). Com esses picos e quedas de demanda, como você aloca recursos? Duas opções: (1) recursos de provisionamento excessivo com base no uso de pico — e pagamentos desnecessários. Ou (2) provisionamento insuficiente para poupar custos em detrimento do desempenho e da satisfação do cliente durante os picos.

Pools de banco de dados elástico resolvem esse problema.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

Pools de banco de dados elástico garantem que os bancos de dados obtenham os recursos de desempenho que necessitam, quando necessário, fornecendo um mecanismo de alocação de recurso simples e um orçamento previsível. Um pool é fornecido com um número definido de eDTUs por um preço definido. Dentro do pool, os bancos de dados individuais recebem a flexibilidade para dimensionar automaticamente no conjunto de parâmetros. Sob carga pesada, um banco de dados pode consumir mais eDTUs para atender à demanda. Bancos de dados sob cargas leves consumem menos e bancos de dados sem carga não consomem nenhum eDTU. O provisionamento de recursos para o pool inteiro em vez do bancos de dados único simplifica as tarefas de gerenciamento. Além disso, você tem um orçamento previsível para o pool.

eDTUs adicionais podem ser adicionados a um pool existente sem tempo de inatividade do banco de dados ou impacto negativo sobre os bancos de dados. Da mesma forma, se eDTUs extras não forem mais necessários, eles poderão ser removidos de um pool existente a qualquer momento.

E você pode adicionar ou subtrair bancos de dados para o pool. Se um banco de dados é previsível por estar subutilizando recursos, mova-o.

## Quais bancos de dados vão em um pool?

![Bancos de dados SQL que compartilham eDTUs em um pool de banco de dados elástico.][1]

Bancos de dados que são ótimos candidatos a pools de banco de dados elástico normalmente têm períodos de atividade e períodos de inatividade. No exemplo acima, você vê a atividade de um banco de dados individual, de 4 bancos de dados e, finalmente, um pool de banco de dados elástico com 20 bancos de dados. Bancos de dados com diferentes atividades ao longo do tempo são bons candidatos para pools Elásticos porque eles não estão todos ativos ao mesmo tempo e podem compartilhar eDTUs. Nem todos os bancos de dados se encaixam nesse padrão. Bancos de dados com uma demanda de recursos mais constante são mais adequados para as camadas de serviço Basic, Standard e Premium, em que os recursos são atribuídos individualmente.

[Considerações de preço e desempenho para um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md).


> [AZURE.NOTE] No momento, os pools de banco de dados elástico estão em visualização e disponíveis apenas com Servidores V12 do Banco de Dados SQL.

## Trabalhos de banco de dados elástico

Com um pool, as tarefas de gerenciamento são simplificadas por execução de scripts em **[trabalhos Elásticos](sql-database-elastic-jobs-overview.md)**. Um Trabalho de Banco de Dados Elástico elimina a maioria do tédio associado a um grande número de bancos de dados. Para começar, consulte [Introdução a trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-getting-started.md).

Para obter mais informações sobre outras ferramentas, consulte o [Mapa de aprendizagem das ferramentas de banco de dados elástico](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/).

## Recursos de continuidade de negócios para bancos de dados em um pool

Atualmente em visualização, os bancos de dados elásticos dão suporte à maioria dos [recursos de continuidade de negócios](sql-database-business-continuity.md) disponíveis para bancos de dados únicos em servidores V12.

### Recuperação Pontual

O sistema realiza backup automático dos bancos de dados em um pool de banco de dados elástico, e a política de retenção de backup é a mesma que a camada de serviços correspondente para bancos de dados únicos. Em suma, os bancos de dados em cada camada têm um intervalo de restauração diferente:

* **Pool básico**: capaz de fazer a restauração para qualquer ponto nos últimos 7 dias. 
* **Pool padrão**: capaz de fazer a restauração para qualquer ponto dentro dos últimos 14 dias.
* **Pool premium**: capaz de fazer a restauração para qualquer ponto nos últimos 35 dias. 

Durante a visualização, os bancos de dados em um pool serão restaurados para um novo banco de dados no mesmo pool. Bancos de dados descartados sempre serão restaurados como um banco de dados autônomo fora do pool para o nível de desempenho mais baixo para essa camada de serviço. Por exemplo, um banco de dados elástico em um pool padrão que é descartado será restaurado como um banco de dados S0. Você pode executar operações de restauração de bancos de dados pelo Portal do Azure ou programaticamente usando a API REST. O suporte ao cmdlet do PowerShell será disponibilizado em breve.

### Restauração geográfica

A Restauração Geográfica permite recuperar o banco de dados de um pool para um servidor em uma região diferente. Durante a visualização, para restaurar um banco de dados de um pool em um servidor diferente, o servidor de destino de ter um pool com o mesmo nome do pool de origem. Se necessário, crie um novo pool no servidor de destino e dê a ele o mesmo nome antes de restaurar o banco de dados. Caso o servidor de destino não contenha um pool com o mesmo nome, a operação de Restauração Geográfica falhará. Para obter detalhes, consulte [Recuperar usando a restauração geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore).


### Replicação Geográfica

A replicação geográfica está disponível para qualquer banco de dados em um pool de banco de dados elástico Standard ou Premium. Um ou todos os bancos de dados em uma parceria de replicação geográfica podem ser em um pool de banco de dados elástico, contanto que as camadas de serviço sejam as mesmas. Você pode configurar a replicação geográfica para pools de bancos de dados elásticos usando o [Portal do Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md) ou [Transact-SQL](sql-database-geo-replication-transact-sql.md).

### Importação e exportação

É possível exportar um banco de dados de dentro de um pool. Atualmente, não há suporte para a importação de um banco de dados diretamente para um pool, mas você pode importar para um único banco de dados e, em seguida, mover o banco de dados para um pool.


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=AcomDC_0218_2016-->