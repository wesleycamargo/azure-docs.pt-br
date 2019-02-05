---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 01/28/2019
ms.author: genemi
ms.openlocfilehash: edeaa932abe4d1882f957d0ed26aaddd97dabe3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198054"
---
<!-- sql-database-connect-query-prerequisites-create-db-includes.md -->

- Um Banco de Dados SQL do Azure é colocado no [Servidor lógico](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) ou na [Instância Gerenciada](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index). Use uma dessas técnicas para criar um banco de dados:

| Servidor lógico | Instância Gerenciada |
| --- | --- |
| [Portal](../articles/sql-database/sql-database-get-started-portal.md) | [Portal](../articles/sql-database/sql-database-managed-instance-get-started.md) |
| [CLI](../articles/sql-database/sql-database-get-started-cli.md) | |
| [PowerShell](../articles/sql-database/sql-database-get-started-powershell.md) | |

- **Somente servidor lógico** – uma regra de firewall de nível de servidor configurada que permite que você se conecte ao seu servidor lógico. Para saber mais, confira [Create server-level firewall rule](../articles/sql-database/sql-database-get-started-portal-firewall.md) (Criar regra de firewall no nível do servidor).
- **Somente Instância Gerenciada** – conexão configurada do computador que está acessando a Instância Gerenciada. É possível usar as seguintes opções:
  - [Máquina Virtual do Azure](../articles/sql-database/sql-database-managed-instance-configure-vm.md) na mesma VNet do Azure que a Instância Gerenciada que pode acessar a instância.
  - [Conexão ponto a site](../articles/sql-database/sql-database-managed-instance-configure-p2s.md) no seu computador que permitirá que você una seu computador à rede virtual na qual a Instância Gerenciada está e use a Instância Gerenciada como qualquer outro SQL Server em sua rede.
