---
title: "Usar a janela do Azure Cloud Shell (visualização) | Microsoft Docs"
description: "Orientações sobre a janela do Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 69316d271fc4847565d183293013580b61695731
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017

---

# <a name="using-the-azure-cloud-shell-window"></a>Usando a janela do Azure Cloud Shell

Este documento explica como usar a janela do Azure Cloud Shell.

## <a name="concurrent-sessions"></a>Sessões simultâneas
O Cloud Shell permite várias sessões simultâneas em guias do navegador permitindo que cada sessão exista como um processo do Bash separado.
Ao sair de uma sessão, saia de cada janela de sessão, pois cada processo é executado de forma independente apesar de serem executados no mesmo computador.

## <a name="restart-cloud-shell"></a>Reiniciar o Cloud Shell
![](media/recycle.png)
* Clique no ícone de reinicialização na barra de ferramentas para redefinir a máquina do Cloud Shell.

> ![AVISO] Reiniciar o Cloud Shell redefinirá o estado da máquina, e quaisquer arquivos não persistidos pelo clouddrive serão perdidos.

## <a name="minimize--maximize-cloud-shell-window"></a>Minimizar e maximizar a janela do Cloud Shell
![](media/minmax.png)
* Clique no ícone de minimizar no canto superior direito da janela para ocultá-lo. Clique novamente no ícone do Cloud Shell para reexibir.
* Clique no ícone de maximizar para definir a janela com a altura máxima. Para restaurar a janela ao tamanho anterior, clique em Restaurar.

## <a name="copy-and-paste"></a>Copiar e colar
* Windows: `Ctrl-insert` e `Shift-insert` ou clique com o botão direito na lista suspensa para copiar/colar
  * Talvez o FireFox/IE não ofereça suporte apropriado a permissões de área de transferência
* Mac OS: `Cmd-c` e `Cmd-v` ou clique com o botão direito na lista suspensa para copiar/colar

## <a name="resize-cloud-shell-window"></a>Redimensionar a janela do Cloud Shell
* Clique e arraste a borda superior da barra de ferramentas para cima ou para baixo para redimensionar a janela do Cloud Shell.

## <a name="scrolling-text-display"></a>Rolagem pela exibição de texto
* Role com o mouse ou touchpad

## <a name="exit-command"></a>Comando de saída
Executar `exit` encerra a sessão ativa. Esse comportamento ocorre por padrão após 10 minutos sem interação.

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Cloud Shell](quickstart.md)  
