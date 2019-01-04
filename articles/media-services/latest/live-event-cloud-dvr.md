---
title: Serviços de Mídia do Azure LiveEvent e um DVR na nuvem|Microsoft Docs
description: Este artigo explica o que é LiveOutput e como usar um DVR na nuvem.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 8df43a9b2c518e77d14dd5cb392b042b0b4846e2
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407959"
---
# <a name="using-a-cloud-dvr"></a>Usando um DVR em nuvem

Uma [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) permite que você controle as propriedades da transmissão ao vivo, como quanto do fluxo é gravado (por exemplo, a capacidade do DVR na nuvem) e se os espectadores podem ou não assistir à exibição transmissão ao vivo. A relação entre **LiveEvent** e seu **LiveOutput** é similar à transmissão de televisão tradicional, em que um canal (**LiveEvent**) representa um fluxo constante de vídeo e uma gravação (**LiveOutput**) tem o escopo definido para um segmento de tempo específico (por exemplo, notícias noturnas das 18h30 às 19h00). Você pode gravar televisão usando um gravador de vídeo digital (DVR) - o recurso equivalente no LiveEvents é gerenciado por meio da propriedade ArchiveWindowLength. É uma duração de tempo ISO-8601 (por exemplo, PTHH: MM: SS), que especifica a capacidade do DVR e pode ser definida de um mínimo de 3 minutos a um máximo de 25 horas.

## <a name="liveoutput"></a>LiveOutput

O valor **ArchiveWindowLength** determina quanto tempo no passado o espectador pode buscar a partir da posição atual.  **ArchiveWindowLength** também determina quanto tempo os manifestos do cliente podem crescer.

Suponha que você esteja transmitindo um jogo de futebol e tenha **ArchiveWindowLength** de apenas 30 minutos. Um espectador que começa a assistir ao seu evento 45 minutos após o início do jogo pode buscar de volta no máximo a marca de 15 minutos. Seus **LiveOutput**s para o jogo continuarão até que **LiveEvent** seja interrompido, mas o conteúdo fora de **ArchiveWindowLength** seja continuamente descartado do armazenamento e não seja recuperável. Neste exemplo, o vídeo entre o início do evento e a marca de 15 minutos teria sido removido do seu DVR e do contêiner no armazenamento de blobs para o [Ativo](https://docs.microsoft.com/rest/api/media/assets). O arquivamento não é recuperável e é removido do contêiner no armazenamento de blobs do Azure.

Cada **LiveOutput** está associado a um **Ativo**, que ele usa para gravar o vídeo no contêiner de armazenamento de blob do Azure associado. Para publicar o LiveOutput, você deve criar um **StreamingLocator** para esse **Ativo**. Depois de criar um [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), você pode criar um URL de streaming que possa ser fornecido aos espectadores.

Um **LiveEvent** suporta até três **LiveOutput**  em execução simultânea, para que você possa criar no máximo três gravações / arquivos de uma transmissão ao vivo. Isso permite que você publique e arquive diferentes partes de um evento, conforme necessário. Suponha que você precise transmitir um feed linear ao vivo 24x7 e criar "gravações" dos diferentes programas ao longo do dia para oferecer aos clientes como conteúdo sob demanda para visualização aproximada. Para esse cenário, crie primeiro uma LiveOutput principal, com uma pequena janela de arquivamento de 1 hora ou menos. Essa é a principal transmissão ao vivo na qual seus espectadores se sintonizariam. Você criaria um **StreamingLocator** para este **LiveOutput** e o publicaria em seu aplicativo ou site como o feed "Ao vivo".
 Enquanto o **LiveEvent** estiver em execução, você pode criar programaticamente uma segunda **LiveOutput** simultânea no início de um programa (ou 5 minutos antes para fornecer algumas alças para aparar mais tarde). Este segundo **LiveOutput** pode ser excluído cinco minutos depois que o programa for finalizado. Com este segundo **Recurso**, você pode criar um novo **StreamingLocator** para publicar este programa como um recurso sob demanda no catálogo de seu aplicativo. Você pode repetir esse processo várias vezes para outros limites do programa ou destaques que deseja compartilhar como vídeos sob demanda, enquanto o feed "Live" da primeira **LiveOutput** continua transmitindo o feed linear. 

> [!NOTE]
> **LiveOutput** s inicie a criação e pare quando for excluído. Quando você exclui **LiveOutput**, não está excluindo o **Ativo** subjacente e o conteúdo no Ativo.  

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Tutorial de live streaming](stream-live-tutorial-with-api.md)

