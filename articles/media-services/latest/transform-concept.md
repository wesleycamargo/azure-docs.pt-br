---
title: Transformações e Trabalhos nos Serviços de Mídia do Azure | Microsoft Docs
description: Ao usar os Serviços de Mídia, é necessário criar um recurso de Transformação para descrever as regras ou especificações para processar seus vídeos. Este artigo fornece uma visão geral do recurso de Transformação e como usá-lo.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d256d87548d54951cb77beffb88bba26a1a3de49
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="transforms-and-jobs"></a>Transformações e Trabalhos

## <a name="overview"></a>Visão geral 

A versão mais recente da API REST dos Serviços de Mídia do Azure (v3) apresenta um novo recurso de modelo de fluxo de trabalho de codificação e/ou análise de vídeos, chamado de **Transformação**. O recurso de **Transformação** pode ser usado para configurar as tarefas comuns de codificação ou análise de vídeos. Cada **Transformação** descreve um fluxo de trabalho simples de tarefas para processar seus arquivos de áudio ou vídeos. 

O objeto **Transformação** é a receita e um **Trabalho** é a solicitação real para os Serviços de Mídia do Azure para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações, como o local da entrada vídeo e o local da saída. Você pode especificar o local do seu vídeo usando: URLs HTTP(s), URLs SAS ou um caminho para arquivos localizados localmente ou no armazenamento de Blobs do Azure. Você pode ter até 100 Transformações em sua conta de Serviços de Mídia do Azure e enviar Trabalhos sob essas Transformações. Em seguida, você pode assinar a eventos como alterações de estado do Trabalho, usando Notificações, que se integram diretamente com o sistema de notificação de Grade de Eventos do Azure. 

Como essa API é orientada pelo Gerenciador de Recursos do Azure, você pode usar modelos do Resource Manager para criar e implantar as Transformações em sua conta de Serviços de Mídia. O controle de acesso baseado na função também pode ser definido a nível de recurso nesta API, permitindo bloquear o acesso a recursos específicos, como Transformações.

## <a name="typical-workflow-and-example"></a>Fluxo de trabalho típico e exemplo

1. Criar uma Transformação 
2. Enviar Trabalhos sob essa Transformação 
3. Listar Transformações 
4. Exclua uma Transformação, caso você não planeje usar esse "receita" no futuro. 

Suponha que você deseja extrair o primeiro quadro de todos os seus vídeos como uma imagem em miniatura. Estas são as etapas necessárias: 

1. Defina a regra de processamento. Por exemplo, use o primeiro quadro do vídeo como a miniatura 
2. Em seguida, para cada vídeo que tem como entrada para o serviço, informe ao serviço: 
    1. Onde encontrar esse vídeo, e 
    2. Onde gravar a saída. Por exemplo, a imagem em miniatura 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transmitir arquivos de vídeo](stream-files-dotnet-quickstart.md)
