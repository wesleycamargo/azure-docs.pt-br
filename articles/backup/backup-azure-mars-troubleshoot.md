---
title: Solucionar problemas do Agente MARS (Agente de Backup do Microsoft Azure)
description: "Solucionar problemas de instalação e registro do agente de Backup do Microsoft Azure"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shreeshd
editor: 
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/4/2017
ms.author: saurse;markgal;
ms.openlocfilehash: da297177ca56822f7d50dbe06ec022640bf5d0cb
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="troubleshooting-azure-backup-agent-configurationregistration-issues"></a>Solucionar problemas de configuração/registro do Agente de Backup do Microsoft Azure
## <a name="recommended-steps"></a>Etapas recomendadas
Consulte a(s) ação(ões) recomendada(s) abaixo para resolver erros correspondentes observados durante a configuração ou o registro do agente de Backup do Microsoft Azure.

## <a name="error-invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Erro: credenciais do cofre fornecidas inválidas. O arquivo está corrompido ou não possui as credenciais mais recentes associadas ao serviço de recuperação.
| Detalhes do erro | Possíveis causas | Ação(ões) recomendada(s) |
| ---     | ---     | ---    |
| **Erro** </br> *Credenciais do cofre inválidas fornecidas. O arquivo está corrompido ou não possui as credenciais mais recentes associadas ao serviço de recuperação. (ID: 34513)* | <ol><li> As credenciais do Cofre não são válidas (ou seja, elas foram baixados mais de 48 horas antes do momento do registro).<li>   O agente de Backup do Microsoft Azure não é capaz de fazer o download de um arquivo temporário para a pasta Temp do Windows. <li>As Credenciais do Cofre estão em um local de rede. <li>TLS 1.0 está desabilitado<li> Um servidor proxy configurado está bloqueando a conexão <br> |  <ol><li>Fazer o download das novas Credenciais do Cofre<li>Acesse as opções da Internet> segurança> Internet> clique em Nível Personalizado> role até ver a seção de download de arquivos e selecione habilitar.<li>Também pode ser necessário adicionar o site para [sites confiáveis](https://docs.microsoft.com/en-us/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Altere as configurações para usar o proxy e forneça os detalhes do proxy <li> Corresponda a Data e Hora (UTC) com seu computador<li>Vá para C:/Windows/Temp e verifique se há mais de 60.000 ou 65.000 arquivos (com extensão .tmp) e exclua esses arquivos.<li>É possível testar isso executando o pacote SDP no servidor. Se você receber o erro ao indicar que os downloads de arquivos não são permitidos, esteja razoavelmente seguro sobre o grande número de arquivos no diretório C:/Windows/Temp.<li>Certifique-se de que tenha o .NET Framework 4.6.2 instalado <li>Se você desabilitou o TLS 1.0 devido à conformidade de PCI, consulte esse [link para solucionar problemas](https://support.microsoft.com/help/4022913). <li>Se você tiver um antivírus instalado no servidor, exclua os seguintes arquivos de verificação de antivírus. <ol><li>CBengine.exe<li>CSC.exe(relacionado ao .NET Framework. Há uma versão CSC.exe por .NET instalada no servidor. Exclua todos os arquivos CSC.exe vinculados a todas as versões do framework .NET no servidor afetado.) <li>Pasta de Rascunho ou local do cache. <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="error-the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Erro: O Agente de Serviços de Recuperação do Microsoft Azure não pôde se conectar ao Backup do Microsoft Azure

| Detalhes do erro | Possíveis causas | Ação(ões) recomendada(s) |
| ---     | ---     | ---    |
| **Erro** </br><ol><li>*O Agente de Serviços de Recuperação do Microsoft Azure não pôde se conectar ao Backup do Microsoft Azure. (ID: 100050) Verifique as configurações de rede e assegure-se de que é possível conectar-se à Internet*<li>*(407) Autenticação de Proxy Obrigatória* |Proxy bloqueando a conexão* |  <ol><li>Acesse as opções da Internet> segurança> Internet> clique em Nível Personalizado> role até ver a seção de download de arquivos e selecione habilitar.<li>Também pode ser necessário adicionar o site para [sites confiáveis](https://docs.microsoft.com/en-us/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Altere as configurações para usar o proxy e forneça os detalhes do proxy <li>Se você tiver um antivírus instalado no servidor, exclua os seguintes arquivos de verificação de antivírus. <ol><li>CBengine.exe<li>(em vez de dpmra.exe)<li>CSC.exe(relacionado ao .NET Framework. Há uma versão CSC.exe por .NET instalada no servidor. Exclua todos os arquivos CSC.exe vinculados a todas as versões do framework .NET no servidor afetado.) <li>Pasta de Rascunho ou local do cache <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="error-failed-to-set-the-encryption-key-for-secure-backups"></a>Erro: Falha ao configurar a chave de criptografia para backups seguros

| Detalhes do erro | Possíveis causas | Ação(ões) recomendada(s) |
| ---     | ---     | ---    |      
| **Erro** </br>*Falha ao configurar a chave de criptografia para backups seguros. A operação atual falhou devido a um erro de serviço interno 'Erro de Entrada Inválida'. Repita a operação após algum tempo. Se o problema persistir, contate o suporte da Microsoft*  |O servidor já está registrado com outro cofre| Remover o registro do servidor do cofre e registrar novamente.

## <a name="error-the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>Erro: a ativação não foi concluída com êxito. A operação atual falhou devido a um erro de serviço interno [0x1FC07]

| Detalhes do erro | Possíveis causas | Ação(ões) recomendada(s) |
| ---     | ---     | ---    |          
| **Erro** </br><ol><li>*A ativação não foi concluída com êxito. A operação atual falhou devido a um erro de serviço interno [0x1FC07]. Repita a operação após algum tempo. Se o problema persistir, contate o suporte da Microsoft*  <li>*Erro 34506. A senha de criptografia armazenada neste computador não está configurada corretamente* | <li> A pasta de Rascunho está localizada em um volume que não possui espaço suficiente <li> Pasta de rascunho é movida incorretamente para outro local <li> O arquivo OnlineBackup.KEK está ausente | <li>Mova a pasta de rascunho ou o local do cache para um volume com espaço livre equivalente a 5-10% do tamanho total dos dados de backup. Consulte as etapas mencionadas [neste artigo](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup) para mover corretamente o local do cache.<li> Certifique-se de que o arquivo OnlineBackup.KEK está presente <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas
* Obtenha mais detalhes sobre [Fazendo backup do Windows Server com o agente de Backup do Microsoft Azure](tutorial-backup-windows-server-to-azure.md).
* Se você precisar restaurar um backup, use este artigo para [restaurar os arquivos para um computador que usa o Windows](backup-azure-restore-windows-server.md).
