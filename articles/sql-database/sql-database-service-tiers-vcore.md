---
title: Serviço do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre as camadas de serviço para bancos de dados únicos e em pool para fornecer níveis de desempenho e tamanhos de armazenamento.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 05/09/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 1424ae2d9ffe7308fe85b7eb8ed6b0062d59ce31
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057913"
---
# <a name="vcore-based-purchasing-model-for-azure-sql-database-preview"></a>Serviço do Banco de Dados SQL do Azure - vCore | Microsoft Docs

O [Banco de Dados SQL do Azure ](sql-database-technical-overview.md) oferece dois modelos de compra para recursos de E/S, armazenamento e computação: um modelo de compra baseado em DTU e um modelo de compra baseado em vCore (versão prévia). A tabela e o gráfico a seguir comparam e contrastam esses dois modelos de compra.

> [!IMPORTANT]
> Para o modelo de compra com base em DTU, consulte [DTU com base no modelo de compra](sql-database-service-tiers-dtu.md).


|**Modelo de compra**|**Descrição**|**Mais adequado para**|
|---|---|---|
|Modelo baseado em DTU|Esse modelo é baseado em uma medida em pacote de recursos de E/S, armazenamento e computação. Os níveis de desempenho são expressos em termos de DTUs (Unidades de transação de banco de dados) para bancos de dados individuais, e eDTUs (Unidades de transação de banco de dados elásticos) para pools elásticos. Para saber mais sobre DTUs e eDTUs, consulte [O que são DTUs e eDTUs](sql-database-what-is-a-dtu.md)?|Mais adequado para clientes que desejam opções de recursos simples e pré-configuradas.| 
|Modelo baseado em vCore|Esse modelo permite escalar recursos de armazenamento e computação de maneira independente. Além disso, permite usar o Benefício Híbrido do Azure para SQL Server para obter redução de custos.|Mais adequado para clientes que valorizam flexibilidade, controle e transparência.|
||||  

![modelo de preços](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model--preview"></a>Modelo de compra baseado no vCore (visão prévia)

Um núcleo virtual representa a CPU lógica oferecida com uma opção para escolher entre gerações de hardware. O modelo de compra baseado em vCore (versão prévia) fornece flexibilidade, controle, transparência do consumo de recursos individual e uma forma simples de mover os requisitos de carga de trabalho local para a nuvem. Esse modelo permite escalar computação, memória e armazenamento com base nas necessidades de carga de trabalho. No modelo de compra baseado em vCore, os clientes podem escolher entre camadas de serviço Comercialmente Crítico e de Uso Geral (versão prévia) para ambos os [bancos de dados individuais](sql-database-single-database-resources.md) e [pools elásticos](sql-database-elastic-pool.md). 

As camadas de serviço são diferenciadas por intervalo de níveis de desempenho, design de alta disponibilidade, isolamento de falhas, tipos de armazenamento e intervalo de E/S. O cliente deve configurar separadamente o período necessário de armazenamento e retenção para backups. Ao usar o modelo vCore, os bancos de dados individuais e os pools elásticos são qualificados para economias de até 30% com o [Benefício de Uso Híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

No modelo de compra baseado em vCore (preview), os clientes pagam por:
- Computação (camada de serviço + número de vCores + geração de hardware)*
- Tipo e quantidade de dados e armazenamento de log 
- Número de E/S**
- Armazenamento de backup (RA-GRS)** 

\* Na visualização pública inicial, as CPUs Lógicas Ger 4 são baseadas em processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz

\*\* Durante a versão prévia, 7 dias de backups e E/S são gratuitos

> [!IMPORTANT]
> Armazenamento de log e dados, E/S e computação são cobrados por banco de dados ou pool elástico. O armazenamento de backups é cobrado por cada banco de dados. Para obter detalhes sobre as cobranças de Instância Gerenciada, consulte [Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance.md).

> [!IMPORTANT]
> Limitações de região: 
>
> O modelo de compra baseado em vCore (versão prévia) ainda não está disponível no Sudoeste da Austrália. A versão prévia não está disponível nas regiões a seguir: Europa Ocidental, França Central, Sul do Reino Unido e Oeste do Reino Unido.
> 

## <a name="choosing-service-tier-compute-memory-storage-and-io-resources"></a>Escolher camada de serviço de recursos de E/S, armazenamento, memória e computação

A conversão para o modelo de compra baseado em vCore (visão prévia) permite que você dimensione recursos de computação e armazenamento de forma independente, corresponda ao desempenho local e otimize o preço. Se o banco de dados ou pool elástico consumir mais de 300 DTU, a conversão para o vCore poderá reduzir o custo. É possível converter usando a API de sua preferência ou o Portal do Azure, sem tempo de inatividade. No entanto, a conversão não é necessária. Se o modelo de compra de DTU atender aos seus requisitos de desempenho e de negócios, você deve continuar utilizando-o. Se você decidir converter do modelo DTU para o modelo vCore, deverá selecionar o nível de desempenho usando a seguinte regra geral: cada 100 DTU na camada Padrão exige pelo menos 1 vCore na camada de Finalidade Geral; cada 125 DTU na camada Premium requer pelo menos 1 vCore no nível Business Critical.

A tabela a seguir ajuda-o a reconhecer as diferenças entre essas duas camadas:

||**Uso geral**|**Comercialmente Crítico**|
|---|---|---|
|Mais adequado para|A maioria das cargas de trabalho comerciais. Oferece opções de armazenamento e computação escalonáveis e equilibradas orientadas a orçamento.|Aplicativos de negócios com altos requisitos de E/S. Oferece maior resiliência a falhas usando várias réplicas isoladas.|
|Computação|1 para 16 vCore|1 para 16 vCore|
|Memória|7 GB por núcleo |7 GB por núcleo |
|Armazenamento|Armazenamento remoto Premium, 5 GB – 4 TB|Armazenamento SSD local, 5 GB – 1 TB|
|Taxa de transferência de E/S (aproximada)|500 IOPS por vCore com 7500 IOPS máximo|5000 IOPS por núcleo|
|Disponibilidade|1 réplica, sem escala de leitura|3 réplica, 1 [escala de leitura](sql-database-read-scale-out.md), HA com redundância de zona|
|Backups|RA-GRS, 7-35 dias (7 dias por padrão)|RA-GRS, 7-35 dias (7 dias por padrão)*|
|Em Memória|N/D|Com suporte|
|||

\* Durante a versão prévia, o período de retenção de backups não é configurável e é fixado em 7 dias.

> [!IMPORTANT]
> Se você precisar de menos de um vCore de capacidade de computação, use o modelo de compra baseado em DTU.

Para obter detalhes sobre níveis de desempenho específicos e opções de tamanho de armazenamento disponíveis para banco de dados individual, consulte [Limites de recursos baseados em vCore do Banco de Dados SQL para banco de dados individuais](sql-database-vcore-resource-limits.md#single-database-storage-sizes-and-performance-levels) e para pools elásticos consulte [Limites de recursos baseados em vCore do Banco de Dados SQL para pools elásticos](sql-database-vcore-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Consulte [Perguntas frequentes sobre Banco de Dados SQL](sql-database-faq.md) para obter respostas para as perguntas frequentes. 

## <a name="storage-considerations"></a>Considerações de armazenamento

Considere o seguinte:
- O armazenamento alocado é usado por arquivos de arquivos de dados (MDF) e arquivos de log (LDF).
- Cada nível de desempenho dá suporte a um tamanho máximo de banco de dados, com um tamanho máximo padrão de 32 GB.
- Ao configurar o tamanho do banco de dados necessário (tamanho do MDF), 30% do armazenamento adicional é adicionado automaticamente para dar suporte ao LDF
- É possível selecionar qualquer tamanho de banco de dados entre 10 GB e o máximo com suporte
 - Para armazenamento Standard, aumente ou diminua o tamanho em incrementos de 10 GB
 - Para armazenamento Premium, aumente ou diminua o tamanho em incrementos de 250 GB
- Na camada de serviço de Uso Geral, `tempdb` usa um SSD anexado e esse custo de armazenamento é incluído no preço do vCore.
- Na camada de serviço Comercialmente Crítico, `tempdb` compartilha o SSD anexado com os arquivos MDF e LDF e o custo de armazenamento tempDB é incluído no preço vCore.

> [!IMPORTANT]
> Você é cobrado pelo armazenamento total alocado para MDF e LDF.

Para monitorar o tamanho total atual do MDF e do LDF, use [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para monitorar o tamanho atual dos arquivos MDF e LDF individuais, use [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

## <a name="backups-and-storage"></a>Backups e armazenamento

O armazenamento para backups de banco de dados é alocado para dar suporte aos recursos PITR (Recuperação Pontual) e LTR (Retenção em Longo Prazo) do Banco de Dados SQL. Esse armazenamento é alocado separadamente para cada banco de dados e faturado como duas cobranças separadas por banco de dados. 

- **PITR**: os backups de banco de dados individuais são copiados para o armazenamento RA-GRS automaticamente. O tamanho do armazenamento aumenta dinamicamente conforme os novos backups são criados.  O armazenamento é usado por backups completos semanais, backups diferenciais diários e backups de log de transações copiados a cada 5 minutos. O consumo de armazenamento depende da taxa de alteração do banco de dados e do período de retenção. É possível configurar um período de retenção separado para cada banco de dados entre 7 e 35 dias. Um valor de armazenamento mínimo igual a 1x de tamanho de dados é fornecido sem nenhum custo adicional. Para a maioria dos bancos de dados, esse valor é suficiente para armazenar 7 dias de backups.
- **LTR**: o Banco de Dados SQL oferece a opção de configurar a retenção em longo prazo de backups completos por até 10 anos. Se a política de LTR estiver habilitada, esses backups serão armazenados no armazenamento RA-GRS automaticamente, mas você poderá controlar com que frequência os backups serão copiados. Para atender a diferentes requisitos de conformidade, é possível selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. Essa configuração definirá quanto armazenamento será usado para os backups de LTR. É possível usar a calculadora de preços de LTR para estimar o custo do armazenamento LTR. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

## <a name="azure-hybrid-use-benefit"></a>Benefício de Uso do Azure Híbrido

No modelo de compra baseado em vCore (visualização), você pode trocar suas licenças existentes por taxas com desconto no Banco de Dados SQL usando o [Benefício de Uso Híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Esse benefício do Azure permite que você use as licenças locais do SQL Server para economizar até 30% no Banco de Dados SQL do Azure usando as licenças do SQL Server local com Software Assurance.

![preços](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-of-single-databases-with-geo-replication-links"></a>Migrar dos bancos de dados individuais com links de replicação geográfica

A migração do modelo baseado em DTU para o modelo baseado em vCore é semelhante ao upgrade ou downgrade dos relacionamentos de replicação geográfica entre os bancos de dados Standard e Premium. Ele não requer a replicação geográfica final, mas o usuário deve observar as regras de sequenciamento. Ao atualizar, será necessário primeiro fazer upgrade do banco de dados secundário e, em seguida, upgrade do primário. Ao fazer downgrade, inverta a ordem: primeiro, você deverá fazer downgrade do banco de dados primário e, em seguida, fazer downgrade do secundário. 

Ao usar a replicação geográfica entre dois pools elásticos, é altamente recomendável designar um pool como o primário e o outro como secundário. Nesse caso, os pools elásticos em migração devem usar a mesma orientação.  No entanto, é tecnicamente possível que um pool elástico contenha bancos de dados primários e secundários. Nesse caso, para migrar corretamente, é necessário tratar o pool com a maior utilização como "principal" e seguir as regras de sequenciamento adequadamente.  

A tabela a seguir fornece orientação para os cenários de migração específicos: 

|Camada de serviço atual|Camada de serviço de destino|Tipo de migração|Ações do usuário|
|---|---|---|---|
|Standard|Propósito geral|Lateral|Pode migrar em qualquer ordem, mas é necessário garantir um tamanho adequado de vCore*|
|Premium|Comercialmente Crítico|Lateral|Pode migrar em qualquer ordem, mas precisa garantir o dimensionamento apropriado de vCore*|
|Standard|Comercialmente Crítico|Atualizar|Deve migrar primeiro o secundário|
|Comercialmente Crítico|Standard|Downgrade|Deve migrar primeiro o primário|
|Premium|Propósito geral|Downgrade|Deve migrar primeiro o primário|
|Propósito geral|Premium|Atualizar|Deve migrar primeiro o secundário|
|Comercialmente Crítico|Propósito geral|Downgrade|Deve migrar primeiro o primário|
|Propósito geral|Comercialmente Crítico|Atualizar|Deve migrar primeiro o secundário|
||||

\* Cada 100 DTU na camada Standard requer pelo menos 1 vCore e cada 125 DTU na camada Premium requer pelo menos 1 vCore

## <a name="migration-of-failover-groups"></a>Migração de grupos de failover 

A migração de grupos de failover com vários bancos de dados requer a migração individual dos bancos de dados primário e secundário. Durante esse processo, as mesmas considerações e regras de sequenciamento se aplicam. Depois que os bancos de dados forem convertidos para o modelo baseado em vCore, o grupo de failover permanecerá em vigor com as mesmas configurações de política. 

## <a name="creation-of-a-geo-replication-secondary"></a>Criação de um secundário para replicação geográfica

Somente é possível criar um secundário geográfico usando a mesma camada de serviço que o primário. Para bancos de dados com alta taxa de geração de logs, é altamente recomendável que o secundário seja criado com o mesmo nível de desempenho que o primário. Se você estiver criando um secundário geográfico no pool elástico para um banco de dados primário individual, é altamente recomendável que o pool tenha a configuração `maxVCore` que corresponde ao nível de desempenho do banco de dados primário. Se você estiver criando um secundário geográfico no pool elástico para um primário em outro pool elástico, é altamente recomendável que os pools tenham as mesmas configurações`maxVCore`

## <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Usando a cópia do banco de dados para converter um banco de dados baseado em DTU em um banco de dados baseado em vCore.

É possível copiar qualquer banco de dados com um nível de desempenho baseado em DTU para um banco de dados com um nível de desempenho baseado em vCore sem restrições ou sequenciamento especial, desde que o nível de desempenho de destino dê suporte ao tamanho máximo do banco de dados de origem. Isso ocorre porque a cópia do banco de dados cria um instantâneo de dados a partir do horário de início da operação de cópia e não executa a sincronização de dados entre a origem e o destino. 

## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre níveis de desempenho específicos e opções de tamanho de armazenamento disponíveis, consulte [Limites de recursos baseados em DTU do Banco de Dados SQL](sql-database-dtu-resource-limits.md) e [Limites de recursos baseados em vCore do Banco de Dados SQL](sql-database-vcore-resource-limits.md).
- Consulte [Perguntas frequentes sobre Banco de Dados SQL](sql-database-faq.md) para obter respostas para as perguntas frequentes.
- Saiba mais sobre a [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)
