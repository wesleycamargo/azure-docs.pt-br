---
title: Solução de problemas do Azure Cloud Shell | Microsoft Docs
description: Solucionando problemas do Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: damaerte
ms.openlocfilehash: cffa67509690f4c594182fbe8104f0620da56bee
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608943"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Solução de problemas e limitações do Azure Cloud Shell

As resoluções conhecidas para solucionar problemas no Azure Cloud Shell incluem:

## <a name="general-troubleshooting"></a>Solução de problemas gerais

### <a name="early-timeouts-in-firefox"></a>Tempos limites iniciais no FireFox
- **Detalhes**: Cloud Shell utiliza um websocket aberto para passar a entrada/saída para o navegador. FireFox tem políticas predefinidas que podem fechar o websocket prematuramente, causando tempos limites iniciais no Cloud Shell.
- **Resolução**: Abra o FireFox e acesse "sobre: config" na caixa URL. Procure por "network.websocket.timeout.ping.request" e altere o valor de 0 para 10.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Caixa de diálogo Armazenamento – Erro: 403 RequestDisallowedByPolicy
- **Detalhes**: a criação de uma conta de armazenamento por meio do Cloud Shell é malsucedida devido a uma política do Azure colocada pelo seu administrador. A mensagem de erro incluirá: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Resolução**: contate o administrador do Azure para remover ou atualizar a política do Azure negando a criação de armazenamento.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Caixa de diálogo – Erro: 400 DisallowedOperation
 - **Detalhes**: Ao usar uma assinatura do Azure Active Directory, você não consegue criar armazenamento.
 - **Solução**: use uma assinatura do Azure capaz de criar recursos de armazenamento. Assinaturas do Azure AD não são capazes de criar recursos do Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal de saída – Erro: Falha ao conectar o terminal: o websocket não pode ser estabelecido. Pressione `Enter` para reconectar.
 - **Detalhes**: o Cloud Shell requer a capacidade de estabelecer uma conexão websocket à infraestrutura do Cloud Shell.
 - **Resolução**: verifique se você definiu as configurações de rede para permitir o envio de solicitações HTTPS e solicitações de websocket para domínios em *.console.azure.com.

## <a name="bash-troubleshooting"></a>Solução de problemas de Bash

### <a name="cannot-run-the-docker-daemon"></a>Não é possível executar o daemon do Docker

- **Detalhes**: O Cloud Shell utiliza um contêiner para hospedar o seu ambiente de shell e, como resultado, a execução do daemon deixa de ser permitida.
- **Resolução**: utilize o [Docker Machine](https://docs.docker.com/machine/overview/), que é instalado por padrão, para gerenciar contêineres do Docker de um host remoto do Docker.

## <a name="powershell-troubleshooting"></a>Solução de problemas do PowerShell

### <a name="no-home-directory-persistence"></a>Sem persistência do diretório $Home

- **Detalhes**: quaisquer dados gravados por esse aplicativo (como git, vim e outros) a `$Home` não serão persistidos entre sessões do PowerShell.
- **Solução**: no seu perfil do PowerShell, crie um link simbólico para a pasta de aplicativo específico em `clouddrive` para $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Ctrl+C não faz sair do prompt Cmdlet

- **Detalhes**: ao tentar sair de um prompt de cmdlet, `Ctrl+C` não sai do prompt.
- **Solução**: para sair do prompt, pressione `Ctrl+C` e, em seguida, `Enter`.

### <a name="gui-applications-are-not-supported"></a>Não há suporte para aplicativos GUI

- **Detalhes**: se um usuário inicia um aplicativo GUI, o prompt não retorna. Por exemplo, quando um usuário clona um repositório do GitHub privado com autenticação de dois fatores habilitada, uma caixa de diálogo é exibida para completar a autenticação de dois fatores.  
- **Resolução**: feche e reabra o shell.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help -online não abre a página de ajuda

- **Detalhes**: se um usuário digita `Get-Help Find-Module -online`, ele vê uma mensagem de erro, como:`Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **Solução**: copie a url e a abra manualmente no navegador.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Solucionando problemas de gerenciamento remoto de máquinas virtuais do Azure

- **Detalhes**: devido às configurações do Firewall do Windows para WinRM, o usuário pode ver o seguinte erro:`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Solução**: verifique se a VM está em execução. Você pode executar `Get-AzureRmVM -Status` para descobrir o Status da VM.  Em seguida, adicione uma nova regra de firewall na VM remota para permitir conexões de WinRM de qualquer sub-rede; por exemplo,

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 Você pode usar [extensão de script personalizada do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript) para evitar o logon na VM remota para adicionar a nova regra de firewall.
 Você pode salvar o script precedente em um arquivo, por exemplo, `addfirerule.ps1`, e carregá-lo em seu contêiner de armazenamento do Azure.
 Em seguida, tente o seguinte comando:

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` armazena em cache o resultado na unidade do Azure

- **Detalhes**: o resultado de `dir` é armazenado em cache na unidade do Azure.
- **Resolução**: depois de criar ou remover um recurso no modo de exibição da unidade do Azure, execute `dir -force` para atualizar.

## <a name="general-limitations"></a>Limitações gerais
O Azure Cloud Shell tem as seguintes limitações conhecidas:

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

O computador que fornece a sessão do Cloud Shell é temporário e é reciclado após a sessão ficar inativa por 20 minutos. O Cloud Shell exige a montagem de um compartilhamento de arquivos do Azure. Como resultado, sua assinatura deve ser capaz de configurar recursos de armazenamento para acessar o Cloud Shell. Outras considerações incluem:

* Com o armazenamento montado, apenas as modificações no diretório `clouddrive` persistem. No Bash, seu `$Home` diretório também persiste.
* Os compartilhamentos de arquivo do Azure só podem ser montados em sua [região atribuída](persisting-shell-storage.md#mount-a-new-clouddrive).
  * No Bash, execute `env` para localizar sua região definida como `ACC_LOCATION`.
* Os Arquivos do Azure oferece suporte apenas a armazenamento com redundância local e a contas de armazenamento com redundância geográfica.

### <a name="browser-support"></a>Suporte ao navegador

O Cloud Shell oferece suporte às versões mais recentes do Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox e Apple Safari. Não há suporte para o Safari no modo particular.

### <a name="copy-and-paste"></a>Copiar e colar

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Apenas um shell pode estar ativo para um dado usuário

Os usuários podem iniciar apenas um tipo de shell por vez, **Bash** ou **PowerShell**. No entanto, você pode ter várias instâncias de Bash ou PowerShell em execução ao mesmo tempo. Trocar entre Bash ou PowerShell faz com que o Cloud Shell seja reinicializado, o que encerra as sessões existentes.

### <a name="usage-limits"></a>Limites de uso

O Cloud Shell destina-se a casos de uso interativos. Como resultado, quaisquer sessões não interativo e de longa execução são finalizadas sem aviso.

## <a name="bash-limitations"></a>Limitações do Bash

### <a name="user-permissions"></a>Permissões de usuário

As permissões são definidas como usuários regulares sem acesso sudo. Qualquer instalação fora do seu diretório `$Home` não será persistida.

### <a name="editing-bashrc"></a>Edição de .bashrc

Tome cuidado ao editar .bashrc, uma vez que isso pode causar erros inesperados no Cloud Shell.

## <a name="powershell-limitations"></a>Limitações do PowerShell

### <a name="slow-startup-time"></a>Tempo de inicialização lento

O PowerShell no Azure Cloud Shell (Versão prévia) pode levar até 60 segundos para inicializar durante a versão prévia.

### <a name="default-file-location-when-created-from-azure-drive"></a>Local padrão do arquivo quando criado da unidade do Azure:

Usando cmdlets do PowerShell, os usuários não podem criar arquivos na unidade do Azure. Quando os usuários criam novos arquivos usando outras ferramentas como vim ou nano, os arquivos são salvos na pasta C:\Usuários por padrão. 

### <a name="gui-applications-are-not-supported"></a>Não há suporte para aplicativos GUI

Se o usuário executar um comando que cria uma caixa de diálogo do Windows, como `Connect-AzureAD` ou `Connect-AzureRmAccount`, vê-se uma mensagem de erro como a seguinte: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

## <a name="gdpr-compliance-for-cloud-shell"></a>Conformidade GDPR para o Cloud Shell

O Azure Cloud Shell leva seus dados pessoais a sério, os dados capturados e armazenados pelo serviço do Azure Cloud Shell são usados para fornecer padrões para a sua experiência, como o seu shell usado mais recentemente, tamanho da fonte preferencial, tipo de fonte preferencial e detalhes de compartilhamento de arquivos que faça clouddrive. Se desejar exportar ou excluir esses dados, incluímos as instruções a seguir.

### <a name="export"></a>Exportação
Para **exportar** as configurações de usuário Cloud Shell salvas para você, como o shell preferencial, o tamanho da fonte e o tipo de fonte, execute os seguintes comandos.

1. Inicie o Bash no Cloud Shell
2. Execute os seguintes comandos:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
```

### <a name="delete"></a>Excluir
Para **excluir** as configurações de usuário Cloud Shell salvas para você, como o shell preferencial, o tamanho da fonte e o tipo de fonte, execute os seguintes comandos. Na próxima vez que você iniciar o Cloud Shell você deverá carregar um compartilhamento de arquivos novamente. 

O compartilhamento de arquivos do Azure real não será excluído se você excluir as configurações de usuário, vá para arquivos do Azure para concluir essa ação.

1. Inicie o Bash no Cloud Shell
2. Execute os seguintes comandos:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
```
