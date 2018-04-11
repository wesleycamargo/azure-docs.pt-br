---
title: Atualizar para a última geração do SQL Data Warehouse do Azure | Microsoft Docs
description: Etapas para atualizar o SQL Data Warehouse do Azure para a última geração de arquitetura de armazenamento e hardware do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 42b716274e655bf91f72c1b3ab207b8a5f1ccee0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="upgrade-to-latest-generation-of-azure-sql-data-warehouse-in-the-azure-portal"></a>Atualizar para a última geração do SQL Data Warehouse do Azure no Portal do Azure

Use o Portal do Azure para atualizar o SQL Data Warehouse do Azure para usar a última geração de arquitetura de armazenamento e hardware do Azure. Ao atualizar, é possível aproveitar o desempenho mais rápido, maior escalabilidade e armazenamento ilimitado para índices columnstore.  

## <a name="applies-to"></a>Aplica-se a
Essa atualização aplica-se aos data warehouses no nível de desempenho do Otimizado para Elasticidade.  As instruções atualizam um data warehouse do nível de desempenho Otimizado para Elasticidade para o nível de desempenho Otimizado para Computação. 

## <a name="sign-in-to-the-azure-portal"></a>Entrar no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

1. Se o data warehouse Otimizado para Elasticidade a ser atualizado estiver pausado, [retomará o data warehouse](pause-and-resume-compute-portal.md).
2. Esteja preparado para alguns minutos de tempo de inatividade. 
3. O processo de atualização encerra todas as sessões e remove todas as conexões. Antes de atualizar, verifique se as consultas foram concluídas. Se você iniciar uma atualização com transações em andamento, o tempo de reversão poderá ser extenso. 

## <a name="start-the-upgrade"></a>Inicie a atualização

1. Abra o data warehouse no Portal do Azure e clique em **Atualizar para Otimizado para Computação**.
2. Observe as opções de nível de desempenho Otimizado para Computação. A seleção padrão é comparável ao nível atual antes da atualização.
3. Escolha um nível de desempenho. Atualmente, o preço do nível de desempenho Otimizado para Computação estará pela metade durante o período de versão prévia.
4. Clique em **Atualizar**.
5. Verifique o status no Portal do Azure.
6. Aguarde até que o data warehouse mude para Online.

## <a name="rebuild-columnstore-indexes"></a>Reconstruir índices columnstore

Quando o data warehouse estiver online, você poderá carregar dados e executar consultas. No entanto, o desempenho pode ser lento no início porque um processo em segundo plano está migrando os dados para o novo hardware. 

Para forçar a migração dos dados o mais rápido possível, recomendamos a recriação dos índices columnstore. Para fazer isso, consulte a orientação para [Recompilar índices columnstore para melhorar a qualidade do segmento](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). 

## <a name="next-steps"></a>Próximas etapas
Seu data warehouse está online. Para usar os novos recursos de desempenho, consulte [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).
 