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
ms.openlocfilehash: 3e678be543adb963d8ebe49b119672ecfd5dafd5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38728864"
---
### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inicialize os recursos de depuração com a extensão do VS Code
Primeiro, é necessário configurar o projeto de código para que o VS Code comunique-se com o espaço de desenvolvimento no Azure. A extensão do VS Code para o Azure Dev Spaces fornece um comando auxiliar para configurar a configuração de depuração. 

Abra a **Paleta de Comandos** (usando o menu **Exibir | Paleta de Comandos**) e use o preenchimento automático para digitar e selecionar este comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Isso adiciona a configuração de depuração para o Azure Dev Spaces na pasta `.vscode`.

![](../media/common/command-palette.png)
