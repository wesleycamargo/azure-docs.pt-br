---
title: Principais diferenças dos serviços do Azure SQL banco de dados de Machine Learning (versão prévia)
description: Este tópico descreve as principais diferenças entre os serviços do Machine Learning do Banco de Dados SQL do Azure (com R) e os serviços do Machine Learning do SQL Server.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 57ea52c179376e8378680f436d396ffaf9357f68
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771843"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Principais diferenças entre os serviços do Machine Learning no banco de dados do SQL do Azure (visualização) e o SQL Server

A funcionalidade dos serviços de aprendizado de máquina do Azure SQL banco de dados (com R) no (visualização) é semelhante ao [serviços do SQL Server Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Abaixo estão algumas das principais diferenças.

> [!IMPORTANT]
> Aprendizado serviços de máquina de banco de dados SQL do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Suporte ao idioma

O SQL Server tem suporte para R e Python por meio da [estrutura de extensibilidade](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). O Banco de Dados SQL não dá suporte às duas linguagens. As principais diferenças são:

- O R é a única linguagem com suporte no Banco de Dados SQL. No momento, não há suporte para Python.
- A versão do R é 3.4.4.
- Não é necessário configurar `external scripts enabled` por meio de `sp_configure`. Quando você estiver [inscrito](sql-database-machine-learning-services-overview.md#signup), o machine learning estará habilitado para o banco de dados SQL.

## <a name="package-management"></a>Gerenciamento de pacote

O gerenciamento e a instalação de pacotes R funcionam de maneira diferente entre o Banco de Dados SQL e o SQL Server. Essas diferenças são:

- Os pacotes R são instalados por meio de [sqlmlutils](https://github.com/Microsoft/sqlmlutils) ou [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Os pacotes não podem realizar chamadas de rede de saída. Essa limitação é semelhante para o [regras de firewall padrão para serviços de Machine Learning](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) no SQL Server, mas não pode ser alterada no banco de dados SQL.
- Não há suporte para pacotes que dependem de tempos de execução externos (como Java) ou que precisam acessar APIs de OS para instalação ou uso.

## <a name="resource-governance"></a>Governança de recursos

Não é possível limitar os recursos de R por meio do [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e de pools de recursos externos.

Durante a visualização pública, os recursos de R são definidos para um máximo de 20% dos recursos de banco de dados SQL e dependem de qual camada de serviço escolhida. Para saber mais, confira [Modelos de compra do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Erro de memória insuficiente

Se não houver memória suficiente disponível para R, você receberá uma mensagem de erro. Mensagens de erro comuns são:

- Não é possível se comunicar com o tempo de execução de script 'R' para a id de solicitação: * * *. Verifique se os requisitos de tempo de execução 'R'
- Erro de script 'R' ocorreu durante a execução de 'sp_execute_external_script' com HRESULT 0x80004004. ... Ocorreu um erro de script externo: "... não foi possível alocar memória (0 Mb) na função de C 'R_AllocStringBuffer' "
- Ocorreu um erro de script externo: Erro: não é possível alocar um vetor de tamanho.

Uso depende do quanto de memória é usada em seus scripts de R e o número de consultas paralelas que está sendo executado. Se você receber os erros acima, você pode dimensionar seu banco de dados para uma camada de serviço superior para resolver o problema.

## <a name="next-steps"></a>Próximas etapas

- Confira a documentação dos [Serviços do Machine Learning do SQL Server](https://docs.microsoft.com/sql/advanced-analytics) para obter informações gerais
- Para saber como usar os Serviços do Microsoft Machine Learning (com R) no Banco de Dados SQL do Azure, confira [Guia de início rápido](sql-database-connect-query-r.md).
- Saiba mais com os [tutoriais da linguagem R do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)