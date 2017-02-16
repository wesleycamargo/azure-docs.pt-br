---
title: Limites de recursos do Banco de Dados SQL do Azure | Microsoft Docs
description: "Esta página descreve alguns limites de recurso comuns para o Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 12/06/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: b5306d864593898246d0e7ba81ed3f93253fab74
ms.openlocfilehash: 81746a6f893eab4fe2b951fb0d52bd1645d2a56c


---
# <a name="azure-sql-database-resource-limits"></a>Limites de recursos do Banco de Dados SQL do Azure
## <a name="overview"></a>Visão geral
O Banco de Dados SQL do Azure gerencia os recursos disponíveis para um banco de dados usando dois mecanismos diferentes: **Governança de Recursos** e **Imposição de Limites**. Este tópico explica essas duas áreas principais do gerenciamento de recursos.

## <a name="resource-governance"></a>Governança de recursos
Uma das metas de design das camadas de serviço Básica, Standard e Premium é que o Banco de Dados SQL do Azure se comporte como se o banco de dados estivesse sendo executado em seu próprio computador, completamente isolado de outros bancos de dados. A governança de recursos emula esse comportamento. Se a utilização de recursos agregada atingir o máximo de recursos da CPU, Memória, E/S de log e de E/S de dados disponíveis atribuídos ao banco de dados, a governança de recursos colocará na fila as consultas em execução e atribuirá recursos às consultas em fila conforme eles sejam liberados.

Assim como em um computador dedicado, utilizar todos os recursos disponíveis resultará em uma execução mais longa das consultas atualmente em execução, o que poderá resultar em tempos limite de comando no cliente. Aplicativos com uma lógica de repetição agressiva e aplicativos que executam consultas no banco de dados com uma alta frequência podem encontrar mensagens de erro ao tentar executar novas consultas quando o limite de solicitações simultâneas for atingido.

### <a name="recommendations"></a>Recomendações:
Monitore a utilização de recursos, bem como os tempos de resposta médios das consultas quando se aproximar da utilização máxima de um banco de dados. Quando encontrar latências maiores de consulta, você geralmente terá três opções:

1. Reduzir a quantidade de solicitações de entrada para o banco de dados para prevenir o tempo limite e o acúmulo de solicitações.
2. Atribuir um nível mais alto de desempenho ao banco de dados.
3. Otimizar consultas para reduzir a utilização de recursos de cada consulta. Para obter mais informações, veja a seção Ajuste/dicas de consulta no artigo Diretrizes de desempenho do Banco de Dados SQL do Azure.

## <a name="enforcement-of-limits"></a>Imposição de limites
Os recursos, além da CPU, Memória, E/S de log e E/S de dados, são impostos pela negação de novas solicitações quando os limites são atingidos. Os clientes receberão uma [mensagem de erro](sql-database-develop-error-messages.md) , dependendo do limite que foi atingido.

Por exemplo, o número de conexões a um Banco de Dados SQL, bem como o número de solicitações simultâneas que podem ser processadas, é restrito. O Banco de Dados SQL permite que o número de conexões ao banco de dados seja maior que o número de solicitações simultâneas para dar suporte ao pooling de conexões. Embora a quantidade de conexões disponíveis possam ser facilmente controladas pelo aplicativo, a quantidade de solicitações paralelas costuma ser mais difícil de estimar e controlar. Especialmente durante os picos de carga quando o aplicativo envia um número excessivo de solicitações ou quando o banco de dados atinge seus limites de recursos e começa a acumular threads de trabalho devido a consultas com execução mais longa, é possível encontrar erros.

## <a name="service-tiers-and-performance-levels"></a>Camadas de serviço e níveis de desempenho
Há níveis de serviço e níveis de desempenho para os pools elásticos e de banco de dados autônomo.

### <a name="standalone-databases"></a>Bancos de dados autônomos
Para um banco de dados autônomo, os limites são definidos pelo nível de serviço e de desempenho do banco de dados. A tabela a seguir descreve as características dos bancos de dados Basic, Standard e Premium em vários níveis de desempenho.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="elastic-pools"></a>Pools elásticos
[Pools elásticos](sql-database-elastic-pool.md) compartilham recursos entre bancos de dados no pool. A tabela a seguir descreve as características dos pools de bancos de dados elásticos Basic, Standard e Premium.

[!INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Para obter uma definição expandida de cada recurso listado nas tabelas anteriores, consulte as descrições em [Limites e recursos das camadas de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits). Para obter uma visão geral das camadas de serviço, consulte [Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure](sql-database-service-tiers.md).

## <a name="other-sql-database-limits"></a>Outros limites de Banco de Dados SQL
| Área | Limite | Descrição |
| --- | --- | --- |
| Bancos de dados usando a exportação Automatizada por assinatura |10 |A exportação automatizada permite que você crie uma agenda personalizada para realização do backup de seus bancos de dados SQL. A versão prévia deste recurso será encerrada em 1º de março de 2017.  |
| Banco de dados por servidor |Até 5.000 |Até 5.000 bancos de dados são permitidos por servidor em servidores V12. |
| DTUs por servidor |45000 |45.000 DTUs estão disponíveis por servidor em servidores V12 para o provisionamento de bancos de dados, pools elásticos e data warehouses. |

## <a name="resources"></a>Recursos
[Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)

[Faixas de Serviço de Banco de Dados SQL do Azure e Níveis de Desempenho](sql-database-service-tiers.md)

[Mensagens de erro para programas cliente do Banco de Dados SQL](sql-database-develop-error-messages.md)




<!--HONumber=Nov16_HO3-->


