---
title: Status do cenário de migração de banco de dados | Microsoft Docs
description: Saiba mais sobre o status dos cenários de migração compatíveis com o Serviço de Migração de Banco de Dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/15/2019
ms.openlocfilehash: edc6e651c3ec352115e360e50f98a3e36cd287c0
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904073"
---
# <a name="status-of-migration-scenarios-supported-by-the-azure-database-migration-service"></a>Status dos cenários de migração compatíveis com o Serviço de Migração de Banco de Dados do Azure
O Serviço de Migração de Banco de Dados do Azure foi projetado para oferecer suporte a diversos cenários de migração (pares de origem/destino), tanto para migrações offline (uma vez) como para migrações online (sincronização contínua). A cobertura do cenário fornecido pelo Serviço de Migração de Banco de Dados do Azure está sendo estendida ao longo do tempo. Novos cenários são adicionados regularmente. Este artigo identifica os cenários de migração atualmente compatíveis com o Serviço de Migração de Banco de Dados do Azure e o status (versão prévia privada [ou limitada], visualização pública ou geralmente disponível).

## <a name="offline-versus-online-migrations"></a>Migrações offline versus online
Ao migrar bancos de dados para o Azure usando o Serviço de Migração de Banco de Dados do Azure, é possível fazer uma migração offline ou online. Nas migrações *offline*, o tempo de inatividade do aplicativo começa quando a migração inicia. Para migrações *online*, o tempo de inatividade está limitado ao tempo necessário para fazer a transferência para o novo ambiente quando a migração for concluída. É recomendável que você teste uma migração offline para determinar se o tempo de inatividade é aceitável; caso contrário, faça uma migração online.

## <a name="migration-scenario-status"></a>Status do cenário de migração
O status de cada cenário de migração compatível com o Serviço de Migração de Banco de Dados do Azure varia conforme o tempo. Em geral, os cenários são lançados pela primeira vez em **Versão prévia privada** e, para aproveitar o recurso, precisam que um cliente envie uma indicação por meio do [site de versão prévia de DMS](https://aka.ms/dms-preview). Quando a Versão prévia privada estiver completa, o status de cenário muda para **Visualização pública**. Todos os usuários do Serviço de Migração de Banco de Dados do Azure podem utilizar os cenários de migração disponíveis em Visualização pública. No entanto, o cenário de migração pode não estar disponível em todas as regiões e o recurso poderá sofrer mais alterações antes do lançamento final. Quando um cenário de migração se torna **Geralmente disponível** (isto é, o status final do lançamento), o recurso está completo e fica acessível a todos os usuários do Serviço de Migração de Banco de Dados do Azure. 

## <a name="migration-scenario-support"></a>Suporte para o cenário de migração

As tabelas a seguir mostram quais cenários de migração recebem suporte ao usar o Serviço de Migração de Banco de Dados do Azure.

> [!NOTE]
> Se um cenário está listado como tendo suporte abaixo, mas não aparece na interface do usuário, entre em contato com a [Equipe de migração de dados](mailto:datamigrationteam@microsoft.com) para saber mais.

### <a name="offline-one-time-migration-support"></a>Suporte para a migração offline (única)
A tabela a seguir mostra o suporte para o Serviço de Migração de Banco de Dados do Azure em migrações offline.

| Destino  | Fonte | Suporte |
| ------------- | ------------- | :-------------: |
| **BD SQL do Azure**  | SQL Server | ✔ |
|   | SQL para RDS  |  ✔ |
|   | Oracle  |   |
| **MI do BD SQL do Azure**  | SQL Server  | ✔ |
|   | SQL para RDS  | ✔ |
|   | Oracle  | ✔  |
| **VM do SQL do Azure**  | SQL Server | ✔ |
|   | Oracle  |   |
| **Azure Cosmos DB**  | MongoDB | ✔ |
| **BD do Azure para MySQL**  | MySQL |  |
|   | MySQL para RDS  |  |
| **BD do Azure para PostgreSQL**  | PostgreSQL |  |
|  | PostgreSQL para RDS  |  |

### <a name="online-continuous-sync-migration-support"></a>Suporte à migração online (sincronização contínua)
A tabela a seguir mostra o suporte para o Serviço de Migração de Banco de Dados do Azure em migrações online.

| Destino  | Fonte | Suporte |
| ------------- | ------------- | :-------------: |
| **BD SQL do Azure**  | SQL Server | ✔ |
|   | SQL para RDS  |   |
|   | Oracle  |  ✔ |
| **MI do BD SQL do Azure**  | SQL Server  | ✔ |
|   | SQL para RDS  |  |
|   | Oracle  | ✔  |
| **VM do SQL do Azure**  | SQL Server  |   |
|   | Oracle  | ✔  |
| **Azure Cosmos DB**  | MongoDB  | ✔ |
| **BD do Azure para MySQL**  | MySQL | ✔ |
|   | MySQL para RDS  | ✔ |
| **BD do Azure para PostgreSQL**  | PostgreSQL | ✔ |
|  | PostgreSQL para RDS  | ✔ |

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral do Serviço de Migração de Banco de Dados do Azure e da disponibilidade regional, consulte o artigo [ O que é o Serviço de Migração de Banco de Dados do Azure ](dms-overview.md). 
