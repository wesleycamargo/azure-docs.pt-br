---
title: "O que é o Banco de Dados SQL? Introdução ao Banco de Dados SQL | Microsoft Docs"
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: "introdução ao sql, introdução ao sql, o que é o banco de dados sql"
services: sql-database
documentationcenter: 
author: shontnew
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 12/20/2016
ms.author: shkurhek;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 187954f3ddafdbc17e341ce41f5b109cb95f8a24
ms.openlocfilehash: 399fdbeee479f5598e6ed7f9bd6b6080ec919c98

---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>O que é o Banco de Dados SQL? Introdução ao Banco de dados SQL
O Banco de Dados SQL é um serviço de banco de dados relacional de nuvem da Microsoft com base no mecanismo líder de mercado do Microsoft SQL Server e é capaz de lidar com cargas de trabalho críticas. O Banco de Dados SQL oferece um desempenho previsível em vários níveis de serviço, escalabilidade dinâmica sem tempo de inatividade, continuidade de negócios interna e proteção de dados, com administração quase zero. Esses recursos permitem que você se concentre no desenvolvimento rápido de aplicativos e acelere seu tempo de colocação no mercado, em vez de alocar tempo e recursos preciosos para gerenciamento de máquinas virtuais e infraestrutura. Como o SQL Database se baseia no mecanismo [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx), o Banco de Dados SQL oferece suporte a ferramentas do SQL Server, bibliotecas e APIs. Como resultado, é fácil desenvolver novas soluções para mover suas soluções existentes do SQL Server e estender as soluções existentes do SQL Server para a nuvem da Microsoft sem precisar aprender novas habilidades.

Este artigo é uma introdução aos principais conceitos e recursos do Banco de Dados SQL relacionados ao desempenho, escalabilidade e gerenciabilidade, com links para explorar os detalhes. Se você estiver pronto para iniciar tutoriais práticos, acesse [Criar seu primeiro banco de dados SQL](sql-database-get-started.md) ou [Criar um pool elástico](sql-database-elastic-pool-create-portal.md). Para obter uma breve demonstração em vídeo, assista a este vídeo.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-create-DBs-in-seconds/player]
> 
> 

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajuste de desempenho e dimensionamento sem tempo de inatividade
O serviço Banco de Dados SQL oferece três camadas de serviço: Básica, Standard e Premium. Cada camada de serviço oferece [diferentes níveis de desempenho e recursos](sql-database-service-tiers.md) para dar suporte a cargas de trabalho leves e pesadas de banco de dados. Você pode criar seu primeiro aplicativo em um banco de dados pequeno por alguns dólares por mês e, em seguida, [alterar sua camada de serviço](sql-database-scale-up.md) manualmente ou por meio de programação a qualquer momento para atender às necessidades de sua solução. Você pode fazer isso sem tempo de inatividade para seu aplicativo ou seus clientes. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente às mudanças rápidas de requisitos de recursos e que você pague apenas pelos recursos de que precisa, quando precisar deles.

## <a name="elastic-pools-to-maximize-resource-utilization"></a>Pools elásticos para maximizar a utilização de recursos
Para muitas empresas e aplicativos, ser capaz de criar bancos de dados únicos e ajustar o desempenho sob demanda é o suficiente, especialmente se os padrões de uso forem relativamente previsíveis. Mas se você tiver os padrões de uso imprevisíveis, pode ser difícil de gerenciar os custos e o seu modelo de negócios. [Pools elásticos](sql-database-elastic-pool.md) são projetados para resolver esse problema. O conceito é simples. Você aloca recursos de desempenho para um pool em vez de um banco de dados individual e paga pelos recursos de desempenho coletivo do pool em vez do desempenho do banco de dados único. Com os pools elásticos, você não precisa se concentrar em expandir ou reduzir o desempenho do banco de dados conforme a demanda de recursos flutua. Os bancos de dados em pool consomem os recursos de desempenho do pool elástico conforme necessário. Os bancos de dados em pool consomem, mas não excedem os limites do pool. Portanto, o custo permanece previsível, mesmo que o uso do banco de dados individual não permaneça. Além disso, é possível [adicionar e remover bancos de dados do pool](sql-database-elastic-pool-manage-portal.md), escalar seu aplicativo por meio de alguns bancos de dados para milhares, tudo dentro de um orçamento que você pode controlar. Por fim, você também pode controlar os recursos mínimos e máximos disponíveis para bancos de dados no pool para garantir que nenhum banco de dados no pool use o recurso de pool e que cada banco de dados no pool tenha um mínimo garantido de recursos. Para saber mais sobre os padrões de design para aplicativos SaaS usando pools Elásticos, confira [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Padrões de Design para aplicativos de SaaS multilocatários com o Banco de Dados SQL).

## <a name="blend-single-databases-with-pooled-databases"></a>Combinar bancos de dados individuais a bancos de dados agrupados
Seja qual for a sua escolha (bancos de dados únicos ou pools elásticos), você não está limitado. Você pode misturar bancos de dados individuais a pools elásticos e alterar as camadas de serviço dos bancos de dados individuais e pools elásticos de forma rápida e fácil para se adaptar à sua situação. E mais, com a potência e o alcance do Azure, você pode combinar e corresponder outros serviços do Azure com o Banco de Dados SQL para atender às suas necessidades exclusivas de design de aplicativo, direcionar as eficiências de recursos e custo, bem como descobrir novas oportunidades de negócios.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas
Mas como você pode comparar o desempenho relativo de bancos de dados únicos e pools elásticos? Como você sabe o momento certo de parar ao fazer o ajuste? Você usa as ferramentas de [monitoramento de desempenho interno](sql-database-performance.md) e [alerta](sql-database-insights-alerts-portal.md), combinadas às classificações de desempenho com base em [DTUs (unidades de transação de banco de dados) para bancos de dados único e eDTUs (DTUs elásticos) para pools elásticos](sql-database-what-is-a-dtu.md). Usando essas ferramentas, você pode avaliar rapidamente o impacto da expansão ou redução com base nas suas necessidades de desempenho atuais ou de projeto. Confira [Opções e desempenho de Banco de Dados SQL: saiba o que está disponível em cada camada de serviço](sql-database-service-tiers.md) para obter detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha seus aplicativos e a continuidade dos negócios
Os acordos de nível de serviço [(SLA)](http://azure.microsoft.com/support/legal/sla/)de disponibilidade de 99,99% de líderes do setor do Azure, alimentados por uma rede global de datacenters gerenciados pela Microsoft, ajuda a manter seu aplicativo em execução 24/7. Com cada banco de dados SQL, você tira proveito dos recursos internos de segurança, tolerância a falhas e de [proteção de dados](sql-database-automated-backups.md) que, em outras situações, você teria que comprar ou projetar, criar e gerenciar. Com o banco de dados SQL, cada camada de serviço oferece um conjunto abrangente de recursos de continuidade de negócios e opções que você pode usar para se recuperar voltar à execução e permanecer assim. Você pode usar a [restauração para um ponto específico](sql-database-recovery-using-backups.md) para retornar um banco de dados para um estado anterior, até 35 dias. Você pode configurar a [retenção de backup de longo prazo](sql-database-long-term-retention.md) para armazenar backups em um cofre seguro por até 10 anos. Além disso, se o datacenter que hospeda seus bancos de dados sofrer uma interrupção, você poderá restaurar bancos de dados de [cópias com redundância geográfica de backups recentes]((sql-database-recovery-using-backups.md). Se necessário, você também pode configurar [réplicas legíveis com redundância geográfica](sql-database-geo-replication-overview.md) em uma ou mais regiões para permitir o failover rápido, no caso de uma paralisação do data center. Você também pode usar essas réplicas para melhorar o desempenho de leitura em diferentes regiões geográficas ou para [atualizações de aplicativo sem tempo de inatividade](sql-database-manage-application-rolling-upgrade.md). 

![Replicação geográfica do Banco de Dados SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)

Veja [Continuidade dos negócios](sql-database-business-continuity.md) para obter detalhes sobre os diferentes recursos de continuidade dos negócios disponíveis para diferentes camadas de serviço.

## <a name="secure-your-data"></a>Proteja seus dados
O SQL Server possui uma tradição de segurança de dados que o banco de dados SQL mantém com recursos que limitam o acesso, protegem os dados e ajudam você a monitorar a atividade. Confira [Protegendo seu banco de dados SQL](sql-database-security-overview.md) para um rápido resumo das opções de segurança em Banco de Dados SQL. Confira a [Central de Segurança para o Mecanismo do Banco de Dados do SQL Server e do Banco de Dados SQL](https://msdn.microsoft.com/library/bb510589) para uma visão mais abrangente dos recursos de segurança. E visite a [Central de Confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/security/) para obter informações sobre segurança da plataforma do Azure.

## <a name="next-steps"></a>Próximas etapas
Agora que você leu uma introdução ao Banco de Dados SQL e respondeu à pergunta "O que é o Banco de Dados SQL?", está pronto para o seguinte:

* Confira a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para ver comparações de custos entre o banco de dados único e pools elásticos e calculadoras.
* Saiba mais sobre [pools elásticos](sql-database-elastic-pool.md).
* Comece pela [criação do seu primeiro banco de dados](sql-database-get-started.md).
* Crie seu primeiro aplicativo em C#, Java, Node.js, PHP, Python ou Ruby: [bibliotecas de conexão para Banco de Dados SQL e SQL Server](sql-database-libraries.md)



<!--HONumber=Jan17_HO2-->


