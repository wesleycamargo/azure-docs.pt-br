---
title: "Usar a janela do Azure Cloud Shell (visualização) | Microsoft Docs"
description: "Orientações sobre a janela do Azure Cloud Shell."
services: 
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
ms.date: 07/13/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: a47961dfdaf178a6b793bd68105d9792a9275bb3
ms.contentlocale: pt-br
ms.lasthandoff: 08/17/2017

---

# <a name="using-the-azure-cloud-shell-window"></a>Usando a janela do Azure Cloud Shell

Este documento explica como usar a janela do Cloud Shell.

## <a name="concurrent-sessions"></a>Sessões simultâneas
O Cloud Shell permite várias sessões simultâneas em guias do navegador permitindo que cada sessão exista como um processo do Bash separado.
Ao sair de uma sessão, saia de cada janela de sessão, pois cada processo é executado de forma independente apesar de serem executados no mesmo computador.

## <a name="restart-cloud-shell"></a>Reiniciar o Cloud Shell
![](media/recycle.png)
> [!WARNING]
> Reiniciar o Cloud Shell redefinirá o estado do computador e quaisquer arquivos não persistidos pelo seu compartilhamento de arquivos serão perdidos.

* Clique no ícone de reinicialização na barra de ferramentas para receber um novo ambiente do Cloud Shell.

## <a name="minimize--maximize-cloud-shell-window"></a>Minimizar e maximizar a janela do Cloud Shell
![](media/minmax.png)
* Clique no ícone de minimizar no canto superior direito da janela para ocultá-lo. Clique novamente no ícone do Cloud Shell para reexibir.
* Clique no ícone de maximizar para definir a janela com a altura máxima. Para restaurar a janela ao tamanho anterior, clique em Restaurar.

## <a name="copy-and-paste"></a>Copiar e colar
* Windows: `Ctrl-insert` para copiar e `Shift-insert` para colar. A lista suspensa aberta com um clique com o botão direito do mouse também pode habilitar copiar/colar.
  * Talvez o FireFox/IE não dê suporte apropriado a permissões de área de transferência.
* Mac OS: `Cmd-c` para copiar e `Cmd-v` para colar. A lista suspensa aberta com um clique com o botão direito do mouse também pode habilitar copiar/colar.

## <a name="resize-cloud-shell-window"></a>Redimensionar a janela do Cloud Shell
* Clique e arraste a borda superior da barra de ferramentas para cima ou para baixo para redimensionar a janela do Cloud Shell.

## <a name="scrolling-text-display"></a>Rolagem pela exibição de texto
* Role com o mouse ou teclado para ir ao texto de terminal.

## <a name="exit-command"></a>Comando de saída
Executar `exit` encerra a sessão ativa. Esse comportamento ocorre por padrão após 20 minutos sem interação.

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Cloud Shell](quickstart.md)

