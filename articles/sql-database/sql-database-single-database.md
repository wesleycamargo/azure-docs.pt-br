---
title: O que é um banco de dados individual do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre o banco de dados individual no Banco de Dados SQL
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 034/08/2019
ms.openlocfilehash: 804e60b141f6ad4f089710d4b85c52a8dad33cfc
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269513"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>O que é um banco de dados individual no Banco de Dados SQL do Azure

A opção de implantação de banco de dados individual cria um banco de dados no Banco de Dados SQL com seu próprio conjunto de recursos e é gerenciada por meio de um servidor de Banco de Dados SQL. Com um banco de dados individual, cada banco de dados é portátil e fica isolado uns dos outros, cada qual com sua própria camada de serviço dentro do [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) ou do [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) e uma garantia de tamanho de computação.

> [!IMPORTANT]
> O banco de dados individual é uma das três opções de implantação para o Banco de Dados SQL. As outras duas são [pools elásticos](sql-database-elastic-pool.md) e [instância gerenciada](sql-database-managed-instance.md).
> [!NOTE]
> Para obter um glossário de termos no Banco de Dados SQL do Azure, consulte [Glossário de termos do Banco de Dados SQL](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Dimensionamento dinâmico

Você pode criar seu primeiro aplicativo em um banco de dados individual pequeno com um baixo custo mensal na camada de serviço de baixo custo/desempenho e [alterar a camada de serviço](sql-database-single-database-scale.md) manualmente ou de forma programática a qualquer momento para a camada de serviço de maior preço/desempenho para atender às necessidades da solução. Você pode ajustar o desempenho sem tempo de inatividade para seu aplicativo ou para seus clientes. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente às mudanças rápidas de requisitos de recursos e que você pague apenas pelos recursos de que precisa, quando precisar deles.

## <a name="single-databases-and-elastic-pools"></a>Bancos de dados individuais e pools elásticos

Um banco de dados individual pode ser movido de ou para um [pool elástico](sql-database-elastic-pool.md) para compartilhamento de recursos. Para muitas empresas e aplicativos, ser capaz de criar bancos de dados únicos e ajustar o desempenho sob demanda é o suficiente, especialmente se os padrões de uso forem relativamente previsíveis. Mas se você tiver os padrões de uso imprevisíveis, pode ser difícil de gerenciar os custos e o seu modelo de negócios. Os pools elásticos são projetados para resolver esse problema. O conceito é simples. Você aloca recursos de desempenho a um pool em vez de a um banco de dados individual e paga pelos recursos de desempenho coletivo do pool, em vez de pelo desempenho de banco de dados único.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas

Use as ferramentas de [monitoramento de desempenho interno](sql-database-performance.md) e [alerta](sql-database-insights-alerts-portal.md) em conjunto com as classificações de desempenho baseadas nos vCores. Usando essas ferramentas, você pode avaliar rapidamente o impacto da expansão ou redução com base nas suas necessidades de desempenho atuais ou de projeto. Além disso, o Banco de Dados SQL pode [emitir métrica e logs de diagnóstico](sql-database-metrics-diag-logging.md) para facilitar o monitoramento.

## <a name="availability-capabilities"></a>Recursos de disponibilidade

Bancos de dados individuais, pools Elásticos e instâncias gerenciadas fornecem muitas características de disponibilidade. Para obter informações, confira [Características de disponibilidade](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Diferenças do Transact-SQL

Há suporte total, tanto no Microsoft SQL Server quanto no Banco de Dados SQL do Azure, para a maioria dos aplicativos e recursos Transact-SQL. Por exemplo, os componentes principais do SQL como tipos de dados, operadores, cadeia de caracteres, funções aritméticas, lógicas e de cursor funcionam da mesma maneira no SQL Server e no Banco de Dados SQL. No entanto, existem algumas diferenças de T-SQL em DDL (linguagem de definição de dados) e elementos DML (linguagem de manipulação de dados), resultando em instruções T-SQL e consultas que têm suporte apenas parcial (o que discutiremos posteriormente neste artigo).
Além disso, há alguns recursos e sintaxe sem suporte nenhum, porque o Banco de Dados SQL do Azure foi criado para isolar recursos de dependências no banco de dados mestre e no sistema operacional. Assim, a maioria das atividades no nível do servidor são inapropriadas para o Banco de Dados SQL. As opções e instruções Transact-SQL não estão disponíveis se elas configuram opções no nível do servidor, componentes do sistema operacional ou se especificam a configuração do sistema de arquivos. Quando essas funcionalidades são necessárias, uma alternativa apropriada costuma estar disponível de alguma forma no Banco de Dados SQL ou em outro recurso ou serviço do Azure.

Para obter mais informações, confira [Resolvendo diferenças de Transact-SQL durante a migração para o Banco de Dados SQL](sql-database-transact-sql-information.md).

## <a name="security"></a>Segurança

O Banco de Dados SQL fornece uma variedade de [recursos internos de segurança e conformidade](sql-database-security-overview.md) para ajudar seu aplicativo a atender a vários requisitos de conformidade e segurança.

> [!IMPORTANT]
> Azure SQL Database (todas as opções de implantação) foi certificado em relação a vários padrões de conformidade. Para obter mais informações, consulte o [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/) onde você pode encontrar a lista mais atual das certificações de conformidade do banco de dados SQL.

## <a name="next-steps"></a>Próximas etapas

- Para começar rapidamente com um banco de dados individual, comece com o [Guia de Início Rápido de banco de dados individual](sql-database-single-database-quickstart-guide.md).
- Para saber mais sobre como migrar um banco de dados do SQL Server para o Azure, confira [Migrar para o Banco de Dados SQL do Azure](sql-database-single-database-migrate.md).
- Para obter informações sobre os recursos com suporte, consulte [Recursos](sql-database-features.md).
