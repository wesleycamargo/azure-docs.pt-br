---
title: Limites – QnA Maker
titleSuffix: Azure Cognitive Services
description: O QnA Maker tem limites de meta para partes do serviço e da base de dados de conhecimento. É importante manter sua base de dados de conhecimento dentro desses limites para testar e publicar.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 772153040ac76f4b7bbee55c48527a841fc69037
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084770"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limites e limites de base de dados de conhecimento do QnA Maker
Lista abrangente dos limites no QnA Maker.

## <a name="knowledge-bases"></a>Bases de Dados de Conhecimento

* Número máximo de bases de dados de conhecimento com base em [limites de camada do Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Camada do Azure Search** | **Gratuito** | **Básico** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo de bases de dados de conhecimento publicadas permitidas (Máx. de índices – 1 (reservado para teste)|2|14|49|199|199|2,999|

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
* Número máximo de pares de pergunta-resposta: Depende da [camada do Azure Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits) escolhida 

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
