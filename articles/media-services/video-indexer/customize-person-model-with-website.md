---
title: Usar o site do Video Indexer para personalizar um modelo de Pessoa – Azure
titlesuffix: Azure Media Services
description: Este artigo mostra como personalizar um modelo de Pessoa com o site do Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 29c32126a5bbe792dd6fe88ae189e09c511d2a22
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997049"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Personalizar um modelo de Pessoa com o site do Video Indexer

O Video Indexer dá suporte à detecção facial e ao reconhecimento de celebridades para conteúdo de vídeo. O recurso de reconhecimento de celebridades abrange aproximadamente um milhão de rostos baseados em fontes de dados geralmente solicitadas, como IMDB, Wikipédia e principais influenciadores do LinkedIn. Os rostos não reconhecidos pelo recurso de reconhecimento de celebridades são detectados; no entanto, eles são deixados sem nome. Depois de carregar o vídeo no Video Indexer e obter os resultados, você pode voltar e nomear os rostos que não foram reconhecidos. Depois que você rotular um rosto com um nome, o rosto e o nome serão adicionados ao modelo de Pessoa de sua conta. Em seguida, o Video Indexer reconhecerá esse rosto em seus vídeos futuros e antigos.

Use o site do Video Indexer para editar os rostos detectados em um vídeo, conforme descrito neste tópico. Use também a API, conforme descrito em [Personalizar o modelo de pessoa usando APIs](customize-person-model-with-api.md).

## <a name="edit-a-face"></a>Editar um rosto

> [!NOTE]
> Os nomes são exclusivos para nos modelos de uma Pessoa; portanto se você nomear dois rostos diferentes com o mesmo nome, o Video Indexer exibirá os rostos como a mesma pessoa e os convergirá depois que você reindexar o vídeo. Se você perceber que o Video Indexer detectou várias ocorrências diferentes do mesmo rosto, dê a eles o mesmo nome e reindexe o vídeo.
> Atualize um rosto que o Video Indexer reconheceu como uma celebridade com um novo nome. O novo nome que você fornecer terá precedência sobre o reconhecimento de celebridades interno.

1. Navegue até o site do [Video Indexer](https://www.videoindexer.ai/) e entre.
2. Pesquise um vídeo que você deseja exibir e editar em sua conta.
3. Para editar um rosto no vídeo, acesse a guia **Insights** e clique no ícone de lápis no canto superior direito da janela.

    ![Personalizar o modelo de pessoa](./media/customize-face-model/customize-face-model.png)

4. Clique em um dos rostos detectados e altere seus nomes de "Desconhecido nº X" (ou o nome que foi atribuído anteriormente ao rosto).
5. Depois de digitar o novo nome, clique no ícone de verificação ao lado do novo nome. Isso salvará o novo nome e reconhecerá e nomeará todas as ocorrências desse rosto nos outros vídeos atuais e nos vídeos futuros que você carregar. O reconhecimento do rosto nos outros vídeos atuais pode levar algum tempo para entrar em vigor, pois esse é um processo em lote. 

    ![Salvar a atualização](./media/customize-face-model/save-update.png)

## <a name="delete-a-face"></a>Excluir um rosto

Para excluir um rosto detectado no vídeo, acesse o painel **Insights** e clique no ícone de lápis no canto superior direito do painel. Clique na opção **Excluir** abaixo do nome do rosto. Isso removerá o rosto detectado do vídeo. O rosto ainda será detectado nos outros vídeos nos quais ele aparecerá, mas você poderá excluir o rosto desses vídeos também, depois de eles serem indexados. O rosto também continuará existindo no modelo de Pessoa de sua conta, caso você o tenha nomeado antes de excluí-lo.

## <a name="next-steps"></a>Próximas etapas

[Personalizar o modelo de Pessoa usando APIs](customize-person-model-with-api.md)
