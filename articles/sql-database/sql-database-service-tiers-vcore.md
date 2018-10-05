---
title: Serviço do Banco de Dados SQL do Azure | Microsoft Docs
description: O modelo de compra baseado em vCore permite que você dimensione recursos de computação e armazenamento de forma independente, corresponda ao desempenho local e otimize o preço.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 09/26/2018
ms.openlocfilehash: 3fb1357b7a70579fa527a896d5bd359749b10ad6
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407648"
---
# <a name="choosing-a-vcore-service-tier-compute-memory-storage-and-io-resources"></a>Escolher uma camada de serviço, recursos de E/S, armazenamento, memória e computação de vCore

O modelo de compra baseado em vCore permite que você dimensione recursos de computação e armazenamento de forma independente, corresponda ao desempenho local e otimize o preço. Também permite escolher a geração de hardware:
- Ger 4 - Até 24 CPUs lógicas baseadas nos processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz, vCore = 1 PP (núcleo físico), 7 GB por núcleo, SSD anexado
- Ger 5 - Até 80 CPUs lógicas baseadas nos processadores Intel E5-2673 v4 (Broadwell) de 2,3 GHz, vCore = 1 LP (hyper-thread), 5.5. GB por núcleo, SSD eNVM rápido

O modelo vCore também permite que você use o [Benefício de Uso Híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) para obter economia de custos.

## <a name="service-tier-characteristics"></a>Características de camada de serviço

O modelo vCore fornece duas camadas de serviço, Uso Geral e Comercialmente Crítico. As camadas de serviço são diferenciadas por um intervalo de tamanhos de computação, design de alta disponibilidade, isolamento de falhas, tipos de armazenamento e intervalo de E/S. O cliente deve configurar separadamente o período necessário de armazenamento e retenção para backups.

A tabela a seguir ajuda-o a reconhecer as diferenças entre essas duas camadas:

||**Uso geral**|**Comercialmente Crítico**|**Hiperescala (versão prévia)**|
|---|---|---|---|
|Mais adequado para|A maioria das cargas de trabalho comerciais. Oferece opções de armazenamento e computação escalonáveis e equilibradas orientadas a orçamento.|Aplicativos de negócios com altos requisitos de E/S. Oferece maior resiliência a falhas usando várias réplicas isoladas.|Maioria das cargas de trabalho empresariais com requisitos de escala de leitura e armazenamento altamente escalonáveis|
|Computação|Gen4: 1 a 24 vCore<br/>Gen5: 1 a 80 vCore|Gen4: 1 a 24 vCore<br/>Gen5: 1 a 80 vCore|Gen4: 1 a 24 vCore<br/>Gen5: 1 a 80 vCore|
|Memória|Gen4: 7 GB por núcleo<br>Gen5: 5,5 GB por núcleo | Gen4: 7 GB por núcleo<br>Gen5: 5,5 GB por núcleo |Gen4: 7 GB por núcleo<br>Gen5: 5,5 GB por núcleo|
|Armazenamento|[Armazenamento remoto Premium](../virtual-machines/windows/premium-storage.md),<br/>Banco de dados individual: 5 GB a 4 TB<br/>Instância Gerenciada: 32 GB - 8 TB |Armazenamento SSD local,<br/>Banco de dados individual: 5 GB a 4 TB<br/>Instância Gerenciada: 32 GB - 4 TB |Flexível, aumento automático de armazenamento conforme necessário. Compatível com até 100 TB de armazenamento e muito mais. Armazenamento SSD local para o cache do pool de buffers local e o armazenamento de dados local. Armazenamento remoto do Azure como armazenamento de dados de longo prazo final. |
|Taxa de transferência de E/S (aproximada)|Banco de dados individual: 500 IOPS por vCore com máximo de 7000 IOPS</br>Instância Gerenciada: Depende do [tamanho do arquivo](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|5000 IOPS por núcleo com máximo de 200.000 IOPS|TBD|
|Disponibilidade|1 réplica, sem escala de leitura|3 réplicas, 1 [réplica em escala de leitura](sql-database-read-scale-out.md),<br/>HA com redundância de zona|?|
|Backups|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por padrão)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por padrão)|backup baseado em instantâneo no armazenamento remoto do Azure e as restaurações usam esses instantâneos para a recuperação rápida. Os backups são instantâneos e não afetam o desempenho de E/S da computação. As restaurações são muito rápidas e não do tamanho das operações de dados (em minutos não horas ou dias).|
|Em Memória|Sem suporte|Com suporte|Sem suporte|
|||

Para obter mais informações, consulte [Limites de recursos vCore no Banco de dados individual](sql-database-vcore-resource-limits-single-databases.md) e [Limites de recurso vCore na Instância Gerenciada](sql-database-managed-instance.md#vcore-based-purchasing-model). 

> [!IMPORTANT]
> Se você precisar de menos de um vCore de capacidade de computação, use o modelo de compra baseado em DTU.

Consulte [Perguntas frequentes sobre Banco de Dados SQL](sql-database-faq.md) para obter respostas para as perguntas frequentes. 

## <a name="storage-considerations"></a>Considerações de armazenamento

### <a name="general-purpose-and-business-critical-service-tiers"></a>Camadas de serviço Comercialmente Crítica e de Uso Geral
Considere o seguinte:
- O armazenamento alocado é usado por arquivos de arquivos de dados (MDF) e arquivos de log (LDF).
- Cada tamanho de computação do banco de dados individual dá suporte a um tamanho máximo de banco de dados, com um tamanho máximo padrão de 32 GB.
- Quando você configura o tamanho desejado do banco de dados individual (tamanho de MDF), 30% do armazenamento adicional é automaticamente adicionado para dar suporte a LDF
- O tamanho do armazenamento na Instância Gerenciada deve ser especificado em múltiplos de 32 GB.
- É possível selecionar qualquer tamanho de banco de dados individual entre 10 GB e o máximo compatível
 - Para armazenamento Standard, aumente ou diminua o tamanho em incrementos de 10 GB
 - Para armazenamento Premium, aumente ou diminua o tamanho em incrementos de 250 GB
- Na camada de serviço de Uso Geral, `tempdb` usa um SSD anexado e esse custo de armazenamento é incluído no preço do vCore.
- Na camada de serviço Comercialmente Crítico, `tempdb` compartilha o SSD anexado com os arquivos MDF e LDF e o custo de armazenamento tempDB é incluído no preço vCore.

> [!IMPORTANT]
> Você é cobrado pelo armazenamento total alocado para MDF e LDF.

Para monitorar o tamanho total atual do MDF e do LDF, use [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para monitorar o tamanho atual dos arquivos MDF e LDF individuais, use [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL](sql-database-file-space-management.md).

### <a name="hyperscale-service-tier-preview"></a>Camada de serviço em hiperescala (versão prévia)

O armazenamento é gerenciado automaticamente para o banco de dados em hiperescala. O armazenamento aumenta conforme necessário. O armazenamento de "Log Infinito" nos SSDs de armazenamento Premium rápido do Azure sem a necessidade de truncamento de log frequente.

## <a name="backups-and-storage"></a>Backups e armazenamento

### <a name="general-purpose-and-business-critical-service-tiers"></a>Camadas de serviço Comercialmente Crítica e de Uso Geral

O armazenamento para backups de banco de dados é alocado para dar suporte a recursos de PITR (Recuperação Pontual) e [LTR (Retenção de Longo Prazo)](sql-database-long-term-retention.md) do Banco de Dados SQL. Esse armazenamento é alocado separadamente para cada banco de dados e faturado como duas cobranças separadas por banco de dados. 

- **PITR**: backups de banco de dados individuais são copiados para [armazenamento RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md) automaticamente. O tamanho do armazenamento aumenta dinamicamente conforme os novos backups são criados.  O armazenamento é usado por backups completos semanais, backups diferenciais diários e backups de log de transações copiados a cada 5 minutos. O consumo de armazenamento depende da taxa de alteração do banco de dados e do período de retenção. É possível configurar um período de retenção separado para cada banco de dados entre 7 e 35 dias. Um valor de armazenamento mínimo igual a 1x de tamanho de dados é fornecido sem nenhum custo adicional. Para a maioria dos bancos de dados, esse valor é suficiente para armazenar 7 dias de backups.
- **LTR**: o Banco de Dados SQL oferece a opção de configurar a retenção em longo prazo de backups completos por até 10 anos. Se a política de LTR estiver habilitada, esses backups serão armazenados no armazenamento RA-GRS automaticamente, mas você poderá controlar com que frequência os backups serão copiados. Para atender a diferentes requisitos de conformidade, é possível selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. Essa configuração definirá quanto armazenamento será usado para os backups de LTR. É possível usar a calculadora de preços de LTR para estimar o custo do armazenamento LTR. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

### <a name="hyperscale-service-tier-preview"></a>Camada de serviço em hiperescala (versão prévia)

Na camada de serviço em hiperescala, os backups são baseados em instantâneo e são armazenados no armazenamento remoto do Azure. As restaurações usam esses instantâneos para recuperação rápida. Os backups são instantâneos e não afetam o desempenho de E/S da computação. As restaurações são muito rápidas e não do tamanho das operações de dados (em minutos não horas ou dias).

## <a name="azure-hybrid-use-benefit"></a>Benefício de Uso do Azure Híbrido

No modelo de compra baseado em vCore, é possível trocar suas licenças existentes por tarifas com desconto no Banco de Dados SQL usando o [Benefício de Uso Híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Esse benefício do Azure permite que você use as licenças locais do SQL Server para economizar até 30% no Banco de Dados SQL do Azure usando as licenças do SQL Server local com Software Assurance.

![preços](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-from-dtu-model-to-vcore-model"></a>Migração do modelo DTU para modelo vCore

### <a name="migration-of-single-databases-with-geo-replication-links"></a>Migrar dos bancos de dados individuais com links de replicação geográfica

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

### <a name="migration-of-failover-groups"></a>Migração de grupos de failover 

A migração de grupos de failover com vários bancos de dados requer a migração individual dos bancos de dados primário e secundário. Durante esse processo, as mesmas considerações e regras de sequenciamento se aplicam. Depois que os bancos de dados forem convertidos para o modelo baseado em vCore, o grupo de failover permanecerá em vigor com as mesmas configurações de política. 

### <a name="creation-of-a-geo-replication-secondary"></a>Criação de um secundário para replicação geográfica

Somente é possível criar um secundário geográfico usando a mesma camada de serviço que o primário. Para bancos de dados com alta taxa de geração de logs, recomendamos que o secundário seja criado com o mesmo tamanho de computação que o primário. Se você estiver criando um secundário geográfico no pool elástico para um banco de dados primário individual, recomendamos que o pool tenha a configuração `maxVCore` que corresponde ao tamanho de computação do banco de dados primário. Se você estiver criando um secundário geográfico no pool elástico para um primário em outro pool elástico, recomendamos que os pools tenham as mesmas configurações `maxVCore`

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Usando a cópia do banco de dados para converter um banco de dados baseado em DTU em um banco de dados baseado em vCore.

É possível copiar qualquer banco de dados com um tamanho de computação baseado em DTU para um banco de dados com um tamanho de computação baseado em vCore sem restrições ou sequenciamento especial, desde que o tamanho de computação de destino dê suporte ao tamanho máximo do banco de dados de origem. Isso ocorre porque a cópia do banco de dados cria um instantâneo de dados a partir do horário de início da operação de cópia e não executa a sincronização de dados entre a origem e o destino. 

## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre os tamanhos de computação específicos e as opções de tamanho de armazenamento disponíveis para bancos de dados individuais, confira [Limites de recursos baseados em vCore do Banco de Dados SQL para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)
- Para obter detalhes sobre tamanhos de computação específicos e opções de tamanho de armazenamento disponíveis para pools elásticos, veja [Limites de recursos baseados em vCore do Banco de Dados SQL para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
