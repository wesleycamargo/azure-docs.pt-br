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
ms.date: 05/23/2018
ms.openlocfilehash: c9a533ed9b9eb9ac53a02439b98a78954c7aaa11
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265241"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versões com suporte do servidor de Banco de Dados do Azure para MySQL
O Banco de Dados do Azure para MySQL foi desenvolvido a partir do [MySQL Community Edition](https://www.mysql.com/products/community/), usando o mecanismo InnoDB.  No momento, o Banco de Dados do Azure para MySQL dá suporte às seguintes versões:

## <a name="mysql-version-5639"></a>MySQL Versão 5.6.39
Veja a [documentação](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) do MySQL para saber mais sobre as melhorias e correções no MySQL 5.6.39.

## <a name="mysql-version-5721"></a>MySQL Versão 5.7.21
Veja a [documentação](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) do MySQL para saber sobre as melhorias e correções no MySQL 5.7.21.

> [!NOTE]
> No serviço, um gateway é usado para redirecionar as conexões para as instâncias de servidor. Depois que a conexão for estabelecida, o cliente MySQL exibirá a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, use o `SELECT VERSION();` comando no prompt do MySQL. 

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades
O serviço gerencia automaticamente a aplicação de patch para atualizações secundárias de versão. Não há suporte para atualizações de versão principal (por exemplo, atualização do MySQL 5.6 para o MySQL 5.7).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre cotas e limitações específicas de recursos com base em sua **camada de serviço**, confira [Camadas de serviço](./concepts-pricing-tiers.md)
