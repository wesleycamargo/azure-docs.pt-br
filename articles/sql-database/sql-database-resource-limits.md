---
title: Limites de recursos do Banco de Dados SQL do Azure | Microsoft Docs
description: "Esta página descreve alguns limites de recurso comuns para o Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: na
author: janeng
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 05/31/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: a7887839b7a3672e824ed6662f6ba5dc4e318fab
ms.contentlocale: pt-br
ms.lasthandoff: 05/16/2017


---
# <a name="azure-sql-database-resource-limits"></a>Limites de recursos do Banco de Dados SQL do Azure
## <a name="overview"></a>Visão geral
O Banco de Dados SQL do Azure gerencia os recursos disponíveis para um banco de dados usando dois mecanismos diferentes: **Governança de Recursos** e **Imposição de Limites**. Este tópico explica essas duas áreas principais do gerenciamento de recursos.

## <a name="resource-governance"></a>Governança de recursos
Uma das metas de design das camadas de serviço Básico, Standard, Premium e Premium RS é que o Banco de Dados SQL do Azure se comporte como se o banco de dados estivesse sendo executado em seu próprio computador, isolado de outros bancos de dados. A governança de recursos emula esse comportamento. Se a utilização de recursos agregada atingir o máximo de recursos da CPU, Memória, E/S de Log e de E/S de Dados disponíveis atribuídos ao banco de dados, a governança de recursos colocará na fila as consultas em execução e atribuirá recursos às consultas na fila conforme eles são liberados.

Assim como ocorre em um computador dedicado, a utilização de todos os recursos disponíveis resultará em uma execução mais longa das consultas em execução, o que poderá resultar em tempos limite de comando no cliente. Aplicativos com uma lógica de repetição agressiva e aplicativos que executam consultas no banco de dados com uma alta frequência podem encontrar mensagens de erro ao tentar executar novas consultas quando o limite de solicitações simultâneas for atingido.

### <a name="recommendations"></a>Recomendações:
Monitore a utilização de recursos e os tempos de resposta médios das consultas quando estiver próximo da utilização máxima de um banco de dados. Quando encontrar latências maiores de consulta, você geralmente terá três opções:

1. Reduzir o número de solicitações de entrada para o banco de dados para prevenir o tempo limite e o acúmulo de solicitações.
2. Atribuir um nível mais alto de desempenho ao banco de dados.
3. Otimizar consultas para reduzir a utilização de recursos de cada consulta. Para obter mais informações, veja a seção Ajuste/dicas de consulta no artigo Diretrizes de desempenho do Banco de Dados SQL do Azure.

## <a name="enforcement-of-limits"></a>Imposição de limites
Os recursos, além da CPU, Memória, E/S de log e E/S de dados, são impostos pela negação de novas solicitações quando os limites são atingidos. Quando um banco de dados atinge o limite de tamanho máximo configurado, inserções e atualizações que aumentam a falha do tamanho de dados, enquanto selecionar e excluir continua a funcionar. Os clientes recebem uma [mensagem de erro](sql-database-develop-error-messages.md) dependendo do limite que foi atingido.

Por exemplo, o número de conexões a um Banco de Dados SQL e o número de solicitações simultâneas que podem ser processadas, é restrito. O Banco de Dados SQL permite que o número de conexões ao banco de dados seja maior que o número de solicitações simultâneas para dar suporte ao pooling de conexões. Embora o número de conexões disponíveis possa facilmente ser controlado pelo aplicativo, o número de solicitações paralelas costuma ser mais difícil de ser estimado e controlado. Especialmente durante os picos de carga quando o aplicativo envia um número excessivo de solicitações ou quando o banco de dados atinge seus limites de recursos e começa a acumular threads de trabalho devido a consultas com execução mais longa, é possível encontrar erros.

## <a name="service-tiers-and-performance-levels"></a>Camadas de serviço e níveis de desempenho
Há camadas de serviço e níveis de desempenho para o banco de dados individual e pools elásticos.

### <a name="single-databases"></a>Bancos de dados únicos
Para um banco de dados individual, os limites são definidos pelo nível de serviço e de desempenho do banco de dados. A tabela a seguir descreve as características dos bancos de dados Básico, Standard, Premium e Premium RS em vários níveis de desempenho.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!IMPORTANT]
> Os clientes que usam os níveis de desempenho P11 e P15 podem usar até 4 TB de armazenamento incluído sem custo adicional. Essa opção de 4 TB está atualmente disponível nas seguintes regiões: Leste dos EUA 2, Oeste dos EUA, US Gov – Virginia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá.
>

### <a name="elastic-pools"></a>Pools elásticos
[Pools elásticos](sql-database-elastic-pool.md) compartilham recursos entre bancos de dados no pool. A tabela a seguir descreve as características dos pools elásticos Básico, Standard, Premium e Premium RS.

[!INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Para obter uma definição expandida de cada recurso listado nas tabelas anteriores, consulte as descrições em [Limites e recursos das camadas de serviço](sql-database-performance-guidance.md#service-tier-capabilities-and-limits). Para obter uma visão geral das camadas de serviço, consulte [Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure](sql-database-service-tiers.md).

## <a name="other-sql-database-limits"></a>Outros limites de Banco de Dados SQL
| Área | Limite | Descrição |
| --- | --- | --- |
| Bancos de dados usando a exportação Automatizada por assinatura |10 |A exportação automatizada permite que você crie uma agenda personalizada para realização do backup de seus bancos de dados SQL. A visualização desse recurso será encerrada em 1º de março de 2017.  |
| Bancos de dados por servidor |Até 5.000 |Até 5.000 bancos de dados são permitidos por servidor. |
| DTUs por servidor |45000 |São permitidas 45.000 DTUs por servidor para o provisionamento de bancos de dados autônomos e pools elásticos. O número total de bancos de dados independentes e pools permitidos por servidor é limitado somente pelo número de DTUs do servidor.  

> [!IMPORTANT]
> A Exportação Automatizada do Banco de Dados SQL do Azure está agora em visualização e será desativada em 1º de março de 2017. A partir de 1º de dezembro de 2016, não será mais possível configurar a exportação automatizada em um banco de dados SQL. Todos os trabalhos de exportação automatizada existentes continuarão funcionando até 1º de março de 2017. Após 1º de dezembro de 2016, você poderá usar a [retenção de backup de longo prazo](sql-database-long-term-retention.md) ou a [Automação do Azure](../automation/automation-intro.md) para arquivar bancos de dados SQL periodicamente, usando o PowerShell periodicamente de acordo com um agendamento de sua escolha. Para obter um script de exemplo, baixe o [script de exemplo no GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export).
>


## <a name="resources"></a>Recursos
[Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)

[Faixas de Serviço de Banco de Dados SQL do Azure e Níveis de Desempenho](sql-database-service-tiers.md)

[Mensagens de erro para programas cliente do Banco de Dados SQL](sql-database-develop-error-messages.md)

