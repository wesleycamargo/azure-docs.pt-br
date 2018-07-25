---
title: Serviço do Banco de Dados SQL do Azure | Microsoft Docs
description: O modelo de compra baseado em vCore permite que você dimensione recursos de computação e armazenamento de forma independente, corresponda ao desempenho local e otimize o preço.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/16/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: d18076486704d5f03acd2253650762c3bd24b0af
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091485"
---
# <a name="choosing-a-vcore-service-tier-compute-memory-storage-and-io-resources"></a>Escolher uma camada de serviço, recursos de E/S, armazenamento, memória e computação de vCore

As camadas de serviço são diferenciadas por intervalo de níveis de desempenho, design de alta disponibilidade, isolamento de falhas, tipos de armazenamento e intervalo de E/S. O cliente deve configurar separadamente o período necessário de armazenamento e retenção para backups. Com o modelo vCore, os bancos de dados individuais e os pools elásticos são qualificados para economias de até 30% com o [Benefício de Uso Híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

A tabela a seguir ajuda-o a reconhecer as diferenças entre essas duas camadas:

||**Uso geral**|**Comercialmente Crítico**|
|---|---|---|
|Mais adequado para|A maioria das cargas de trabalho comerciais. Oferece opções de armazenamento e computação escalonáveis e equilibradas orientadas a orçamento.|Aplicativos de negócios com altos requisitos de E/S. Oferece maior resiliência a falhas usando várias réplicas isoladas.|
|Computação|1 a 80 vCores, Gen4 e Gen5 |1 a 80 vCores, Gen4 e Gen5|
|Memória|Gen4: 7 GB por núcleo<br>Gen5: 5,5 GB por núcleo | Gen4: 7 GB por núcleo<br>Gen5: 5,5 GB por núcleo |
|Armazenamento|Armazenamento remoto Premium, 5 GB – 4 TB|Armazenamento SSD local, 5 GB – 4 TB|
|Taxa de transferência de E/S (aproximada)|500 IOPS por vCore com máximo de 7.000 IOPS|5.000 IOPS por núcleo com máximo de 200.000 IOPS|
|Disponibilidade|1 réplica, sem escala de leitura|3 réplica, 1 [escala de leitura](sql-database-read-scale-out.md), HA com redundância de zona|
|Backups|RA-GRS, 7-35 dias (7 dias por padrão)|RA-GRS, 7-35 dias (7 dias por padrão)|
|Em Memória|N/D|Com suporte|
|||

> [!IMPORTANT]
> Se você precisar de menos de um vCore de capacidade de computação, use o modelo de compra baseado em DTU.

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

No modelo de compra baseado em vCore, é possível trocar suas licenças existentes por tarifas com desconto no Banco de Dados SQL usando o [Benefício de Uso Híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Esse benefício do Azure permite que você use as licenças locais do SQL Server para economizar até 30% no Banco de Dados SQL do Azure usando as licenças do SQL Server local com Software Assurance.

![preços](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-of-single-databases-with-geo-replication-links"></a>Migrar dos bancos de dados individuais com links de replicação geográfica

A migração do modelo baseado em DTU para o modelo baseado em vCore é semelhante ao upgrade ou downgrade dos relacionamentos de replicação geográfica entre os bancos de dados Standard e Premium. Ele não requer a replicação geográfica final, mas o usuário deve observar as regras de sequenciamento. Ao atualizar, será necessário primeiro fazer upgrade do banco de dados secundário e, em seguida, upgrade do primário. Ao fazer downgrade, inverta a ordem: primeiro, você deverá fazer downgrade do banco de dados primário e, em seguida, fazer downgrade do secundário. 

Ao usar a replicação geográfica entre dois pools elásticos, recomendamos designar um pool como o primário e o outro como secundário. Nesse caso, os pools elásticos em migração devem usar a mesma orientação.  No entanto, é tecnicamente possível que um pool elástico contenha bancos de dados primários e secundários. Nesse caso, para migrar corretamente, é necessário tratar o pool com a maior utilização como "principal" e seguir as regras de sequenciamento adequadamente.  

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

Somente é possível criar um secundário geográfico usando a mesma camada de serviço que o primário. Para bancos de dados com alta taxa de geração de logs, recomendamos que o secundário seja criado com o mesmo nível de desempenho que o primário. Se você estiver criando um secundário geográfico no pool elástico para um banco de dados primário individual, recomendamos que o pool tenha a configuração `maxVCore` que corresponde ao nível de desempenho do banco de dados primário. Se você estiver criando um secundário geográfico no pool elástico para um primário em outro pool elástico, recomendamos que os pools tenham as mesmas configurações `maxVCore`

## <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Usando a cópia do banco de dados para converter um banco de dados baseado em DTU em um banco de dados baseado em vCore.

É possível copiar qualquer banco de dados com um nível de desempenho baseado em DTU para um banco de dados com um nível de desempenho baseado em vCore sem restrições ou sequenciamento especial, desde que o nível de desempenho de destino dê suporte ao tamanho máximo do banco de dados de origem. Isso ocorre porque a cópia do banco de dados cria um instantâneo de dados a partir do horário de início da operação de cópia e não executa a sincronização de dados entre a origem e o destino. 

## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre níveis de desempenho específicos e opções de tamanho de armazenamento disponíveis para um banco de dados individual, veja [Limites de recursos baseados em vCore do Banco de Dados SQL para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels)
- Para obter detalhes sobre níveis de desempenho específicos e opções de tamanho de armazenamento disponíveis para pools elásticos, veja [Limites de recursos baseados em vCore do Banco de Dados SQL para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).
