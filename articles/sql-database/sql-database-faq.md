---
title: Perguntas frequentes do Banco de dados SQL do Azure | Microsoft Docs
description: Respostas a perguntas comuns feitas por clientes sobre bancos de dados de nuvem e o Banco de Dados SQL do Azure, o sistema de gerenciamento de bancos de dados relacionais da Microsoft (RDBMS) e o banco de dados como um serviço na nuvem.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/22/2018
ms.openlocfilehash: 7bb692735cb3541fb05b2c39dd7325fa74a00034
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232198"
---
# <a name="sql-database-frequently-asked-questions-faq"></a>Perguntas Frequentes do Banco de Dados SQL (FAQ)

## <a name="what-is-the-current-version-of-sql-database"></a>Qual é a versão atual do banco de dados SQL

A versão atual do banco de dados SQL é V12. Versão V11 foi desativado.

## <a name="what-is-the-sla-for-sql-database"></a>O que é o SLA para o banco de dados SQL

Garantimos que por pelo menos 99,99% do tempo você terá conectividade entre o Banco de Dados SQL do Microsoft Azure e o nosso gateway da Internet, independentemente da sua camada de serviço. 0,01% é reservado para patches, upgrades e failovers. Para saber mais, veja [SLA](http://azure.microsoft.com/support/legal/sla/). Para obter mais informações sobre a arquitetura de disponibilidade do Banco de Dados SQL do Azure, consulte [Alta Disponibilidade e Banco de Dados SQL do Azure](sql-database-high-availability.md).

## <a name="can-i-control-when-patching-downtime-occurs"></a>É possível controlar quando ocorre tempo de inatividade de patch

Não. O impacto de patch geralmente não será perceptível, se você [empregar lógica de repetição](sql-database-develop-overview.md#resiliency) no aplicativo.

## <a name="what-is-the-new-vcore-based-purchasing-model-for-azure-sql-database"></a>Qual é o novo modelo de compra baseado em vCore para o Banco de Dados SQL do Azure?

O novo modelo de compra é um acréscimo ao modelo existente baseado em DTU. O modelo baseado em vCore foi projetado para oferecer aos clientes flexibilidade, controle, transparência e uma maneira simples de mover os requisitos de carga de trabalho local para a nuvem. Ele também permite aos clientes escalar recursos de armazenamento e computação com base nas necessidades da carga de trabalho. Opções de banco de dados único e pool elástico usando o modelo vCore também são elegíveis para economias de até 30% com o [Benefício Híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/#services). Consulte [Modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [Modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md) para obter mais informações.

## <a name="what-is-a-vcore"></a>O que é um vCore

Um núcleo virtual representa a CPU lógica oferecida com uma opção para escolher entre gerações de hardware. As CPUs Lógicas Geração 4 são baseadas em processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz e as CPUs Lógicas Geração 5 são baseadas em processadores Intel E5-2673 v4 (Broadwell) de 2,3 GHz.

## <a name="is-moving-to-the-vcore-based-model-required"></a>Está migrando para o modelo baseado em vCore exigido

Não, a introdução do modelo baseado em vCore às opções de implantação de Banco de Dados Individual e de Pool Elástico refletem nosso compromisso com a escolha do cliente e com a flexibilidade. Se os clientes quiserem continuar usando o modelo baseado em DTU, eles não precisarão tomar nenhuma medida com esse anúncio, e a experiência e cobrança permanecerão inalteradas.

Em muitos casos, os aplicativos podem se beneficiar da simplicidade de um pacote de recursos pré-configurado. Portanto, continuamos a oferecer e apoiar essas escolhas baseadas em DTU para nossos clientes. Se você estiver utilizando-os e eles atendem aos requisitos de negócios, você deverá continuar fazendo isso.

Os modelos baseados em DTU e vCore continuarão a existir paralelamente. Estamos lançando o modelo baseado em vCore em reposta às solicitações de clientes por maior transparência em relação aos recursos de banco de dados e à capacidade para escalar recursos de armazenamento e computação de maneira separada. O modelo baseado em vCore também possibilita economia adicional aos clientes com o Software Assurance ativo por meio do Benefício Híbrido do Azure para SQL Server.

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model"></a>Como devo escolher entre o modelo de compra baseado em DTU em comparação ao modelo de compra baseado em vCore?

A DT (Unidade de Transmissão de Dados) é baseada em uma medida combinada de CPU, memória, leituras e gravações. Os tamanhos de computação baseados em DTU representam pacotes pré-configurados de recursos para gerar níveis diferentes de desempenho do aplicativo. Clientes que não querem se preocupar com os recursos subjacentes e preferem a simplicidade de um pacote pré-configurado enquanto pagam um valor fixa a cada mês podem achar o modelo baseado em DTU mais adequado às suas necessidades. No entanto, para clientes que precisam de mais informações sobre os recursos subjacentes ou precisam escaloná-los independentemente para atingir o desempenho ideal, o modelo baseado em vCore será a melhor escolha.  Além disso, se um cliente tiver um SA (Software Assurance) ativo para SQL Server, ele poderá aproveitar o investimento existente e economizar até 30% com o [Benefício de Uso Híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  As opções dentro de cada um dos modelos de compra fornecem os benefícios de um serviço totalmente gerenciado, como backups automáticos, atualizações de software e patches.

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>O que é o Benefício Híbrido do Azure para SQL Server?

O [Benefício Híbrido do Azure para SQL Server ](../virtual-machines/windows/hybrid-use-benefit-licensing.md) ajuda você a maximizar o valor de seus investimentos em licenciamento atuais e acelerar sua migração para a nuvem. O Benefício Híbrido do Azure para SQL Server é um benefício baseado no Azure que permite usar as licenças do SQL Server com o Software Assurance para pagar uma taxa reduzida (“taxa base”) no Banco de Dados SQL. O Benefício Híbrido do Azure para SQL Server está disponível na visualização pública do modelo de compra baseado em vCore para bancos de dados individuais do Bando de Dados SQL e pools elásticos. É possível aplicar esse benefício mesmo se a SKU estiver ativa, mas observe que a taxa base será aplicada a partir do momento em que selecioná-la no Portal do Azure. Não há emissão retroativa de crédito.

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Há direitos de uso duplo com o Benefício Híbrido do Azure para o SQL Server

Você tem 180 dias de direitos de uso duplo da licença para garantir que as migrações estejam executando perfeitamente. Após esse período de 180 dias, a licença do SQL Server somente poderá ser utilizada na nuvem no Banco de Dados SQL e não há direitos de uso duplo no local e na nuvem.

## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Como o Benefício Híbrido do Azure para SQL Server difere da mobilidade de licenças?

Atualmente, oferecemos os benefícios de mobilidade de licença para clientes do SQL Server com Software Assurance, o que permite a reatribuição de suas licenças a servidores de terceiros compartilhados. Esse benefício pode ser usado na IaaS do Azure e no AWS EC2.
O Benefício Híbrido do Azure para SQL Server difere da mobilidade de licenças em duas áreas principais:

- Ele fornece benefícios econômicos para mover cargas de trabalho altamente virtualizadas para o Azure. Os clientes do SQL EE podem obter 4 núcleos no Azure na SKU de Uso Geral para cada núcleo que possuem no local para aplicativos altamente virtualizados. A mobilidade de licenças não permite nenhum benefício de custo especial para mover cargas de trabalho virtualizadas para a nuvem.
- Ele fornece para um destino de PaaS no Azure (Instância Gerenciada do Banco de Dados SQL) que é altamente compatível com o SQL Server local

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Há direitos de uso duplo com o Benefício Híbrido do Azure para o SQL Server

Clientes do Banco de Dados SQL terão os direitos associados ao Benefício Híbrido do Azure para SQL Server a seguir:

|Licença de volume|O que o Benefício Híbrido do Azure para SQL Server oferece?|
|---|---|
|Clientes principais do SQL Server Enterprise Edition com SA|<li>É possível pagar Taxa Base em SKUs de Uso Geral ou Comercialmente Crítico</li><br><li>1 núcleo local = 4 núcleos na SKU de Uso Geral</li><br><li>1 núcleo local = 1 núcleo em SKU para Comercialmente Crítico</li>|
|Clientes principais do SQL Server Standard Edition com SA|<li>É possível pagar somente Taxa Base em SKU de Uso Geral</li><br><li>1 núcleo local = 1 núcleo na SKU de Uso Geral</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>O modelo baseado em vCore está disponível para a Instância Gerenciada do Banco de Dados SQL?

A [Instância Gerenciada](sql-database-managed-instance.md) está disponível apenas com o modelo baseado em vCore. Para obter mais informações, consulte também a [página de preços do Banco de Dados SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>O custo de computação e armazenamento depende do nível de serviço que eu escolho

O custo de computação reflete a capacidade de computação total provisionada para o aplicativo. Na camada de serviço Business Critical, alocamos automaticamente pelo menos três réplicas. Para refletir essa alocação adicional de recursos de computação, o preço do vCore é aproximadamente 2,7 vezes maior no Comercialmente Crítico. Pelo mesmo motivo, o maior preço de armazenamento por GB na camada Comercialmente Crítico reflete E/S elevada e baixa latência do armazenamento SSD. Ao mesmo tempo, o custo do armazenamento de backup não é diferente porque, em ambos os casos, usamos uma classe de armazenamento Standard.

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>Como sou cobrado pelo armazenamento - com base no que configuro inicialmente ou sobre o que usa o banco de dados

Diferentes tipos de armazenamento são cobrados de formas diferentes. Para armazenamento de dados, você será cobrado pelo armazenamento provisionado baseado no tamanho máximo do banco de dados ou do pool selecionado. O custo não muda, a menos que você reduza ou aumente esse máximo. O armazenamento de backup está associado a backups automatizados de sua instância é alocado dinamicamente. Aumentar o período de retenção de backup aumenta o armazenamento de backup consumido por sua instância. Não haverá cobrança adicional se o armazenamento de backup chegar até 100% do armazenamento de servidor provisionado total. O consumo adicional de armazenamento de backup é cobrado em GB por mês. Por exemplo, se você tiver um tamanho de armazenamento de banco de dados de 100 GB, terá 100 GB de backup sem custo adicional. Mas se o backup é de 110 GB, você paga pelos 10 GB adicionais.

Para o armazenamento de backup de um banco de dados individual, você é cobrado em uma base proporcional para o armazenamento que foi alocado para os backups do banco de dados menos o tamanho do banco de dados. Para o armazenamento de backup de um pool elástico, você é cobrado em uma base proporcional para o armazenamento que foi alocado para os backups do banco de dados de todos os bancos de dados no pool, menos o tamanho máximo de dados do pool elástico. Qualquer aumento no tamanho do banco de dados ou pool elástico, ou aumento na taxa de transações, requer mais armazenamento e, portanto, aumenta a sua conta de armazenamento de backup.  Quando você aumenta o tamanho máximo de dados, esse novo valor é deduzido do tamanho do armazenamento de backup cobrado.

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>Como faço para selecionar o SKU certo ao converter um banco de dados existente para os novos níveis de serviço

Para aplicativos de Banco de Dados SQL existentes que usam o modelo baseado em DTU, a camada de serviço de Uso Geral é comparável à camada Standard. A camada de serviço Comercialmente Crítico é comparável à camada Premium. Em ambos os casos, você deve alocar pelo menos 1 vCore para cada 100 DTUs que o aplicativo usa no modelo baseado em DTU.

## <a name="do-the-new-vcore-based-service-tiers-offer-the-compute-sizes-compatible-with-all-existing-compute-sizes"></a>As novas camadas de serviço baseadas no vCore oferecem os tamanhos de computação compatíveis com todos os tamanhos de computação existentes?

As novas camadas de serviço baseadas em vCore oferecem opções de desempenho comparáveis para todos os pools elásticos e bancos de dados usando 100 DTUs ou mais.  Continuaremos a adicionar mais tamanhos de computação ao longo do tempo para acomodar cargas de trabalho com menos de 100 DTUs.

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>Há alguma diferença de recurso de banco de dados entre as camadas de serviço baseadas em DTU e novas baseadas em vCore?

As novas camadas de serviço dão suporte a um superconjunto dos recursos disponíveis com as ofertas atuais baseadas em DTU. Os recursos adicionais incluem um conjunto de DMVs (exibições de gerenciamento dinâmico) e opções adicionais de configuração de recurso.

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>Se meu banco de dados estiver limitado à CPU e não usar muito armazenamento, posso aumentar o processamento sem pagar por armazenamento extra

Sim, você pode selecionar independentemente o nível de computação que o aplicativo precisa e manter o armazenamento inalterado. O armazenamento pode ser definido como 32 GB.

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>As novas camadas baseadas no vCore suportarão a restauração ponto a ponto (PITR) por 35 dias como hoje?

Você pode configurar a retenção de backup para PITR entre 7 e 35 dias. O armazenamento de backups será cobrado separadamente com base no consumo real de armazenamento, se exceder o valor de armazenamento igual ao tamanho máximo dos dados. Na versão prévia, por padrão, o período de retenção PITR é definido como 7 dias. Em muitos casos, o tamanho máximo dos dados é suficiente para armazenar 7 dias de backups.

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>Por que você permite a seleção da geração de hardware para computação?

Nossa meta é permitir flexibilidade máxima para que você possa escolher uma configuração de desempenho que corresponda às necessidades do aplicativo. Hardware Ger 4 oferece substancialmente mais memória por vCore. No entanto, o hardware Gen5 permite que você amplie recursos de computação muito maiores. Para obter mais informações, consulte [modelo de compra de vCore](sql-database-service-tiers-vcore.md)

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Preciso colocar meu aplicativo off-line para converter de um banco de dados baseado em DTU em um nível de serviço baseado em vCore

As novas camadas de serviço oferecem um método de conversão online simples, semelhante ao processo existente de atualização de bancos de dados da camada de serviço Standard para Premium e vice-versa. Esta conversão pode ser iniciada usando o portal do Azure, o PowerShell, a CLI do Azure, o T-SQL ou a API REST. Consulte [Gerenciar bancos de dados individuais](sql-database-single-database-scale.md) e [Gerenciar pools elásticos](sql-database-elastic-pool.md).

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>Posso converter um banco de dados de uma camada de serviço baseada em vCore em uma baseada em DTU

Sim, você pode converter facilmente o banco de dados para qualquer objetivo de desempenho com suporte usando o portal do Azure, o PowerShell, a CLI do Azure, o T-SQL ou a API REST. Consulte [Gerenciar bancos de dados individuais](sql-database-single-database-scale.md) e [Gerenciar pools elásticos](sql-database-elastic-pool.md).

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>Posso fazer o upgrade ou o downgrade entre as camadas de serviço General Purpose e Business Critical?

Sim, com algumas restrições. A SKU de destino deve atender ao tamanho máximo do banco de dados ou do pool elástico que você configurou para a implantação existente. Se você estiver usando o [Benefício Híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md), o SKU Crítico para os Negócios estará disponível apenas para clientes com licenças Enterprise Edition. Somente os clientes que migraram de local para Uso Geral usando o Benefício Híbrido do Azure para SQL Server com licenças do Enterprise Edition poderão fazer upgrade para Comercialmente Crítico. Para obter detalhes, consulte [quais são os direitos específicos do benefício híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md)?

Essa conversão não resultará em tempo de inatividade e pode ser iniciada usando o portal do Azure, o PowerShell, a CLI do Azure, o T-SQL ou a API REST. Consulte [Gerenciar bancos de dados individuais](sql-database-single-database-scale.md) e [Gerenciar pools elásticos](sql-database-elastic-pool.md).

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>Estou usando um banco de dados Premium RS que não estará disponível em geral - posso atualizá-lo para um novo nível e obter um benefício semelhante de preço / desempenho

Como o modelo vCore permite o controle independente da quantidade de computação e armazenamento provisionados, você pode gerenciar com mais eficiência os custos resultantes, tornando-se um destino atraente para os bancos de dados Premium RS. Além disso, o [Benefício Híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) fornece um desconto substancial quando o modelo baseado em vCore é usado.

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>Com que frequência posso ajustar os recursos por pool

Quantas vezes desejar. Consulte [Gerenciar pools elásticos](sql-database-elastic-pool.md).

## <a name="how-long-does-it-take-to-change-the-service-tier-or-compute-size-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Quanto tempo leva para alterar a camada de serviço ou o tamanho de computação de um único banco de dados ou mover um banco de dados para dentro e para fora de um pool elástico

A alteração da camada de serviço de um banco de dados e a inclusão e a retirada de um pool exige que o banco de dados seja copiado para a plataforma como uma operação em segundo plano. Alterar a camada de serviço pode levar alguns minutos a várias horas, dependendo do tamanho dos bancos de dados. Em ambos os casos, os bancos de dados permanecem online e disponíveis durante a movimentação. Para obter detalhes sobre como alterar os bancos de dados únicos, consulte [Alterar a camada de serviço de um banco de dados](sql-database-service-tiers-dtu.md).

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Quando devo usar um único banco de dados versus bancos de dados elásticos

Em geral, os pools elásticos são projetados para um típico [padrão de aplicativo de software como serviço (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md), em que há um banco de dados por cliente ou locatário. A compra de bancos de dados individuais e o provisionamento excessivo para atender a variável e a demanda de pico de cada banco de dados geralmente não são eficientes em termos de custo. Com os pools, você gerencia o desempenho coletivo do pool e os bancos de dados são escalados e reduzidos verticalmente. O mecanismo inteligente do Azure recomendará um pool para bancos de dados quando perceber um padrão de uso que o garanta. Para obter detalhes, consulte [Diretrizes de pool elástico](sql-database-elastic-pool.md).

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Como o uso do banco de dados SQL usando o modelo de compra baseado em DTU aparece na minha fatura

O Banco de Dados SQL cobra uma taxa horária previsível com base no [modelo de compra](sql-database-service-tiers-dtu.md). O uso real é calculado e rateado por hora, de modo que sua fatura poderá mostrar frações de hora. Por exemplo, se um banco de dados existir por 12 horas em um mês, sua fatura mostrará a utilização de 0,5 dia.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>E se um único banco de dados estiver ativo por menos de uma hora ou usar um nível de serviço mais alto por menos de uma hora?

Você será cobrado pelas horas em que um banco de dados existir usando a camada de serviço mais alta mais o tamanho de computação aplicado durante essas horas, independentemente do uso ou se o banco de dados ficou ativo por menos de uma hora. Por exemplo, se você criar um banco de dados individual e o excluir depois de cinco minutos, sua fatura apresentará uma cobrança referente a uma hora de banco de dados.

Exemplos:

- Se você criar um banco de dados Básico e atualizá-lo imediatamente para Standard S1, será cobrado pela tarifa Standard S1 na primeira hora.
- Se você atualizar um banco de dados do Básico para Premium às 22h e a atualização for concluída à 1h35, no dia seguinte, você será cobrado na taxa Premium começando à 1h.
- Se você fizer o downgrade de um banco de dados Premium para Básico às 11h e se ele for concluído às 14h15, o banco de dados será cobrado na taxa Premium até 15h e depois serão cobradas as taxas Básico.

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Como o uso do pool elástico usando o modelo de compra baseado em DTU aparece na minha conta

As cobranças do pool elástico aparecem na fatura como eDTUs (DTU Elástico) ou vCores mais armazenamento nos incrementos mostrados na [página de preços](https://azure.microsoft.com/pricing/details/sql-database/). Não há cobrança por banco de dados para pools elásticos. Você é cobrado por cada hora de existência de um pool no vCores ou eDTU mais elevado, independentemente do uso ou se o pool estava ativo por menos de uma hora.

Exemplos de modelos de compra baseados em DTU:

- Se você criar um pool elástico Standard com 200 eDTUs às 11h18, adicionando cinco bancos de dados ao pool, será cobrado por 200 eDTUs pela hora cheia, começando às 11h e se estendendo pelo resto do dia.
- No Dia 2, às 5:05h, o Banco de Dados 1 começa a consumir 50 eDTUs e se mantém estável ao longo do dia. Os Bancos de Dados 2 a 5 flutuam entre 0 e 80 eDTUs. Durante o dia, você adiciona cinco outros bancos de dados que consomem eDTUs variados durante o dia. O Dia 2 é um dia inteiro cobrado a 200 eDTUs.
- No Dia 3, às 5h,  você adiciona mais 15 bancos de dados. O uso do banco de dados aumenta durante o tia até o ponto em que você decide aumentar o número de eDTUs para o pool de 200 para 400, às 20:05h. As cobranças no nível de 200 eDTUs estavam em vigor até às 20h e aumentam para 400 eDTUs pelas quatro horas restantes.

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>Como o pool elástico é faturado para o modelo de compra baseado em DTU?

Pools elásticos são cobrados de acordo com as seguintes características:

- Um pool elástico é cobrado após sua criação, mesmo quando ele não contém bancos de dados.
- Um pool elástico é cobrado por hora. Essa é a mesma frequência de medição que para tamanhos de computação de bancos de dados individuais.
- Se um pool elástico for redimensionado, o pool não será cobrado de acordo com a nova quantidade de recursos até que a operação de redimensionamento seja concluída. Isso segue o mesmo padrão que a alteração do tamanho de computação de bancos de dados individuais.
- O preço de um pool elástico é baseado nos recursos do pool. O preço de um pool elástico é independente do número e da utilização dos bancos de dados elásticos dentro dele.

Para obter detalhes, consulte [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/), [Modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e [Modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>Como o uso baseado em vCore aparece na minha conta

No modelo baseado em vCore, o serviço é cobrado em uma taxa horária previsível com base na camada de serviço, computação provisionada no vCores, armazenamento provisionado em GB/mês e armazenamento de backup consumido. Se o armazenamento para backups exceder o tamanho total do banco de dados (ou seja, 100% do tamanho do banco de dados), haverá encargos adicionais. As horas de vCore, o armazenamento do banco de dados configurado, E/S consumida e armazenamento de backup são claramente discriminados na fatura, facilitando a visualização dos detalhes dos recursos que você usou. Armazenamento de backup até 100% do tamanho máximo do banco de dados incluído, além do qual você é cobrado em GB/mês consumido em um mês.

Por exemplo: 

- Se o Banco de Dados SQL existir por 12 horas em um mês, a fatura mostrará o uso por 12 horas de vCore. Se o Banco de Dados SQL provisionar 100 GB adicionais, a fatura mostra o uso do armazenamento em unidades de GB/Mês em horas proporcionais e o número de E/S consumidas em um mês.
- Se o Banco de Dados SQL estiver ativo por menos de uma hora, você será cobrado em relação a cada hora de existência do banco de dados usando a camada de serviço mais alta selecionada, o armazenamento provisionado e a E/S aplicados durante essa hora, independentemente do uso ou do servidor ter estado ativo por menos de uma hora ou não.
- Se você criar uma Instância Gerenciada e excluí-la cinco minutos depois, será cobrado por uma hora de banco de dados.
- Se criar uma Instância Gerenciada na camada de Uso Geral com 8 vCores e fizer upgrade dela imediatamente para 16 vCores, você será cobrado pelos 16 vCores pela primeira hora.

> [!NOTE]
> Por um período limitado, os encargos de backup e encargos de E/S estarão gratuitos.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Como o uso da georeplicação ativa em um pool elástico aparece na minha conta

Ao contrário dos bancos de dados únicos, o uso da [replicação geográfica ativa](sql-database-geo-replication-overview.md) com bancos de dados elásticos não tem um impacto direto no faturamento.  Você é cobrado apenas pelos recursos provisionados para cada um dos pools (pool primário e secundário)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Como o uso do recurso de auditoria afeta minha fatura

A auditoria é integrada ao serviço do Banco de Dados SQL sem custo adicional e está disponível em todas as camadas de serviço. No entanto, para armazenar os logs de auditoria, o recurso de auditoria usa uma conta do Armazenamento do Azure e as taxas para tabelas e consultas no Armazenamento do Azure são aplicadas com base no tamanho do seu log de auditoria.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Como faço para redefinir a senha para o administrador do servidor

No [Portal do Azure](https://portal.azure.com), clique em **Servidores SQL**, escolha o servidor na lista e clique em **Redefinir Senha**.

## <a name="how-do-i-manage-databases-and-logins"></a>Como gerenciar logons e bancos de dados

Consulte [gerenciamento de bancos de dados e logons](sql-database-manage-logins.md).

> [!NOTE]
> Não será possível alterar o nome da conta do administrador do servidor após a criação.

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Como tornar-se de que somente endereços IP autorizados têm permissão para acessar um servidor

Confira [Como definir as configurações de firewall no Banco de Dados SQL](sql-database-configure-firewall-settings.md).

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>O que é o retardo de replicação esperado durante a replicação geográfica um banco de dados entre duas regiões na mesma região geográfica do Azure

Atualmente, damos suporte a um RPO de cinco segundos e o retardo de replicação tem sido menor que isso, desde que o secundário geográfico esteja hospedado na região emparelhada recomendada pelo Azure e esteja na mesma camada de serviço.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>O que é um atraso de replicação esperado quando o geo-secundário é criado na mesma região que o banco de dados primário

Com base em dados empíricos, não há muita diferença entre o retardo de replicação intra e entre regiões quando a região emparelhada recomendada pelo Azure é usada.

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Se houver uma falha de rede entre duas regiões, como a lógica de repetição funciona quando a replicação geográfica é configurada?

Se houver uma desconexão, haverá uma repetição a cada 10 segundos para restabelecer as conexões.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>O que posso fazer para garantir que uma alteração crítica no banco de dados primário seja replicada

O secundário geográfico é uma réplica assíncrona e não tentamos mantê-la em sincronização completa com o primário. Mas nós fornecemos um método para forçar a sincronização de modo a assegurar a replicação das alterações críticas (por exemplo, atualizações de senha). A sincronização forçada afeta o desempenho, já que bloqueia o thread de chamada até que todas as transações confirmadas sejam replicadas. Para obter detalhes, veja [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx).

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Quais ferramentas estão disponíveis para monitorar o atraso de replicação entre o banco de dados primário e o geo-secundário

Expomos o retardo de replicação em tempo real entre o banco de dados primário e o secundário geográfico por meio de um DMV. Para obter detalhes, veja [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Para mover um banco de dados para um servidor diferente na mesma assinatura

No [Portal do Azure](https://portal.azure.com), clique em **Bancos de Dados SQL**, selecione um banco de dados na lista e, em seguida, clique em **Copiar**. Para obter mais detalhes, consulte [Copiar um Banco de Dados SQL do Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Para mover um banco de dados entre assinaturas

No [Portal do Azure](https://portal.azure.com), clique em **servidores SQL** e, em seguida, selecione o servidor que hospeda seu banco de dados da lista. Clique em **Mover**, então selecione os recursos a mover e a assinatura para a qual movê-los.
