---
title: Limites – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker tem limites de meta para partes do serviço e da base de dados de conhecimento. É importante manter sua base de dados de conhecimento dentro desses limites para testar e publicar.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/26/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: fe15cafceea8128735f7241fa5e4187d4d9c47a9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960047"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limites e limites de base de dados de conhecimento do QnA Maker
Lista abrangente dos limites no QnA Maker.

## <a name="knowledge-bases"></a>Bases de Dados de Conhecimento

* Número máximo de bases de dados de conhecimento com base em [limites de camada do Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Camada do Azure Search** | **Gratuito** | **Básico** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo de bases de dados de conhecimento publicadas permitidas|2|14|49|199|199|2,999|

 Por exemplo, se a camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento (1 índice por base de dados de conhecimento publicada). O décimo quinto índice, `testkb`, é usado para todas as bases de dados de conhecimento para criação e teste. 

## <a name="extraction-limits"></a>Limites de extração
* Número máximo de arquivos que podem ser extraídos e o tamanho máximo do arquivo: Consulte os [preços do QnAMaker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)
* Número máximo de links profundos que podem ser rastreados para extração de QnAs de páginas HTML de perguntas Frequentes: 20

## <a name="metadata-limits"></a>Limites de metadados
* Número máximo de campos de metadados por base de dados de conhecimento com base nos [limites de camada do Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Camada do Azure Search** | **Gratuito** | **Básico** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Máximo de campos de metadados por serviço QnA Maker (entre todas as bases de dados de conhecimento)|1.000|100*|1.000|1.000|1.000|1.000|

## <a name="knowledge-base-content-limits"></a>Limites de conteúdo da Base de Dados de Conhecimento
Limites gerais sobre o conteúdo na base de dados de conhecimento:
* Tamanho do texto de resposta: 25.000
* Tamanho do texto da pergunta: 1.000
* Tamanho do texto de chave/valor dos metadados: 100
* Caracteres com suporte para nome de metadados: caracteres alfabéticos, dígitos e _  
* Caracteres com suporte para valor de metadados: Todos, exceto : e | 
* Tamanho do nome do arquivo: 200
* Formatos de arquivo com suporte: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Número máximo de perguntas alternativas: 100
* Número máximo de pares de pergunta-resposta: Depende de [camada de Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits) escolhido. Um par de pergunta e resposta é mapeado para um documento no índice de Azure Search. 

## <a name="create-knowledge-base-call-limits"></a>Criar limites de chamada da base de dados de conhecimento:
Eles representam os limites de cada ação de criação da base de dados de conhecimento; ou seja, clicar em *Criar KB* ou chamar a API CreateKnowledgeBase.
* Número máximo de perguntas alternativas por resposta: 100
* Número máximo de URLs: 10
* Número máximo de arquivos: 10

## <a name="update-knowledge-base-call-limits"></a>Atualizar limites de chamada da base de dados de conhecimento
Eles representam os limites de cada ação de atualização; ou seja, clique em *Salvar e treinar* ou chame a API UpdateKnowledgeBase.
* Tamanho de cada nome de origem: 300
* Número máximo de perguntas alternativas adicionadas ou excluídas: 100
* Número máximo de campos de metadados adicionados ou excluídos: 10
* Número máximo de URLs que podem ser atualizadas: 5

## <a name="next-steps"></a>Próximas etapas

Saiba quando e como alterar as camadas de serviço:

* [O QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): Quando você precisa ter mais arquivos de origem ou quanto maiores os documentos em sua base de dados de Conhecimento, além de sua camada atual, atualize seu serviço QnA Maker tipo de preço.
* [O serviço de aplicativo](how-to/upgrade-qnamaker-service.md#upgrade-app-service): Quando sua base de dados de conhecimento precisar fornecer mais solicitações do aplicativo cliente, atualize o tipo de preço do serviço de aplicativo.
* [O Azure Search](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): Ao planejar ter muitas bases de conhecimento, atualize o tipo de preço do serviço do Azure Search.
