---
title: Visão geral das principais diferenças dos Serviços do Machine Learning (com R) no Banco de Dados SQL do Azure (versão prévia)
description: Este tópico descreve as principais diferenças entre os serviços do Machine Learning do Banco de Dados SQL do Azure (com R) e os serviços do Machine Learning do SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: adc303e9b375aaa6f37e9e79ea5434675c75523d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824762"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-and-sql-server"></a>Principais diferenças entre os Serviços do Machine Learning no Banco de Dados SQL do Azure e no SQL Server

A funcionalidade dos Serviços de Machine Learning (com R) no Banco de Dados SQL é semelhante aos [Serviços de Machine Learning do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Abaixo há algumas importantes diferenças entre eles.

## <a name="language-support"></a>Suporte ao idioma

O SQL Server tem suporte para R e Python por meio da [estrutura de extensibilidade](https://docs.microsoft.com/en-us/sql/advanced-analytics/concepts/extensibility-framework). O Banco de Dados SQL não dá suporte às duas linguagens. As principais diferenças são:

- O R é a única linguagem com suporte no Banco de Dados SQL. No momento, não há suporte para Python.
- A versão do R é 3.4.4.
- Não é necessário configurar `external scripts enabled` por meio de `sp_configure`. Quando você estiver [inscrito](sql-database-machine-learning-services-overview.md#signup), o machine learning estará habilitado para o banco de dados SQL.

## <a name="package-management"></a>Gerenciamento de pacote

O gerenciamento e a instalação de pacotes R funcionam de maneira diferente entre o Banco de Dados SQL e o SQL Server. Essas diferenças são:

- Os pacotes R são instalados por meio de [sqlmlutils](https://github.com/Microsoft/sqlmlutils) ou [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Os pacotes não podem realizar chamadas de rede de saída. Essa limitação é semelhante às [regras de firewall padrão para os serviços do Machine Learning](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) no SQL Server, mas não pode ser alterada no Banco de Dados SQL.
- Não há suporte para pacotes que dependem de tempos de execução externos (como Java) ou que precisam acessar APIs de OS para instalação ou uso.

## <a name="resource-governance"></a>Governança de recursos

Não é possível limitar os recursos de R por meio do [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e de pools de recursos externos. Os recursos do R são um percentual dos recursos do Banco de Dados SQL e dependem da camada de serviço escolhida. Para saber mais, confira [Modelos de compra do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

## <a name="security-isolation"></a>Isolamento de segurança

No Banco de Dados SQL do Azure, a SQLPAL (Camada de Abstração da Plataforma SQL) fornece isolamento para processos externos. Esse isolamento fornece uma camada extra de segurança para a execução de scripts R.

## <a name="next-steps"></a>Próximas etapas

- Confira a documentação dos [Serviços do Machine Learning do SQL Server](https://docs.microsoft.com/sql/advanced-analytics) para obter informações gerais
- Para saber como usar os Serviços do Microsoft Machine Learning (com R) no Banco de Dados SQL do Azure, confira [Guia de início rápido](sql-database-connect-query-r.md).
- Saiba mais com os [tutoriais da linguagem R do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)