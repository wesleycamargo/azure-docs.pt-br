---
title: Versões com suporte no Banco de Dados do Azure para PostgreSQL
description: Descreve as versões com suporte no Banco de Dados do Azure para PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/07/2018
ms.openlocfilehash: f337679279f4a3eb6d01808f15a59fee4c8f3cd1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630559"
---
# <a name="supported-postgresql-database-versions"></a>Versões de banco de dados PostgreSQL com suporte
A Microsoft pretende dar suporte a versões n-2 do mecanismo PostgreSQL no serviço Banco de Dados do Azure para PostgreSQL, o que significa a versão principal liberada no momento (n) e as duas versões principais anteriores (-2).

No momento, o Banco de Dados do Azure para PostgreSQL oferece suporte às seguintes versões:

## <a name="postgresql-version-104"></a>PostgreSQL Versão 10.4
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-4.html) para saber mais sobre aperfeiçoamentos e correções nesta versão secundária.

## <a name="postgresql-version-969"></a>PostgreSQL Versão 9.6.9
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-9.html) para saber mais sobre aperfeiçoamentos e correções nesta versão secundária.

## <a name="postgresql-version-9513"></a>PostgreSQL Versão 9.5.13
Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-13.html) para saber mais sobre aperfeiçoamentos e correções nesta versão secundária.

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades
O Banco de Dados do Azure para PostgreSQL gerencia automaticamente a aplicação de patches para atualizações secundárias de versão. Atualmente, upgrade da versão principal não tem suporte. Por exemplo, não há suporte para atualização do PostgreSQL 9.5 para PostgreSQL 9.6. Se você quiser atualizar para a próxima versão principal, faça um [despejo e restaure](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o suporte a extensões do PostgreSQL diferentes, veja [Extensões do PostgreSQL](concepts-extensions.md).
