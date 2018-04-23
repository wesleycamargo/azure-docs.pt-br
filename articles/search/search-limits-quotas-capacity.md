---
title: Limites de serviço no Azure Search | Microsoft Docs
description: Limites do serviço usados para o planejamento de capacidade e limites máximos de solicitações e respostas para o Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: heidist
ms.openlocfilehash: 59b6efd8459f675bc44891d7b04251260fdd38e9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="service-limits-in-azure-search"></a>Limites de serviço no Azure Search
Os limites máximos de armazenamento, cargas de trabalho e quantidades de índices, documentos e outros objetos dependem de você [provisionar o Azure Search](search-create-service-portal.md) nos tipos de preço **Gratuito**, **Básico** ou **Standard**.

+ **Gratuito** é um serviço compartilhado multilocatário fornecido com sua assinatura do Azure.

+ **Básico** fornece recursos de computação dedicados para cargas de trabalho de produção em uma escala menor.

+ **Standard** é executado em computadores dedicados, com mais capacidade de armazenamento e processamento em cada nível. Standard vem em quatro níveis: S1, S2, S3 e S3 HD.

  O S3 HD (S3 de Alta Densidade) foi projetado para cargas de trabalho específicas: [multilocatário](search-modeling-multitenant-saas-applications.md) e grandes quantidades de índices pequenos (um milhão de documentos por índice, três mil índices por serviço). Essa camada não fornece o [recurso de indexador](search-indexer-overview.md). No S3 HD, a ingestão de dados deve aproveitar a abordagem de push, usando chamadas à API para efetuar push de dados da origem para o índice. 

> [!NOTE]
> Um serviço é provisionado em uma camada específica. Saltar camadas para obter capacidade envolve o provisionamento de um novo serviço (não há nenhuma atualização local). Para obter mais informações, confira [Escolher uma camada ou SKU](search-sku-tier.md). Para saber mais sobre o ajuste de capacidade em um serviço já provisionado, consulte [Dimensionar níveis de recursos para cargas de trabalho de indexação e consulta](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limites de assinatura
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Limites de armazenamento
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Limites de índice

| Recurso | Grátis | Básico&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD |
| -------- | ---- | ------------------- | --- | --- | --- | --- |
| Índices máximos |3 |5 ou 15 |50 |200 |200 |1000 por partição ou 3000 por serviço |
| Máximo de campos por índice |1000 |100 |1000 |1000 |1000 |1000 |
| Máximo de perfis de pontuação por índice |100 |100 |100 |100 |100 |100 |
| Máximo de funções por perfil |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Os serviços básicos criados no final de 2017 têm um limite aumentado de 15 índices, fontes de dados e indexadores. Serviços criados anteriormente têm 5. A camada tipo Básico é a única SKU com um limite inferior de 100 campos por índice.

## <a name="document-limits"></a>Limites do documento 

Na maioria das regiões, os tipos de preço do Azure Search (Básico, S1, S2, S3, S3 HD) têm contagens de documentos ilimitadas para todos os serviços criados após novembro/dezembro de 2017. Esta seção identifica as regiões onde os limites aplicam-se e como determinar se o serviço foi afetado. 

Para determinar se o serviço tem limites de documentos, verifique o bloco Uso na página de visão geral do serviço. As contagens de documentos são ilimitadas ou estão sujeitas a um limite com base na camada.

  ![Bloco Uso](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-and-services-having-document-limits"></a>Regiões e serviços com limites de documentos

Serviços com limites ou foram criados antes do final de 2017 ou estão executando em data centers usando clusters com menor capacidade para hospedar os serviços do Azure Search. Os data centers afetados estão nas regiões a seguir:

+ Leste da Austrália
+ Ásia Oriental
+ Índia Central
+ Oeste do Japão
+ Centro-Oeste dos EUA

Para serviços sujeitos a limites de documentos, aplicam-se os limites máximos a seguir:

|  Grátis | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10.000 |1 milhão |15 milhões por partição ou 180 milhões por serviço |60 milhões por partição ou 720 milhões por serviço |120 milhões por partição ou 1,4 bilhão por serviço |1 milhão por serviço, 200 milhões por partição |

> [!Note] 
> Para serviços de Alta Densidade S3 criados no final de 2017, o documento de 200 milhões por partição foi removido, mas o limite de 1 milhão de documentos por índice permanece.


### <a name="document-size-limits-per-api-call"></a>Limites de tamanho do documento por chamada à API

O tamanho máximo do documento ao chamar uma API de Índice é de aproximadamente 16 megabytes.

O tamanho do documento é realmente um limite ao tamanho do corpo da solicitação de API do Índice. Como é possível transmitir um lote de vários documentos para a API de Índice de uma só vez, o limite de tamanho depende praticamente de quantos documentos estão no lote. Para um lote com um único documento, o tamanho máximo de documentos é de 16 MB de JSON.

Para reduzir o tamanho do documento, lembre-se de excluir dados não consultáveis da solicitação. Imagens e outros dados binários não podem ser diretamente consultados e não devem ser armazenados no índice. Para integrar dados que não podem ser consultados aos resultados da pesquisa, defina um campo não pesquisável que armazene uma referência uma URL para o recurso.

## <a name="indexer-limits"></a>Limites de indexador

Os serviços básicos criados no final de 2017 têm um limite aumentado de 15 índices, fontes de dados e indexadores.

| Recurso | Gratuito&nbsp;<sup>1</sup> | Básico&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|
| -------- | ----------------- | ----------------- | --- | --- | --- | --- |
| Indexadores máximos |3 |5 ou 15|50 |200 |200 |N/D |
| Máximo de fontes de dados |3 |5 ou 15 |50 |200 |200 |N/D |
| Carga de indexação máxima por invocação |10.000 documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |N/D |
| Tempo de execução máximo | 1 a 3 minutos |24 horas |24 horas |24 horas |24 horas |N/D  |
| Indexador de blob: tamanho máximo do blob, MB |16 |16 |128 |256 |256 |N/D  |
| Indexador de blob: número máximo de caracteres de conteúdo extraído de um blob |32.000 |64.000 |4 milhões |4 milhões |4 milhões |N/D |

<sup>1</sup> Os serviços gratuitos têm um tempo máximo de execução do indexador de 3 minutos para fontes do blob e 1 minuto para todas as outras fontes de dados.

<sup>2</sup> Os serviços básicos criados após o final de 2017 têm um limite aumentado de 15 índices, fontes de dados e indexadores. Serviços criados anteriormente têm 5.

<sup>3</sup> Os serviços do S3 HD não incluem suporte ao indexador.

## <a name="queries-per-second-qps"></a>Consultas por segundo (QPS)

As estimativas QPS devem ser desenvolvidas independentemente por cada cliente. Tamanho do índice e complexidade, o tamanho da consulta e complexidade e a quantidade de tráfego são determinantes principais de QPS. Não é possível oferecer estimativas significativas quando esses fatores são desconhecidos.

As previsões são mais previsíveis quando calculada em serviços em execução em recursos dedicados (camadas Básico e Standard). No nível padrão, é possível estimar melhor o QPS, porque você tem controle sobre mais parâmetros. Para obter orientação sobre como a estimativa da abordagem, consulte [desempenho e otimização do Azure Search](search-performance-optimization.md).

## <a name="api-request-limits"></a>Limites de Solicitação da API
* Máximo de 16 MB por solicitação <sup>1</sup>
* Comprimento máximo da URL de 8 KB
* Máximo de 1000 documentos por lote de carregamentos, mesclagens ou exclusões de índice
* Máximo de 32 campos na cláusula $orderby
* O tamanho máximo do termo de pesquisa é de 32.766 bytes (32 KB menos 2 bytes) de texto codificado em UTF-8

<sup>1</sup> no Azure Search, o corpo de uma solicitação está sujeito a um limite superior de 16 MB, impondo um limite prático no conteúdo de campos individuais ou Coleções que, caso contrário, não são limitadas por limites teóricos (consulte [Tipos de dados com suporte](https://msdn.microsoft.com/library/azure/dn798938.aspx) para obter mais informações sobre restrições e composição de campos).

## <a name="api-response-limits"></a>Limites de resposta da API
* Máximo de 1000 documentos retornados por página de resultados da pesquisa
* Máximo de 100 sugestões retornadas por solicitação de Sugerir API

## <a name="api-key-limits"></a>Limites de chave de API
As chaves de API são usadas para autenticação de serviço. Há dois tipos. Chaves de administração são especificadas no cabeçalho da solicitação e concedem acesso completo de leitura/gravação ao serviço. Chaves de consulta são somente leitura, especificadas na URL, e, geralmente, são distribuídas para aplicativos cliente.

* Máximo de duas chaves de administração por serviço
* Máximo de 50 chaves de consulta por serviço
