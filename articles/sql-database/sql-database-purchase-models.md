---
title: Modelos de compra do Banco de Dados SQL do Azure| Microsoft Docs
description: Saiba mais sobre os modelos de compra que são bancos de dados disponíveis no serviço de Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 46a620900896d07273da22e53171330b85d3f1ec
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360180"
---
# <a name="azure-sql-database-purchasing-models"></a>Modelos de compra Banco de Dados SQL do Microsoft Azure

O Banco de dados SQL do Azure permite que você adquira com facilidade o mecanismo de banco de dados PaaS totalmente gerenciado que atenda às suas necessidades de desempenho e custo. Dependendo do modelo de implantação do Banco de Dados SQL do Azure, você pode selecionar o modelo de compra que atenda às suas necessidades:

- [Modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) (recomendado), que permite escolher a quantidade exata de capacidade de armazenamento e computação que você precisa para sua carga de trabalho.
- [Modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) em que você pode escolher pacotes agrupados de computação e armazenamento para cargas de trabalho comuns.

Diferentes modelos de compra estão disponíveis em modelos de implantação do Banco de Dados SQL do Azure:

- As opções de implantação [banco de dados individual](sql-database-single-databases-manage.md) e [pool elástico](sql-database-elastic-pool.md) no [Banco de Dados SQL do Azure](sql-database-technical-overview.md) oferecem tanto o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) quanto o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- A opção de implantação [instância gerenciada](sql-database-managed-instance.md) no Banco de Dados SQL do Azure oferece apenas o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

> [!IMPORTANT]
> A [camada de serviço em hiperescala (versão prévia)](sql-database-service-tier-hyperscale.md) está em versão prévia pública somente para bancos de dados individuais usando o modelo de compra de vCore.

A tabela e o gráfico a seguir comparam e contrastam esses dois modelos de compra.

|**Modelo de compra**|**Descrição**|**Mais adequado para**|
|---|---|---|
|Modelo baseado em DTU|Esse modelo é baseado em uma medida em pacote de recursos de E/S, armazenamento e computação. Os tamanhos da computação são expressos em termos de DTUs (unidades de transação de banco de dados) para bancos de dados individuais e de eDTUs (unidades de transação do banco de dados elástico) para pools elásticos. Para saber mais sobre DTUs e eDTUs, confira [O que são DTUs e eDTUs?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Mais adequado para clientes que desejam opções de recursos simples e pré-configuradas.|
|Modelo baseado em vCore|Esse modelo permite escolher recursos de armazenamento e computação de maneira independente. O modelo de compra baseado em vCore também permite que você use o [Benefício Híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para poupar custos.|Mais adequado para clientes que valorizam flexibilidade, controle e transparência.|
||||  

![modelo de preços](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Custos de computação

O custo de computação reflete a capacidade de computação total provisionada para o aplicativo. Na camada de serviço comercialmente crítico, alocamos automaticamente pelo menos três réplicas. Para refletir essa alocação adicional de recursos de computação, o preço no modelo de compra baseado em vCore é aproximadamente 2,7 vezes maior na camada de serviço comercialmente crítico do que na camada de serviço de uso geral. Pelo mesmo motivo, o maior preço de armazenamento por GB na camada de serviço comercialmente crítico reflete E/S elevada e baixa latência do armazenamento SSD. Ao mesmo tempo, o custo do armazenamento de backup não é diferente entre essas duas camadas de serviço porque, em ambos os casos, usamos uma classe de Armazenamento Standard.

## <a name="storage-costs"></a>Custos de armazenamento

Diferentes tipos de armazenamento são cobrados de formas diferentes. Para armazenamento de dados, você será cobrado pelo armazenamento provisionado baseado no tamanho máximo do banco de dados ou do pool selecionado. O custo não muda, a menos que você reduza ou aumente esse máximo. O armazenamento de backup está associado a backups automatizados de sua instância é alocado dinamicamente. Aumentar o período de retenção de backup aumenta o armazenamento de backup consumido por sua instância.

Sete dias de backups automatizados dos bancos de dados são copiados para o armazenamento de blobs RA-GRS Standard por padrão. O armazenamento é usado por backups completos semanais, backups diferenciais diários e backups de log de transações copiados a cada 5 minutos. O tamanho do log de transações depende da taxa de alteração do banco de dados. Uma quantidade de armazenamento mínima igual a 100% do tamanho do banco de dados é fornecida sem encargos extras. O consumo adicional de armazenamento de backup será cobrado em GB/mês.

Para obter mais informações sobre preços de armazenamento, confira a página de [preços](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

Um núcleo virtual representa a CPU lógica oferecida com uma opção para escolher entre gerações de hardware e características físicas de hardware (por exemplo, número de núcleos, memória e tamanho de armazenamento). O modelo de compra baseado em vCore fornece flexibilidade, controle, transparência do consumo de recursos individual e uma forma simples de mover os requisitos de carga de trabalho local para a nuvem. Esse modelo permite escolher computação, memória e armazenamento com base nas necessidades de carga de trabalho. No modelo de compra baseado em vCore, você pode escolher entre as camadas de serviço [uso geral](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) e [comercialmente crítico](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) para [bancos de dados individuais](sql-database-single-database-scale.md), [pools elásticos](sql-database-elastic-pool.md) e [instâncias gerenciadas](sql-database-managed-instance.md). Para bancos de dados individuais, você também pode escolher a [camada de serviço em hiperescala (versão prévia)](sql-database-service-tier-hyperscale.md).

O modelo de compra baseado em vCore permite que você escolha recursos de computação e armazenamento de forma independente, corresponda ao desempenho local e otimize o preço. No modelo de compra baseado em vCore, os clientes pagam por:

- Computação (camada de serviço + número de vCores e quantidade de memória + geração de hardware)
- Tipo e quantidade de dados e armazenamento de log
- Armazenamento de backup (RA-GRS)

> [!IMPORTANT]
> Armazenamento de log e dados, E/S e computação são cobrados por banco de dados ou pool elástico. O armazenamento de backups é cobrado por cada banco de dados. Para obter mais informações sobre encargos de instância gerenciada, confira [instâncias gerenciadas](sql-database-managed-instance.md).
> **Limitações de região:** Para obter a lista atual de regiões compatíveis, consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Se você deseja criar uma Instância Gerenciada na região que atualmente não é suportada, você pode [enviar solicitação de suporte por meio do portal do Azure](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).
.

Se o banco de dados individual ou pool elástico consome mais de 300 DTUs, convertê-lo para o modelo de compra baseado em vCore pode reduzir o custo. Se você decidir converter, poderá fazê-lo usando a API de sua preferência ou o portal do Azure, sem tempo de inatividade. No entanto, a conversão não é necessária e não é feita automaticamente. Se o modelo de compra baseado em DTU atender aos seus requisitos de desempenho e de negócios, você deverá continuar utilizando-o. Se você decidir converter do modelo de compra baseado em DTU para aquele baseado em vCore, selecione o tamanho da computação usando as seguintes regras básicas:

- Cada 100 DTUs na camada Standard requerem pelo menos 1 vCore na camada de uso geral
- Cada 125 DTUs na camada Premium requerem pelo menos 1 vCore na camada comercialmente crítico

## <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

A DTU (Unidade de Transação do Banco de Dados) representa uma medida combinada de CPU, memória, leituras e gravações. O modelo de compra baseado em DTU oferece um conjunto de pacotes pré-configurados de recursos de computação e inclui armazenamento para impulsionar diferentes níveis de desempenho do aplicativo. Clientes que preferem a simplicidade de um pacote pré-configurado e pagamentos fixos por mês podem localizar o modelo baseado em DTU mais adequado às suas necessidades. No modelo de compra baseado em DTU, os clientes podem escolher entre as camadas de serviço **Básico**, **Standard** e **Premium** para ambos os [bancos de dados individuais](sql-database-single-database-scale.md) e [pools elásticos](sql-database-elastic-pool.md). Esse modelo de compra não está disponível em [instâncias gerenciadas](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>DTUs (Unidades de Transação de Banco de Dados)

Para um banco de dados individual em um tamanho da computação específico dentro de uma [camada de serviço](sql-database-single-database-scale.md), a Microsoft garante um certo nível de recursos para o banco de dados (independentemente de qualquer outro banco de dados na nuvem do Azure), fornecendo um nível previsível de desempenho. A quantidade de recursos é calculada como um número de unidades de transação do banco de dados ou DTUs e é uma medida combinada de recursos de computação, armazenamento e E/S. A proporção entre esses recursos originalmente foi determinada por uma [carga de trabalho de parâmetro de comparação de OLTP](sql-database-benchmark-overview.md) projetada para ser o cenário típico de cargas de trabalho OLTP reais. Quando sua carga de trabalho excede o valor de qualquer um desses recursos, a taxa de transferência é restringida, resultando em desempenho mais lento e inatividade. Os recursos usados pela sua carga de trabalho não afetam os recursos disponíveis para outros Bancos de Dados SQL na nuvem do Azure, e os recursos usados por outras cargas de trabalho não afetam os recursos disponíveis para o Banco de Dados SQL.

![caixa delimitadora](./media/sql-database-what-is-a-dtu/bounding-box.png)

As DTUs são úteis para entender a quantidade relativa de recursos entre Bancos de Dados SQL do Azure em diferentes tamanhos da computação e camadas de serviço. Por exemplo, dobrar as DTUs aumentando o tamanho da computação de um banco de dados equivale a dobrar o conjunto de recursos disponíveis para esse banco de dados. Por exemplo, um banco de dados Premium P11 com 1.750 DTUs fornece 350x mais capacidade de computação DTU que um banco de dados básico com 5 DTUs.  

Para ver informações mais aprofundadas sobre o consumo de recursos (DTU) da sua carga de trabalho, use a [Análise de Desempenho de Consultas](sql-database-query-performance.md) para:

- Identifique as principais consultas por contagem de CPU/Duração/Execução, que potencialmente podem ser ajustadas para melhorar o desempenho. Por exemplo, uma consulta com uso intensivo de E/S pode se beneficiar do uso de [técnicas de otimização na memória](sql-database-in-memory.md) para fazer melhor uso da memória disponível em um determinado tamanho da computação e camada de serviço.
- Analise detalhadamente os dados de uma consulta e exiba o texto e o histórico de utilização de recursos.
- Acesse recomendações de ajuste de desempenho que mostram as ações executadas pelo [Assistente do Banco de Dados SQL do Azure](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unidades de Transação de Banco de Dados Elástico (eDTUs)

Em vez de fornecer um conjunto dedicado de recursos (DTUs) que pode nem sempre ser necessários para um Banco de Dados SQL que está sempre disponível, é possível colocar os bancos de dados em um [pool elástico](sql-database-elastic-pool.md) em um servidor do Banco de Dados SQL que compartilha um pool de recursos entre esses bancos de dados. Os recursos compartilhados em um pool elástico são medidos por Unidades de Transação de Banco de Dados elástico ou eDTUs. Pools elásticos fornecem uma solução simples e econômica para gerenciar as metas de desempenho para vários bancos de dados que têm padrões de uso muito variáveis e imprevisíveis. Um pool elástico garante que os recursos não possam ser consumidos por um banco de dados no pool, enquanto garantem que cada banco de dados no pool sempre tem uma quantidade mínima de recursos disponíveis.

Um pool é fornecido com um número definido de eDTUs por um preço definido. No pool elástico, os bancos de dados individuais recebem a flexibilidade do dimensionamento automático dentro dos limites configurados. Um banco de dados sob carga mais pesada consumirá mais eDTUs para atender à demanda. Bancos de dados sob cargas mais leves consumirão menos eDTUs. Bancos de dados sem carga não consumirão eDTUs. Ao provisionar recursos para o pool inteiro, em vez de por banco de dados, as tarefas de gerenciamento são simplificadas, fornecendo um orçamento previsível para o pool.

eDTUs adicionais podem ser adicionados a um pool existente sem tempo de inatividade do banco de dados ou impacto sobre os bancos de dados no pool. Da mesma forma, se eDTUs extras não forem mais necessários, eles poderão ser removidos de um pool existente a qualquer momento. Você pode adicionar ou subtrair bancos de dados para o pool ou limitar a quantidade de eDTUs que um banco de dados pode usar sob carga pesada para reservar eDTUs para outros bancos de dados. Se um banco de dados estiver subutilizando recursos de forma previsível, você poderá movê-lo para fora do pool e configurá-lo como um único banco de dados com uma quantidade previsível de recursos necessários.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Determinar o número de DTUs necessárias para uma carga de trabalho

Se você quiser migrar uma carga de trabalho local existente, ou uma carga de trabalho virtual do SQL Server, para o Banco de Dados SQL do Azure, use a [Calculadora de DTU](https://dtucalculator.azurewebsites.net/) para obter um valor aproximado do número de DTUs necessárias. Para uma carga de trabalho existente do Banco de Dados SQL do Azure, use a [Análise de Desempenho de Consultas](sql-database-query-performance.md) para compreender o consumo de recursos do banco de dados (DTUs) e saber mais sobre como otimizar sua carga de trabalho. Você também pode usar o DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) para exibir o consumo de recursos da última hora. Como alternativa, o catálogo [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) exibe o consumo de recursos para os últimos 14 dias, porém com uma fidelidade menor de médias de cinco minutos.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Cargas de trabalho que se beneficiam de um pool elástico de recursos

Pools também são indicados para um grande número de bancos de dados com padrões de utilização específicos. Para um determinado banco de dados, esse padrão é caracterizado por uma baixa utilização média com picos de utilização relativamente pouco frequentes. O Banco de Dados SQL avalia automaticamente a utilização histórica de recursos dos bancos de dados em um servidor do Banco de Dados SQL existente e recomenda a configuração de pool apropriada no portal do Azure. Para saber mais , confira [Quando um pool elástico deve ser usado?](sql-database-elastic-pool.md)

## <a name="purchase-model-frequently-asked-questions-faq"></a>Perguntas frequentes sobre o modelo de compra

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Preciso colocar meu aplicativo off-line para converter de um banco de dados baseado em DTU em uma camada de serviço baseado em vCore

As novas camadas de serviço oferecem um método de conversão online simples, semelhante ao processo existente de atualização de bancos de dados da camada de serviço Standard para Premium e vice-versa. Esta conversão pode ser iniciada usando o portal do Azure, o PowerShell, a CLI do Azure, o T-SQL ou a API REST. Consulte [Gerenciar bancos de dados individuais](sql-database-single-database-scale.md) e [Gerenciar pools elásticos](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-using-the-vcore-based-purchase-to-a-service-tier-using-the-dtu-based-purchasing-model"></a>Posso converter um banco de dados de uma camada de serviço usando o modelo de compra baseado em vCore para uma camada de serviço usando o modelo de compra baseado em DTU?

Sim, você pode converter facilmente o banco de dados para qualquer objetivo de desempenho com suporte usando o portal do Azure, o PowerShell, a CLI do Azure, o T-SQL ou a API REST. Confira [Gerenciar bancos de dados individuais](sql-database-single-database-scale.md) e [Gerenciar pools elásticos](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Próximos passos

- Para Modelo de compra baseado em vCore, consulte [Modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md)
- Para o modelo de compra baseado em DTU, consulte [Modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md).
