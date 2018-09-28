---
title: Versões com suporte no banco de dados do Azure para MariaDB
description: Descreve as versões com suporte no Banco de Dados do Azure para o MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: aa83b9955839aaa03aa2ebf46c9e464dc75c8d3a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977495"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Banco de Dados do Azure Suportado para as versões do servidor MariaDB
O Banco de Dados do Azure para o MariaDB foi desenvolvido a partir do [servidor MariaDB de código aberto](https://downloads.mariadb.org/), usando o mecanismo InnoDB. O Banco de Dados do Azure para MariaDB atualmente suporta a seguinte versão:

## <a name="mariadb-version-10217"></a>Versão do MariaDB 10.2.17
Consulte a [documentação do MariaDB](https://downloads.mariadb.org/mariadb/10.2.17/) para saber mais sobre melhorias e correções no MariaDB 10.2.17.

> [!NOTE]
> No serviço, um gateway é usado para redirecionar as conexões para as instâncias de servidor. Depois que a conexão é estabelecida, o cliente MySQL exibe a versão do MariaDB definida no gateway, não a versão real em execução na sua instância do servidor MariaDB. Para determinar a versão da sua instância do servidor MariaDB, use o comando `SELECT VERSION();` no prompt do MySQL.

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades
O serviço gerencia automaticamente a aplicação de patch para atualizações secundárias de versão.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre cotas e limitações específicas de recursos com base em sua **camada de serviço**, confira [Camadas de serviço](./concepts-pricing-tiers.md)