---
title: Camada de Uso Geral - Serviço do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre a camada de Uso Geral do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 943982b056a83488426c48763deac14fd5347b8e
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508984"
---
# <a name="general-purpose-tier---azure-sql-database"></a>Camada de Uso Geral - Banco de Dados SQL do Azure

> [!NOTE]
> A camada de Uso Geral é chamada de Padrão no modelo de compra de DTU. Para obter uma comparação do modelo de compra baseado no vCore com o modelo de compra baseado em DTU, consulte [Modelos e recursos de compra do Banco de Dados SQL do Azure](sql-database-service-tiers.md).

O Banco de Dados SQL do Azure baseia-se na arquitetura de mecanismo de banco de dados do SQL Server, que é ajustada para o ambiente de nuvem para garantir disponibilidade de 99,99%, até mesmo no caso de falhas de infraestrutura. Há três modelos de arquitetura que são usados no Banco de Dados SQL do Azure:
- Uso geral 
- Comercialmente Crítico
- Hiperescala

O modelo Uso Geral baseia-se em uma separação de computação e armazenamento. Esse modelo de arquitetura baseia-se na alta disponibilidade e confiabilidade do Armazenamento Premium do Azure, que replica os arquivos de banco de dados de forma transparente e garante que não haja perda de dados se ocorrer uma falha na infraestrutura subjacente.

A figura a seguir mostra quatro nós no modelo de arquitetura padrão com as camadas de computação e armazenamento separadas.

![Separação de computação e armazenamento](media/sql-database-managed-instance/general-purpose-service-tier.png)

No modelo de Uso Geral há duas camadas:

- Uma camada de computação sem estado que está executando o processo `sqlserver.exe` e contém apenas dados temporários e armazenados em cache (por exemplo - cache de plano, conjunto de buffers, conjunto de armazenamentos de colunas). Este nó do SQL Server sem estado é operado pelo Microsoft Azure Service Fabric, que inicializa o processo, controla a integridade do nó e executa o failover para outro local, se necessário.
- Uma camada de dados com estado com arquivos de banco de dados (.mdf/.ldf) que são armazenados no Armazenamento Premium do Azure. O Armazenamento do Azure garante que não haja perda de dados de nenhum registro colocados em qualquer arquivo de banco de dados. O Armazenamento do Azure tem disponibilidade/redundância de dados interna, que garante que cada registro no arquivo de log ou página no arquivo de dados será preservado mesmo se o processo do SQL Server falhar.

Sempre que o mecanismo de banco de dados ou sistema operacional é atualizado, alguma parte da infraestrutura subjacente falhar ou se for detectado algum problema crítico no processo do SQL Server, o Azure Service Fabric moverá o processo do SQL Server sem estado para outro nó de computação sem estado. Há um conjunto de nós sobressalentes que fica esperando a execução do novo serviço de computação se ocorrer um failover do nó primário a fim de minimizar o tempo de failover. Os dados na camada de Armazenamento do Azure não são afetados, e arquivos de dados/log são anexados ao processo do SQL Server recém-inicializado. Esse processo garante a disponibilidade de 99,99%, mas pode ter alguns impactos de desempenho na carga de trabalho pesada que está sendo executada devido ao tempo de transição e ao fato de o novo nó do SQL Server ser iniciado com cache frio.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher essa camada de serviço?

A camada de Uso Geral é uma camada de serviço padrão no Banco de Dados SQL que foi projetada para a maioria das cargas de trabalho genéricas. Caso você precise de um mecanismo de banco de dados totalmente gerenciado com SLA de 99,99% com latência de armazenamento entre 5 ms e 10 ms e que corresponda à IaaS do Azure SQL na maioria dos casos, a camada de Uso Geral é a opção adequada para você.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre camadas [Comercialmente Críticas](sql-database-service-tier-business-critical.md) e de [Hiperescala](sql-database-service-tier-hyperscale.md).
- Saiba mais sobre o [Service Fabric](../service-fabric/service-fabric-overview.md).
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte [Continuidade de Negócios](sql-database-business-continuity.md).
