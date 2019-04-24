---
title: Versões com suporte no Banco de Dados do Azure para MySQL
description: Descreve as versões com suporte no Banco de Dados do Azure para MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/13/2019
ms.openlocfilehash: e7e81632b2be135fb74d375ab8a11f1b4b3ef39d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525926"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versões com suporte do servidor de Banco de Dados do Azure para MySQL

O Banco de Dados do Azure para MySQL foi desenvolvido a partir do [MySQL Community Edition](https://www.mysql.com/products/community/), usando o mecanismo InnoDB.

O MySQL usa o esquema de nomenclatura X.Y.Z. X é a versão principal, Y é a versão secundária e Z é a versão de correção de bug. Para obter mais informações sobre o esquema, confira a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

No momento, o Banco de Dados do Azure para MySQL dá suporte às seguintes versões:

## <a name="mysql-version-56"></a>MySQL Versão 5.6

Versão de correção de bug: 5.6.42

Confira a MySQL [notas de versão](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-42.html) para saber mais sobre aperfeiçoamentos e correções no MySQL 5.6.42.

## <a name="mysql-version-57"></a>MySQL Versão 5.7

Versão de correção de bug: 5.7.24

Confira a MySQL [notas de versão](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-24.html) para saber mais sobre aperfeiçoamentos e correções no MySQL 5.7.24.

> [!NOTE]
> No serviço, um gateway é usado para redirecionar as conexões para as instâncias de servidor. Depois que a conexão for estabelecida, o cliente MySQL exibirá a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, use o `SELECT VERSION();` comando no prompt do MySQL.

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades
O serviço gerencia automaticamente a aplicação de patch para atualizações de versão de correção de bug. Por exemplo, 5.7.20 a 5.7.21.  

Atualmente, não há suporte para atualizações de versão principal e secundária. Por exemplo, não há suporte para a atualização do MySQL 5.6 para o MySQL 5.7. Caso deseje atualizar da versão 5.6 para a 5.7, faça um [despejo e restaure-a](./concepts-migrate-dump-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre cotas e limitações específicas de recursos com base em sua **camada de serviço**, confira [Camadas de serviço](./concepts-pricing-tiers.md)
