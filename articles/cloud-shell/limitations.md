---
title: "Limitações do Azure Cloud Shell (Visualização) | Microsoft Docs"
description: "Visão geral das limitações do Azure Cloud Shell"
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: juluk
ms.openlocfilehash: 66f07481d6e62b4375dd2752c5e7d6f8e02d4a3c
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2017
---
# <a name="limitations-of-azure-cloud-shell"></a>Limitações do Azure Cloud Shell

O Azure Cloud Shell tem as seguintes limitações conhecidas:

## <a name="general-limitations"></a>Limitações gerais

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

O computador que fornece a sessão do Cloud Shell é temporário e é reciclado após a sessão ficar inativa por 20 minutos. O Cloud Shell exige a montagem de um compartilhamento de arquivos. Como resultado, sua assinatura deve ser capaz de configurar recursos de armazenamento para acessar o Cloud Shell. Outras considerações incluem:

* Com o armazenamento montado, apenas as modificações no diretório `clouddrive` persistem. No Bash, seu `$Home` diretório também persiste.
* Os compartilhamentos de arquivo só podem ser montados em sua [região atribuída](persisting-shell-storage.md#mount-a-new-clouddrive).
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
Apesar de alguns comandos dentro do diretório `clouddrive`, como `git clone`, não terem as permissões adequadas, seu diretório `$Home` tem essas permissões.

### <a name="editing-bashrc"></a>Edição de .bashrc

Tome cuidado ao editar .bashrc, uma vez que isso pode causar erros inesperados no Cloud Shell.

### <a name="bashhistory"></a>.bash_history

Seu histórico de comandos bash pode ser inconsistente devido a interrupções de sessão a ou sessões simultâneas do Cloud Shell.

## <a name="powershell-limitations"></a>Limitações do PowerShell

### <a name="slow-startup-time"></a>Tempo de inicialização lento

O PowerShell no Azure Cloud Shell pode levar até 60 segundos para inicializar durante a visualização.

### <a name="no-home-directory-persistence"></a>Sem persistência do diretório $Home

Os dados gravados `$Home` por qualquer aplicativo (como git, vim e outros) não persistem entre as sessões do PowerShell. Para obter uma solução, [veja aqui](troubleshooting.md#powershell-resolutions).

### <a name="default-file-location-when-created-from-azure-drive"></a>Local padrão do arquivo quando criado da unidade do Azure:
Usando cmdlets do PowerShell, os usuários não podem criar arquivos na unidade do Azure. Quando os usuários criam novos arquivos usando outras ferramentas como vim ou nano, os arquivos são salvos na pasta C:\Usuários por padrão. 

## <a name="next-steps"></a>Próximas etapas

[Solucionando problemas do Cloud Shell](troubleshooting.md) <br>
[Guia de início rápido do Bash](quickstart.md) <br>
[Guia de início rápido do PowerShell](quickstart-powershell.md)
