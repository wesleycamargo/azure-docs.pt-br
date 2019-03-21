---
title: Limitações do Azure Cloud Shell | Microsoft Docs
description: Visão geral das limitações do Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 8fd88221818d28c227c33719c03e522e815a408b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245736"
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitações do Azure Cloud Shell

O Azure Cloud Shell tem as seguintes limitações conhecidas:

## <a name="general-limitations"></a>Limitações gerais

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

O computador que fornece a sessão do Cloud Shell é temporário e é reciclado após a sessão ficar inativa por 20 minutos. O Cloud Shell exige a montagem de um compartilhamento de arquivos do Azure. Como resultado, sua assinatura deve ser capaz de configurar recursos de armazenamento para acessar o Cloud Shell. Outras considerações incluem:

* Com o armazenamento montado, apenas as modificações no diretório `$Home` persistem.
* Os compartilhamentos de arquivo do Azure só podem ser montados em sua [região atribuída](persisting-shell-storage.md#mount-a-new-clouddrive).
  * No Bash, execute `env` para localizar sua região definida como `ACC_LOCATION`.

### <a name="browser-support"></a>Suporte ao navegador

O Cloud Shell oferece suporte às versões mais recentes do Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox e Apple Safari. Não há suporte para o Safari no modo particular.

### <a name="copy-and-paste"></a>Copiar e colar

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Apenas um shell pode estar ativo para um dado usuário

Os usuários podem iniciar apenas um tipo de shell por vez, **Bash** ou **PowerShell**. No entanto, você pode ter várias instâncias de Bash ou PowerShell em execução ao mesmo tempo. Trocar entre Bash ou PowerShell por meio do menu faz com que o Cloud Shell seja reinicializado, que encerra as sessões existentes. Como alternativa, você pode executar o bash dentro do PowerShell digitando `bash`, e você pode executar o PowerShell dentro de bash digitando `pwsh`.

### <a name="usage-limits"></a>Limites de uso

O Cloud Shell destina-se a casos de uso interativos. Como resultado, quaisquer sessões não interativo e de longa execução são finalizadas sem aviso.

## <a name="bash-limitations"></a>Limitações do Bash

### <a name="user-permissions"></a>Permissões de usuário

As permissões são definidas como usuários regulares sem acesso sudo. Qualquer instalação fora do seu diretório `$Home` não será persistida.

### <a name="editing-bashrc-or-profile"></a>Editar. bashrc ou $PROFILE

Tome cuidado ao editar. bashrc ou arquivo de $PROFILE do PowerShell, isso pode causar erros inesperados no Cloud Shell.

## <a name="powershell-limitations"></a>Limitações do PowerShell

### <a name="azuread-module-name"></a>Nome do módulo `AzureAD`

Atualmente, o nome do módulo `AzureAD` é `AzureAD.Standard.Preview`, o módulo fornece a mesma funcionalidade.

### <a name="sqlserver-module-functionality"></a>Funcionalidade do módulo `SqlServer`

O módulo `SqlServer` incluído no Cloud Shell tem suporte apenas de pré-lançamento para o PowerShell Core. Especificamente, `Invoke-SqlCmd` ainda não está disponível.

### <a name="default-file-location-when-created-from-azure-drive"></a>Local padrão do arquivo quando criado da unidade do Azure:

Usando cmdlets do PowerShell, os usuários não é podem criar arquivos do Azure: unidade. Quando os usuários criam novos arquivos usando outras ferramentas como vim ou nano, os arquivos são salvos em `$HOME` por padrão. 

### <a name="gui-applications-are-not-supported"></a>Não há suporte para aplicativos GUI

Se o usuário executa um comando que cria uma caixa de diálogo do Windows, vê-se uma mensagem de erro, como: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="large-gap-after-displaying-progress-bar"></a>Intervalo grande depois de exibir a barra de progresso

Se o usuário executa uma ação que exibe uma barra de progresso, por exemplo, um preenchimento de guia na unidade `Azure:`, é possível que o cursor não esteja definido corretamente e uma lacuna apareça onde antes estava a barra de progresso.

## <a name="next-steps"></a>Próximas etapas

[Solucionando problemas do Cloud Shell](troubleshooting.md) <br>
[Guia de início rápido do Bash](quickstart.md) <br>
[Guia de início rápido para o PowerShell](quickstart-powershell.md)
