---
title: Modelo de dados para os Backup do Azure
description: Este artigo aborda detalhes de modelo de dados do Power BI para relatórios de Backup do Azure.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
origin.date: 06/26/2017
ms.date: 08/08/2018
ms.author: v-junlch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c6160570644da108ba713e8229b38f9587495c92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337571"
---
# <a name="data-model-for-azure-backup-reports"></a>Modelo de dados para relatórios de Backup do Azure
Este artigo descreve o modelo de dados do Power BI usado para criar relatórios de Backup do Azure. Usando esse modelo de dados, você pode filtrar os relatórios existentes com base em campos relevantes e, mais importante, criar seus próprios relatórios usando tabelas e campos no modelo. 

## <a name="creating-new-reports-in-power-bi"></a>Criando novos relatórios no Power BI
O Power BI fornece recursos de personalização com o qual você pode [criar relatórios usando o modelo de dados](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Usando o modelo de dados de Backup do Azure
Você pode usar os seguintes campos fornecidos como parte do modelo de dados para criar relatórios e personalizar relatórios existentes.

### <a name="alert"></a>Alerta
Esta tabela fornece campos e agregações básicos em vários campos relacionados ao alerta.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Número inteiro |Número de alertas criados no período de tempo selecionado |
| %ActiveAlertsCreatedInPeriod |Percentual |Porcentagem de alertas ativos no período de tempo selecionado |
| %CriticalAlertsCreatedInPeriod |Percentual |Porcentagem de alertas críticos no período de tempo selecionado |
| AlertOccurrenceDate |Data |Data em que o alerta foi criado |
| AlertSeverity |Text |Severidade do alerta, por exemplo, crítico |
| AlertStatus |Text |Status do alerta, por exemplo, ativo |
| AlertType |Text |Tipo de alerta gerado, por exemplo, Backup |
| AlertUniqueId |Text |ID exclusiva do alerta gerado |
| AsOnDateTime |Data/hora |Hora da atualização mais recente para a linha selecionada |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Número decimal |Tempo médio (em minutos) para resolver o alerta para o período de tempo selecionado |
| EntityState |Text |Estado atual do objeto do alerta, por exemplo, ativo, excluído |

### <a name="backup-item"></a>Item de backup
Esta tabela fornece campos e agregações básicos em vários campos relacionados ao item de backup.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| #BackupItems |Número inteiro |Número de itens de backup |
| #UnprotectedBackupItems |Número inteiro |Número de itens de backup interrompidos para proteção ou configurados para backups, mas os backups não iniciados|
| AsOnDateTime |Data/hora |Hora da atualização mais recente para a linha selecionada |
| BackupItemFriendlyName |Text |Nome amigável do item de backup |
| BackupItemId |Text |ID do item de backup |
| BackupItemName |Text |ID do item de backup |
| BackupItemType |Text |Tipo de item de backup, por exemplo, VM, FileFolder |
| EntityState |Text |Estado atual do objeto do item de backup, por exemplo, ativo, excluído |
| LastBackupDateTime |Data/hora |Hora do último backup para o item de backup selecionado |
| LastBackupState |Text |Estado do último backup para o item de backup selecionado, por exemplo, com êxito, falha |
| LastSuccessfulBackupDateTime |Data/hora |Hora do último backup feito com sucesso para o item de backup selecionado |
| ProtectionState |Text |Estado de proteção atual do item de backup, por exemplo, protegido, ProtectionStopped |

### <a name="calendar"></a>Calendário
Esta tabela fornece detalhes sobre os campos relacionados ao calendário.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| Data |Data |Data selecionada para filtrar dados |
| DateKey |Text |Chave exclusiva para cada item de data |
| DayDiff |Número decimal |Diferença no dia para filtrar dados, por exemplo, 0 indica os dados do dia atual, -1 indica os dados do dia anterior, 0 e -1 indicam os dados do dia atual e anterior  |
| Mês |Text |Mês do ano selecionado para filtrar dados, o mês começa no primeiro dia e termina no dia 31 |
| MonthDate | Data |Dia do mês quando o mês termina, selecionado para filtrar dados |
| MonthDiff |Número decimal |Diferença no mês para filtrar dados, por exemplo, 0 indica os dados do mês atual, -1 indica os dados do mês anterior, 0 e -1 indicam os dados do mês atual e anterior |
| Semana |Text |Semana selecionada para filtrar dados, a semana começa no domingo e termina no sábado |
| WeekDate |Data |Data em que a semana termina, selecionada para filtrar dados |
| WeekDiff |Número decimal |Diferença na semana para filtrar dados, por exemplo, 0 indica os dados da semana atual, -1 indica os dados da semana anterior, 0 e -1 indicam os dados para a semana atual e anterior |
| Ano |Text |Ano do calendário selecionado para filtrar dados |
| YearDate |Data |Data em que o ano termina, selecionada para filtrar dados |

### <a name="job"></a>Trabalho
Esta tabela fornece campos e agregações básicos em vários campos relacionados ao trabalho.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| #JobsCreatedInPeriod |Número inteiro |Número de trabalhos criados no período de tempo selecionado |
| %FailuresForJobsCreatedInPeriod |Percentual |Porcentagem geral de trabalho com falhas no período de tempo selecionado |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Número decimal |Valor percentual de dados transferidos 80 em MB para os trabalhos de **backup** criados no período de tempo selecionado |
| AsOnDateTime |Data/hora |Hora da atualização mais recente para a linha selecionada |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Número decimal |Média de tempo em minutos para trabalhos de **backup concluídos** criados no período de tempo selecionado |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Número decimal |Média de tempo em minutos para trabalhos de **restauração concluídos** criados no período de tempo selecionado |
| BackupStorageDestination |Text |Destino de armazenamento de backup, por exemplo, nuvem, disco  |
| EntityState |Text |Estado atual do objeto de trabalho, por exemplo, ativo, excluído |
| JobFailureCode |Text |Cadeia de caracteres de código de falha pela qual a falha no trabalho ocorreu |
| JobOperation |Text |Operação para a qual o trabalho é executado, por exemplo, backup, restauração, backup de configuração |
| JobStartDate |Data |Data em que o trabalho começou a ser executado |
| JobStartDate |Hora |Hora em que o trabalho começou a ser executado |
| JobStatus |Text |Status do trabalho concluído, por exemplo, concluído, com falha |
| JobUniqueId |Text |ID exclusivo para identificar o trabalho |

### <a name="policy"></a>Política
Esta tabela fornece campos e agregações básicos em vários campos relacionados à política.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| #Policies |Número inteiro |Número de políticas de backup que existem no sistema |
| #PoliciesInUse |Número inteiro |Número de políticas que estão sendo usadas no momento para configurar backups |
| AsOnDateTime |Data/hora |Hora da atualização mais recente para a linha selecionada |
| BackupDaysOfTheWeek |Text |Dias da semana nos quais os backups foram agendados |
| BackupFrequency |Text |Frequência com que os backups são executados, por exemplo, diária, semanal |
| BackupTimes |Text |Data e hora quando os backups são agendados |
| DailyRetentionDuration |Número inteiro |Duração total da retenção em dias para backups configurados |
| DailyRetentionTimes |Text |Data e hora quando a retenção diária foi configurada |
| EntityState |Text |Estado atual do objeto da política, por exemplo, ativo, excluído |
| MonthlyRetentionDaysOfTheMonth |Text |Datas do mês selecionadas para a retenção mensal |
| MonthlyRetentionDaysOfTheMonth |Text |Dias da semana selecionados para a retenção mensal |
| MonthlyRetentionDuration |Número decimal |Duração total da retenção em meses para backups configurados |
| MonthlyRetentionFormat |Text |Tipo de configuração de retenção mensal, por exemplo, diário, semanal |
| MonthlyRetentionTimes |Text |Data e hora quando a retenção mensal foi configurada |
| MonthlyRetentionWeeksOfTheMonth |Text |Semanas do mês quando a retenção mensal é configurada, por exemplo, a primeira, a última, etc. |
| PolicyName |Text |Nome da política definido |
| PolicyUniqueId |Text |ID exclusivo para identificar a política |
| RetentionType |Text |Tipo de política de retenção, por exemplo, diário, semanal, mensal, anual |
| WeeklyRetentionDaysOfTheWeek |Text |Dias da semana selecionados para a retenção semanal |
| WeeklyRetentionDuration |Número decimal |Duração total da retenção em semanas para backups configurados |
| WeeklyRetentionTimes |Text |Data e hora quando a retenção semanal foi configurada |
| YearlyRetentionDaysOfTheMonth |Text |Dias do mês selecionados para a retenção anual |
| YearlyRetentionDaysOfTheMonth |Text |Dias da semana selecionados para a retenção anual |
| YearlyRetentionDuration |Número decimal |Duração total da retenção em ano para backups configurados |
| YearlyRetentionFormat |Text |Tipo de configuração de retenção anual, por exemplo, diário, semanal |
| YearlyRetentionMonthsOfTheYear |Text |Meses do ano selecionados para a retenção anual |
| YearlyRetentionTimes |Text |Data e hora quando a retenção anual foi configurada |
| YearlyRetentionWeeksOfTheMonth |Text |Semanas do mês quando a retenção anual é configurada, por exemplo, a primeira, a última, etc. |

### <a name="protected-server"></a>Servidor protegido
Esta tabela fornece campos e agregações básicos em vários campos relacionados a servidores protegidos.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| #ProtectedServers |Número inteiro |Número de servidores protegidos |
| AsOnDateTime |Data/hora |Hora da atualização mais recente para a linha selecionada |
| AzureBackupAgentOSType |Text |Tipo de OS do Agente de Backup do Azure |
| AzureBackupAgentOSVersion |Text |Versão do OS do Agente de Backup do Azure |
| AzureBackupAgentUpdateDate |Text |Data quando o Agente de Backup foi atualizado |
| AzureBackupAgentVersion |Text |Número de versão da versão de Backup do Agente |
| BackupManagementType |Text |Tipo de provedor para executar o backup, por exemplo, IaaSVM, FileFolder |
| EntityState |Text |Estado atual do objeto de servidor protegido, por exemplo, ativo, excluído |
| ProtectedServerFriendlyName |Text |Nome amigável do servidor protegido |
| ProtectedServerName |Text |Nome do servidor protegido |
| ProtectedServerType |Text |Tipo de backup do servidor protegido, por exemplo, IaaSVMContainer |
| ProtectedServerName |Text |Nome do servidor protegido para o qual o item de backup pertence |
| RegisteredContainerId |Text |ID do contêiner registrado para backup |

### <a name="storage"></a>Armazenamento
Esta tabela fornece campos e agregações básicos em vários campos relacionados ao armazenamento.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| #ProtectedInstances |Número decimal |Número de instâncias protegidas usadas para calcular o armazenamento de front-end no faturamento, calculado com base no valor mais recente de tempo selecionado |
| AsOnDateTime |Data/hora |Hora da atualização mais recente para a linha selecionada |
| CloudStorageInMB |Número decimal |Armazenamento de backup de nuvem usado por backups, calculado com base no valor mais recente de tempo selecionado |
| EntityState |Text |Estado atual do objeto, por exemplo, ativo, excluído |
| LastUpdatedDate |Data |Data na qual a linha selecionada foi atualizada |

### <a name="time"></a>Hora
Esta tabela fornece detalhes sobre os campos relacionados ao tempo.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| Hora |Hora |Hora do dia, por exemplo, 1:00:00 PM |
| HourNumber |Número decimal |Número de horas do dia, por exemplo, 13.00 |
| Minuto |Número decimal |Minuto da hora |
| PeriodOfTheDay |Text |O intervalo do período de tempo do dia, por exemplo, 12 a 3 AM |
| Hora |Hora |Hora do dia, por exemplo, 12:00:01 AM |
| TimeKey |Text |Valor chave para representar a hora |

### <a name="vault"></a>Cofre
Esta tabela fornece campos e agregações básicos em vários campos relacionados ao cofre.

| Campo | Tipo de Dados | DESCRIÇÃO |
| --- | --- | --- |
| #Vaults |Número inteiro |Número de cofres |
| AsOnDateTime |Data/hora |Hora da atualização mais recente para a linha selecionada |
| AzureDataCenter |Text |Data center onde o cofre está localizado |
| EntityState |Text |Estado atual do objeto do cofre, por exemplo, ativo, excluído |
| StorageReplicationType |Text |Tipo de replicação de armazenamento para o cofre, por exemplo, GeoRedundant |
| SubscriptionId |Text |ID da assinatura do cliente selecionado para geração de relatórios |
| VaultName |Text |Nome do cofre |
| VaultTags |Text |Marcas associadas ao cofre |

## <a name="next-steps"></a>Próximas etapas
Depois de examinar o modelo de dados para criar relatórios de Backup do Azure, consulte os seguintes artigos para obter mais detalhes sobre como criar e exibir relatórios no Power BI.

- [Criando relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
- [Filtrando relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)


<!-- Update_Description: update metedata properties -->