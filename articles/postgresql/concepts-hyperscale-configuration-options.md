---
title: Banco de dados do Azure para PostgreSQL – em hiperescala (Citus) (visualização) opções de desempenho
description: Opções para um grupo de servidores em hiperescala (Citus), incluindo regiões, armazenamento e computação de nós.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e8c1c2e51ca14ae9b17f0d7efb20552cdd55139b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077284"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-preview-performance-options"></a>Banco de dados do Azure para PostgreSQL – em hiperescala (Citus) (visualização) opções de desempenho

## <a name="compute-and-storage"></a>Computação e armazenamento
 
Você pode selecionar as configurações de computação e armazenamento independentemente para nós de trabalho e o nó coordenador em um grupo de servidor em hiperescala (Citus).  Os recursos de computação são fornecidos como vCores, que representam a CPU lógica do hardware subjacente. O tamanho do armazenamento para o provisionamento se refere à capacidade disponível para os nós do coordenador e de trabalho em seu grupo de servidores em hiperescala (Citus). O armazenamento inclui arquivos de banco de dados, arquivos temporários, logs de transações e os logs do servidor Postgres. A quantidade total de armazenamento que você provisiona também define a capacidade de e/s disponível para cada nó coordenador e de trabalho.
 
|                       | Nó de trabalho           | Nó coordenador      |
|-----------------------|-----------------------|-----------------------|
| Computação, vCores       | 4, 8, 16, 32          | 4, 8, 16, 32          |
| Memória por vCore, GiB | 8                     | 4                     |
| Tamanho de armazenamento, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tipo de armazenamento          | Uso geral (SSD) | Uso geral (SSD) |
| IOPS                  | Até 3 IOPS/GiB      | Até 3 IOPS/GiB      |


## <a name="regions"></a>Regiões
Grupos de servidores em hiperescala (Citus) estão disponíveis nas seguintes regiões do Azure:
* Leste dos EUA 2
* Sudeste da Ásia
* Europa Ocidental
* Oeste dos EUA 2

## <a name="pricing"></a>Preços
Para as informações mais recentes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) do serviço.
Para ver o custo para a configuração desejada, o [portal do Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal na **configurar** guia com base nas opções que você selecionar. Se você não tiver uma assinatura do Azure, poderá usar a calculadora de preços do Azure para obter um preço estimado. Sobre o [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) site, selecione **adicionar itens**, expanda o **bancos de dados** categoria e escolha **banco de dados do Azure para PostgreSQL – Hiperescala (Citus)** para personalizar as opções.
 
## <a name="next-steps"></a>Próximas etapas
Saiba como [criar um grupo de servidores em hiperescala (Citus) no portal do](quickstart-create-hyperscale-portal.md).
