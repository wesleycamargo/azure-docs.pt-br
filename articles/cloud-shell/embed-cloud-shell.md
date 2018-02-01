---
title: Inserir o Azure Cloud Shell | Microsoft Docs
description: Aprenda a inserir o Azure Cloud Shell.
services: cloud-shell
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
ms.date: 12/11/2017
ms.author: juluk
ms.openlocfilehash: 3ceddb94336fc2703e6f916f05ab1ec3676cb50d
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="embed-azure-cloud-shell"></a>Inserir o Azure Cloud Shell

Inserir o Azure Cloud Shell permite que desenvolvedores e gravadores de conteúdo abram diretamente o Azure Cloud Shell a partir de uma URL dedicada, [shell.azure.com](https://shell.azure.com). Isso fornece de imediato o controle total da autenticação do Azure Cloud Shell, das ferramentas e atualizações das ferramentas do Azure PowerShell/CLI do Azure a seus usuários.

Botão de tamanho regular

[![](https://shell.azure.com/images/launchcloudshell.png "Inicializar o Azure Cloud Shell")](https://shell.azure.com)

Botão de tamanho grande

[![](https://shell.azure.com/images/launchcloudshell@2x.png "Inicializar o Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Instruções

Integre o botão de inicialização do Azure Cloud Shell em arquivos markdown, copiando o seguinte:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

O HTML para inserir um pop-up do Azure Cloud Shell é apresentado a seguir:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Personalizar a experiência

Defina uma experiência de shell específica aumentando a URL.
|Experiência   |URL   |
|---|---|
|Shell usado mais recentemente   |shell.azure.com           |
|Bash                       |shell.azure.com/bash       |
|PowerShell                 |shell.azure.com/powershell |

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Bash no Cloud Shell](quickstart.md)<br>
[Início rápido do PowerShell no Azure Cloud Shell](quickstart-powershell.md)
