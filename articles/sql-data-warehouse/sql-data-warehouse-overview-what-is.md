---
title: "O que é o SQL Data Warehouse do Azure? | Microsoft Docs"
description: "Banco de dados distribuído de nível corporativo com capacidade de processar volumes de petabytes de dados relacionais e não relacionais. Ele é o primeiro data warehouse de nuvem do setor que pode ser expandido, reduzido e pausado questão de segundos."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jenniehubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 10/23/2017
ms.author: jrj
ms.openlocfilehash: 7fd17c29c413905084c6b3736018d111d3b28fea
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2017
---
# <a name="what-is-azure-sql-data-warehouse"></a>O que é o SQL Data Warehouse do Azure?

O SQL Data Warehouse é um EDW (Enterprise Data Warehouse) que aproveita o MPP (Massively Parallel Processing) para executar rapidamente consultas complexas em petabytes de dados. Use o SQL Data Warehouse como um componente fundamental de uma solução de big data. Importe big data para o SQL Data Warehouse com consultas T-SQL simples do PolyBase e, em seguida, use o poder do MPP para executar a análise de alto desempenho. À medida que você integrar e analisar, o data warehouse se tornará a única versão da verdade com que sua empresa poderá contar para obter informações.  


## <a name="key-component-of-big-data-solution"></a>Componente fundamental da solução de big data
SQL Data Warehouse é um componente fundamental de uma solução de ponta a ponta de big data na Nuvem.

![Solução de data warehouse](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

Em uma solução de dados de nuvem, os dados são incluídos nos repositórios de big data de uma variedade de fontes. Uma vez em um repositório de big data, os algoritmos do Hadoop, do Spark e de machine learning preparam e treinam os dados. Quando os dados estiverem prontos para análises complexas, o SQL Data Warehouse usará o PolyBase para consultar os armazenamentos de big data. O PolyBase usa consultas T-SQL padrão para trazer os dados para o SQL Data Warehouse.
 
O SQL Data Warehouse armazena dados em tabelas relacionais com armazenamento colunar. Esse formato reduz consideravelmente os custos de armazenamento de dados e melhora o desempenho da consulta. Depois que os dados forem armazenados no SQL Data Warehouse, você poderá executar análises em grande escala. Em comparação aos tradicionais sistemas de banco de dados, as consultas de análise são concluídas em segundos em vez de minutos, ou em horas em vez de dias. 

Os resultados da análise podem ir para aplicativos ou bancos de dados em todo o mundo. Os analistas de negócios podem então obter informações para tomar decisões de negócios bem informadas.

## <a name="optimization-choices"></a>Opções de otimização

O SQL Data Warehouse oferece [níveis de desempenho](performance-tiers.md) projetados para flexibilidade para atender às suas necessidades dos dados, grandes ou pequenos. Você pode escolher um data warehouse otimizado para elasticidade ou para a computação. 

- A **camada de desempenho Otimizado para Elasticidade** separa as camadas de armazenamento e computação na arquitetura. Essa opção se destaca em cargas de trabalho que podem aproveitar ao máximo a separação entre o armazenamento e a computação, dimensionando com frequência para dar suporte a curtos períodos de atividade na atividade de pico. Essa camada de computação tem o menor ponto de preço de entrada e é dimensionada para dar suporte à maioria das cargas de trabalho do cliente.

- O **nível de desempenho Otimizado para Computação** usa o hardware mais recente do Azure para apresentar um novo cache de disco de estado sólido NVMe que mantém os dados acessados com mais frequência próximos às CPUs, que está exatamente onde você os deseja. Ao criar camadas de armazenamento automaticamente, esse nível de desempenho se destaca com consultas complexas, já que todas as E/S são mantidas locais na camada de computação. Além disso, o columnstore foi aprimorado para armazenar uma quantidade ilimitada de dados no SQL Data Warehouse. O nível de desempenho Otimizado para Computação fornece o maior nível de escalabilidade, permitindo que você aumente até 30.000 cDWU (Unidades de Data Warehouse de computação). Escolha essa camada para cargas de trabalho que exijam desempenho contínuo e ultrarrápido.

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe um pouco sobre o SQL Data Warehouse, saiba como [criar um SQL Data Warehouse][create a SQL Data Warehouse] e [carregar dados de amostra][load sample data] rapidamente. Se você ainda não conhece o Azure, poderá achar o [Glossário do Azure][Azure glossary] útil à medida que encontrar nova terminologia. Ou, dê uma olhada em alguns desses outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogs]
* [Solicitações de recursos]
* [Vídeos]
* [Blogs da Equipe Consultoria para Clientes]
* [Criar um tíquete de suporte]
* [Fórum do MSDN]
* [Fórum Stack Overflow]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar um tíquete de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da Equipe Consultoria para Clientes]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/en-us/support/legal/sla/
