---
title: Perguntas frequentes sobre o backup de bancos de dados do SQL Server em VMs do Azure com o Backup do Microsoft Azure
description: Encontre respostas para perguntas comuns sobre o backup de bancos de dados do SQL Server em VMs do Azure com o Backup do Azure.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: sachdevaswati
ms.openlocfilehash: 8d6323c73e5313a29b7b0df09ebdd24a190879f5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791886"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Perguntas Frequentes sobre bancos de dados do SQL Server que estão em execução em um backup de VM do Azure

Este artigo responde a perguntas comuns sobre como fazer backup de bancos de dados do SQL Server que são executados em máquinas virtuais (VMs) e que usam o [Backup do Azure](backup-overview.md) service.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>A solução novamente ou restabelecimento automático os backups?

Em algumas circunstâncias, o serviço de Backup do Azure dispara backups corretivas. Recuperação automática pode ocorrer por qualquer uma das seis condições mencionadas a seguir:

  - Se o log ou backup diferencial falhar devido a erro de validação de LSN, próximo log ou backup diferencial em vez disso, é convertido em um backup completo.
  - Se nenhum backup completo aconteceu antes de um log ou backup diferencial, esse log ou backup diferencial em vez disso, é convertido em um backup completo.
  - Se point-in-time do backup completo mais recente é mais de 15 dias, o próximo log ou backup diferencial em vez disso, é convertido em um backup completo.
  - Todos os trabalhos de backup que obterem cancelados devido a uma atualização de extensão são disparados novamente após a conclusão da atualização e a extensão é iniciada.
  - Se você optar por substituir o banco de dados durante a restauração, o próximo backup de log/diferencial falhará e um backup completo é acionado em vez disso.
  - Em casos em que um backup completo é necessário redefinir as cadeias de logs devido à alteração no modelo de recuperação do banco de dados, uma completa é disparada automaticamente no próximo agendamento.

Restabelecimento automático como um recurso for habilitado para todos os usuários por padrão. No entanto, caso você optar por recusar dele, em seguida, executar a abaixo:

  * Na instância do SQL Server, nos *C:\Program Files \ carga de trabalho Backup\bin* pasta, crie ou edite o **ExtensionSettingsOverrides.json** arquivo.
  * No **ExtensionSettingsOverrides.json**, defina *{"EnableAutoHealer": false}*.
  * Salve suas alterações e feche o arquivo.
  * Na instância do SQL Server, abra **tarefa gerenciar** e, em seguida, reinicie o **AzureWLBackupCoordinatorSvc** service.  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>É possível controlar como para qualquer número de backups simultâneos executar no SQL server?

Sim. Você pode limitar a taxa com que a política de backup é executada para minimizar o impacto em uma instância do SQL Server. Para alterar a configuração:
1. Na instância do SQL Server, nos *C:\Program Files \ carga de trabalho Backup\bin* pasta, crie o *ExtensionSettingsOverrides.json* arquivo.
2. No *ExtensionSettingsOverrides.json* file, altere o **DefaultBackupTasksThreshold** definindo como um valor mais baixo (por exemplo, 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`

3. Salve suas alterações e feche o arquivo.
4. Na instância do SQL Server, abra **Gerenciador de Tarefas**. Reinicie o serviço **AzureWLBackupCoordinatorSvc**.

> [!NOTE]
> A experiência do usuário ainda pode prosseguir e agendar todos os backups em um determinado momento, no entanto, elas serão processadas em uma janela deslizante de, digamos, 5, de acordo com o exemplo acima.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Posso executar um backup completo usando uma réplica secundária?
De acordo com as limitações do SQL, você pode executar copiar somente backup completo na réplica secundária; No entanto, não é permitido o backup completo.

## <a name="can-i-protect-availability-groups-on-premises"></a>Posso proteger grupos de disponibilidade no local?
 Não. O Backup do Azure protege os bancos de dados do SQL Server em execução no Azure. Se um grupo de disponibilidade (AG) é distribuído entre computadores locais e do Azure, o grupo de disponibilidade pode ser protegido somente se a réplica primária está em execução no Azure. Além disso, o Azure Backup protege apenas os nós que são executados na mesma região do Azure que o Cofre de serviços de recuperação.

## <a name="can-i-protect-availability-groups-across-regions"></a>Pode proteger grupos de disponibilidade entre regiões?
O cofre dos serviços de recuperação de Backup do Azure pode detectar e proteger todos os nós que estão na mesma região que o cofre. Se seu grupo de disponibilidade Always On do SQL Server se estender por várias regiões do Azure, configure o backup da região que tem o nó primário. O Backup do Microsoft Azure poderá detectar e proteger todos os bancos de dados no grupo de disponibilidade, de acordo com a sua preferência de backup. Quando sua preferência de backup não for atendida, os backups falharão e você receber o alerta de falha.

## <a name="do-successful-backup-jobs-create-alerts"></a>Trabalhos de backup bem-sucedidos criam alertas?
 Não. Trabalhos de backup bem-sucedidos não geram alertas. Os alertas são enviados somente para trabalhos de backup com falha. Comportamento detalhado para alertas do portal está documentado [aqui](backup-azure-monitoring-built-in-monitor.md). No entanto, caso você esteja interessado têm alertas mesmo para trabalhos com êxito, você pode usar [monitoramento usando o Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Posso ver os trabalhos de backup agendados no menu trabalhos de Backup?
O **trabalho de Backup** menu mostrará apenas ad-hoc trabalhos de backup. Para o trabalho agendado, use [monitoramento usando o Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Bancos de dados futuros são adicionados automaticamente ao backup?
Sim, você pode obter essa funcionalidade com [proteção automática](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Se eu excluir um banco de dados de uma instância de autoprotected, o que acontecerá com os backups?
Se um banco de dados é descartado de uma instância de autoprotected, os backups de banco de dados ainda serão tentados. Isso implica que o banco de dados excluído começa sejam exibidos como não íntegro sob **itens de Backup** e ainda está protegido.

A maneira correta de interromper a proteção deste banco de dados é fazer **parar Backup** com **excluir dados** neste banco de dados.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Se parar a operação de backup de um banco de dados autoprotected qual será seu comportamento?
Se você fizer **parar backup e reter dados**, nenhum backup futuro será realizada e os pontos de recuperação existentes permanecerão intactos. O banco de dados ainda será considerado como protegido e exibido sob o **itens de Backup**.

Se você fizer **parar backup e excluir dados**, nenhum backup futuro será realizada e os pontos de recuperação existente também serão excluídos. O banco de dados será considerado não protegido e exibido sob a instância em Configurar Backup. No entanto, ao contrário de outros protegido por cima bancos de dados que podem ser selecionadas manualmente ou que podem obter autoprotected, esse banco de dados aparece esmaecido e não pode ser selecionado. A única maneira de proteger novamente esse banco de dados é desabilitar a proteção automática na instância. Agora você pode selecionar esse banco de dados e configurar a proteção ou reabilitar a proteção automática na instância novamente.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Se eu alterar o nome do banco de dados depois que ele foi protegido, qual será o comportamento?
Um banco de dados nomeado novamente é tratado como um novo banco de dados. Portanto, o serviço tratará essa situação como se o banco de dados não foram encontrados e com falha de backups.

Você pode selecionar o banco de dados, que agora está renomeado e configurar a proteção. No caso da proteção automática está habilitada na instância, o banco de dados renomeado serão automaticamente detectado e protegido.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Por que não consigo ver um banco de dados para uma instância de autoprotected adicionado?
Um banco de dados que você [adicionar a uma instância de autoprotected](backup-sql-server-database-azure-vms.md#enable-auto-protection) não pode aparecer imediatamente em itens protegidos. Isso ocorre porque a descoberta normalmente é executada a cada 8 horas. No entanto, você pode descobrir e proteger os novos bancos de dados imediatamente se você executar manualmente uma descoberta, selecionando **recuperar bancos de dados**, conforme mostrado na imagem a seguir.

  ![Descobrir manualmente um banco de dados adicionado recentemente](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Próximos passos

Saiba como [fazer backup de um banco de dados do SQL Server](backup-azure-sql-database.md) que é executado em uma VM do Azure.
