---
title: Arquivo de inclusão
description: Arquivo de inclusão
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: e4bde4f521e0e19e7acd36260c98cfe80973e284
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198228"
---
## <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inicialize os recursos de depuração com a extensão do VS Code
Primeiro, é necessário configurar o projeto de código para que o VS Code comunique-se com o ambiente de desenvolvimento no Azure. A extensão do VS Code para o Azure Dev Spaces fornece um comando auxiliar para configurar a configuração de depuração. 

Abra a **Paleta de Comandos** (usando o menu **Exibir | Paleta de Comandos**) e use o preenchimento automático para digitar e selecionar este comando: `Azure Dev Spaces: Create configuration files for connected development`. 

Isso adiciona a configuração de depuração para o Azure Dev Spaces na pasta `.vscode`.

![](../media/common/command-palette.png)

> [!Important]
> Devido a um bug, feche e abra novamente o VS Code antes de prosseguir.