---
title: "O que são pools elásticos? Gerenciar vários Bancos de Dados SQL – Azure | Microsoft Docs"
description: "Gerenciar e dimensionar vários Bancos de Dados SQL – centenas de milhares – usando pools elásticos. Um preço para os recursos que você pode distribuir quando necessário."
keywords: "vários bancos de dados, recursos de banco de dados, desempenho de banco de dados"
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 04/19/2017
ms.author: ddove
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: cc81b842833942019d5afec9b9dc33813ac48964
ms.lasthandoff: 04/20/2017


---

# <a name="elastic-pools-help-you-manage-and-scale-multiple-sql-databases"></a>Pools elásticos ajudam você a gerenciar e dimensionar vários Bancos de Dados SQL

Pools elásticos do Banco de Dados SQL são uma solução simples e econômica para gerenciar e dimensionar a vários bancos de dados com demandas de uso variadas e imprevisíveis. Os bancos de dados em um pool elástico estão em um único servidor de Banco de Dados SQL do Azure e compartilham um número definido de recursos ([Unidades de transação de banco de dados Elástico](sql-database-what-is-a-dtu.md) (eDTUs)) por um preço definido. Os pools elásticos no Banco de Dados SQL do Azure permitem que desenvolvedores de SaaS otimizem o desempenho de preço para um grupo de bancos de dados dentro de um orçamento prescrito oferecendo elasticidade de desempenho para cada banco de dados.   

> [!NOTE]
> Os pools elásticos têm uma disponibilidade geral (DG) em todas as regiões do Azure, exceto na Índia Ocidental, onde atualmente estão no modo de visualização.  A GA dos pools elásticos nessa região ocorrerá assim que possível.
>

## <a name="overview-of-elastic-pools"></a>Visão geral de pools elásticos 

Desenvolvedores de SaaS compilam aplicativos com base em camadas de dados de grande escala compostas por vários bancos de dados. Um padrão de aplicativo comum é provisionar um banco de dados individual para cada cliente. Mas diferentes clientes geralmente têm padrões de uso variados e imprevisíveis, e é difícil prever os requisitos de recursos de cada usuário de banco de dados individual. Tradicionalmente, você tinha duas opções: 

- Provisionar excessivamente os recursos com base no uso de pico e pagamento, ou
- Provisionamento insuficiente para poupar custos, às custas do desempenho e satisfação do cliente durante picos. 

Os pools elásticos resolvem esse problema, garantindo que os bancos de dados recebam os recursos de desempenho que precisam, quando precisam. Eles fornecem um mecanismo de alocação de recursos simples dentro de um orçamento previsível. Para saber mais sobre os padrões de design para aplicativos SaaS usando pools Elásticos, confira [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Padrões de Design para aplicativos de SaaS multilocatários com o Banco de Dados SQL).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Os pools permitem ao desenvolvedor comprar eDTUs [(Unidades de Transação de Banco de Dados elástico)](sql-database-what-is-a-dtu.md) para um pool compartilhado por vários bancos de dados para acomodar períodos de uso imprevisíveis em bancos de dados individuais. O requisito de eDTU para um pool é determinado pela utilização de agregação de seus bancos de dados. O número de eDTUs disponíveis para o pool é controlado pelo orçamento do desenvolvedor. O desenvolvedor simplesmente adiciona bancos de dados ao pool, define as eDTUs mínima e máxima para os bancos de dados e define a eDTU do pool com base no seu orçamento. Um desenvolvedor pode usar pools para aumentar seu serviço com perfeição desde uma startup lean até uma empresa madura em escala cada vez maior.

Dentro do pool, os bancos de dados individuais recebem a flexibilidade para dimensionar automaticamente no conjunto de parâmetros. Sob carga pesada, um banco de dados pode consumir mais eDTUs para atender à demanda. Bancos de dados sob cargas leves consumem menos e bancos de dados sem carga não consomem nenhum eDTU. O provisionamento de recursos para o pool inteiro em vez do bancos de dados único simplifica as tarefas de gerenciamento. Além disso, você tem um orçamento previsível para o pool. As eDTUs adicionais podem ser adicionadas a um pool existente sem tempo de inatividade do banco de dados, exceto que os bancos de dados podem precisar ser movidos para fornecer os recursos de computação adicionais para a nova reserva de eDTU. Da mesma forma, se eDTUs extras não forem mais necessários, eles poderão ser removidos de um pool existente a qualquer momento. E você pode adicionar ou subtrair bancos de dados para o pool. Se um banco de dados é previsível por estar subutilizando recursos, mova-o.

Você pode criar e gerenciar um pool elástico usando o [Portal do Azure](sql-database-elastic-pool-manage-portal.md), o [PowerShell](sql-database-elastic-pool-manage-powershell.md), o [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), o [C#](sql-database-elastic-pool-manage-csharp.md)e a API REST. 

## <a name="when-to-consider-a-pool"></a>Quando considerar um pool
Pools também são indicados para um grande número de bancos de dados com padrões de utilização específicos. Para um determinado banco de dados, esse padrão é caracterizado por baixa utilização média com picos de utilização relativamente pouco frequentes.

Quanto mais bancos de dados você conseguir adicionar a um pool, maior será a sua economia. Dependendo de seu padrão de utilização do aplicativo, é possível ver economias com apenas dois bancos de dados S3.  

As seções a seguir ajudam a compreender como avaliar se sua coleção específica de bancos de dados se beneficia de estar em um pool. Os exemplos usam pools Standard, mas os mesmos princípios também se aplicam aos pools Basic e Premium.

### <a name="assessing-database-utilization-patterns"></a>Avaliar os padrões de utilização do banco de dados
A figura a seguir mostra um exemplo de um banco de dados que passa muito tempo ocioso, mas também apresenta picos de atividade periodicamente. Este é um padrão de utilização adequado para um pool:

   ![um banco de dados individual adequado para um pool](./media/sql-database-elastic-pool/one-database.png)

Para o período de cinco minutos ilustrado, DB1 apresenta picos de até 90 DTUs, mas seu uso geral médio é inferior a cinco DTUs. Um nível de desempenho S3 é necessário para executar essa carga de trabalho em um banco de dados individual, mas isso deixa a maioria dos recursos não utilizados durante períodos de baixa atividade.

Um pool permite que essas DTUs não utilizadas sejam compartilhadas entre vários bancos de dados, reduzindo as DTUs necessárias e os custos gerais.

Considerando ainda o exemplo anterior, suponha que há outros bancos de dados com padrões de utilização semelhantes ao do DB1. Nas próximas duas figuras abaixo, a utilização de quatro e 20 bancos de dados é disposta no mesmo gráfico para ilustrar como sua utilização não se sobrepõe ao longo do tempo:

   ![quatro bancos de dados com um padrão de utilização adequado para um pool](./media/sql-database-elastic-pool/four-databases.png)

  ![vinte bancos de dados com um padrão de utilização adequado para um pool](./media/sql-database-elastic-pool/twenty-databases.png)

A utilização de DTU agregada em todos os 20 bancos de dados é ilustrada pela linha preta na figura anterior. Ela mostra que a utilização de DTU agregada nunca excede 100 DTUs e indica que os 20 bancos de dados podem compartilhar 100 eDTUs durante esse período de tempo. Isso resulta em uma redução de 20x em DTUs e em uma redução de 13x no preço quando comparado à colocação de cada um dos bancos de dados nos níveis de desempenho S3 para bancos de dados individuais.

Este exemplo é ideal pelas seguintes razões:

* Há grandes diferenças entre o pico de utilização e a utilização média por banco de dados.  
* O pico de utilização de cada banco de dados ocorre em diferentes momentos.
* eDTUs são compartilhados entre vários bancos de dados.

O preço de um pool é uma função das eDTUs do pool. Embora o preço unitário de eDTU para um pool seja 1,5x maior que o preço unitário de DTU para um banco de dados individual, **as eDTUs do pool podem ser compartilhadas por vários bancos de dados e, assim, menos eDTUs são necessárias no total**. Essas distinções no preço e compartilhamento de eDTU são a base do potencial de economia que os pools podem oferecer.  

As seguintes regras básicas relacionadas à contagem e utilização de banco de dados ajudam a garantir que um pool ofereça um custo reduzido em comparação ao uso de níveis de desempenho de bancos de dados individuais.


### <a name="minimum-number-of-databases"></a>Número mínimo de bancos de dados
Se a soma das DTUs dos níveis de desempenho para bancos de dados individuais for maior que 1,5x as eDTUs necessárias para o pool, um pool elástico será mais econômico. Para ver os tamanhos disponíveis, consulte [Limites de eDTU e armazenamento para pools e bancos de dados elásticos](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

***Exemplo***<br>
pelo menos dois bancos de dados S3 ou 15 bancos de dados S0 são necessários para um pool de 100 eDTUs ser mais econômico do que o uso de níveis de desempenho para bancos de dados individuais.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Número máximo de banco de dados em pico simultaneamente
Ao compartilhar eDTUs, nem todos os bancos de dados em um pool podem usar as eDTUs simultaneamente até o limite disponível ao usar níveis de desempenho de bancos de dados individuais. Quanto menos bancos de dados em pico simultaneamente, menor poderá ser o eDTU do pool e mais econômico ainda ele se torna. Em geral, no máximo 2/3 (ou 67%) dos bancos de dados no pool deve atingir o pico simultaneamente de seus limites de eDTU.

***Exemplo***<br>
para reduzir os custos de três bancos de dados S3 em um pool com 200 eDTUs, no máximo dois desses bancos de dados podem atingir simultaneamente o pico em sua utilização. Caso contrário, se mais de dois desses quatro bancos de dados S3 entrarem em pico simultaneamente, o pool precisará ser dimensionado para mais de 200 eDTUs. Se o pool for redimensionado para mais de 200 eDTUs, mais bancos de dados S3 precisarão ser adicionados ao pool para manter os custos menores do que os níveis de desempenho de bancos de dados individuais.

Observe que esse exemplo não considera a utilização de outros bancos de dados no pool. Se todos os bancos de dados tiverem uma certa utilização em um determinado momento, menos de 2/3 (ou 67%) dos bancos de dados pode atingir o pico simultaneamente.

### <a name="dtu-utilization-per-database"></a>Utilização de DTU por banco de dados
Uma grande diferença entre o máximo e média de utilização de um banco de dados indica longos períodos de baixa utilização e curtos períodos de alta utilização. Esse padrão de utilização é ideal para compartilhar recursos entre bancos de dados. Um banco de dados deve ser considerado para um pool quando seu pico de utilização for aproximadamente 1,5 vez maior que sua utilização média.

***Exemplo***<br>
um banco de dados S3 com picos de 100 DTUs e que usa em média 67 DTUs, ou menos, é um bom candidato para o compartilhamento de eDTUs em um pool. Outra opção de bom candidato para um pool elástico seria um banco de dados S1 com pico de 20 DTUs e média de uso de 13 DTUs ou menos.

## <a name="sizing-an-elastic-pool"></a>Dimensionar um pool elástico
O melhor tamanho de um pool depende das eDTUs e recursos de armazenamento agregados necessários para todos os bancos de dados no pool. Isso inclui determinar a maior das quantidades a seguir:

* DTUs máximo utilizadas por todos os bancos de dados no pool.
* Bytes de armazenamento máximo utilizados por todos os bancos de dados no pool.

Para ver os tamanhos disponíveis, consulte [Limites de eDTU e armazenamento para pools e bancos de dados elásticos](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

O Banco de Dados SQL avalia automaticamente a utilização histórica de recursos dos bancos de dados em um servidor de Banco de Dados SQL existente e recomenda a configuração de pool apropriada no portal do Azure. Além das recomendações, uma experiência interna calcula o uso de eDTU para um grupo personalizado de bancos de dados no servidor. Isso permite que você faça uma análise "e se" interativamente adicionando bancos de dados ao pool e removendo-os para obter a análise de uso de recursos e conselhos de dimensionamento antes de confirmar as alterações. Para obter instruções, confira [Monitorar, gerenciar e dimensionar um pool elástico](sql-database-elastic-pool-manage-portal.md).

Em casos em que você não pode usar as ferramentas, os procedimentos passo a passo a seguir podem ajudá-lo a estimar se um pool é mais econômico do que bancos de dados individuais:

1. Estime as eDTUs necessárias para o pool da seguinte maneira:

   MAX (<*Número total de bancos de dados* X *utilização média de DTU por banco de dados*>,<br>
   <*Número de bancos de dados em pico simultaneamente* X *Utilização de DTU em pico por banco de dados*)
2. Estime o espaço de armazenamento necessário para o pool adicionando o número de bytes necessários para todos os bancos de dados no pool. Determine o tamanho do pool em eDTU que fornece essa quantidade de armazenamento. Para ver os limites de armazenamento em pool baseados no tamanho do pool em eDTUs, confira [eDTU and storage limits for elastic pools and elastic databases](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)(Limites de armazenamento e eDTU para pools elásticos e bancos de dados elásticos).
3. Escolha a maior entre as estimativas de eDTU das etapas 1 e 2.
4. Consulte a [Página de preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) e localize o menor pool de eDTU que seja maior que a estimativa da Etapa 3.
5. Compare o preço do pool da Etapa 5 com o preço de usar os níveis de desempenho adequados para bancos de dados individuais.

## <a name="edtu-and-storage-limits-for-elastic-pools"></a>eDTU e limites de armazenamento para pools elásticos

A tabela a seguir descreve as características dos pools elásticos Básico, Standard, Premium e Premium RS.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Se todas as DTUs de um pool elástico forem usadas, cada banco de dados no pool receberá uma quantidade igual de recursos para processar as consultas.  O serviço de Banco de Dados SQL fornece integridade de compartilhamento de recursos entre os bancos de dados ao garantir fatias iguais de tempo de computação. A integridade de compartilhamento de recursos do pool elástico é adicional a qualquer quantidade de recursos garantidos de outra forma a cada banco de dados quando o mínimo de DTUs por banco de dados é definido com um valor diferente de zero.

## <a name="database-properties-for-pooled-databases"></a>Propriedades do banco de dados para bancos de dados em pool

A tabela a seguir descreve as propriedades dos bancos de dados em pool.

| Propriedade | Descrição |
|:--- |:--- |
| Máximo de eDTUs por banco de dados |O número máximo de eDTUs que qualquer banco de dados no pool pode usar, se disponível, com base na utilização por outros bancos de dados no pool.  O máximo de eDTUs por banco de dados não é uma garantia de recursos para um banco de dados.  Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. Defina um valor para o máximo de eDTUs por banco de dados que seja alto o suficiente para lidar com picos de utilização do banco de dados. Espera-se um grau de sobrecarga, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente. Por exemplo, suponha que o pico de utilização por banco de dados seja de 20 eDTUs e apenas 20% dos 100 bancos de dados no pool atinjam o pico simultaneamente.  Se o máximo de eDTUs por banco de dados for definido para 20 eDTUs, será razoável sobrecarregar o pool em 5 vezes e definir os eDTUs por pool como 400. |
| Mínimo de eDTUs por banco de dados |O número mínimo de eDTUs garantido para qualquer banco de dados no pool.  Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. O mínimo de eDTUs por banco de dados pode ser definido como 0 e também é o valor padrão. Essa propriedade é definida entre 0 e a utilização média de eDTUs por banco de dados. O produto do número de bancos de dados no pool e o mínimo de eDTUs por banco de dados não pode exceder os eDTUs por pool.  Por exemplo, se um pool tiver 20 bancos de dados e o mínimo de eDTUs por banco de dados for definido como 10 eDTUs, os eDTUs por pool deverão ser de pelo menos 200 eDTUs. |
| Armazenamento máximo de dados por banco de dados |O armazenamento máximo para um banco de dados em um pool. Os bancos de dados em pool compartilham o armazenamento de pool, de modo que o armazenamento do banco de dados é limitado ao menor entre o armazenamento de pool restante e o armazenamento máximo por banco de dados. O armazenamento máximo por banco de dados refere-se ao tamanho máximo de armazenamento dos arquivos de dados e não inclui o espaço usado pelos arquivos de log. |
|||

## <a name="elastic-jobs"></a>Trabalhos elásticos
Com um pool, as tarefas de gerenciamento são simplificadas com a execução de scripts em **[trabalhos elásticos](sql-database-elastic-jobs-overview.md)**. Um trabalho elástico elimina a maioria do tédio associado a um grande número de bancos de dados. Para começar, consulte [Introdução aos Trabalhos Elásticos](sql-database-elastic-jobs-getting-started.md).

Para saber mais sobre outras ferramentas de banco de dados para trabalhar com vários bancos de dados, veja [Expansão com o Banco de Dados SQL](sql-database-elastic-scale-introduction.md).

## <a name="business-continuity-features-for-databases-in-a-pool"></a>Recursos de continuidade de negócios para bancos de dados em um pool
Os bancos de dados elásticos normalmente dão suporte aos mesmos [recursos de continuidade dos negócios](sql-database-business-continuity.md) disponíveis para bancos de dados individuais.

### <a name="point-in-time-restore"></a>Recuperação pontual
A restauração pontual usa backups de banco de dados automáticos para recuperar um banco de dados em um pool para um ponto específico no tempo. Confira [Restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>Restauração geográfica
A restauração geográfica fornecerá a opção de recuperação padrão quando um banco de dados estiver indisponível devido a um incidente na região onde está hospedado. Confira [Restaurar um Banco de Dados SQL ou fazer failover para um secundário](sql-database-disaster-recovery.md)

### <a name="active-geo-replication"></a>Replicação geográfica ativa
Para aplicativos que têm requisitos de recuperação mais agressivos do que a Restauração Geográfica pode oferecer, configure a [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md).

## <a name="next-steps"></a>Próximas etapas

* Você pode criar e gerenciar um pool elástico usando o [Portal do Azure](sql-database-elastic-pool-manage-portal.md), o [PowerShell](sql-database-elastic-pool-manage-powershell.md), o [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), o [C#](sql-database-elastic-pool-manage-csharp.md)e a API REST.
* Para obter um vídeo, confira [Curso em vídeo da Microsoft Virtual Academy sobre os recursos elásticos do Banco de Dados SQL do Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Para saber mais sobre os padrões de design para aplicativos SaaS usando pools Elásticos, confira [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Padrões de Design para aplicativos de SaaS multilocatários com o Banco de Dados SQL).
