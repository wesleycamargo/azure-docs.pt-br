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
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: e13afe26d06f5b5b2dcf7eddf00f9ee481312b2c
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024249"
---
# <a name="transforms-and-jobs"></a>Transformações e Trabalhos
 
Os Serviços de Mídia do Microsoft Azure v3 introduzem novo recurso de modelo de fluxo de trabalho para uma receita que você deseja usar para codificar e/ou analisar seus vídeos, chamados [Transformações](https://docs.microsoft.com/rest/api/media/transforms). **Transformações** podem ser usadas para configurar as tarefas comuns para codificar ou analisar os vídeos. Cada **Transformação** descreve uma receita ou um fluxo de trabalho de tarefas para processar os arquivos de áudio ou vídeos. 

Um **Trabalho** é a solicitação real para os serviços de mídia do Azure para aplicar a **Transformação** a um determinado conteúdo de áudio ou vídeo de entrada. O **Trabalho** especifica informações como o local do vídeo de entrada e o local para a saída. Você pode especificar o local de sua entrada usando vídeo: URLs HTTPs, URLs de SAS, ou [Ativos de Serviços de Mídia](https://docs.microsoft.com/rest/api/media/assets).  

## <a name="typical-workflow"></a>Fluxo de trabalho típico

1. Criar uma Transformação 
2. Enviar Trabalhos sob essa Transformação 
3. Listar Transformações 
4. Exclua uma transformação, se você não planeja usá-la no futuro. 

Suponha que você deseja extrair o primeiro quadro de todos os seus vídeos como uma imagem em miniatura. Estas são as etapas necessárias: 

1. Defina a receita ou a regra para processar seus vídeos – "usar o primeiro quadro do vídeo como a miniatura". 
2. Para cada vídeo informar ao serviço: 
    1. Onde encontrar esse vídeo,  
    2. Onde gravar a saída a imagem em miniatura de saída. 

Uma **Transformação** ajuda você a criar uma vez a receita (Etapa 1) e enviar trabalhos usando essa receita (Etapa 2).

## <a name="transforms"></a>Transformações

Você pode criar transformações em sua conta de Serviços de Mídia do Microsoft Azure usando a API v3 diretamente ou usando qualquer uma das SDKs publicados. A API é controlada pelo Azure Resource Manager v3, portanto, você também pode usar modelos do Azure Resource Manager para criar Transformações na sua conta dos Serviços de Mídia do Microsoft Azure. O controle de acesso baseado em função pode ser usado para bloquear o acesso a transformações.

### <a name="transform-definition"></a>Definição de transformação

A tabela a seguir mostra as propriedades da transformação e fornece suas definições.

|NOME|DESCRIÇÃO|
|---|---|
|ID|ID de recurso totalmente qualificada para o recurso.|
|Nome|O nome do recurso.|
|properties.created |A data e hora UTC quando a transformação foi criada no formato “AAAA-MM-DDThh:mm:ssZ”.|
|properties.description |Uma descrição detalhada opcional da transformação.|
|properties.lastModified |A data e hora UTC quando a transformação foi atualizada no formato “AAAA-MM-DDThh:mm:ssZ”.|
|properties.outputs |Uma matriz de um ou mais TransformOutputs que a Transformação deve gerar.|
|Tipo|Tipo do recurso.|

Para a definição completa, consulte [Transformações](https://docs.microsoft.com/rest/api/media/transforms).

Conforme explicado acima, uma transformação ajuda a criar uma receita ou a regra para processar seus vídeos. Uma única transformação pode aplicar mais de uma regra. Por exemplo, uma transformação poderia especificar que cada vídeo seja codificado em um arquivo MP4 em uma determinada taxa de bits, e que uma imagem em miniatura gerada desde o primeiro quadro do vídeo. Você precisaria adicionar uma entrada de TransformOutput para cada regra que você deseja incluir em sua Transformação.

> [!NOTE]
> Enquanto a definição de Transformações dá suporte a uma operação de atualização, você deve ter cuidado para garantir que todos os trabalhos em andamento concluir antes de fazer uma modificação. Normalmente, você atualizaria uma transformação existente para alterar a descrição ou modificar as prioridades do TransformOutputs subjacente. Se você quisesse reescrever a receita, em seguida, você criaria uma nova transformação.

## <a name="jobs"></a>Trabalhos

Quando uma transformação tiver sido criada, você pode enviar trabalhos usando as APIs de Serviços de Mídia do Microsoft Azure ou qualquer uma das SDKs publicadas. O progresso e o estado de trabalhos podem ser obtidos pelo monitoramento de eventos com a Grade de Eventos do Azure. Para obter mais informações, consulte [Monitorar eventos usando EventGrid](job-state-events-cli-how-to.md ).

### <a name="jobs-definition"></a>Definição de trabalho

A tabela a seguir mostra as propriedades de trabalhos e fornece suas definições.

|NOME|DESCRIÇÃO|
|---|---|
|ID|ID de recurso totalmente qualificada para o recurso.|
|Nome   |O nome do recurso.|
|properties.correlationData |O Cliente forneceu os dados de correlação (imutáveis) que são retornados como parte das notificações de alteração de estado Job e JobOutput. Para obter mais informações, consulte [Esquemas de evento](media-services-event-schemas.md)<br/><br/>A propriedade também pode ser usada para uso com vários locatários dos Serviços de Mídia do Microsoft Azure. Você pode colocar IDs de locatário nos trabalhos. |
|properties.created |A data e hora UTC quando a transformação foi criada no formato “AAAA-MM-DDThh:mm:ssZ”.|
|properties.description |Descrição opcional fornecida para o cliente do trabalho.|
|properties.input|As entradas para o trabalho.|
|properties.lastModified    |A data e hora UTC quando o trabalho foi atualizado no formato “AAAA-MM-DDThh:mm:ssZ”.|
|properties.outputs|As saídas para o trabalho.|
|properties.priority    |Prioridade com que o trabalho deve ser processado. Trabalhos com prioridade mais alta são processados antes dos trabalhos de prioridade mais baixa. Se não for definido, o padrão é normal.|
|properties.state   |Estado atual do trabalho.|
|Tipo   |Tipo do recurso.|

Para a definição completa, consulte [Trabalhos](https://docs.microsoft.com/rest/api/media/jobs).

> [!NOTE]
> Enquanto a definição de trabalhos dá suporte a uma operação de atualização, as únicas propriedades que podem ser modificadas depois que o trabalho é enviado são a descrição e a prioridade. Além disso, uma alteração para a prioridade é eficaz somente se o trabalho ainda estiver em um estado na fila. Se o trabalho tiver iniciado o processamento ou tiver sido concluído, a alteração da prioridade não tem nenhum efeito.

### <a name="pagination"></a>Paginação

A paginação de trabalhos é compatível com os Serviços de Mídia do Microsoft Azure v3.

> [!TIP]
> Você deve sempre usar o próximo link para enumerar a coleção e não depender de um tamanho de página específico.

Se uma resposta de consulta contiver muitos itens, o serviço retornará uma propriedade "\@ odata.nextLink" para obter a próxima página de resultados. Isso pode ser usado para percorrer o conjunto de resultados inteiro. É possível configurar o tamanho da página. 

Se Trabalhos são criados ou excluídos durante a paginação por meio da coleção, as alterações são refletidas nos resultados retornados (se essas alterações estiverem na parte da coleção que não foi possível fazer o download.) 

O exemplo de C# a seguir mostra como enumerar todos os trabalhos na conta.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

Para obter exemplos REST, consulte [Trabalhos - Lista](https://docs.microsoft.com/rest/api/media/jobs/list)


## <a name="next-steps"></a>Próximas etapas

[Transmitir arquivos de vídeo](stream-files-dotnet-quickstart.md)
