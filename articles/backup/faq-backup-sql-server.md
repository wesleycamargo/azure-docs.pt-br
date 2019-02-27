---
title: Perguntas frequentes sobre o backup de bancos de dados do SQL Server em VMs do Azure com o Backup do Microsoft Azure
description: Fornece respostas a perguntas comuns sobre o backup de bancos de dados do SQL Server em VMs do Azure com o Backup do Microsoft Azure.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: a14406733ff60d53d4bf7792ff0c9a015c57d9b3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430920"
---
# <a name="faq-on-sql-server-running-on-azure-vm-backup"></a>Perguntas Frequentes sobre o SQL Server em execução no backup da VM do Azure

Este artigo responde a perguntas comuns sobre como fazer backup dos bancos de dados do SQL Server em execução em VMs do Azure com o serviço de [Backup do Azure](backup-overview.md).

> [!NOTE]
> Esse recurso está atualmente em visualização pública.



## <a name="can-i-throttle-the-backup-speed"></a>É possível limitar a velocidade de backup?

Sim. Você pode limitar a taxa com que a política de backup é executada para minimizar o impacto em uma instância do SQL Server. Para alterar a configuração:
1. Na instância do SQL Server, na pasta *C:\Arquivos de Programas\Azure Workload Backup\bin*, crie o arquivo **ExtensionSettingsOverrides.json**.
2. No arquivo **ExtensionSettingsOverrides.json**, altere a configuração **DefaultBackupTasksThreshold** para um valor mais baixo (por exemplo, 5). <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Salve suas alterações. Feche o arquivo.
4. Na instância do SQL Server, abra **Gerenciador de Tarefas**. Reinicie o serviço **AzureWLBackupCoordinatorSvc**.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Posso executar um backup completo usando uma réplica secundária?
 Não. Não há suporte para esse recurso.

## <a name="do-successful-backup-jobs-create-alerts"></a>Trabalhos de backup bem-sucedidos criam alertas?

 Não. Trabalhos de backup bem-sucedidos não geram alertas. Os alertas são enviados somente para trabalhos de backup com falha.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>Posso ver os trabalhos de backup agendados no menu Trabalhos?

 Não. O menu **Trabalhos de Backup** mostra detalhes do trabalho sob demanda, mas não os trabalhos de backup agendados. Se algum trabalho de backup agendado falhar, os detalhes estarão disponíveis nos alertas de trabalho com falha. Para monitorar todos os trabalhos de backup ad hoc e agendados, use o [SQL Server Management Studio](manage-monitor-sql-database-backup.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Bancos de dados futuros são adicionados automaticamente ao backup?

 Não. Quando você configura a proteção para uma instância do SQL Server, se você seleciona a opção de nível de servidor, todos os bancos de dados são adicionados. Se você adiciona bancos de dados a uma instância do SQL Server depois de configurar a proteção, deve adicionar manualmente novos bancos de dados para protegê-los. Os bancos de dados não são incluídos automaticamente na proteção configurada.

##  <a name="how-do-i-restart-protection-after-changing-recovery-type"></a>Como fazer para reiniciar a proteção depois de alterar o tipo de recuperação?

Dispare um backup completo. Os backups de log começam conforme o esperado.

## <a name="can-i-protect-availability-groups-on-premises"></a>Posso proteger grupos de disponibilidade local?

 Não. O Backup do Azure protege os SQL Servers em execução no Azure. Se um AG (Grupo de Disponibilidade) é distribuído entre computadores locais e do Azure, o grupo de disponibilidade pode ser protegido somente se a réplica primária está em execução no Azure. Além disso, o Backup do Azure protege apenas os nós em execução na mesma região do Azure que o cofre de Serviços de Recuperação.

## <a name="can-i-protect-availability-groups-across-regions"></a>Posso proteger grupos de disponibilidade entre regiões?

O Cofre dos Serviços de Recuperação do Backup do Azure pode detectar e proteger todos os nós que estão na mesma região que o Cofre dos Serviços de Recuperação. Se tiver um Grupo de Disponibilidade Always On do SQL que abranja várias regiões do Azure, você precisará configurar o backup da região que tem o nó primário. O Backup do Azure será capaz de detectar e proteger todos os bancos de dados no grupo de disponibilidade, de acordo com a preferência de backup. Se a preferência de backup não for atendida, os backups falharão e você receberá o alerta de falha.

## <a name="can-i-exclude-databases-with-auto-protection-enabled"></a>Posso excluir os bancos de dados com a proteção automática habilitada?

Não, a [proteção automática](backup-azure-sql-database.md#enable-auto-protection) aplica-se à instância inteira. Você seletivamente não pode proteger os bancos de dados de uma instância usando a proteção automática.

## <a name="can-i-have-different-policies-in-an-auto-protected-instance"></a>Posso ter políticas diferentes em uma instância protegida automaticamente?

Se você já tiver alguns bancos de dados protegidos em uma instância, eles continuarão a ser protegidos com suas respectivas políticas mesmo depois que você **ATIVAR** a opção de [proteção automática](backup-azure-sql-database.md#enable-auto-protection). No entanto, todos os bancos de dados desprotegidos junto com aqueles que você adicionaria futuramente, terão apenas uma única política que você define em **Configurar Backup** depois que o banco de dados for selecionado. Na verdade, ao contrário de outros bancos de dados protegidos, é possível até mesmo alterar a política para um banco de dados em uma instância protegida automaticamente.
Se você quiser fazer isso, a única maneira é desabilitar a proteção automática na instância por enquanto e, em seguida, alterar a política para esse banco de dados. Agora você pode reabilitar a proteção automática para esta instância.

## <a name="if-i-delete-a-database-from-auto-protection-will-backups-stop"></a>Se eu excluir um banco de dados de proteção automática os backups serão interrompidos?

Não, se um banco de dados for descartado de uma instância protegida automaticamente, os backups de banco de dados ainda serão tentados. Isso implica que o banco de dados excluído começa a ser exibido como não íntegro em **Itens de Backup** e ainda é tratado como protegido.

A única maneira de interromper a proteção deste banco de dados é desabilitar a [proteção automática](backup-azure-sql-database.md#enable-auto-protection) na instância temporariamente e, em seguida, escolher a opção **Parar Backup** nos **Itens de Backup** desse banco de dados. Agora você pode reabilitar a proteção automática para esta instância.

##  <a name="why-cant-i-see-an-added-database-for-an-auto-protected-instance"></a>Por que não consigo ver um banco de dados adicionado a uma instância protegida automaticamente?

Você não poderá ver um banco de dados recém-adicionado a uma instância [protegida automaticamente](backup-azure-sql-database.md#enable-auto-protection) de forma instantânea nos itens protegidos. Isso ocorre porque a descoberta normalmente é executada a cada 8 horas. No entanto, o usuário pode executar uma descoberta manual usando a opção **Recuperar bancos de dados** para descobrir e proteger novos bancos de dados imediatamente, conforme mostrado na imagem abaixo:

  ![Exibir banco de dados adicionado recentemente](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Próximas etapas

[Saiba como](backup-azure-sql-database.md) fazer backup de um banco de dados do SQL Server em execução em uma VM do Azure.
