---
title: Solucionar problemas do Agente de Backup do Azure
description: Solucionar problemas de instalação e registro do Azure Backup Agent
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 12/4/2017
ms.author: saurse
ms.openlocfilehash: 89a39f6189367f91248b3868b1e1cb9f6abf0407
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228383"
---
# <a name="troubleshoot-azure-backup-agent-configuration-and-registration-issues"></a>Solucionar problemas de configuração e registro do Azure Backup Agent
## <a name="recommended-steps"></a>Etapas recomendadas
Consulte as ações recomendadas nas tabelas a seguir para resolver os erros que podem ocorrer durante a configuração ou o registro do Azure Backup Agent.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Credenciais do cofre fornecidas inválidas. O arquivo está corrompido ou não possui as credenciais mais recentes associadas ao serviço de recuperação.
| Detalhes do erro | Possíveis causas | Ações recomendadas |
| ---     | ---     | ---    |
| **Erro** </br> *Credenciais do cofre inválidas fornecidas. O arquivo está corrompido ou não possui as credenciais mais recentes associadas ao serviço de recuperação. (ID: 34513)* | <ul><li> As credenciais do cofre são inválidas (ou seja, elas são baixadas mais de 48 horas antes da hora de registro).<li>O Azure Backup Agent não é capaz de fazer o download de um arquivo temporário para a pasta Temp do Windows. <li>As credenciais do cofre estão em um local de rede. <li>TLS 1.0 está desabilitado<li> Um servidor proxy configurado está bloqueando a conexão. <br> |  <ul><li>Faça o download das novas credenciais do cofre.<li>Vá a **Opções da Internet** > **Segurança** > **Internet**. Em seguida, selecione **Nível personalizado** e role até encontrar a seção de download de arquivo. Em seguida, selecione **Habilitar**.<li>Também pode ser necessário adicionar o site aos [sites confiáveis](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Altere as configurações para usar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy. <li> Sincronize a data e a hora com as de seu computador.<li>Vá para C:/Windows/Temp e verifique se há mais de 60.000 ou 65.000 arquivos com a extensão .tmp. Se houver, exclua esses arquivos.<li>É possível testar isso executando o pacote SDP no servidor. Se você receber um erro informando que o download de arquivos não é permitido, é muito provável que haja um grande número de arquivos no diretório C:/Windows/Temp.<li>Certifique-se de que tenha o .NET Framework 4.6.2 instalado. <li>Se você desabilitou o TLS 1.0 devido à conformidade de PCI, consulte esta [página de solução de problemas](https://support.microsoft.com/help/4022913). <li>Se você tiver um antivírus instalado no servidor, exclua os seguintes arquivos da verificação de antivírus: <ul><li>CBengine.exe<li>CSC.exe, relacionado ao .NET Framework. Há um CSC.exe para cada versão .NET instalada no servidor. Exclua os arquivos CSC.exe vinculados a todas as versões do .NET Framework no servidor afetado. <li>Pasta de Rascunho ou local do cache. <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>O Agente de Serviços de Recuperação do Microsoft Azure não pôde se conectar ao Backup do Microsoft Azure

| Detalhes do erro | Possíveis causas | Ações recomendadas |
| ---     | ---     | ---    |
| **Erro** </br><ol><li>*O Agente de Serviços de Recuperação do Microsoft Azure não pôde se conectar ao Backup do Microsoft Azure. (ID: 100050) Verifique as configurações de rede e assegure-se de que é possível conectar-se à Internet*<li>*(407) Autenticação de Proxy Obrigatória* |Proxy bloqueando a conexão. |  <ul><li>Vá a **Opções da Internet** > **Segurança** > **Internet**. Em seguida, selecione **Nível personalizado** e role até encontrar a seção de download de arquivo. Selecione **Habilitar**.<li>Também pode ser necessário adicionar o site aos [sites confiáveis](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Altere as configurações para usar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy. <li>Se você tiver um antivírus instalado no servidor, exclua os seguintes arquivos da verificação de antivírus. <ul><li>CBEngine.exe (em vez de dpmra.exe).<li>CSC.exe (relacionado ao .NET Framework). Há um CSC.exe para cada versão .NET instalada no servidor. Exclua os arquivos CSC.exe vinculados a todas as versões do .NET Framework no servidor afetado. <li>Pasta de Rascunho ou local do cache. <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Falha ao configurar a chave de criptografia para backups seguros

| Detalhes do erro | Possíveis causas | Ações recomendadas |
| ---     | ---     | ---    |      
| **Erro** </br>*Falha ao definir a chave de criptografia para backups seguros A ativação não foi bem-sucedida, mas a frase secreta de criptografia foi salva no seguinte arquivo*. |<li>O servidor já está registrado com outro cofre.<li>Durante a configuração, a frase secreta foi corrompida| Cancele o registro do servidor do cofre e registre-se novamente com uma nova frase secreta.

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>A ativação não foi concluída com êxito. A operação atual falhou devido a um erro de serviço interno [0x1FC07]

| Detalhes do erro | Possíveis causas | Ações recomendadas |
| ---     | ---     | ---    |          
| **Erro** </br><ol><li>*A ativação não foi concluída com êxito. A operação atual falhou devido a um erro de serviço interno [0x1FC07]. Repita a operação após algum tempo. Se o problema persistir, contate o suporte da Microsoft* | <li> A pasta de Rascunho está localizada em um volume que não possui espaço suficiente. <li> A pasta de Rascunho é movida incorretamente para outro local. <li> O arquivo OnlineBackup.KEK está ausente. | <li>Atualize para a [versão mais recente](http://aka.ms/azurebackup_agent) do MARS Agent.<li>Mova a pasta de rascunho ou o local do cache para um volume com espaço livre equivalente a 5-10% do tamanho total dos dados de backup. Para mover corretamente o local do cache, consulte as etapas em [Perguntas sobre o Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Certifique-se de que o arquivo OnlineBackup.KEK está presente. <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.
  
## <a name="error-34506-the-encryption-passphrase-stored-on-this-computer-is-not-correctly-configured"></a>Error 34506. A senha de criptografia armazenada neste computador não está configurada corretamente

| Detalhes do erro | Possíveis causas | Ações recomendadas |
| ---     | ---     | ---    |          
| **Erro** </br><ol><li>*Erro 34506. A senha de criptografia armazenada neste computador não está configurada corretamente*. | <li> A pasta de Rascunho está localizada em um volume que não possui espaço suficiente. <li> A pasta de Rascunho é movida incorretamente para outro local. <li> O arquivo OnlineBackup.KEK está ausente. | <li>Atualize para a [versão mais recente](http://aka.ms/azurebackup_agent) do MARS Agent.<li>Mova a pasta de rascunho ou o local do cache para um volume com espaço livre equivalente a 5-10% do tamanho total dos dados de backup. Para mover corretamente o local do cache, consulte as etapas em [Perguntas sobre o Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Certifique-se de que o arquivo OnlineBackup.KEK está presente. <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.  

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas
* Obtenha mais detalhes sobre [como fazer backup do Windows Server com o Azure Backup Agent](tutorial-backup-windows-server-to-azure.md).
* Se você precisar restaurar um backup, use este artigo para [restaurar os arquivos para um computador que usa o Windows](backup-azure-restore-windows-server.md).
