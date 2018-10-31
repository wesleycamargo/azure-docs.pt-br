---
title: Versões com suporte no Banco de Dados do Azure para MySQL
description: Descreve as versões com suporte no Banco de Dados do Azure para MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: f2a9348e267ad6a5929fda64cc08dcd837243e71
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093333"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versões com suporte do servidor de Banco de Dados do Azure para MySQL
O Banco de Dados do Azure para MySQL foi desenvolvido a partir do [MySQL Community Edition](https://www.mysql.com/products/community/), usando o mecanismo InnoDB. No momento, o Banco de Dados do Azure para MySQL dá suporte às seguintes versões:

## <a name="mysql-version-5639"></a>MySQL Versão 5.6.39
Veja a [documentação](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) do MySQL para saber mais sobre as melhorias e correções no MySQL 5.6.39.

## <a name="mysql-version-5721"></a>MySQL Versão 5.7.21
Veja a [documentação](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) do MySQL para saber sobre as melhorias e correções no MySQL 5.7.21.

> [!NOTE]
> No serviço, um gateway é usado para redirecionar as conexões para as instâncias de servidor. Depois que a conexão for estabelecida, o cliente MySQL exibirá a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, use o `SELECT VERSION();` comando no prompt do MySQL.

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades
O serviço gerencia automaticamente a aplicação de patch para atualizações de versão de correção de bug. No momento, não há suporte para a atualização secundária de versão. Por exemplo, não há suporte para atualização do MySQL 5.6 para MySQL 5.7. Se você quiser atualizar para a próxima versão secundária, [faça um despejo e restaure](./concepts-migrate-dump-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre cotas e limitações específicas de recursos com base em sua **camada de serviço**, confira [Camadas de serviço](./concepts-pricing-tiers.md)
