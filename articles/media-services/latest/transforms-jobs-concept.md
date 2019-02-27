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
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: d621afd682e6040179777f4cd6d991ff31acb5a3
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445484"
---
# <a name="transforms-and-jobs"></a>Transformações e Trabalhos
 
Use [Transformações](https://docs.microsoft.com/rest/api/media/transforms) para configurar tarefas comuns para codificar ou analisar vídeos. Cada **Transformação** descreve uma receita ou um fluxo de trabalho de tarefas para processar os arquivos de áudio ou vídeos. Uma única transformação pode aplicar mais de uma regra. Por exemplo, uma transformação poderia especificar que cada vídeo seja codificado em um arquivo MP4 em uma determinada taxa de bits, e que uma imagem em miniatura gerada desde o primeiro quadro do vídeo. Você precisaria adicionar uma entrada de TransformOutput para cada regra que você deseja incluir em sua Transformação. Você pode criar Transformações em sua conta dos Serviços de Mídia usando a API v3 dos Serviços de Mídia ou um dos SDKs publicados. A API é controlada pelo Azure Resource Manager v3, portanto, você também pode usar modelos do Azure Resource Manager para criar Transformações na sua conta dos Serviços de Mídia do Microsoft Azure. O controle de acesso baseado em função pode ser usado para bloquear o acesso a transformações.

A operação de atualização na entidade [Transformação](https://docs.microsoft.com/rest/api/media/transforms) serve para fazer alterações na descrição ou nas prioridades do TransformOutputs subjacente. É recomendável que essas atualizações sejam realizadas depois que todos os trabalhos em andamento forem concluídos. Se você pretende reescrever a receita, precisa criar uma nova transformação.

Um [Trabalho](https://docs.microsoft.com/rest/api/media/jobs) é a solicitação real para os serviços de mídia do Azure para aplicar a **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. Quando a Transformação for criada, você poderá enviar trabalhos usando as APIs dos Serviços de Mídia ou um dos SDKs publicados. O **Trabalho** especifica informações como o local do vídeo de entrada e o local para a saída. É possível especificar a localização do vídeo de entrada usando: URLs HTTPS, URLs SAS ou [Ativos](https://docs.microsoft.com/rest/api/media/assets). O progresso e o estado de trabalhos podem ser obtidos pelo monitoramento de eventos com a Grade de Eventos do Azure. Para obter mais informações, consulte [Monitorar eventos usando EventGrid](job-state-events-cli-how-to.md).

A operação de atualização na entidade [Trabalho](https://docs.microsoft.com/rest/api/media/jobs) pode ser usada para modificar as propriedades *description* e *priority* depois que o trabalho é enviado. Uma alteração na propriedade *priority* só será eficaz se o trabalho ainda estiver na fila. Se o trabalho tiver iniciado o processamento ou tiver sido concluído, a alteração da prioridade não terá qualquer efeito.

O diagrama a seguir mostra o fluxo de trabalho/transformações.

![Transformações](./media/encoding/transforms-jobs.png)

> [!NOTE]
> Propriedades de **Transformação** e **Trabalho** que são do tipo Datetime estão sempre em formato UTC.

## <a name="typical-workflow"></a>Fluxo de trabalho típico

1. Criar uma Transformação 
2. Enviar Trabalhos sob essa Transformação 
3. Listar Transformações 
4. Exclua uma transformação, se você não planeja usá-la no futuro. 

### <a name="example"></a>Exemplo

Suponha que você deseja extrair o primeiro quadro de todos os seus vídeos como uma imagem em miniatura. Estas são as etapas necessárias: 

1. Defina a receita ou a regra para processar seus vídeos – "usar o primeiro quadro do vídeo como a miniatura". 
2. Para cada vídeo informar ao serviço: 
    1. Onde encontrar esse vídeo,  
    2. Onde gravar a saída a imagem em miniatura de saída. 

Uma **Transformação** ajuda você a criar uma vez a receita (Etapa 1) e enviar trabalhos usando essa receita (Etapa 2).

## <a name="job-error-codes"></a>Códigos de erro do trabalho

Confira [Códigos de erro](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="paging"></a>Paginamento

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="configure-media-reserved-units"></a>Configurar unidades reservadas de mídia

Para os trabalhos de análise de áudio e análise de vídeo acionados pelo Serviços de Mídia do Microsoft Azure v3 ou pelo Video Indexer, é altamente recomendável provisionar sua conta com 10 MRUs (Unidades reservadas de mídia) do S3. Se você precisar de mais de 10 MRUs do S3, abra um ticket de suporte usando o [Portal do Microsoft Azure](https://portal.azure.com/).

Para obter detalhes, confira [Dimensionar o processamento de mídia com a CLI](media-reserved-units-cli-how-to.md).

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Carregar, codificar e transmitir vídeos usando .NET](stream-files-tutorial-with-api.md)
- [Tutorial: Analisar vídeos com os Serviços de Mídia v3 usando .NET](analyze-videos-tutorial-with-api.md)
