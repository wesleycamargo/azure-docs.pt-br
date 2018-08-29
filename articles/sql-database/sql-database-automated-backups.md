---
title: Backups do Banco de Dados SQL do Azure automáticos e com redundância geográfica | Microsoft Docs
description: O Banco de dados SQL cria automaticamente um backup de banco de dados local a cada poucos minutos e usa o armazenamento com redundância geográfica de acesso de leitura do Azure para redundância geográfica.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.workload: Active
ms.date: 07/25/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: ac548d90d5a5ed931dc199b6fed52c7cd8f25239
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42141865"
---
# <a name="learn-about-automatic-sql-database-backups"></a>Saiba mais sobre backups automáticos de Banco de Dados SQL

O Banco de Dados SQL cria automaticamente backups do banco de dados e usa o RA-GRS (armazenamento com redundância geográfica de acesso de leitura) do Azure para fornecer redundância geográfica. Esses backups são criados automaticamente e sem nenhum custo adicional. Você não precisa fazer nada para que isso ocorra. Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Se as regras de segurança exigem que os backups fiquem disponíveis por um longo período de tempo, configure uma política de retenção de backup de longo prazo. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>O que é um backup de Banco de Dados SQL?

O Banco de Dados SQL usa tecnologia SQL Server para criar backups [completos](https://msdn.microsoft.com/library/ms186289.aspx), [diferenciais](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) e de [log de transações](https://msdn.microsoft.com/library/ms191429.aspx) para finalidades de PITR ( Recuperação Pontual). Os backups de log de transações geralmente ocorrem a cada 5 a 10 minutos e os backups diferenciais geralmente ocorrem a cada 12 horas, com a frequência baseada no nível de desempenho e na quantidade de atividade do banco de dados. Os backups de log de transações, com os backups completos e diferenciais, permitem restaurar um banco de dados para um ponto no tempo específico e para o mesmo servidor que hospeda o banco de dados. Os backups de banco de dados completos e diferenciais também são replicados para um [data center emparelhado](../best-practices-availability-paired-regions.md) para proteção contra uma interrupção do data center. Quando você restaura um banco de dados, o serviço descobre quais backups completos, diferenciais e de log de transações precisam ser restaurados.


Use esses backups para:

* Restaurar um banco de dados para um ponto no tempo dentro do período de retenção. Essa operação criará um novo banco de dados no mesmo servidor do banco de dados original.
* Restaure um banco de dados excluído para a hora em que ele foi excluído ou em qualquer momento dentro do período de retenção. O banco de dados excluído pode ser restaurado apenas no mesmo servidor em que o banco de dados original foi criado.
* Restaure um banco de dados para outra região geográfica. Isso permite a recuperação de um desastre geográfico quando você não consegue acessar o servidor nem o banco de dados. Ele cria um novo banco de dados em qualquer servidor existente do mundo. 
* Se um banco de dados tiver sido configurado com uma política de LTR (retenção de longo prazo), restaure-o do backup de longo prazo específico. Isso permite restaurar uma versão antiga do banco de dados para atender a uma solicitação de conformidade ou para executar uma versão antiga do aplicativo. Consulte [Retenção de longo prazo](sql-database-long-term-retention.md).
* Para executar uma restauração, consulte [Restaurar um banco de dados de backups](sql-database-recovery-using-backups.md).

> [!NOTE]
> No armazenamento do Azure, o termo *replicação* refere-se a copiar arquivos de uma localização para outra. *Replicação de banco de dados* do SQL significa manter vários bancos de dados secundários sincronizados com um banco de dados primário. 
> 

## <a name="how-long-are-backups-kept"></a>Por quanto tempo os backups são mantidos?
Cada backup de Banco de Dados SQL tem um período de retenção padrão baseado na camada de serviço do banco de dados e difere entre o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) e o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md). Você pode atualizar o período de retenção de backup de um banco de dados. Veja [Alterar o período de retenção de backup](#how-to-change-backup-retention-period) para obter mais detalhes.

Se você excluir um banco de dados, o Banco de Dados SQL manterá os backups da mesma maneira que em um banco de dados online. Por exemplo, se você excluir um banco de dados Básico que tenha um período de retenção de sete dias, um backup de quatro dias será salvo por mais três dias.

Se você precisar manter os backups por mais tempo que o período máximo de retenção de PITR, modifique as propriedades de backup para adicionar um ou mais períodos retenção de longo prazo para o banco de dados. Veja [Retenção de backup de longo prazo](sql-database-long-term-retention.md) para obter mais detalhes.

> [!IMPORTANT]
> Se você excluir o SQL Server do Azure que hospeda os bancos de dados SQL, todos os pools elásticos e bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído. Mas, se você tiver configurado a retenção de longo prazo, os backups dos bancos de dados com LTR não serão excluídos e esses bancos de dados poderão ser restaurados.

### <a name="pitr-retention-period"></a>Período de retenção de PITR
O período de retenção padrão para um banco de dados criado usando o modelo de compra baseado em DTU depende da camada de serviço:

* A camada de serviço Básica é de uma semana.
* A camada de serviço Standard é de cinco semanas.
* A camada de serviço Premium é de cinco semanas.

Se você estiver usando o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md), a retenção de backups será configurável em até 35 dias. 

Se você reduzir o período de retenção de PITR atual, os backups existentes mais antigos que o novo período de retenção não estarão mais disponíveis. 

Se você aumentar o período de retenção de PITR atual, o Banco de Dados SQL manterá os backups existentes até que o período de retenção mais longo seja atingido.

## <a name="how-often-do-backups-happen"></a>Com que frequência os backups ocorrem?
### <a name="backups-for-point-in-time-restore"></a>Backups para a Recuperação Pontual
O Banco de Dados SQL permite o autoatendimento para PITR (Recuperação Pontual) ao criar automaticamente o backup completo, backups diferenciais e backups de log de transações. Backups completos de banco de dados são criados semanalmente, backups diferenciais de banco de dados geralmente são criados a cada 12 horas e backups de log de transações geralmente são criados a cada 5 a 10 minutos, com a frequência baseada no nível de desempenho e na quantidade de atividade do banco de dados. O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Normalmente ele é concluído em 30 minutos, mas pode levar mais tempo quando o banco de dados tem um tamanho significativo. Por exemplo, o backup inicial pode levar mais tempo para um banco de dados restaurado ou uma cópia do banco de dados. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato de todos os backups de banco de dados é determinado pelo serviço do Banco de Dados SQL, pois ele equilibra a carga de trabalho geral do sistema.

Os backups de PITR têm redundância geográfica e são protegidos pela [Replicação inter-regional do Armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Para obter mais informações, consulte [Restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Backups de retenção de longo prazo
O Banco de Dados SQL oferece a opção de configurar LTR (retenção de longo prazo) de backups completos por até 10 anos no Armazenamento de Blobs do Azure. Quando a política de LTR está habilitada, os backups completos semanais são copiados automaticamente para um contêiner de armazenamento de RA-GRS diferente. Para atender a diferentes requisitos de conformidade, é possível selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. O consumo do armazenamento depende da frequência selecionada para os backups e dos períodos de retenção. Você pode usar a [Calculadora de preços de LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para estimar o custo do armazenamento de LTR. 

Como o PITR, os backups de LTR têm redundância geográfica e são protegidos pela [Replicação inter-regional do Armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Para obter mais informações, confira [Retenção de backup de longo prazo](sql-database-long-term-retention.md).

## <a name="are-backups-encrypted"></a>Os backups são criptografados?

Quando o banco de dados é criptografado com TDE, os backups são criptografados automaticamente em repouso, incluindo os backups de LTR. Quando a TDE está habilitada para um banco de dados SQL do Azure, os backups também são criptografados. Todos os novos bancos de dados SQL do Azure são configurados com TDE habilitada por padrão. Para obter mais informações sobre a TDE, confira [Transparent Data Encryption com o Banco de Dados SQL do Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-do-automated-backups-impact-my-compliance"></a>Como os backups automatizados afetam minha conformidade?

Quando você migra o banco de dados de uma camada de serviço baseada em DTU, com a retenção de PITR padrão de 35 dias, para uma camada de serviço baseada em vCore, a retenção de PITR é preservada para garantir que a política de recuperação de dados do aplicativo não seja comprometida. Se a retenção padrão não atender aos seus requisitos de conformidade, você poderá alterar o período de retenção de PITR usando o PowerShell ou a API REST. Veja [Alterar o período de retenção de backup](#how-to-change-backup-retention-period) para obter mais detalhes.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-backup-retention-period"></a>Como alterar o período de retenção de backup
Você pode alterar a retenção padrão usando a API REST ou o PowerShell. Os valores com suporte são: 7, 14, 21, 28 ou 35 dias. Os exemplos a seguir ilustram como alterar a retenção de PITR para 28 dias. 

> [!NOTE]
> As APIs afetarão somente o período de retenção de PITR. Se você tiver configurado a LTR para o banco de dados, ela não será afetada. Veja [Retenção de backup de longo prazo](sql-database-long-term-retention.md) para obter detalhes de como alterar os períodos de retenção de LTR.

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Alterar o período de retenção de backup de PITR usando o PowerShell
```powershell
Set-AzureRmSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```
> [!IMPORTANT]
> Essa API está incluída no módulo para PowerShell AzureRM.Sql começando com a versão [4.7.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.7.0-preview). 

### <a name="change-pitr-retention-period-using-rest-api"></a>Alterar o período de retenção de PITR usando a API REST
**Solicitação de exemplo**
```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```
**Corpo da solicitação**
```json
{
  "properties":{  
      "retentionDays":28
   }
}
```
**Resposta de exemplo**

Código de status: 200
```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```
Veja [API REST de retenção de backup](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies) para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas

- Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL do Azure, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
- Para restaurar para um determinado ponto no tempo usando o Portal do Azure, consulte [Restaurar um banco de dados para um ponto no tempo usando o Portal do Azure](sql-database-recovery-using-backups.md).
- Para restaurar para um determinado ponto no tempo usando o PowerShell, consulte [Restaurar um banco de dados para um ponto no tempo usando o PowerShell](scripts/sql-database-restore-database-powershell.md).
- Para configurar, gerenciar e restaurar a retenção de longo prazo de backups automatizados no Armazenamento de Blobs do Azure usando o portal do Azure, consulte [Gerenciar retenção de backup de longo prazo usando o portal do Azure](sql-database-long-term-backup-retention-configure.md).
- Para configurar, gerenciar e restaurar a retenção de longo prazo de backups automatizados no Armazenamento de Blobs do Azure usando o PowerShell, consulte [Gerenciar retenção de backup de longo prazo usando o PowerShell](sql-database-long-term-backup-retention-configure.md).
