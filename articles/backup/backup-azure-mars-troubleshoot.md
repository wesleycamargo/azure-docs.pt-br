---
title: Solucionar problemas do Agente de Backup do Azure
description: Solucionar problemas de instalação e registro do Azure Backup Agent
services: backup
author: saurabhsensharma
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/25/2018
ms.author: saurse
ms.openlocfilehash: 2c8978cfba8fc56d4dbc565cb3a91c75d9d54679
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700188"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent-issues"></a>Solucionar problemas do agente do Microsoft Azure Recovery Services (MARS)
## <a name="recommended-steps"></a>Etapas recomendadas
Consulte este artigo para resolver erros durante a configuração, registro, backup e restauração usando o Agente MARS.

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>Credenciais do cofre fornecidas inválidas. O arquivo está corrompido ou não possui as credenciais mais recentes associadas ao serviço de recuperação.
| Detalhes do erro | Possíveis causas | Ações recomendadas |
| ---     | ---     | ---    |
| **Erro** </br> *Credenciais do cofre inválidas fornecidas. O arquivo está corrompido ou não possui as credenciais mais recentes associadas ao serviço de recuperação. (ID: 34513)* | <ul><li> As credenciais do cofre são inválidas (ou seja, elas são baixadas mais de 48 horas antes da hora de registro).<li>MARS Agent não consegue fazer o download de arquivos para o diretório Temp do Windows. <li>As credenciais do cofre estão em um local de rede. <li>TLS 1.0 está desabilitado<li> Um servidor proxy configurado está bloqueando a conexão. <br> |  <ul><li>Faça o download das novas credenciais do cofre.<li>Vá a **Opções da Internet** > **Segurança** > **Internet**. Em seguida, selecione **Nível personalizado** e role até encontrar a seção de download de arquivo. Em seguida, selecione **Habilitar**.<li>Também pode ser necessário adicionar o site aos [sites confiáveis](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements).<li>Altere as configurações para usar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy. <li> Sincronize a data e a hora com as de seu computador.<li>Vá para C:/Windows/Temp e verifique se há mais de 60.000 ou 65.000 arquivos com a extensão .tmp. Se houver, exclua esses arquivos.<li>Você pode verificar isso executando o pacote SDP no servidor. Se você receber um erro informando que os downloads de arquivos não são permitidos, é provável que haja um grande número de arquivos no diretório C: / Windows / Temp directory.<li>Certifique-se de que tenha o .NET Framework 4.6.2 instalado. <li>Se você desabilitou o TLS 1.0 devido à conformidade de PCI, consulte esta [página de solução de problemas](https://support.microsoft.com/help/4022913). <li>Se você tiver um antivírus instalado no servidor, exclua os seguintes arquivos da verificação de antivírus: <ul><li>CBengine.exe<li>CSC.exe, relacionado ao .NET Framework. Há um CSC.exe para cada versão .NET instalada no servidor. Exclua os arquivos CSC.exe vinculados a todas as versões do .NET Framework no servidor afetado. <li>Pasta de Rascunho ou local do cache. <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.

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
|---------|---------|---------|
|**Erro** </br><ol>*A ativação não foi concluída com êxito. A operação atual falhou devido a um erro de serviço interno [0x1FC07]. Repita a operação após algum tempo. Se o problema persistir, contate o suporte da Microsoft*      | <li> A pasta de Rascunho está localizada em um volume que não possui espaço suficiente. <li> A pasta de Rascunho é movida incorretamente para outro local. <li> O arquivo OnlineBackup.KEK está ausente.         | <li>Atualize para a [versão mais recente](http://aka.ms/azurebackup_agent) do MARS Agent.<li>Mova a pasta temporária ou o local do cache para um volume com espaço livre igual a 5-10% do tamanho total dos dados de backup. Para mover corretamente o local do cache, consulte as etapas em [Perguntas sobre o Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Certifique-se de que o arquivo OnlineBackup.KEK está presente. <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="error-34506-the-encryption-passphrase-stored-on-this-computer-is-not-correctly-configured"></a>Error 34506. A senha de criptografia armazenada neste computador não está configurada corretamente

| Detalhes do erro | Possíveis causas | Ações recomendadas |
|---------|---------|---------|
|**Erro** </br><ol>*Erro 34506. A senha de criptografia armazenada neste computador não está configurada corretamente*.    | <li> A pasta de Rascunho está localizada em um volume que não possui espaço suficiente. <li> A pasta de Rascunho é movida incorretamente para outro local. <li> O arquivo OnlineBackup.KEK está ausente.        | <li>Atualize para a [versão mais recente](http://aka.ms/azurebackup_agent) do MARS Agent.<li>Mova a pasta de rascunho ou o local do cache para um volume com espaço livre equivalente a 5-10% do tamanho total dos dados de backup. Para mover corretamente o local do cache, consulte as etapas em [Perguntas sobre o Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Certifique-se de que o arquivo OnlineBackup.KEK está presente. <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |

## <a name="backups-do-not-run-as-per-schedule"></a>Os backups não são executados conforme o cronograma
Execute as etapas a seguir quando os backups agendados não forem acionados automaticamente, enquanto os backups manuais funcionam sem problemas. 
 
<li>Certifique-se de que o PowerShell 3.0 ou superior esteja instalado no servidor. Para verificar a versão do PowerShell, execute o seguinte comando e verifique se o número da versão *Maior* é igual ou maior que 3.

`$PSVersionTable.PSVersion`
<li>Assegure-se de que o caminho a seguir seja parte da variável de ambiente *PSMODULEPATH*.

`<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`
<li>Se a diretiva de execução do powershell do *LocalMachine* estiver definida como restrita, o cmdlet do powershell que aciona a tarefa de backup poderá falhar. Execute os seguintes comandos no modo elevado para verificar e definir a política de execução para *Irrestrito* ou *RemotoSignificado*

`PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

`PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`
<li>Vá ao Painel de Controle -> Ferramentas Administrativas -> Agendador de Tarefas -> expandir Microsoft -> selecione Backup Online
<li>Clique duas vezes na tarefa 'Microsoft-OnlineBackup' e vá para a guia 'Triggers'.
<li>Certifique-se de que o "Status" da tarefa esteja definido como "Ativado". Se não, clique em 'Editar' e selecione a caixa de seleção 'Ativado'
<li>Navegue até a seção *Opções de segurança* da guia *Geral*
<li>Assegure-se de que a conta de usuário selecionada para executar a tarefa seja *SYSTEM* ou o grupo de Administradores Locais no servidor

> [!TIP]
> Recomenda-se reinicializar o servidor após executar as etapas acima para garantir que as alterações feitas sejam aplicadas de forma consistente


## <a name="troubleshooting-restore-issues"></a>Solução de problemas de restauração

### <a name="failure-to-mount-the-recovery-volume-during-recovery-of-files-and-folders"></a>Falha ao montar o volume de recuperação durante a recuperação de arquivos e pastas
Se o Backup do Azure não tiver montado com êxito o volume de recuperação mesmo após os diversos minutos de clicar em **Montar** ou falha ao montar o volume de recuperação com um ou mais erros, siga as etapas abaixo para iniciar a recuperação normalmente.

1.  Cancele o processo de montagem em andamento caso ele esteja sendo executado por vários minutos.

2.  Verifique se você está na versão mais recente do agente de Backup do Azure. Para obter as informações de versão do agente de Backup do Azure, clique em **Sobre o Agente dos Serviços de Recuperação do Microsoft Azure** no painel **Ações** do console de Backup do Microsoft Azure e verifique se o número de **Versão** é igual a ou maior do que a versão mencionada [neste artigo](https://go.microsoft.com/fwlink/?linkid=229525). Você pode baixar a versão mais recente [aqui](https://go.microsoft.com/fwLink/?LinkID=288905)

3.  Vá para **Gerenciador de Dispositivos** -> **Controladores de Armazenamento** e verifique se você pode localizar o **Iniciador do Microsoft iSCSI**. Se você puder localizá-lo, vá diretamente para a etapa 7 abaixo. 

4.  Se você não puder localizar o serviço Iniciador do Microsoft iSCSI conforme mencionado na etapa 3, verifique se você pode encontrar uma entrada em **Gerenciador de Dispositivos** -> **Controladores de Armazenamento** chamada **Dispositivo Desconhecido** com a ID de Hardware **ROOT\ISCSIPRT**.

5.  Clique com o botão direito do mouse em **Dispositivo Desconhecido** e selecione **Atualizar Software de Driver**.

6.  Atualize o driver ao selecionar a opção para **Pesquisar automaticamente software de driver atualizado**. A conclusão da atualização deve alterar o **Dispositivo Desconhecido** para o **Iniciador do Microsoft iSCSI** conforme mostrado abaixo. 

    ![Criptografia](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Vá para **Gerenciador de Tarefas** -> **Serviços (Local)** -> **Serviço Iniciador do Microsoft iSCSI**. 

    ![Criptografia](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Reinicie o serviço Iniciador iSCSI da Microsoft clicando com o botão direito do mouse no serviço, clicando em Parar e clicando com o botão direito do mouse novamente e clicando em **Iniciar**.

9.  Repita a recuperação usando a Restauração Instantânea. 

Se a recuperação ainda falhar, reinicialize o servidor/cliente. Se uma reinicialização não for desejável ou a recuperação ainda falhar mesmo após a reinicialização do servidor, tente recuperar de uma máquina alternativa seguindo as etapas listadas em [neste artigo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas
* Obtenha mais detalhes sobre [como fazer backup do Windows Server com o Azure Backup Agent](tutorial-backup-windows-server-to-azure.md).
* Se você precisar restaurar um backup, use este artigo para [restaurar os arquivos para um computador que usa o Windows](backup-azure-restore-windows-server.md).
