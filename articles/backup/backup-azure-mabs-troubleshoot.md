---
title: Solucionar problemas do Servidor de Backup do Azure | Microsoft Docs
description: "Solucionar problemas de instalação, registro de Servidor de Backup do Azure e backup e restauração de cargas de trabalho do aplicativo."
services: backup
documentationcenter: 
author: pvrk
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;
ms.openlocfilehash: e9517672138a4ea7577af1295dea13771733983e
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2017
---
# <a name="troubleshoot-azure-backup-server"></a>Solucionar problemas de Servidor de Backup do Azure

Use as informações nas tabelas a seguir para solucionar problemas de erros encontrados durante o uso do Servidor de Backup do Azure.

## <a name="invalid-vault-credentials-provided"></a>Credenciais do cofre fornecidas inválidas 

Para resolver esse problema, siga [essas etapas de solução de problemas](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues).

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>A operação do agente falhou devido a um erro de comunicação com o serviço Coordenador de Agentes do DPM no Servidor 

Para resolver esse problema, siga [essas etapas de solução de problemas](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues). 

## <a name="setup-could-not-update-registry-metadata"></a>A instalação não pôde atualizar os metadados do Registro

Para resolver esse problema, siga [essas etapas de solução de problemas](https://docs.microsoft.com/azure/backup/backup-azure-mabs-troubleshoot#installation-issues).




## <a name="installation-issues"></a>Problemas de instalação

| Operação | Detalhes do erro | Solução alternativa |
|-----------|---------------|------------|
|Instalação | A instalação não pôde atualizar os metadados do Registro. Essa falha de atualização pode levar ao uso excessivo do consumo de armazenamento. Para evitar isso, atualize a entrada do Registro da Filtragem ReFS. | Ajuste a chave do Registro **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Defina o valor Dword como 1. |
|Instalação | A instalação não pôde atualizar os metadados do Registro. Essa falha de atualização pode levar ao uso excessivo do consumo de armazenamento. Para evitar isso, atualize a entrada do Registro da SnapOptimization do Volume. | Crie a chave do Registro **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** com um valor de cadeia de caracteres vazia. |
## <a name="registration-and-agent-related-issues"></a>Problemas relacionados ao registro e ao agente
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Registrando em um cofre | Credenciais do cofre fornecidas inválidas. O arquivo está corrompido ou não possui as credenciais mais recentes associadas ao serviço de recuperação. | Para corrigir esse erro:  <ul><li> Faça o download do arquivo de credenciais mais recentes do cofre e tente novamente. <br>(OU)</li> <li> Se a ação anterior não funcionar, tente fazer o download das credenciais para um diretório local diferente ou criar um novo cofre. <br>(OU)</li> <li> Tente atualizar as configurações de data e hora conforme indicado [neste blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>(OU)</li> <li> Verifique para ver se c:\windows\temp tem mais de 65000 arquivos. Mover arquivos obsoletos para outro local ou exclua os itens na pasta Temp. <br>(OU)</li> <li> Verificar o status dos certificados. <br> a. Abra **Gerenciar Certificados de Computador** (no painel de controle). <br> b. Expandir o nó **Pessoal** e seus **Certificados** de nó filho.<br> c.  Remover o certificado **Ferramentas do Microsoft Azure**. <br> d. Repetir o registro no cliente de Backup do Azure. <br> (OU) </li> <li> Verificar se alguma política de grupo está em vigor. </li></ul> |
| Enviar agentes por push para servidores protegidos | A operação do agente falhou devido a um erro de comunicação com o serviço Coordenador de Agentes do DPM em \<ServerName>. | **Se a ação recomendada exibida no produto não funcionar, realize as seguintes etapas**: <ul><li> Se você estiver anexando um computador de um domínio não confiável, siga [estas](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx) etapas. <br> (OU) </li><li> Se você estiver anexando um computador de um domínio confiável, solucione os problemas usando as etapas descritas [neste blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>(OU)</li><li> Tente desabilitar o antivírus como uma etapa de solução de problemas. Se isso resolver o problema, modifique as configurações de antivírus conforme sugerido [neste artigo](https://technet.microsoft.com/library/hh757911.aspx).</li></ul> |
| Enviar agentes por push para servidores protegidos | As credenciais especificadas para o servidor são inválidas. | **Se a ação recomendada exibida no produto não funcionar, realize as seguintes etapas**: <br> Tente instalar o agente de proteção manualmente no servidor de produção, como especificado [neste artigo](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| O Agente de Backup do Azure não pôde se conectar ao serviço de Backup do Azure (ID: 100050) | O Agente de Backup do Azure não pôde se conectar ao serviço de Backup do Azure. | **Se a ação recomendada exibida no produto não funcionar, realize as seguintes etapas**: <br>1. Execute o comando a seguir de um prompt elevado: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Isso abrirá a janela do Internet Explorer. <br/> 2. Acesse **Ferramentas** > **Opções de Internet** > **Conexões** > **Configurações de LAN**. <br/> 3. Verifique as configurações de proxy para a conta do sistema. Defina o IP e a porta do proxy. <br/> 4. Feche o Internet Explorer.|
| Falha na instalação do Agente de backup do Azure | Falha na instalação dos Serviços de Recuperação do Microsoft Azure. Todas as alterações feitas ao sistema pela instalação dos Serviços de Recuperação do Microsoft Azure foram revertidas. (ID: 4024) | Instalar o Agente do Azure manualmente.


## <a name="configuring-protection-group"></a>Configurar grupo de proteção
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Configurar grupos de proteção | O DPM não pôde enumerar o componente do aplicativo no computador protegido (nome do computador protegido). | Selecione **Atualizar** na tela da interface do usuário configurar grupo de proteção no nível de componente/fonte de dados relevante. |
| Configurar grupos de proteção | Não é possível configurar a proteção | Se o servidor protegido é um SQL Server, verifique se as permissões da função sysadmin foram fornecidas para a conta do sistema (NTAuthority\System) no computador protegido conforme descrito [neste artigo](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Configurar grupos de proteção | Não há espaço livre suficiente no pool de armazenamento para esse grupo de proteção. | Os discos que são adicionados ao pool de armazenamento [não devem conter uma partição](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Exclua todos os volumes existentes nos discos. Em seguida, adicione-os ao pool de armazenamento.|
| Alteração da política |Não foi possível modificar a política de backup. Erro: a operação atual falhou devido a um erro de serviço interno [0x29834]. Repita a operação após algum tempo ter passado. Se o problema persistir, contate o Suporte da Microsoft. |**Causa:**<br/>Esse erro ocorre em três condições: quando as configurações de segurança estiverem habilitadas, quando você tentar reduzir o período de retenção para abaixo dos valores mínimos especificados anteriormente, e quando você estiver usando uma versão sem suporte. (Versões sem suporte são aquelas abaixo da versão 2.0.9052 do Servidor de Backup do Microsoft Azure e atualização do Servidor de Backup do Azure 1). <br/>**Ação recomendada:**<br/> Para continuar com atualizações relacionadas à política, defina o período de retenção acima do período mínimo de retenção especificado. (O período mínimo de retenção é de sete dias para diariamente, quatro semanas para semanal, três semanas para mensal ou um ano para anual.) <br><br>Opcionalmente, outra abordagem preferencial será atualizar o agente de backup e o Servidor de Backup do Azure para utilizar todas as atualizações de segurança. |

## <a name="backup"></a>Backup
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Backup | A réplica está inconsistente | Verifique se a opção de verificação de consistência automática no Assistente de grupo de proteção está ativada. Para obter mais informações sobre as causas de inconsistência de réplica e sugestões relevantes, consulte o artigo Microsoft TechNet [A réplica está inconsistente](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> No caso de backup de BMR/estado do sistema, verifique se o Backup do Windows Server está instalado no servidor protegido.</li><li> Verifique se há problemas relacionados ao espaço no pool de armazenamento do DPM no Servidor de Backup do Azure/DPM, e alocar armazenamento conforme necessário.</li><li> Verificar o estado do Serviço de Cópias de Sombra de Volume no servidor protegido. Se ele estiver em um estado desabilitado, defina-o para iniciar manualmente. Inicie o serviço no servidor. Em seguida, volte para o console do Servidor de Backup do Azure/DPM e inicie a sincronização com o trabalho de verificação de consistência.</li></ol>|
| Backup | Ocorreu um erro inesperado durante a execução do trabalho. O dispositivo não está pronto. | **Se a ação recomendada exibida no produto não funcionar, realize as seguintes etapas:** <br> <ul><li>Defina o Espaço de Armazenamento de Cópia de Sombra para ilimitado nos itens no grupo de proteção e execute a verificação de consistência.<br></li> (OU) <li>Tente excluir o grupo de proteção existente e criar vários grupos novos. Cada novo grupo de proteção deve ter um item individual dentro dele.</li></ul> |
| Backup | Se você estiver fazendo backup somente do estado do sistema, verifique se há espaço livre suficiente no computador protegido para armazenar o backup do estado do sistema. | <ol><li>Verifique se o Backup do Windows Server está instalado no computador protegido.</li><li>Verifique se há espaço suficiente no computador protegido para o estado do sistema. A maneira mais fácil de verificar isso é acessar o computador protegido, abrir o Backup do Windows Server, clicar nas seleções e, em seguida, selecionar a BMR. A interface do usuário lhe mostrará quanto espaço é necessário. Abra **WSB** > **Backup local** > **Agendamento de backup** > **Selecionar configuração de backup**  >  **Servidor completo** (o tamanho é exibido). Use esse tamanho para verificação.</li></ol>
| Backup | Falha na criação de ponto de recuperação online | Se a mensagem de erro informar que "O Microsoft Azure Backup Agent não pôde criar um instantâneo do volume selecionado", tente aumentar o espaço no volume de ponto de recuperação e de réplica.
| Backup | Falha na criação de ponto de recuperação online | Se a mensagem de erro disser "O Microsoft Azure Backup Agent não pode se conectar ao serviço OBEngine", verifique se o OBEngine consta na lista de serviços em execução no computador. Se o serviço OBEngine não estiver em execução, use o comando "net start OBEngine" para iniciar o serviço OBEngine.
| Backup | Falha na criação de ponto de recuperação online | Se a mensagem de erro disser que "A frase secreta de criptografia para este servidor não está definida. Configure uma senha de criptografia", tente configurar uma frase secreta de criptografia. Se falhar, execute as seguintes etapas: <br> <ol><li>Verifique se o local de rascunho existe. Esse é o local que é mencionado no registro **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, com o nome **ScratchLocation** deve existir.</li><li> Se houver um local temporário, tente registrar novamente usando a frase secreta antiga. *Sempre que você configurar uma frase secreta de criptografia, salve-a em um local seguro.*</li><ol>
| Backup | Falha de backup de BMR | Se o tamanho de BMR for grande, mova alguns arquivos do aplicativo para a unidade do sistema operacional e tente novamente. |
| Backup | A opção de proteger novamente uma VM do VMware em um novo Servidor de Backup do Azure não aparece como disponível para adicionar. | As propriedades do VMware são apontadas para uma instância antiga e obsoleta do Servidor de Backup do Azure. Para resolver esse problema:<br><ol><li>No VCenter (equivalente do SC-VMM), vá para a guia **Resumo** e então **Atributos Personalizados**.</li>  <li>Exclua o antigo nome do Servidor de Backup do Azure do valor **DPMServer**.</li>  <li>Volte para o novo Servidor de Backup do Azure e modifique o PG.  Depois de selecionar o botão **Atualizar**, a VM é exibida com uma caixa de seleção conforme disponível para adicionar para proteção.</li></ol> |
| Backup | Erro ao acessar pastas/arquivos compartilhados | Tente modificar as configurações de antivírus conforme sugerido no artigo da TechNet [Executar um software antivírus no servidor DPM](https://technet.microsoft.com/library/hh757911.aspx).|
| Backup | Falha em trabalhos de criação de ponto de recuperação online para VM do VMware. O DPM encontrou um erro de VMware ao tentar obter informações de ChangeTracking. ErrorCode - FileFaultFault (ID 33621 ) |  <ol><li> Redefinir o CTK no VMware para as VMs afetadas.</li> <li>Verificar se o disco independente não está em vigor no VMware.</li> <li>Interromper a proteção para as VMs afetadas e proteger novamente com o botão **Atualizar**. </li><li>Executar uma CC para as VMs afetadas.</li></ol>|


## <a name="change-passphrase"></a>Alterar frase secreta
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Alterar frase secreta |O PIN de Segurança que foi inserido está incorreto. Forneça o PIN de Segurança correto para concluir esta operação. |**Causa:**<br/> Esse erro ocorre quando você insere um PIN de segurança inválido ou expirado enquanto você estiver executando uma operação crítica (como alterar uma senha). <br/>**Ação recomendada:**<br/> Para concluir a operação, você deve inserir um PIN de Segurança válido. Para obter o PIN, entre no portal do Azure e acesse o cofre dos Serviços de Recuperação. Em seguida, vá para **Configurações** > **Propriedades** > **Gerar PIN de segurança**. Use esse PIN para alterar a frase secreta. |
| Alterar frase secreta |Falha na operação. ID: 120002 |**Causa:**<br/>Esse erro ocorre quando as configurações de segurança estão habilitadas, ou quando você tenta alterar a frase secreta quando você está usando uma versão sem suporte.<br/>**Ação recomendada:**<br/> Para alterar a senha, você deve primeiro atualizar o agente de backup para a versão mínima, que é 2.0.9052. Você também precisa atualizar o Servidor de Backup do Azure para a versão mínima de atualização 1 e, em seguida, inserir um PIN de segurança válido. Para obter o PIN, entre no portal do Azure e acesse o cofre dos Serviços de Recuperação. Em seguida, vá para **Configurações** > **Propriedades** > **Gerar PIN de segurança**. Use esse PIN para alterar a frase secreta. |


## <a name="configure-email-notifications"></a>Configurar notificações por email
| Operação | Detalhes do erro | Solução alternativa |
| --- | --- | --- |
| Configurar as notificações de e-mail usando uma conta do Office 365 |ID do Erro: 2013| **Causa:**<br> Tentativa de usar a conta do Office 365 <br>**Ação recomendada:**<ol><li> A primeira coisa que se deve assegurar é que "Permitir retransmissão anônima em um conector de recebimento" para o servidor DPM esteja configurada no Exchange. Para obter mais informações sobre como configurar isso, consulte [Permitir retransmissão anônima em um conector de recebimento](http://technet.microsoft.com/en-us/library/bb232021.aspx) no TechNet.</li> <li> Se você não pode usar uma retransmissão SMTP interna e precisa configurar usando o servidor do Office 365, você pode configurar o IIS para ser uma retransmissão. Configure o servidor DPM para [retransmitir o SMTP para o O365 usando o IIS](https://technet.microsoft.com/en-us/library/aa995718(v=exchg.65).aspx).<br><br> **IMPORTANTE:** Certifique-se de usar o formato user@domain.com e *não* domínio\usuário.<br><br><li>Aponte o DPM para usar o nome do servidor local como servidor SMTP, porta 587. Em seguida, aponte para o e-mail de usuário de onde os e-mails devem vir.<li> O nome de usuário e a senha na página de instalação de SMTP do DPM devem ser para uma conta de domínio no domínio em que o DPM está. </li><br> **OBSERVAÇÃO**: quando você estiver alterando o endereço do servidor SMTP, faça a alteração para as novas configurações, feche a caixa de configurações e a abra novamente para se certificar que ela reflita o novo valor.  Simplesmente alterar e testar pode nem sempre fazer com que as novas configurações tenham efeito, então testar assim é a melhor prática.<br><br>A qualquer momento durante esse processo, você pode limpar essas configurações fechando o console do DPM e editando as seguintes chaves do registro: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Excluir SMTPPassword e as chaves SMTPUserName**. Você pode adicioná-las novamente na interface do usuário quando você iniciá-lo novamente.
