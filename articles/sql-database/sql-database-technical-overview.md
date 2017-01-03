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
ms.date: 11/08/2016
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: 3ba16154857f8e7b59a1013b736d6131a4161185
ms.openlocfilehash: 2d0792046bf55d691df21e26f2df23235318665c


---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>O que é o Banco de Dados SQL? Introdução ao Banco de dados SQL
O Banco de Dados SQL é um serviço de banco de dados relacional na nuvem com base no mecanismo líder de mercado do Microsoft SQL Server que contém recursos internos críticos. O Banco de Dados SQL oferece desempenho previsível, escalabilidade sem tempo de inatividade, continuidade dos negócios e proteção de dados e quase não exige administração. Você pode se concentrar no desenvolvimento rápido de aplicativos e acelerar seu tempo de colocação no mercado, em vez de gerenciar máquinas virtuais e infraestrutura. Como ele se baseia no mecanismo [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) , o Banco de Dados SQL oferece suporte a ferramentas do SQL Server, bibliotecas e APIs, o que torna mais fácil mover e estender para a nuvem.

Este artigo é uma introdução aos principais conceitos e recursos do Banco de Dados SQL relacionados ao desempenho, escalabilidade e gerenciabilidade, com links para explorar os detalhes. Se você estiver pronto para entrar, poderá [Criar seu primeiro Banco de Dados SQL](sql-database-get-started.md) ou [Criar um pool elástico](sql-database-elastic-pool-create-portal.md) em minutos. Se você quiser se aprofundar, assista a este vídeo de 30 minutos.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-SQL-Database-create-DBs-in-seconds/player]
> 
> 

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajuste de desempenho e dimensionamento sem tempo de inatividade
Os banco de dados SQL estão disponíveis nas *camadas de serviço*Básica, Standard e Premium. Cada camada de serviço oferece [diferentes níveis de desempenho e recursos](sql-database-service-tiers.md) para dar suporte a cargas de trabalho leves e pesadas de banco de dados. Você pode compilar seu primeiro aplicativo em um banco de dados pequeno com alguns dólares por mês, e, em seguida, [alterar a camada de serviço](sql-database-scale-up.md) manualmente ou de forma programática a qualquer momento à medida que o seu aplicativo torna-se viral em todo o mundo, sem tempo de inatividade para seu aplicativo ou para seus clientes.

Para muitas empresas e aplicativos, ser capaz de criar bancos de dados únicos e ajustar o desempenho sob demanda é o suficiente, especialmente se os padrões de uso forem relativamente previsíveis. Mas se você tiver os padrões de uso imprevisíveis, pode ser difícil de gerenciar os custos e o seu modelo de negócios.

[pools elásticos](sql-database-elastic-pool.md) no Banco de Dados SQL resolvem esse problema. O conceito é simples. Você alocar desempenho a um pool e paga pelo desempenho coletivo do pool em vez do desempenho do banco de dados individual. Você não precisa ajustar o desempenho de banco de dados. Os bancos de dados no pool, chamados *bancos de dados elásticos*, são automaticamente escalados e reduzidos verticalmente para atender à demanda. Os bancos de dados elásticos consomem, mas não excedem os limites do pool, então o custo permanece previsível, mesmo que o uso do banco de dados não. Além disso, é possível [adicionar e remover bancos de dados do pool](sql-database-elastic-pool-manage-portal.md), escalar seu aplicativo por meio de alguns bancos de dados para milhares, tudo dentro de um orçamento que você pode controlar. Para saber mais sobre padrões de design para aplicativos SaaS usando pools elásticos, confira [Padrões de design para aplicativos de SaaS multilocatários com o banco de dados SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Seja qual for a forma escolhida por você, individual ou elástico, você não fica limitado. Você pode misturar bancos de dados individuais a pools elásticos e alterar as camadas de serviço dos bancos de dados individuais e pools de forma rápida e fácil para se adaptar à sua situação. E mais, com a potência e o alcance do Azure, você pode combinar e corresponder outros serviços do Azure com o Banco de Dados SQL para atender às suas necessidades exclusivas de design de aplicativo, direcionar as eficiências de recursos e custo, bem como descobrir novas oportunidades de negócios.

Mas como você pode comparar o desempenho relativo de bancos de dados e pools de banco de dados? Como você sabe o momento certo de parar ao fazer o ajuste? A resposta está no desempenho de monitoramento interno e nas ferramentas de alerta, combinadas com as classificações de desempenho com base em DTUs (Unidades de Transação do Banco de Dados) para bancos de dados individuais e DTUs Elásticos (eDTUs) para bancos de dados elásticos e pools de banco de dados, que permitem que você rapidamente avalie o impacto de escalar ou reduzir verticalmente com base em sua necessidades de desempenho atual ou projetadas. Confira [Opções e desempenho de Banco de Dados SQL: saiba o que está disponível em cada camada de serviço](sql-database-service-tiers.md) para obter detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha seus aplicativos e a continuidade dos negócios
Os acordos de nível de serviço [(SLA)](http://azure.microsoft.com/support/legal/sla/)de disponibilidade de 99,99% de líderes do setor do Azure, alimentados por uma rede global de datacenters gerenciados pela Microsoft, ajuda a manter seu aplicativo em execução 24/7. Com cada banco de dados SQL, você tira proveito dos recursos internos de segurança, tolerância a falhas e de proteção de dados que, em outras situações, você teria que comprar ou projetar, criar e gerenciar. Mesmo assim, dependendo das demandas do seu negócio, você pode precisar de camadas adicionais de proteção para garantir que seu aplicativo e sua empresa possam recuperar-se rapidamente no caso de um desastre, um erro ou outra interrupção. Com o banco de dados SQL, cada camada de serviço oferece um conjunto abrangente de recursos de continuidade de negócios e opções que você pode usar para se recuperar voltar à execução e permanecer assim. Você pode usar a restauração para um ponto específico para retornar um banco de dados para um estado anterior, até 35 dias. Além disso, se o datacenter que hospeda seus bancos de dados sofrer uma interrupção, você pode restaurar bancos de dados de cópias de backups recentes com redundância geográfica ou fazer failover de réplicas de banco de dados em uma região diferente. Você também pode usar réplicas para atualizações ou realocação para diferentes regiões.

![Replicação geográfica do Banco de Dados SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)

Veja [Continuidade dos negócios](sql-database-business-continuity.md) para obter detalhes sobre os diferentes recursos de continuidade dos negócios disponíveis para diferentes camadas de serviço.

## <a name="secure-your-data"></a>Proteja seus dados
O SQL Server possui uma tradição de segurança de dados sólida que o banco de dados SQL mantém com recursos que limitam o acesso, protegem os dados e ajudam você a monitorar a atividade. Confira [Protegendo seu banco de dados SQL](sql-database-security.md) para um rápido resumo das opções de segurança em Banco de Dados SQL. Confira a [Central de Segurança para o Mecanismo do Banco de Dados do SQL Server e do Banco de Dados SQL](https://msdn.microsoft.com/library/bb510589) para uma visão mais abrangente dos recursos de segurança. E visite a [Central de Confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/security/) para obter informações sobre segurança da plataforma do Azure.

## <a name="next-steps"></a>Próximas etapas
Agora que você leu uma introdução ao Banco de Dados SQL e respondeu à pergunta "O que é o Banco de Dados SQL?", está pronto para o seguinte:

* Confira a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para ver comparações de custos entre o banco de dados único e pools elásticos e calculadoras.
* Saiba mais sobre [pools elásticos](sql-database-elastic-pool.md).
* Comece pela [criação do seu primeiro banco de dados](sql-database-get-started.md).
* [Conectar e consultar com SSMS](sql-database-connect-query-ssms.md)
* Crie seu primeiro aplicativo em C#, Java, Node.js, PHP, Python ou Ruby: [bibliotecas de conexão para Banco de Dados SQL e SQL Server](sql-database-libraries.md)



<!--HONumber=Dec16_HO3-->


