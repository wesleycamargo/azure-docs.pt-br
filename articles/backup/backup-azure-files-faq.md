---
title: Perguntas frequentes sobre fazer backup de Arquivos do Azure
description: "Este artigo fornece detalhes sobre como proteger seus Arquivos do Azure no Azure. Este resumo é exibido no resultado da pesquisa."
services: backup
keywords: "Não adicione ou edite palavras-chave sem consultar seu especialista em SEO."
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: 3d09914c93d0f48b8f6bed405202682aaf925a5f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="questions-about-backing-up-azure-files"></a>Perguntas sobre como fazer backup de Arquivos do Azure
Este artigo responde perguntas frequentes sobre como fazer backup de Arquivos do Azure. Em algumas das respostas, há links para artigos com informações abrangentes. Você também pode postar perguntas sobre o serviço de Backup do Azure no [fórum de discussão](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Para verificar rapidamente as seções neste artigo, use os links à direita, em **Neste artigo**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Configurando o trabalho de backup para Arquivos do Azure

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-file-shares-br"></a>Por que não consigo ver algumas das minhas contas de armazenamento que desejo proteger e que contêm compartilhamentos de arquivo válidos? <br/>
Durante a versão prévia, o backup de compartilhamentos de arquivos do Azure não dá suporte a todos os tipos de contas de armazenamento. Consulte a lista de [aqui](troubleshoot-azure-files.md#preview-boundaries) para ver a lista de Contas de Armazenamento com suporte.

### <a name="why-cant-i-see-some-of-my-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Por que não vejo alguns dos meus compartilhamentos de arquivos na conta de armazenamento quando estou tentando configurar o backup? <br/>
Verifique se o compartilhamento de arquivos já está protegido no mesmo cofre dos Serviços de Recuperação ou se foi excluído recentemente.

### <a name="why-cant-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>Por que não posso proteger compartilhamentos de arquivos conectados a um grupo de sincronização na Sincronização de arquivos do Azure? <br/>
A proteção de compartilhamentos de arquivos do Azure conectados a grupos de sincronização está em versão prévia limitada. Escreva para [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) com sua ID de assinatura para acesso solicitado. 

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>Em quais áreas geográficas posso fazer backup de compartilhamentos de Arquivos do Azure <br/>
O backup para compartilhamentos de arquivos do Azure está atualmente em versão prévia e está disponível apenas nas áreas geográficas abaixo: 
-   Sudeste da Austrália (ASE) 
- Sul do Brasil (BRS)
- Central do Canadá (CNC)
-   Leste do Canadá (CE)
-   Central dos EUA (CUS)
-   Ásia Oriental (EA)
-   Leste da Austrália (AE) 
-   Leste dos EUA (EUS)
-   Leste dos EUA 2 (EUS2)
-   Índia Central (INC) 
-   Centro-Norte dos EUA (NCUS) 
-   Europa Setentrional (NE) 
-   Centro-Sul dos EUA (SCUS) 
-   Sudeste Asiático (SEA)
-   Sul do Reino Unido (UKS) 
-   Oeste do Reino Unido (UKW) 
-   Europa Ocidental (WE) 
-   Oeste dos EUA (WUS)
-   Centro-Oeste dos EUA (WCUS)
-   Oeste dos EUA 2 (WUS 2)

Escreva para [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) caso precise usá-lo em uma área geográfica específica que não esteja listada acima.

### <a name="how-many-file-shares-can-i-protect-in-a-vaultbr"></a>Quantos compartilhamentos de arquivos posso proteger em um cofre?<br/>
Durante a versão prévia, é possível proteger compartilhamentos de arquivos de até 25 contas de armazenamento por cofre. Também é possível proteger até 200 compartilhamentos de arquivos em um único cofre. 

## <a name="backup"></a>Backup

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Quantos backups sob demanda posso ter por compartilhamento de arquivo? <br/>
Você pode ter até 200 instantâneos para um compartilhamento de arquivos a qualquer hora. O limite inclui instantâneos tirados pelo Backup do Azure conforme definido pela sua política. Se os backups começarem a falhar após atingir o limite, exclua pontos de restauração sob demanda para ter êxito nos backups futuros.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>Depois de habilitar as redes virtuais na minha conta de armazenamento, o backup dos compartilhamentos de arquivo na conta começa a falhar. Por quê?
O backup de compartilhamentos de arquivos do Azure não dá suporte a Contas de Armazenamento que têm redes virtuais habilitadas. Desabilite as redes virtuais nas Contas de Armazenamento para permitir backups bem-sucedidos. 

## <a name="restore"></a>Restore

### <a name="can-i-recover-from-a-deleted-file-share-br"></a>Posso recuperar a partir de um compartilhamento de arquivo excluído? <br/>
Quando um compartilhamento de arquivo é excluído, você vê a lista de backups que também serão excluídos e uma solicitação de confirmação. Um compartilhamento de arquivos excluído não pode ser restaurado.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-a-file-share-br"></a>Posso restaurar a partir de backups se parar a proteção em um compartilhamento de arquivo? <br/>
Sim. Caso tenha escolhido **Reter Dados de Backup**, ao parar a proteção, você poderá restaurar a partir de todos os pontos de restauração existentes.

## <a name="manage-backup"></a>Gerenciar backup

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>Posso acessar os instantâneos tirados por Backups do Azure e montá-los? <br/>
Todos os instantâneos tirados pelo Backup do Azure podem ser acessados pelas opções Exibir Instantâneos no portal, PowerShell ou CLI. É possível montá-los usando este procedimento [aqui](../storage/files/storage-how-to-use-files-snapshots.md#mount-a-file-share).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Qual é a retenção máxima que posso configurar para backups? <br/>
O backup para compartilhamentos de Arquivos do Azure oferece a capacidade de manter seus backups diários por até 120 dias.

### <a name="what-happens-when-i-change-the-backup-policy-for-a-file-share-br"></a>O que acontece quando altero a política de backup de um compartilhamento de arquivo? <br/>
Quando uma nova política for aplicada em compartilhamento de arquivos, a agenda e a retenção da nova política serão seguidas. Se a retenção for estendida, os pontos de recuperação existentes serão marcados para mantê-los de acordo com a nova política. Se a retenção for reduzida, eles serão marcados para remoção no próximo trabalho de limpeza e subsequentemente excluídos.


## <a name="see-also"></a>Consulte também
Essas informações são apenas sobre estar fazendo backup de Arquivos do Azure, para saber mais sobre outras áreas do Backup do Azure, consulte algumas dessas outras perguntas frequentes de backup:
-  [Perguntas Frequentes sobre o cofre dos Serviços de Recuperação](backup-azure-backup-faq.md)
-  [Perguntas frequentes sobre o backup de VM do Azure](backup-azure-vm-backup-faq.md)
-  [Perguntas frequentes sobre o agente de Backup do Azure](backup-azure-file-folder-backup-faq.md)
