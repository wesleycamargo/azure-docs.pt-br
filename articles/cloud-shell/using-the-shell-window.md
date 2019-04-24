---
title: Usando a janela do Azure Cloud Shell | Microsoft Docs
description: Visão geral de como usar a janela do Azure Cloud Shell.
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
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 2511f2c8fb706e232cde9ee4c02c7f8114bd3a2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60200540"
---
# <a name="using-the-azure-cloud-shell-window"></a>Usando a janela do Azure Cloud Shell

Este documento explica como usar a janela do Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Alternar entre os ambientes Bash e PowerShell

Use o seletor de ambiente na barra de ferramentas do Cloud Shell para alternar entre os ambientes Bash e PowerShell.  
![Selecionar ambiente](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Reiniciar o Cloud Shell
Clique no ícone de reinicialização na barra de ferramentas do Cloud Shell para redefinir o estado da máquina.  
![Reiniciar o Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Reiniciar o Cloud Shell redefinirá o estado do computador, e quaisquer arquivos não persistentes no seu compartilhamento de arquivos do Azure serão perdidos.

## <a name="change-the-text-size"></a>Alterar o tamanho do texto
Clique no ícone de configurações na parte superior esquerda da janela, em seguida, passe o mouse sobre a opção "Tamanho do texto" e selecione o tamanho do texto desejado. Sua seleção persistirá entre as sessões.
![Tamanho do texto](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Alterar a fonte
Clique no ícone de configurações na parte superior esquerdo da janela, em seguida, passe o mouse sobre a opção "Fonte" e selecione sua fonte desejada.  Sua seleção persistirá entre as sessões.
![Font](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Carregar e baixar arquivos
Clique no ícone de arquivos de upload/download de na parte superior esquerdo da janela, em seguida, selecione upload ou download.  
![Upload/download de arquivos](media/using-the-shell-window/uploaddownload.png)
* Para carregar arquivos, use o pop-up para navegar até o arquivo no computador local, selecione o arquivo desejado e clique no botão "Abrir".  O arquivo será carregado para o `/home/user` directory.
* Para baixar arquivos, insira o caminho de arquivo totalmente qualificado para a janela pop-up e selecione o botão "Download".  
> [!NOTE] 
> Arquivos e caminhos de arquivo diferenciam maiusculas de minúsculas no Cloud Shell. Verifique novamente seu uso de maiusculas e minúsculas em seu caminho de arquivo.

## <a name="open-another-cloud-shell-window"></a>Abrir outra janela do Cloud Shell
O Cloud Shell permite várias sessões simultâneas em guias do navegador, permitindo que cada sessão exista como um processo separado.
Ao sair de uma sessão, saia de cada janela de sessão, pois cada processo é executado de forma independente apesar de serem executados no mesmo computador.  
Clique no ícone de nova sessão aberta na parte superior esquerdo da janela. Uma nova guia será aberta com outra sessão conectada ao contêiner existente.
![Abrir nova sessão](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Editor do Cloud Shell
* Consulte a [usando o editor do Azure Cloud Shell](using-cloud-shell-editor.md) página.

## <a name="web-preview"></a>Visualização da Web
Clique no ícone de visualização da web na parte superior esquerda da janela, selecione "Configurar", especifique a porta desejada ao abrir.  Selecione qualquer um dos "porta aberta" apenas para abre a porta, ou "abrir e procurar" para abrir a porta e a porta em uma nova guia de visualização.  
![Visualização da Web](media/using-the-shell-window/preview.png)  
<br>
![Configurar a porta](media/using-the-shell-window/preview-configure.png)  
Clique no ícone de visualização da web na parte superior esquerdo da janela, selecione "Visualizar a porta..." Para visualizar uma porta aberta em uma nova guia. Clique no ícone de visualização da web na parte superior esquerdo da janela, selecione "Fechar porta..." para fechar a porta aberta.  
![Porta de visualização/de fechamento](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimizar e maximizar a janela do Cloud Shell
Clique no ícone de minimizar no canto superior direito da janela para ocultá-lo. Clique novamente no ícone do Cloud Shell para reexibir.
Clique no ícone de maximizar para definir a janela com a altura máxima. Para restaurar a janela ao tamanho anterior, clique em Restaurar.  
![Minimizar ou maximizar a janela](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Copiar e colar
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Redimensionar a janela do Cloud Shell
Clique e arraste a borda superior da barra de ferramentas para cima ou para baixo para redimensionar a janela do Cloud Shell.

## <a name="scrolling-text-display"></a>Rolagem pela exibição de texto
Role com o mouse ou teclado para ir ao texto de terminal.

## <a name="exit-command"></a>Comando de saída
Executar `exit` encerra a sessão ativa. Esse comportamento ocorre por padrão após 20 minutos sem interação.

## <a name="next-steps"></a>Próximos passos

[Início rápido do Bash no Cloud Shell](quickstart.md) <br>
[Início Rápido do PowerShell no Cloud Shell](quickstart-powershell.md)