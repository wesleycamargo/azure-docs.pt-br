---
title: Backups do Banco de Dados SQL do Azure automáticos e com redundância geográfica | Microsoft Docs
description: O Banco de dados SQL cria automaticamente um backup de banco de dados local a cada poucos minutos e usa o armazenamento com redundância geográfica de acesso de leitura do Azure para redundância geográfica.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/12/2019
ms.openlocfilehash: f0cff30f246bfeec528f440b507da9248ebbea9f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678591"
---
# <a name="automated-backups"></a>Backups automatizados

Banco de dados SQL automaticamente cria backups de banco de dados que são mantidos entre 7 e 35 dias e usa o armazenamento com redundância geográfica de acesso de leitura do Azure (RA-GRS) para garantir que eles sejam preservados, mesmo se o data center não está disponível. Esses backups são criados automaticamente e sem nenhum custo adicional. Você não precisa fazer nada para que eles ocorram e você pode [Alterar o período de retenção de backup](#how-to-change-the-pitr-backup-retention-period). Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Se as regras de segurança exigem que os backups fiquem disponíveis por um longo período de tempo (até 10 anos), configure uma [retenção de longo prazo](sql-database-long-term-retention.md).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>O que é um backup de banco de dados SQL

O Banco de Dados SQL usa tecnologia SQL Server para criar backups [completos](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server), [diferenciais](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) e de [log de transações](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) para finalidades de PITR ( Recuperação Pontual). Os backups de log de transações geralmente ocorrem a cada 5 a 10 minutos e os backups diferenciais geralmente ocorrem a cada 12 horas, com a frequência baseada no tamanho da computação e na quantidade de atividade do banco de dados. Os backups completos, diferenciais e de log de transação permitem restaurar um banco de dados em um momento específico para o mesmo servidor que hospeda o banco de dados. Os backups são armazenados em blobs de armazenamento RA-GRS que são replicados para um [data center emparelhado](../best-practices-availability-paired-regions.md) para proteção contra uma interrupção do data center. Quando você restaura um banco de dados, o serviço descobre quais backups completos, diferenciais e de log de transações precisam ser restaurados.

Use esses backups para:

- Restaurar um banco de dados para um ponto no tempo dentro do período de retenção. Essa operação criará um novo banco de dados no mesmo servidor do banco de dados original.
- Restaure um banco de dados excluído para a hora em que ele foi excluído ou em qualquer momento dentro do período de retenção. O banco de dados excluído pode ser restaurado apenas no mesmo servidor em que o banco de dados original foi criado.
- Restaure um banco de dados para outra região geográfica. A restauração geográfica permite a recuperação de um desastre geográfico quando você não consegue acessar o servidor nem o banco de dados. Ele cria um novo banco de dados em qualquer servidor existente do mundo.
- Se um banco de dados tiver sido configurado com uma política de LTR (retenção de longo prazo), restaure-o do backup de longo prazo específico. A LTR permite restaurar uma versão antiga do banco de dados para atender a uma solicitação de conformidade ou para executar uma versão antiga do aplicativo. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).
- Para executar uma restauração, consulte [Restaurar um banco de dados de backups](sql-database-recovery-using-backups.md).

> [!NOTE]
> No armazenamento do Azure, o termo *replicação* refere-se a copiar arquivos de uma localização para outra. *Replicação de banco de dados* do SQL significa manter vários bancos de dados secundários sincronizados com um banco de dados primário.

## <a name="how-long-are-backups-kept"></a>Quanto tempo são backups mantidos

Cada Banco de Dados SQL do Microsoft Azure tem um período de retenção de backup padrão entre 7 e 35 dias que depende de modelo de compra e camada de serviço. Você pode atualizar o período de retenção de backup para um banco de dados no servidor de banco de dados SQL. Para obter mais informações, veja [Alterar o período de retenção de backup](#how-to-change-the-pitr-backup-retention-period).

Se você excluir um banco de dados, o Banco de Dados SQL manterá os backups da mesma maneira que em um banco de dados online. Por exemplo, se você excluir um banco de dados Básico que tenha um período de retenção de sete dias, um backup de quatro dias será salvo por mais três dias.

Se você precisar manter os backups por mais tempo que o período máximo de retenção, modifique as propriedades de backup para adicionar um ou mais períodos de retenção de longo prazo para o banco de dados. Para obter mais informações, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).

> [!IMPORTANT]
> Se você excluir o SQL Server do Azure que hospeda os bancos de dados SQL, todos os pools elásticos e bancos de dados que pertencem ao servidor também serão excluídos e não poderão ser recuperados. Você não pode restaurar um servidor excluído. Mas, se você tiver configurado a retenção de longo prazo, os backups dos bancos de dados com LTR não serão excluídos e esses bancos de dados poderão ser restaurados.

### <a name="default-backup-retention-period"></a>Período de retenção de backup padrão

#### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

O período de retenção padrão para um banco de dados criado usando o modelo de compra baseado em DTU depende da camada de serviço:

- A camada de serviço Básica é de uma semana.
- A camada de serviço Standard é de cinco semanas.
- A camada de serviço Premium é de cinco semanas.

#### <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

Se estiver usando o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md), o período de retenção de backup padrão será de 7 dias (para bancos de dados individuais, pools de bancos de dados e bancos de dados de instâncias). Para todos os bancos de dados SQL do Azure (individuais, pool de bancos de dados e bancos de dados de instância, é possível [alterar o período de retenção de backup para até 35 dias](#how-to-change-the-pitr-backup-retention-period).

> [!WARNING]
> Se você reduzir o período de retenção atual, todos os backups existentes mais antigos que o novo período de retenção não estão mais disponíveis. Se você aumentar o período de retenção atual, o Banco de Dados SQL manterá os backups existentes até que o período de retenção mais longo seja atingido.

## <a name="how-often-do-backups-happen"></a>A frequência com que os backups ocorrem

### <a name="backups-for-point-in-time-restore"></a>Backups para a Recuperação Pontual

O Banco de Dados SQL permite o autoatendimento para PITR (Recuperação Pontual) ao criar automaticamente o backup completo, backups diferenciais e backups de log de transações. Os backups completos de banco de dados são criados semanalmente, os backups diferenciais de banco de dados geralmente são criados a cada 12 horas e os backups de log de transações geralmente são criados a cada 5 a 10 minutos, com a frequência baseada no tamanho da computação e na quantidade de atividade do banco de dados. O primeiro backup completo é agendado imediatamente após a criação de um banco de dados. Normalmente ele é concluído em 30 minutos, mas pode levar mais tempo quando o banco de dados tem um tamanho significativo. Por exemplo, o backup inicial pode levar mais tempo para um banco de dados restaurado ou uma cópia do banco de dados. Após o primeiro backup completo, todos os outros backups são agendados automaticamente e gerenciados de forma silenciosa em segundo plano. O tempo exato de todos os backups de banco de dados é determinado pelo serviço do Banco de Dados SQL, pois ele equilibra a carga de trabalho geral do sistema. Você não pode alterar ou desabilitar os trabalhos de backup. 

Os backups de PITR têm redundância geográfica e são protegidos pela [Replicação inter-regional do Armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Para obter mais informações, consulte [Restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="backups-for-long-term-retention"></a>Backups de retenção de longo prazo

Bancos de dados individuais e em pool oferecem a opção de configurar LTR (retenção de longo prazo) de backups completos por até 10 anos no Armazenamento de Blobs do Azure. Quando a política de LTR está habilitada, os backups completos semanais são copiados automaticamente para um contêiner de armazenamento de RA-GRS diferente. Para atender a diferentes requisitos de conformidade, é possível selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuais. O consumo do armazenamento depende da frequência selecionada para os backups e dos períodos de retenção. Você pode usar a [Calculadora de preços de LTR](https://azure.microsoft.com/pricing/calculator/?service=sql-database) para estimar o custo do armazenamento de LTR.

Como o PITR, os backups de LTR têm redundância geográfica e são protegidos pela [Replicação inter-regional do Armazenamento do Azure](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage).

Para obter mais informações, confira [Retenção de backup de longo prazo](sql-database-long-term-retention.md).

## <a name="storage-costs"></a>Custos de armazenamento
Sete dias de backups automatizados dos bancos de dados são copiados para o armazenamento de blobs RA-GRS Standard por padrão. O armazenamento é usado por backups completos semanais, backups diferenciais diários e backups de log de transações copiados a cada 5 minutos. O tamanho do log de transações depende da taxa de alteração do banco de dados. Uma quantidade de armazenamento mínima igual a 100% do tamanho do banco de dados é fornecida sem encargos extras. O consumo adicional de armazenamento de backup será cobrado em GB/mês.

Para saber mais sobre preços de armazenamento, confira a página de [preços](https://azure.microsoft.com/pricing/details/sql-database/single/). 

## <a name="are-backups-encrypted"></a>Os backups são criptografados

Quando o banco de dados é criptografado com TDE, os backups são criptografados automaticamente em repouso, incluindo os backups de LTR. Quando a TDE está habilitada para um banco de dados SQL do Azure, os backups também são criptografados. Todos os novos bancos de dados SQL do Azure são configurados com TDE habilitada por padrão. Para obter mais informações sobre a TDE, confira [Transparent Data Encryption com o Banco de Dados SQL do Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Como a Microsoft garante a integridade do backup

Continuamente, a equipe de engenharia do Banco de Dados SQL do Azure testa automaticamente a restauração de backups de banco de dados automatizados entre o serviço. Após a restauração, os bancos de dados também recebem verificações de integridade usando DBCC CHECKDB. Os problemas encontrados durante a verificação de integridade resultarão em um alerta para a equipe de engenharia. Para obter mais informações sobre a integridade dos dados no Banco de Dados SQL do Azure, confira [Integridade dos dados no Banco de Dados SQL do Azure](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/).

## <a name="how-do-automated-backups-impact-compliance"></a>Como os backups automatizados afetam a conformidade

Quando você migra o banco de dados de uma camada de serviço baseada em DTU, com a retenção de PITR padrão de 35 dias, para uma camada de serviço baseada em vCore, a retenção de PITR é preservada para garantir que a política de recuperação de dados do aplicativo não seja comprometida. Se a retenção padrão não atender aos seus requisitos de conformidade, você poderá alterar o período de retenção de PITR usando o PowerShell ou a API REST. Veja [Alterar o período de retenção de backup](#how-to-change-the-pitr-backup-retention-period) para obter mais detalhes.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>Como alterar o período de retenção de backup de PITR

Você pode alterar o período de retenção de backup do PITR padrão usando o portal do Azure, PowerShell ou a API REST. Os valores com suporte são: 7, 14, 21, 28 ou 35 dias. Os exemplos a seguir ilustram como alterar a retenção de PITR para 28 dias.

> [!NOTE]
> Essas APIs afetarão somente o período de retenção de PITR. Se você tiver configurado a LTR para o banco de dados, ela não será afetada. Para obter mais informações sobre como alterar o(s) período(s) de retenção de LTR, confira [Retenção de longo prazo](sql-database-long-term-retention.md).

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Alterar o período de retenção de backup de PITR usando o portal do Azure

Para alterar o período de retenção de backup do PITR usando o portal do Azure, navegue até o objeto de servidor cujo período de retenção que você deseja alterar dentro do portal e, em seguida, selecione a opção apropriada com base em qual objeto de servidor você está modificando.

#### <a name="change-pitr-for-a-sql-database-server"></a>Alteração de PITR para um servidor do Banco de Dados SQL

![Alterar PITR no portal do Azure](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="change-pitr-for-a-managed-instance"></a>Alterar PITR para uma Instância Gerenciada

![Alterar PITR no portal do Azure](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>Alterar o período de retenção de backup de PITR usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é compatível com o banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>Alterar o período de retenção de PITR usando a API REST

#### <a name="sample-request"></a>Solicitação de Exemplo

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Corpo da solicitação

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Exemplo de Resposta

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

Para obter mais informações, confira [API REST de retenção de backup](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies).

## <a name="next-steps"></a>Próximos passos

- Os backups de banco de dados são uma parte essencial de qualquer estratégia de recuperação de desastre e continuidade dos negócios, porque eles protegem seus dados contra exclusão ou corrupção acidentais. Para saber mais sobre as outras soluções de continuidade dos negócios do Banco de Dados SQL do Azure, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
- Para restaurar para um determinado ponto no tempo usando o Portal do Azure, consulte [Restaurar um banco de dados para um ponto no tempo usando o Portal do Azure](sql-database-recovery-using-backups.md).
- Para restaurar para um determinado ponto no tempo usando o PowerShell, consulte [Restaurar um banco de dados para um ponto no tempo usando o PowerShell](scripts/sql-database-restore-database-powershell.md).
- Para configurar, gerenciar e restaurar a retenção de longo prazo de backups automatizados no Armazenamento de Blobs do Azure usando o portal do Azure, consulte [Gerenciar retenção de backup de longo prazo usando o portal do Azure](sql-database-long-term-backup-retention-configure.md).
- Para configurar, gerenciar e restaurar da retenção de longo prazo de backups automatizados em armazenamento de BLOBs do Azure usando o PowerShell, consulte [gerenciar retenção de longo prazo usando o PowerShell](sql-database-long-term-backup-retention-configure.md).
