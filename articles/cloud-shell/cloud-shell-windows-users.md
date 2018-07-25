---
title: Azure Cloud Shell para usuários do Windows | Microsoft Docs
description: Guia para usuários que não estão familiarizados com sistemas Linux
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 5e318a0f64033aa0c4b306e547c11e1994afa229
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860957"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell no Azure Cloud Shell para usuários do Windows

Em maio de 2018, as alterações foram [anunciadas](https://azure.microsoft.com/blog/pscloudshellrefresh/) no PowerShell no Azure Cloud Shell.  A experiência do PowerShell no Azure Cloud Shell agora é PowerShell Core 6 no Linux.
Com essa alteração, há alguns aspectos do PowerShell no Cloud Shell que podem ser diferentes do esperado no Windows PowerShell 5.1.

## <a name="case-sensitivity"></a>Diferenciar maiúsculas de minúsculas

No Windows, o sistema de arquivos diferencia maiúsculas de minúsculas.  No Linux, o sistema de arquivos diferencia maiúsculas de minúsculas.
Isso significa que, anteriormente, `file.txt` e `FILE.txt` foram considerados o mesmo arquivo, agora eles são considerados arquivos diferentes.
As maiúsculas e minúsculas apropriadas devem ser usadas durante o preenchimento de `tab` no sistema de arquivos.  Experiências específicas do PowerShell, como cmdlets `tab`, não diferenciam maiúsculas de minúsculas. 

## <a name="windows-powershell-alias-vs-linux-utilities"></a>Alias do Windows PowerShell versus utilitários Linux

Comandos existentes no Linux, como `ls`, `sort` e `sleep` têm precedência sobre os aliases do PowerShell.  Veja abaixo os aliases comuns removidos, bem como os comandos equivalentes:  

|Alias removido   |Comando equivalente   |
|---|---|
|`ls`     | `dir` <br> `Get-ChildItem` |
|`sort`   | `Sort-Object` |
|`sleep`  | `Start-Sleep` |

## <a name="persisting-home-vs-homeclouddrive"></a>Persisting $home versus $home\clouddrive

Para usuários que persistiram scripts e outros arquivos na Unidade de Nuvem, o diretório $HOME agora é persistido entre as sessões.

## <a name="powershell-profile"></a>Perfil do PowerShell

Por padrão, um perfil do PowerShell não é criado.  Para criar seu perfil, crie um diretório `PowerShell` sob `$HOME/.config`.  No `$HOME/.config/PowerShell`, você pode criar seu perfil com o nome `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>O que há de novo no PowerShell Core 6

Para saber mais sobre o que há de novo no PowerShell Core 6, consulte os [documentos do PowerShell](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) e a postagem de blog [Introdução ao PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/)
