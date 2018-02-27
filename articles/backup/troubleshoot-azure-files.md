---
title: Solucionar problemas do backup de Arquivos do Azure
description: "Este artigo tem informações sobre a solução de problemas que ocorrem ao proteger seus Arquivos do Azure (compartilhamentos de arquivos) no Azure."
services: backup
ms.service: backup
keywords: "Não adicione ou edite palavras-chave sem consultar seu especialista em SEO."
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: ce4e53b3fa839bfc2da6bedecca0b4f730a6adbe
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="troubleshoot-problems-backing-up-azure-files"></a>Solucionar problemas do backup de Arquivos do Azure

É possível solucionar os problemas e os erros encontrados durante a utilização do backup de Arquivos do Azure com as informações listadas nas tabelas a seguir.

## <a name="preview-boundaries"></a>Limites da versão prévia
O backup de Arquivos do Azure está em versão prévia. Não há suporte para os seguintes cenários de backup para compartilhamentos de Arquivos do Azure:
- Protegendo compartilhamentos de arquivos nas contas de armazenamento com [armazenamento com redundância de zona](../storage/common/storage-redundancy.md#zone-redundant-storage) (ZRS) ou replicação [armazenamento com redundância geográfica com acesso de leitura](../storage/common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS).
- Protegendo compartilhamentos de arquivos em contas de armazenamento que têm redes virtuais habilitadas.
- Fazendo backup de Arquivos do Azure usando o PowerShell ou a CLI.

## <a name="configuring-backup"></a>Configurando o backup
A tabela a seguir serve para configurar o backup.

| Configurando o backup | Dicas de solução alternativa ou resolução |
| ------------------ | ----------------------------- |
| Não foi possível encontrar a minha Conta de Armazenamento para configurar o backup para o compartilhamento de Arquivo do Azure | <ul><li>Aguarde até a descoberta ser concluída. <li>Verifique se algum compartilhamento de arquivos da conta de armazenamento já está protegido com outro cofre dos Serviços de Recuperação. **Observação**: todos os compartilhamentos de arquivos em uma Conta de Armazenamento só podem ser protegidos em um cofre dos Serviços de Recuperação. <li>Verifique se o compartilhamento de arquivos não está presente em nenhuma das Contas de Armazenamento sem suporte.|
| O erro no portal indica que a descoberta de contas de armazenamento falhou. | Se sua assinatura for de parceiro (habilitado para CSP), ignore o erro. Se sua assinatura não estiver habilitada para CSP e suas contas de armazenamento não puderem ser descobertas, entre em contato com o suporte.|
| A validação ou o registro da Conta de Armazenamento selecionada falhou.| Repita a operação, e se o problema persistir, entre em contato com o suporte.|
| Não foi possível listar ou localizar os compartilhamentos de arquivos na Conta de Armazenamento selecionada. | <ul><li> Verifique se a Conta de Armazenamento existe no Grupo de Recursos (e se não foi excluída ou movida após a última validação/registro no cofre).<li>Verifique se o compartilhamento de arquivos que você deseja proteger não foi excluído. <li>Verifique se a Conta de Armazenamento tem suporte para backup de compartilhamento de arquivos.<li>Verifique se o compartilhamento de arquivos já está protegido no mesmo cofre dos Serviços de Recuperação.|
| A configuração de compartilhamento de arquivos de backup (ou a configuração de política de proteção) está falhando. | <ul><li>Repita a operação para ver se o problema persiste. <li> Verifique se o compartilhamento de arquivos que você deseja proteger não foi excluído. <li> Se estiver tentando proteger vários compartilhamentos de arquivos ao mesmo tempo e alguns deles estiverem falhando, repita a configuração de backup para os compartilhamentos de arquivos com falha. |
| Não é possível excluir o cofre dos Serviços de Recuperação depois de desproteger um compartilhamento de arquivos. | No portal do Azure, abra a **Infraestrutura de backup** > **Contas de armazenamento** e clique em **Cancelar o registro** para remover a conta de armazenamento do cofre dos Serviços de Recuperação.|


## <a name="error-messages-for-backup-or-restore-job-failures"></a>Mensagens de erro para falhas de trabalho de backup ou restauração

| Mensagens de erro | Dicas de solução alternativa ou resolução |
| -------------- | ----------------------------- |
| A operação falhou porque o compartilhamento de arquivos não foi encontrado. | Verifique se o compartilhamento de arquivos que você deseja proteger não foi excluído.|
| Conta de armazenamento não encontrada ou sem suporte. | <ul><li>Verifique se a conta de armazenamento existe no Grupo de Recursos e se não foi excluída ou removida após a última validação. <li> Verifique se a conta de armazenamento tem suporte para backup de compartilhamento de arquivos.|
| Você atingiu o limite máximo de instantâneos para este compartilhamento de arquivos e poderá tirar outros depois que os antigos expirarem. | <ul><li> Este erro pode ocorrer ao criar vários backups sob demanda para um arquivo. <li> Há um limite de 200 instantâneos por compartilhamento de arquivos, incluindo aqueles tirados pelo Backup do Azure. Os backups (ou instantâneos) agendados mais antigos são limpos automaticamente. Os backups (ou instantâneos) sob demanda devem ser excluídos se o limite máximo for atingido.<li> Exclua os backups sob demanda (instantâneos de compartilhamento de Arquivo do Azure) no portal de Arquivos do Azure. **Observação**: os pontos de recuperação serão perdidos se você excluir instantâneos criados pelo Backup do Azure. |
| O backup de compartilhamento de arquivos ou a restauração falhou devido à limitação do serviço de armazenamento. Isso pode ser devido ao serviço de armazenamento estar ocupado processando outras solicitações para determinada conta de armazenamento.| Repita a operação após algum tempo. |
| Falha na restauração com o compartilhamento de arquivos de destino não encontrado. | <ul><li>Verifique se a Conta de Armazenamento selecionada existe e se o compartilhamento de Arquivos de Destino não foi excluído. <li> Verifique se a Conta de Armazenamento tem suporte para backup de compartilhamento de arquivos. |
| Atualmente, o backup do Azure não tem suporte para Contas de Armazenamento dos Arquivos do Azure com Redes Virtuais habilitadas. | Desabilite as Redes Virtuais na sua Conta de Armazenamento para garantir o êxito dos backups ou operações de restauração. |
| Os trabalhos de backup ou restauração falharam porque a conta de armazenamento estava no estado Bloqueado. | Remova o bloqueio da Conta de Armazenamento ou use o bloqueio de exclusão em vez de bloqueio de leitura e repita a operação. |
| A recuperação falhou porque o número de arquivos com falha é maior do que o limite. | <ul><li> As razões da falha de recuperação são listadas em um arquivo (caminho fornecido nos detalhes do trabalho). Aborde as falhas e repita a operação de restauração apenas para os arquivos com falha. <li> Motivos comuns para falhas na restauração de arquivo: <br/> – verifique se os arquivos que falharam não estão em uso no momento; <br/> – existe um diretório com o mesmo nome que o arquivo com falha no diretório pai. |
| A recuperação falhou porque nenhum arquivo pôde ser recuperado. | <ul><li> As razões da falha de recuperação são listadas em um arquivo (caminho fornecido nos detalhes do trabalho). Aborde as falhas e repita as operações de restauração apenas para os arquivos com falha. <li> Motivos comuns para falhas na restauração de arquivo: <br/> – verifique se os arquivos que falharam não estão em uso no momento; <br/> – existe um diretório com o mesmo nome que o arquivo com falha no diretório pai. |
| A restauração falhou porque um dos arquivos de origem não existe. | <ul><li> Os itens selecionados não estão presentes nos dados de ponto de recuperação. Para recuperar os arquivos, forneça a lista correta de arquivos. <li> O instantâneo de compartilhamento de arquivos que corresponde ao ponto de recuperação é excluído manualmente. Selecione um ponto de recuperação diferente e repita a operação de restauração. |
| Um trabalho de recuperação está em andamento para o mesmo destino. | <ul><li>O backup do compartilhamento de arquivos não oferece suporte à recuperação paralela para o mesmo compartilhamento de arquivos de destino. <li>Aguarde até que recuperação existente seja concluída e tente novamente. Caso não encontre um trabalho de recuperação no cofre dos Serviços de Recuperação, verifique outros cofres dos Serviços de Recuperação na mesma assinatura. |
| A operação de restauração falhou porque o compartilhamento de arquivos de destino está cheio. | Aumente a cota de tamanho do compartilhamento de arquivo de destino para acomodar os dados de restauração e repita a operação. |
| Um ou mais arquivos não puderam ser recuperados. Para obter mais informações, verifique a lista de arquivos com falha no caminho fornecido acima. | <ul> <li> Os motivos para falha na recuperação estão listados no arquivo (caminho fornecido nos detalhes do trabalho). Aborde os motivos e repita a operação de restauração apenas para os arquivos com falha. <li> Motivos comuns para falhas na restauração de arquivo são: <br/> – verifique se os arquivos com falha não estão em uso no momento. <br/> – existe um diretório com o mesmo nome que o arquivo com falha no diretório pai. |

## <a name="see-also"></a>Veja também
Para obter mais informações sobre o backup de compartilhamentos de Arquivos do Azure, confira:
- [Backup de compartilhamentos de Arquivos do Azure](backup-azure-files.md)
- [Perguntas frequentes sobre backup de compartilhamentos de Arquivos do Azure](backup-azure-files-faq.md)