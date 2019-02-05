---
title: Início Rápido – bancos de dados individuais no Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba como se familiarizar rapidamente com os bancos de dados individuais no Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 72ffdb357b5469b71da7655104add1135bf114fb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464799"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Introdução aos bancos de dados individuais no Banco de Dados SQL do Azure

[Um banco de dados individual](sql-database-single-index.yml) no Banco de Dados SQL do Azure é um DbaaS (banco de dados como serviço) de PaaS totalmente gerenciado que é o mecanismo de armazenamento ideal para aplicativos modernos criados na nuvem. Nesta seção, você aprenderá como configurar e criar rapidamente o Banco de Dados SQL.

## <a name="quickstart-overview"></a>Visão geral do início rápido

Nesta seção, você terá uma visão geral dos artigos disponíveis que podem ajudá-lo a se familiarizar rapidamente com os bancos de dados individuais. A maneira mais fácil de criar seu primeiro Banco de Dados SQL é usar o [portal do Azure](sql-database-get-started-portal.md), no qual você pode configurar os parâmetros necessários.
Após a criação, você precisaria [proteger seu banco de dados configurando regras de firewall](sql-database-get-started-portal-firewall.md). 

Se tiver um banco de dados existente no SQL Server que você deseja migrar para o Azure, você deverá instalar o [AMD (Assistente de Migração de Dados),](https://www.microsoft.com/download/details.aspx?id=53595) que analisará seus bancos de dados no SQL Server e encontrará qualquer problema que possa bloquear a migração para o banco de dados individual. Se não encontrar nenhum problema, você poderá exportar seu banco de dados como um arquivo `.bacpac` e [importá-lo usando o portal do Azure ou o SqlPackage](sql-database-import.md).

Esses guias de início rápido permitem que você configure, crie e importe rapidamente seus bancos de dados para a nuvem do Azure.

## <a name="automating-management-operations"></a>Automação das operações de gerenciamento

O portal do Azure permite que você crie e modifique facilmente seu banco de dados individual. Como alternativa, é possível usar o [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) ou a [CLI do Azure](scripts/sql-database-create-and-configure-database-cli.md) para criar bancos de dados.
Você também pode atualizar seu banco de dados individuais e dimensionar os recursos usando o [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) ou a [CLI do Azure](scripts/sql-database-monitor-and-scale-database-cli.md).

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migrando para um banco de dados individual com tempo de inatividade mínimo

Os artigos nesses guias de início rápido permitem que você crie ou importe rapidamente seus bancos de dados para o Azure usando o `.bacpac`. No entanto, os arquivos `.bacpac` e `.dacpack` são projetados para mover o banco de dados rapidamente entre diferentes versões do SQL Server e do Banco de Dados SQL do Azure (banco de dados individual, pool elástico ou Instância Gerenciada) ou para implementar a integração contínua em seu pipeline de DevOps. No entanto, esse método não foi projetado para fazer a migração de seus bancos de dados de produção com tempo de inatividade mínimo, pois você precisaria esperar para exportar o banco de dados de origem como um arquivo `.bacpac` e importá-lo para Banco de Dados SQL do Azure, o que poderia causar algum tempo de inatividade de seu aplicativo, especialmente se o banco de dados for maior. Se estiver movendo seu banco de dados de produção, você provavelmente precisará de uma forma melhor de migração, que garanta um tempo de inatividade mínimo. O [Serviço de Migração de Dados](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) é um serviço que pode migrar seu banco de dados com tempo de inatividade mínimo. Dessa forma, você pode alternar rapidamente seu aplicativo do banco de dados de origem para o de destino com tempo de inatividade mínimo.

## <a name="next-steps"></a>Próximas etapas

* Localizar uma [lista de alto nível dos recursos compatíveis no Banco de Dados SQL do Azure](sql-database-features.md). 
* Saiba como [tornar seu banco de dados mais seguro](sql-database-security-tutorial.md). 
* Encontre tutoriais mais avançados [na seção de instruções](sql-database-howto-single-database.md). 
* Encontre mais exemplos de scripts escritos no [PowerShell](sql-database-powershell-samples.md) e na [CLI do Azure](sql-database-cli-samples.md). 
* Saiba mais sobre a [API de Gerenciamento](sql-database-single-databases-manage.md) que você pode usar para configurar seus bancos de dados. 
