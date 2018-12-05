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
ms.date: 11/27/2018
ms.openlocfilehash: 4d71e54beac6e4816d8bcc9097219b2e7b7cabb7
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441852"
---
# <a name="vcore-service-tiers-azure-hybrid-benefit-and-migration"></a>camadas de serviço de vCore, Benefício Híbrido do Azure e migração

O modelo de compra baseado em vCore permite que você dimensione recursos de computação e armazenamento de forma independente, corresponda ao desempenho local e otimize o preço. Também permite escolher a geração de hardware:

- Ger 4 - Até 24 CPUs lógicas baseadas nos processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz, vCore = 1 PP (núcleo físico), 7 GB por núcleo, SSD anexado
- Ger 5 - Até 80 CPUs lógicas baseadas nos processadores Intel E5-2673 v4 (Broadwell) de 2,3 GHz, vCore = 1 LP (hyper-thread), 5.5. GB por núcleo, SSD eNVM rápido

o modelo vCore também permite que você use [o Benefício Híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) Obtenha economia de custo.

> [!NOTE]
> Para obter informações sobre as camadas de serviço baseado em DTU, consulte [camadas de serviço baseado em DTU](sql-database-service-tiers-dtu.md). Para obter informações sobre a diferença entre camadas de serviço com base em DTU e camadas de serviço com base em vCore, confira [modelos de compra do Banco de Dados SQL do Azure](sql-database-service-tiers.md).

## <a name="service-tier-characteristics"></a>Características de camada de serviço

O modelo vCore fornece duas camadas de serviço, Uso Geral e Comercialmente Crítico. As camadas de serviço são diferenciadas por um intervalo de tamanhos de computação, design de alta disponibilidade, isolamento de falhas, tipos de armazenamento e intervalo de E/S. O cliente deve configurar separadamente o período necessário de armazenamento e retenção para backups. Configure separadamente o período necessário de armazenamento e retenção para backups. No portal do Azure, vá para o servidor (não o banco de dados) > Backups gerenciados > Configurar política > Ponto no tempo de restauração da configuração > 7 a 35 dias.

A tabela a seguir ajuda-o a reconhecer as diferenças entre essas duas camadas:

||**Uso geral**|**Comercialmente Crítico**|**Hiperescala (versão prévia)**|
|---|---|---|---|
|Mais adequado para|A maioria das cargas de trabalho comerciais. Oferece opções de armazenamento e computação escalonáveis e equilibradas orientadas a orçamento.|Aplicativos de negócios com altos requisitos de E/S. Oferece maior resiliência a falhas usando várias réplicas isoladas.|Maioria das cargas de trabalho empresariais com requisitos de escala de leitura e armazenamento altamente escalonáveis|
|Computação|Gen4: 1 a 24 vCore<br/>Gen5: 1 a 80 vCore|Gen4: 1 a 24 vCore<br/>Gen5: 1 a 80 vCore|Gen4: 1 a 24 vCore<br/>Gen5: 1 a 80 vCore|
|Memória|Gen4: 7 GB por núcleo<br>Gen5: GB 5.1 por núcleo | Gen4: 7 GB por núcleo<br>Gen5: GB 5.1 por núcleo |Gen4: 7 GB por núcleo<br>Gen5: GB 5.1 por núcleo|
|Armazenamento|Use o [armazenamento remoto premium](../virtual-machines/windows/premium-storage.md):<br/>Banco de dados individual: 5 GB a 4 TB<br/>Instância Gerenciada: 32 GB - 8 TB |Use o armazenamento SSD local:<br/>Banco de dados individual: 5 GB a 1 TB<br/>Instância Gerenciada: 32 GB - 4 TB |Aumento automático flexível, de armazenamento conforme necessário. Compatível com até 100 TB de armazenamento e muito mais. Armazenamento SSD local para o cache do pool de buffers local e o armazenamento de dados local. Armazenamento remoto do Azure como armazenamento de dados de longo prazo final. |
|Taxa de transferência de E/S (aproximada)|Banco de dados individual: 500 IOPS por vCore com máximo de 7000 IOPS</br>Instância Gerenciada: Depende do [tamanho do arquivo](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|5000 IOPS por núcleo com máximo de 200.000 IOPS|TBD|
|Disponibilidade|1 réplica, sem escala de leitura|3 réplicas, 1 [réplica em escala de leitura](sql-database-read-scale-out.md),<br/>HA com redundância de zona|?|
|Backups|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por padrão)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dias (7 dias por padrão)|backup baseado em instantâneo no armazenamento remoto do Azure e as restaurações usam esses instantâneos para a recuperação rápida. Os backups são instantâneos e não afetam o desempenho de E/S da computação. As restaurações são muito rápidas e não têm um tamanho de operação de dados (levando minutos em vez de horas ou dias).|
|Em Memória|Sem suporte|Com suporte|Sem suporte|
|||

> [!NOTE]
> Você pode obter um banco de dados SQL do Azure gratuito na camada de serviço Básico em conjunto com uma conta gratuita do Azure para explorar o Azure. Para obter informações, consulte [Crie um banco de dados de nuvem gerenciado com sua conta gratuita do Azure](https://azure.microsoft.com/free/services/sql-database/).

- Para obter mais informações, consulte [Limites de recursos vCore no Banco de dados individual](sql-database-vcore-resource-limits-single-databases.md) e [Limites de recurso vCore na Instância Gerenciada](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Para obter mais informações sobre as camadas de serviço de uso geral e comercialmente crítico, consulte [Camadas de Serviço de uso geral e Comercialmente Crítico](sql-database-service-tiers-general-purpose-business-critical.md).
- Para obter detalhes sobre a camada de serviço Hyperscale no modelo de compra baseado no vCore, consulte [Camada do serviço de hiperescala](sql-database-service-tier-hyperscale.md).  

> [!IMPORTANT]
> Se você precisar de menos de um vCore de capacidade de computação, use o modelo de compra baseado em DTU.

Consulte [Perguntas frequentes sobre Banco de Dados SQL](sql-database-faq.md) para obter respostas para as perguntas frequentes.

## <a name="azure-hybrid-benefit"></a>Benefício Híbrido do Azure

No modelo de compra baseado em vCore, é possível trocar suas licenças existentes por tarifas com desconto no Banco de Dados SQL usando o [Benefício Híbrido do Azure para SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Esse benefício do Azure permite que você use as licenças locais do SQL Server para economizar até 30% no Banco de Dados SQL do Azure usando as licenças do SQL Server local com Software Assurance.

![preços](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-from-dtu-model-to-vcore-model"></a>Migração do modelo DTU para modelo vCore

### <a name="migration-of-a-database"></a>Migração de um banco de dados

Migrando um banco de dados do modelo de compra baseado em DTU para o modelo de compra baseado em vCore é semelhante ao upgrade ou downgrade entre bancos de dados Standard e Premium no modelo de compra baseado em DTU.

### <a name="migration-of-databases-with-geo-replication-links"></a>Migração de bancos de dados com links de replicação geográfica

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

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Usando a cópia do banco de dados para converter um banco de dados baseado em DTU em um banco de dados baseado em vCore

É possível copiar qualquer banco de dados com um tamanho de computação baseado em DTU para um banco de dados com um tamanho de computação baseado em vCore sem restrições ou sequenciamento especial, desde que o tamanho de computação de destino dê suporte ao tamanho máximo do banco de dados de origem. O banco de dados cria um instantâneo de dados a partir do horário de início da operação de cópia e não executa a sincronização de dados entre a origem e o destino.

## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre os tamanhos de computação específicos e as opções de tamanho de armazenamento disponíveis para bancos de dados individuais, confira [Limites de recursos baseados em vCore do Banco de Dados SQL para bancos de dados individuais](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- Para obter detalhes sobre tamanhos de computação específicos e opções de tamanho de armazenamento disponíveis para pools elásticos, veja [Limites de recursos baseados em vCore do Banco de Dados SQL para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
