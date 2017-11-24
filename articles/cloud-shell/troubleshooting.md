---
title: "Solução de problemas do Azure Cloud Shell | Microsoft Docs"
description: Solucionando problemas do Azure Cloud Shell
services: azure
documentationcenter: 
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: damaerte
ms.openlocfilehash: 59aec1cb6433e90caf683b4bce98c92e9f79fd18
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="troubleshooting-azure-cloud-shell"></a>Solucionando problemas do Azure Cloud Shell

As soluções conhecidas para problemas no Azure Cloud Shell incluem:

## <a name="general-resolutions"></a>Resoluções gerais

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Caixa de diálogo Armazenamento – Erro: 403 RequestDisallowedByPolicy
- **Detalhes**: a criação de uma conta de armazenamento por meio do Cloud Shell é malsucedida devido a uma política do Azure colocada pelo seu administrador. A mensagem de erro incluirá: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Resolução**: contate o administrador do Azure para remover ou atualizar a política do Azure negando a criação de armazenamento.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Caixa de diálogo – Erro: 400 DisallowedOperation
 - **Detalhes**: Ao usar uma assinatura do Azure Active Directory, você não consegue criar armazenamento.
 - **Solução**: use uma assinatura do Azure capaz de criar recursos de armazenamento. Assinaturas do Azure AD não são capazes de criar recursos do Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal de saída – Erro: Falha ao conectar o terminal: o websocket não pode ser estabelecido. Pressione `Enter` para reconectar.
 - **Detalhes**: o Cloud Shell requer a capacidade de estabelecer uma conexão websocket à infraestrutura do Cloud Shell.
 - **Resolução**: verifique se você definiu as configurações de rede para permitir o envio de solicitações HTTPS e solicitações de websocket para domínios em *.console.azure.com.

## <a name="bash-resolutions"></a>Resoluções de bash

### <a name="cannot-run-az-login"></a>Não é possível executar o az login

- **Detalhes**: executar o `az login` não funcionará, já que você já está autenticado com a conta usada para entrar no Portal do Azure ou no Cloud Shell.
- **Resolução**: use a conta que você utilizou para entrar ou sair e autentique-se novamente com a conta desejada do Azure.

### <a name="cannot-run-the-docker-daemon"></a>Não é possível executar o daemon do Docker

- **Detalhes**: O Cloud Shell utiliza um contêiner para hospedar o seu ambiente de shell e, como resultado, a execução do daemon deixa de ser permitida.
- **Resolução**: utilize o [Docker Machine](https://docs.docker.com/machine/overview/), que é instalado por padrão, para gerenciar contêineres do Docker de um host remoto do Docker.

## <a name="powershell-resolutions"></a>Soluções do PowerShell

### <a name="no-home-directory-persistence"></a>Sem persistência do diretório $Home

- **Detalhes**: quaisquer dados gravados por esse aplicativo (como git, vim e outros) a `$Home` não serão persistidos entre sessões do PowerShell.
- **Solução**: no seu perfil do PowerShell, crie um link simbólico para a pasta de aplicativo específico em `clouddrive` para $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>Ctrl+C não faz sair do prompt Cmdlet

- **Detalhes**: ao tentar sair de um prompt de cmdlet, `Ctrl+C` não sai do prompt.
- **Solução**: para sair do prompt, pressione `Ctrl+C` e, em seguida, `Enter`.

### <a name="gui-applications-are-not-supported"></a>Não há suporte para aplicativos GUI

- **Detalhes**: se um usuário inicia um aplicativo GUI, o prompt não retorna. Por exemplo, quando um usuário clona um repositório do GitHub privado com autenticação de dois fatores habilitada, uma caixa de diálogo é exibida para completar a autenticação de dois fatores.
- **Solução**: `Ctrl+C` para sair do comando.

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
