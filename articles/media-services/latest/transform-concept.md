---
title: Transformações e Trabalhos nos Serviços de Mídia do Azure | Microsoft Docs
description: Ao usar os Serviços de Mídia, é necessário criar um recurso de Transformação para descrever as regras ou especificações para processar seus vídeos. Este artigo fornece uma visão geral do recurso de Transformação e como usá-lo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 214d4d3d11255e417f3df1e5f6e648b2a30225ea
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377301"
---
# <a name="transforms-and-jobs"></a>Transformações e Trabalhos

## <a name="overview"></a>Visão geral 

A versão mais recente da API REST dos Serviços de Mídia do Azure (v3) apresenta um novo recurso de modelo de fluxo de trabalho de codificação e/ou análise de vídeos, chamado de **Transformação**. O recurso de **Transformação** pode ser usado para configurar as tarefas comuns de codificação ou análise de vídeos. Cada **Transformação** descreve um fluxo de trabalho simples de tarefas para processar seus arquivos de áudio ou vídeos. 

O objeto **Transformação** é a receita e um **Trabalho** é a solicitação real para os Serviços de Mídia do Azure para aplicar a **Transformação** a um determinado conteúdo de vídeo ou áudio de entrada. O **Trabalho** especifica informações, como o local da entrada vídeo e o local da saída. Você pode especificar o local do seu vídeo usando: URLs HTTP(s), URLs SAS ou um caminho para arquivos localizados localmente ou no armazenamento de Blobs do Azure. Você pode ter até 100 Transformações em sua conta de Serviços de Mídia do Azure e enviar Trabalhos sob essas Transformações. Em seguida, você pode assinar a eventos como alterações de estado do Trabalho, usando Notificações, que se integram diretamente com o sistema de notificação de Grade de Eventos do Azure. 

Como essa API é orientada pelo Gerenciador de Recursos do Azure, você pode usar modelos do Resource Manager para criar e implantar as Transformações em sua conta de Serviços de Mídia. O controle de acesso baseado na função também pode ser definido a nível de recurso nesta API, permitindo bloquear o acesso a recursos específicos, como Transformações.

## <a name="transform-definition"></a>Definição de transformação

A tabela a seguir mostra as propriedades da transformação e retorna suas definições.

|NOME|Tipo|DESCRIÇÃO|
|---|---|---|
|ID|string|ID de recurso totalmente qualificada para o recurso.|
|Nome|string|O nome do recurso.|
|properties.created |string|A data e hora UTC quando a transformação foi criada no formato “AAAA-MM-DDThh:mm:ssZ”.|
|properties.description |string|Uma descrição detalhada opcional da transformação.|
|properties.lastModified |string|A data e hora UTC quando a transformação foi atualizada no formato “AAAA-MM-DDThh:mm:ssZ”.|
|properties.outputs |TransformOutput[]|Uma matriz de um ou mais TransformOutputs que a Transformação deve gerar.|
|Tipo|string|Tipo do recurso.|

Para a definição completa, consulte [Transformações](https://docs.microsoft.com/rest/api/media/transforms).

## <a name="job-definition"></a>Definição de trabalho

A tabela a seguir mostra as propriedades da trabalho e retorna suas definições.

|NOME|Tipo|DESCRIÇÃO|
|---|---|---|
|ID|string|ID de recurso totalmente qualificada para o recurso.|
|Nome|string|O nome do recurso.|
|properties.created |string|A data e hora UTC quando a transformação foi criada no formato “AAAA-MM-DDThh:mm:ssZ”.|
|properties.description |string|Uma descrição detalhada opcional do trabalho.|
|properties.lastModified |string|A data e hora UTC quando a transformação foi atualizada no formato “AAAA-MM-DDThh:mm:ssZ”.|
|properties.outputs |JobOutput[]:JobOutputAsset[] |As saídas para o trabalho.|
|properties.priority |Prioridade |Prioridade com que o trabalho deve ser processado. Trabalhos com prioridade mais alta são processados antes dos trabalhos de prioridade mais baixa. Se não for definido, o padrão é normal.
|properties.state |JobState |Estado atual do trabalho.
|Tipo|string|Tipo do recurso.|

Para a definição completa, consulte [Trabalhos](https://docs.microsoft.com/rest/api/media/jobs).

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

[Transmitir arquivos de vídeo](stream-files-dotnet-quickstart.md)
