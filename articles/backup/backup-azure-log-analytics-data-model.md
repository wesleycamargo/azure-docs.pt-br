---
title: Modelo de dados do Log Analytics para o Backup do Azure
description: Este artigo aborda detalhes de modelo de dados do Log Analytics para dados de Backup do Azure.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f9cdb11bad5d4aa94fdc083a0fc7dc6a2c5787cd
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635145"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Modelo de dados do Log Analytics para dados de Backup do Azure
Use o modelo de dados do Log Analytics para criar relatórios. Com o modelo de dados, você pode criar painéis e consultas personalizadas ou personalizar dados de Backup do Azure, de maneira que desejar.

## <a name="using-azure-backup-data-model"></a>Usando o modelo de dados de Backup do Azure
Você pode usar os seguintes campos fornecidos como parte do modelo de dados para criar visuais, consultas personalizadas e painel de acordo com seus requisitos.

### <a name="alert"></a>Alerta
Essa tabela fornece detalhes sobre campos relacionados ao alerta.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| AlertUniqueId_s |Texto |Identificador exclusivo do alerta gerado |
| AlertType_s |Texto |Tipo de alerta, por exemplo, Backup |
| AlertStatus_s |Texto |Status do alerta, por exemplo, Ativo |
| AlertOccurenceDateTime_s |Data/hora |Data e hora em que o alerta foi criado |
| AlertSeverity_s |Texto |Gravidade do alerta, por exemplo, Crítico |
| EventName_s |Texto |O nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador exclusivo do item backup associado ao alerta |
| SchemaVersion_s |Texto |Versão atual do esquema, por exemplo, **V1** |
| State_s |Texto |Estado atual do objeto de alerta, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de provedor para executar o backup, por exemplo, IaaSVM, FileFolder ao qual esse alerta pertence |
| OperationName |Texto |Nome da operação atual, por exemplo, alerta |
| Categoria |Texto |Categoria de push de dados de diagnóstico para Log Analytics. Sempre AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Texto |Identificador exclusivo do item protegido associado ao alerta |
| VaultUniqueId_s |Texto |Identificador exclusivo do cofre protegido associado ao alerta |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador exclusivo para o recurso sobre quais dados são coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="backupitem"></a>BackupItem
Esta tabela fornece detalhes sobre os campos relacionados ao item de backup.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| EventName_s |Texto |O nome do evento. Sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |Identificador único do item de backup |
| BackupItemId_s |Texto |Identificador do item de backup |
| BackupItemName_s |Texto |ID do item de backup |
| BackupItemFriendlyName_s |Texto |Nome amigável do item de backup |
| BackupItemType_s |Texto |Tipo de item de backup, por exemplo, VM, FileFolder |
| ProtectedServerName_s |Texto |Nome do servidor protegido para o qual o item de backup pertence |
| ProtectionState_s |Texto |Estado de proteção atual do item de backup, por exemplo, Protegido, ProtectionStopped |
| SchemaVersion_s |Texto |Versão do esquema, por exemplo, **V1** |
| State_s |Texto |Estado do objeto do item de backup, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de provedor para executar o backup, por exemplo, IaaSVM, FileFolder para qual esse item de backup pertence |
| OperationName |Texto |Nome da operação, por exemplo, BackupItem |
| Categoria |Texto |Categoria de push de dados de diagnóstico para Log Analytics. Sempre AzureBackupReport |
| Recurso |Texto |Recursos para os quais dados são coletados, por exemplo, nome do cofre dos serviços de recuperação |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Id do recurso para os dados sendo coletados, por exemplo, serviços de recuperação do Cofre de id de recurso |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo cofre de Serviços de Recuperação) para dados sendo coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo cofre de Serviços de Recuperação) para dados sendo coletados |
| ResourceProvider |Texto |fornecedor de Recurso para dados sendo coletados, por exemplo Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para os dados que estão sendo coletados, por exemplo, os cofres |

### <a name="backupitemassociation"></a>BackupItemAssociation
Esta tabela fornece detalhes sobre associações de itens de backup com várias entidades.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |  
| BackupItemUniqueId_s |Texto |ID exclusivo do item de backup |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de associação de item de backup, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - BackupItemAssociation |
| Categoria |Texto |Este campo representa a categoria dos dados de diagnóstico enviados por push para o Log Analytics, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Texto |Identificador exclusivo para a política associada com o item de backup |
| ProtectedServerUniqueId_s |Texto |Identificador exclusivo do servidor protegido associado com o item de backup |
| VaultUniqueId_s |Texto |Identificador exclusivo do cofre que contém o item de backup |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados estão sendo coletados |
| ResourceProvider |Texto |fornecedor de Recurso para dados sendo coletados, por exemplo Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recurso para os dados que estão sendo coletados, por exemplo, os cofres |

### <a name="job"></a>Trabalho
Esta tabela fornece detalhes sobre campos relacionados ao trabalho.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| EventName_s |Texto |O nome do evento. Sempre AzureBackupCentralReport |
| BackupItemUniqueId_s |Texto |Identificador único do item de backup |
| SchemaVersion_s |Texto |Versão do esquema, por exemplo, **V1** |
| State_s |Texto |Estado atual do objeto de trabalho, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - Trabalho |
| Categoria |Texto |Este campo representa a categoria dos dados de diagnóstico enviados por push para o Log Analytics, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Texto |Identificador exclusivo do servidor protegido associado ao trabalho |
| VaultUniqueId_s |Texto |Identificador exclusivo do cofre protegido |
| JobOperation_s |Texto |Operação para a qual o trabalho é executado, por exemplo, backup, restauração, backup de configuração |
| JobStatus_s |Texto |Status do trabalho concluído, por exemplo, Concluído, Com Falha |
| JobFailureCode_s |Texto |Cadeia de caracteres de código de falha pela qual a falha no trabalho ocorreu |
| JobStartDateTime_s |Data/hora |Data e hora em que o trabalho iniciou a execução |
| BackupStorageDestination_s |Texto |Destino do armazenamento de backup, por exemplo, Nuvem, Disco  |
| JobDurationInSecs_s | Número |Duração total do trabalho em segundos |
| DataTransferredInMB_s | Número |Dados transferidos em MB para este trabalho|
| JobUniqueId_g |Texto |ID exclusivo para identificar o trabalho |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação|
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="policy"></a>Política
Esta tabela fornece detalhes sobre campos relacionados a políticas.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de política, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - Política |
| Categoria |Texto |Este campo representa a categoria dos dados de diagnóstico enviados por push para o Log Analytics, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Texto |ID exclusivo para identificar a política |
| PolicyName_s |Texto |Nome da política definido |
| BackupFrequency_s |Texto |Frequência com que os backups são executados, por exemplo, diária, semanal |
| BackupTimes_s |Texto |Data e hora quando os backups são agendados |
| BackupDaysOfTheWeek_s |Texto |Dias da semana nos quais os backups foram agendados |
| RetentionDuration_s |Número inteiro |Duração de retenção para backups configurados |
| DailyRetentionDuration_s |Número inteiro |Duração total da retenção em dias para backups configurados |
| DailyRetentionTimes_s |Texto |Data e hora quando a retenção diária foi configurada |
| WeeklyRetentionDuration_s |Número decimal |Duração total da retenção em semanas para backups configurados |
| WeeklyRetentionTimes_s |Texto |Data e hora quando a retenção semanal foi configurada |
| WeeklyRetentionDaysOfTheWeek_s |Texto |Dias da semana selecionados para a retenção semanal |
| MonthlyRetentionDuration_s |Número decimal |Duração total da retenção em meses para backups configurados |
| MonthlyRetentionTimes_s |Texto |Data e hora quando a retenção mensal foi configurada |
| MonthlyRetentionFormat_s |Texto |Tipo de configuração de retenção mensal, por exemplo, diário, semanal |
| MonthlyRetentionDaysOfTheWeek_s |Texto |Dias da semana selecionados para a retenção mensal |
| MonthlyRetentionWeeksOfTheMonth_s |Texto |Semanas do mês quando a retenção mensal é configurada, por exemplo, Primeira, Última, etc. |
| YearlyRetentionDuration_s |Número decimal |Duração total da retenção em ano para backups configurados |
| YearlyRetentionTimes_s |Texto |Data e hora quando a retenção anual foi configurada |
| YearlyRetentionMonthsOfTheYear_s |Texto |Meses do ano selecionados para a retenção anual |
| YearlyRetentionFormat_s |Texto |Tipo de configuração de retenção anual, por exemplo, diário, semanal |
| YearlyRetentionDaysOfTheMonth_s |Texto |Dias do mês selecionados para a retenção anual |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="policyassociation"></a>PolicyAssociation
Esta tabela fornece detalhes sobre associações de políticas com várias entidades.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de política, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - PolicyAssociation |
| Categoria |Texto |Este campo representa a categoria dos dados de diagnóstico enviados por push para o Log Analytics, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| PolicyUniqueId_g |Texto |ID exclusivo para identificar a política |
| VaultUniqueId_s |Texto |ID exclusivo do cofre ao qual esta política pertence |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="protectedserver"></a>ProtectedServer
Esta tabela fornece detalhes sobre campos protegidos relacionados ao servidor protegido.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| ProtectedServerName_s |Texto |Nome do servidor protegido |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de servidor protegido, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - ProtectedServer |
| Categoria |Texto |Este campo representa a categoria dos dados de diagnóstico enviados por push para o Log Analytics, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Texto |ID exclusivo do servidor protegido |
| RegisteredContainerId_s |Texto |ID do contêiner registrado para backup |
| ProtectedServerType_s |Texto |Tipo de servidor protegido, por exemplo, Windows |
| ProtectedServerFriendlyName_s |Texto |Nome amigável do servidor protegido |
| AzureBackupAgentVersion_s |Texto |Número de versão da versão de Backup do Agente |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Esta tabela fornece detalhes sobre associações de servidores protegidos com outras entidades.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de associação de servidor protegido, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - ProtectedServerAssociation |
| Categoria |Texto |Este campo representa a categoria dos dados de diagnóstico enviados por push para o Log Analytics, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Texto |ID exclusivo do servidor protegido |
| VaultUniqueId_s |Texto |ID exclusivo do cofre ao qual este servidor protegido pertence |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="storage"></a>Armazenamento
Esta tabela fornece detalhes sobre campos relacionados ao armazenamento.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| CloudStorageInBytes_s |Número decimal |Armazenamento de backup na nuvem usado por backups, calculado baseado no valor mais recente |
| ProtectedInstances_s |Número decimal |Número de instâncias protegidas utilizadas para calcular o armazenamento de front-end na cobrança, calculada baseada no valor mais recente |
| EventName_s |Texto |Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto de armazenamento, por exemplo, Ativo, Excluído |
| BackupManagementType_s |Texto |Tipo de fornecedor para servidor fazendo trabalho de backup, por exemplo, IaaSVM, FileFolder |
| OperationName |Texto |Este campo representa o nome da operação atual - Armazenamento |
| Categoria |Texto |Este campo representa a categoria dos dados de diagnóstico enviados por push para o Log Analytics, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| ProtectedServerUniqueId_s |Texto |ID exclusivo do servidor protegido para o qual o armazenamento é calculado |
| VaultUniqueId_s |Texto |ID exclusivo do cofre para armazenamento é calculado |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

### <a name="vault"></a>Cofre
Esta tabela fornece detalhes sobre campos relacionados ao cofre.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| EventName_s |Texto |Este campo representa o nome desse evento, é sempre AzureBackupCentralReport |
| SchemaVersion_s |Texto |Este campo indica a versão atual do esquema, é **V1** |
| State_s |Texto |Estado atual do objeto do sofre, por exemplo, Ativo, Excluído |
| OperationName |Texto |Este campo representa o nome da operação atual - Cofre |
| Categoria |Texto |Este campo representa a categoria dos dados de diagnóstico enviados por push para o Log Analytics, é AzureBackupReport |
| Recurso |Texto |Este é o recurso para o qual os dados estão sendo coletados, ele mostra o nome do cofre dos Serviços de Recuperação |
| VaultUniqueId_s |Texto |ID exclusivo do cofre |
| VaultName_s |Texto |Nome do cofre |
| AzureDataCenter_s |Texto |Data center onde o cofre está localizado |
| StorageReplicationType_s |Texto |Tipo de replicação de armazenamento para o cofre, por exemplo, GeoRedundant |
| SourceSystem |Texto |Sistema de origem dos dados atuais - Azure |
| ResourceId |Texto |Identificador de recurso para os dados sendo coletados. Por exemplo, id de recurso do Cofre de serviços de recuperação |
| SubscriptionId |Texto |Identificador de assinatura do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceGroup |Texto |grupo de Recursos do recurso (por exemplo Cofre dos serviços de recuperação) para o qual os dados são coletados |
| ResourceProvider |Texto |Provedor de recursos para o qual os dados são coletados. Por exemplo, Microsoft.RecoveryServices |
| ResourceType |Texto |Tipo de recursos para o qual os dados são coletados. Por exemplo, Cofres |

## <a name="next-steps"></a>Próximas etapas
Após revisar o modelo de dados para criar relatórios de Backup do Azure, você poderá iniciar a [criação de painel](../azure-monitor/platform/dashboards.md) no Log Analytics.
