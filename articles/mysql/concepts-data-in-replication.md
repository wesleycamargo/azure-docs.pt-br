---
title: Replicar dados no Banco de Dados do Azure para MySQL.
description: Este artigo descreve a replicação nos dados para o Banco de Dados do Azure para MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/20/2018
ms.openlocfilehash: 72f8211ecc0534b15402911de8fc0ec3d541a835
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294897"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replicar dados no Banco de Dados do Azure para MySQL

A Replicação nos dados permite sincronizar dados de um Servidor MySQL em execução local, em máquinas virtuais ou em serviços de banco de dados hospedados por outros provedores de nuvem com o serviço do Banco de Dados do Azure para MySQL. A Replicação nos dados se baseia na replicação nativa baseada na posição do arquivo de log binário (binlog) para o MySQL. Para saber mais sobre a replicação do binlog, confira a [visão geral da replicação do binlog do MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Quando usar a replicação nos dados
Os cenários principais nos quais considerar o uso da replicação nos dados são:

- **Sincronização de dados híbrida:** com a replicação nos dados, você pode manter os dados sincronizados entre os servidores locais e o Banco de Dados do Azure para MySQL. Essa sincronização é útil para criar aplicativos híbridos. Esse método é atraente quando você tem um servidor de banco de dados local existente, mas deseja mover os dados para uma região mais próxima aos usuários finais.
- **Sincronização de várias nuvens:** para soluções de nuvem complexas, use a replicação nos dados para sincronizar dados entre o Banco de Dados do Azure para MySQL e provedores de nuvem diferentes, incluindo máquinas virtuais e serviços de banco de dados nessas nuvens.

## <a name="limitations-and-considerations"></a>Limitações e considerações

### <a name="data-not-replicated"></a>Dados não replicados
O [*mysql system database*](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) no servidor primário não é replicado. As alterações nas contas e permissões no servidor primário não são replicadas. Se você criar uma conta no servidor primário e essa conta precisar acessar o servidor de réplica, crie manualmente a mesma conta no lado do servidor de réplica. Para entender quais tabelas estão contidas no banco de dados do sistema, confira o [Manual do MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Requisitos
- A versão do servidor primário deve ser pelo menos o MySQL versão 5.6. 
- As versões do servidor primário e de réplica devem ser a mesma. Por exemplo, ambos devem ser MySQL versão 5.6 ou ambos devem ser MySQL versão 5.7.
- Cada tabela deve ter uma chave primária.
- O servidor primário deve usar o mecanismo de InnoDB do MySQL.
- O usuário deve ter permissões para configurar o log binário e criar novos usuários no servidor primário.

### <a name="other"></a>Outros
- O GTID (identificadores de transação globais) não são compatíveis.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [configurar a replicação nos dados](howto-data-in-replication.md)
