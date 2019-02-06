---
title: Evento ao vivo dos Serviços de Mídia do Azure e um DVR em nuvem | Microsoft Docs
description: Este artigo explica o que é a Saída ao Vivo e como usar um DVR em nuvem.
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
ms.date: 01/14/2019
ms.author: juliako
ms.openlocfilehash: 4dd14587ec7e1473953981c1ef8c32c59eb9a1d6
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888508"
---
# <a name="using-a-cloud-dvr"></a>Usando um DVR em nuvem

Uma [Saída ao Vivo](https://docs.microsoft.com/rest/api/media/liveoutputs) permite que você controle as propriedades da transmissão ao vivo, como quanto da transmissão é gravada (por exemplo, a capacidade do DVR na nuvem) e se os visualizadores podem ou não começar a inspecionar a transmissão ao vivo. A relação entre um **Evento ao Vivo** e a respectiva **Saída ao Vivo** é semelhante à difusão de televisão tradicional, em que um canal (**Evento ao Vivo**) representa uma transmissão constante de vídeo e uma gravação (**Saída ao Vivo**) tem o escopo definido para um segmento de tempo específico (por exemplo, notícias noturnas das 18h30 às 19h00). Você pode gravar televisão usando um DVR (Gravador de Vídeo Digital) – o recurso equivalente no Evento ao Vivo é gerenciado por meio da propriedade ArchiveWindowLength. É uma duração de tempo ISO-8601 (por exemplo, PTHH: MM: SS), que especifica a capacidade do DVR e pode ser definida de um mínimo de 3 minutos a um máximo de 25 horas.

## <a name="live-output"></a>Saída ao Vivo

O valor **ArchiveWindowLength** determina quanto tempo no passado o espectador pode buscar a partir da posição atual.  **ArchiveWindowLength** também determina quanto tempo os manifestos do cliente podem crescer.

Suponha que você esteja transmitindo um jogo de futebol e tenha **ArchiveWindowLength** de apenas 30 minutos. Um espectador que começa a assistir ao seu evento 45 minutos após o início do jogo pode buscar de volta no máximo a marca de 15 minutos. A **Saída ao Vivo** para o jogo continuará até que o **Evento ao Vivo** seja interrompido, mas o conteúdo fora de **ArchiveWindowLength** será continuamente descartado do armazenamento e não poderá ser recuperado. Neste exemplo, o vídeo entre o início do evento e a marca de 15 minutos teria sido removido do seu DVR e do contêiner no armazenamento de blobs para o [Ativo](https://docs.microsoft.com/rest/api/media/assets). O arquivamento não é recuperável e é removido do contêiner no armazenamento de blobs do Azure.

Cada **Saída ao Vivo** está associada a um **Ativo**, que ela usa para gravar o vídeo no contêiner de armazenamento de blob do Azure associado. Para publicar a Saída ao Vivo, você deve criar um **Localizador de Streaming** para esse **Ativo**. Depois de criar um [Localizador de Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators), é possível criar uma URL de streaming que você pode fornecer aos seus visualizadores.

Um **Evento ao Vivo** dá suporte a até três **Saídas ao Vivo** em execução simultânea para que você possa criar no máximo três gravações/arquivos de uma transmissão ao vivo. Isso permite que você publique e arquive diferentes partes de um evento, conforme necessário. Suponha que você precise transmitir um feed linear ao vivo 24x7 e criar "gravações" dos diferentes programas ao longo do dia para oferecer aos clientes como conteúdo sob demanda para visualização aproximada. Para esse cenário, crie primeiro uma Saída ao Vivo principal, com uma pequena janela de arquivamento de 1 hora ou menos. Essa é a principal transmissão ao vivo na qual os visualizadores se sintonizam. Você cria um **Localizador de Streaming** para esta **Saída ao Vivo** e o publica em seu aplicativo ou site como o feed "Ao vivo". Enquanto o **Evento ao Vivo** estiver em execução, você poderá criar programaticamente uma segunda **Saída ao Vivo** simultânea no início de um programa (ou 5 minutos antes para fornecer algumas alças para aparar mais tarde). Essa segunda **Saída ao Vivo** pode ser excluída 5 minutos depois que o programa é finalizado. Com esse segundo **Ativo**, você cria uma novo **Localizador de Streaming** para publicar esse programa como um ativo sob demanda no seu catálogo de aplicativos. Você pode repetir esse processo várias vezes para outros limites do programa ou destaques que queira compartilhar como vídeos sob demanda, enquanto o feed "Ao vivo" da primeira **Saída ao Vivo** continua difundindo o feed linear. 

> [!NOTE]
> As **Saídas ao Vivo** começam na criação e terminam quando são excluídas. Ao excluir a **Saída ao Vivo**, você não está excluindo o **Ativo** subjacente nem o conteúdo no ativo. 
>
> Caso tenha publicado o ativo **Saída ao Vivo** usando um **Localizador de Streaming**, o **Evento ao Vivo** (até a duração da janela DVR) continuará visível até a expiração ou exclusão do **Localizador de Streaming**, o que ocorrer primeiro.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Tutorial de live streaming](stream-live-tutorial-with-api.md)

