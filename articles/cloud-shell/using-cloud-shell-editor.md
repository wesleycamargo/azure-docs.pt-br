---
title: Como usar o editor do Azure Cloud Shell | Microsoft Docs
description: Visão geral de como usar o editor do Azure Cloud Shell.
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: juluk
ms.openlocfilehash: caf6e18a9a30654710f5445ed6ab957a5253d62e
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259407"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Como usar o editor do Azure Cloud Shell

O Azure Cloud Shell inclui um editor de arquivo integrado, criado com base no [Editor Monaco](https://github.com/Microsoft/monaco-editor) de software livre. O editor do Cloud Shell oferece suporte a recursos como realce de linguagem, paleta de comandos e explorador de arquivos.

![Editor do Cloud Shell](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Como abrir o editor

Para a criação e edição de arquivo simples, inicie o editor executando `code .` no terminal do Cloud Shell. Essa ação abre o editor com o seu diretório de trabalho ativo definido no terminal.

Para abrir diretamente um arquivo para edição rápida, execute `code <filename>` para abrir o editor sem o explorador de arquivos.

Para abrir o editor usando o botão de interface do usuário, clique no ícone do editor `{}` na barra de ferramentas. Isso abrirá o editor e usará como padrão o explorador de arquivos para o diretório `/home/<user>`.

## <a name="closing-the-editor"></a>Como fechar o editor

Para fechar o editor, abra o painel de ação `...` no canto superior direito do editor e selecione `Close editor`.

![Fechar editor](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Paleta de comandos

Para inicializar a paleta de comandos, use a tecla `F1` quando o foco estiver definido no editor. A paleta de comandos também pode ser aberta feito por meio do painel de ação.

![Paleta de cmd](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Colaboração para o Editor do Mônaco

Há suporte para realce de linguagem no editor do Cloud Shell usando a funcionalidade upstream no uso das definições de sintaxe Monarch do [Editor do Mônaco](https://github.com/Microsoft/monaco-editor). Para saber como fazer contribuições, leia o [Guia do colaborador do Mônaco](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>Próximas etapas
[Experimente o início rápido para Bash no Cloud Shell](quickstart.md)
[Exibir a lista completa de ferramentas integradas do Cloud Shell](features.md)