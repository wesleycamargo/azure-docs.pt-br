---
title: Limites de serviço para camadas e SKUs - Azure Search
description: Limites do serviço usados para o planejamento de capacidade e limites máximos de solicitações e respostas para o Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 419c533aabd67637efa64777387c491dd890596e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024633"
---
# <a name="service-limits-in-azure-search"></a>Limites de serviço no Azure Search
Limites máximos de armazenamento, cargas de trabalho e quantidades de índices, documentos e outros objetos dependem se você [provisionar o Azure Search](search-create-service-portal.md) na **gratuito**, **básica**,  **Standard**, ou **otimizados para armazenamento** tipos de preço.

+ **Gratuito** é um serviço compartilhado multilocatário fornecido com sua assinatura do Azure.

+ **Básico** fornece recursos de computação dedicados para cargas de trabalho de produção em uma escala menor.

+ **Standard** é executado em computadores dedicados, com mais capacidade de armazenamento e processamento em cada nível. Standard é fornecido em quatro níveis: S1, S2, S3 e S3 HD.

+ **Armazenamento otimizado** é executado em computadores dedicados, com mais armazenamento total, largura de banda de armazenamento e memória do que **padrão**. Armazenamento otimizado é fornecido em dois níveis: L1 e L2

> [!NOTE]
> As camadas de serviço com otimização de armazenamento estão atualmente disponíveis como uma visualização a um preço com desconto para fins de teste e experimentação com o objetivo de coletar comentários. Preço final será anunciado posteriormente quando essas camadas são geralmente disponíveis. É recomendável usar essas camadas para aplicativos de produção.

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

| Resource | Grátis | Básico&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Índices máximos |3 |5 ou 15 |50 |200 |200 |1000 por partição ou 3000 por serviço |10 |10 |
| Máximo de campos simple por índice |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Campos de coleção complexa máximo por índice |40 |40 |40 |40 |40 |40 |40 |40 |
| Máximo de elementos em todas as coleções complexas por documento |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Profundidade máxima dos campos complexos |10 |10 |10 |10 |10 |10 |10 |10 |
| Número máximo de [encarregados da sugestão](https://docs.microsoft.com/rest/api/searchservice/suggesters) por índice |1 |1 |1 |1 |1 |1 |1 |1 |
| Número máximo de [perfis de pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) por índice |100 |100 |100 |100 |100 |100 |100 |100 |
| Máximo de funções por perfil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> serviços básicos criados antes de dezembro de 2017 têm limites mais baixos (5 em vez de 15) em índices. A camada tipo Básico é a única SKU com um limite inferior de 100 campos por índice.

<a name="document-limits"></a>

## <a name="document-limits"></a>Limites do documento 

A partir de outubro de 2018, não haverá mais limites de documentos para novos serviços criados em qualquer camada faturável (Básica, S1, S2, S3, S3 HD), em qualquer região. Embora a maioria das regiões tenha contagens de documentos ilimitadas desde novembro/dezembro de 2017, havia cinco regiões que continuavam impondo limites de documentos. Dependendo de quando e onde você criou um serviço de pesquisa, talvez esteja executando um serviço ainda sujeito a limites de documentos.

Para determinar se o serviço tem limites de documentos, verifique o bloco Uso na página de visão geral do serviço. As contagens de documentos são ilimitadas ou estão sujeitas a um limite com base na camada.

  ![Bloco Uso](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>Regiões que anteriormente tinham limites de documentos

Se o portal indicar um limite de documentos, significa que o serviço foi criado antes do final de 2017 ou foi criado em um datacenter usando clusters de baixa capacidade para hospedar serviços do Azure Search:

+ Leste da Austrália
+ Ásia Oriental
+ Índia Central
+ Oeste do Japão
+ Centro-Oeste dos EUA

Para serviços sujeitos a limites de documentos, aplicam-se os limites máximos a seguir:

|  Grátis | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10.000 |1&nbsp;milhões |15 milhões por partição ou 180 milhões por serviço |60 milhões por partição ou 720 milhões por serviço |120 milhões por partição ou 1,4 bilhão por serviço |1 milhão por serviço, 200 milhões por partição |

Se o serviço tiver limites que estejam causando bloqueio, crie um novo serviço e, em seguida, publique novamente todo o conteúdo desse serviço. Não há mecanismo para reprovisionar o serviço em um novo hardware nas cenas.

> [!Note] 
> Para serviços de Alta Densidade S3 criados no final de 2017, o documento de 200 milhões por partição foi removido, mas o limite de 1 milhão de documentos por índice permanece.


### <a name="document-size-limits-per-api-call"></a>Limites de tamanho do documento por chamada à API

O tamanho máximo do documento ao chamar uma API de Índice é de aproximadamente 16 megabytes.

O tamanho do documento é realmente um limite ao tamanho do corpo da solicitação de API do Índice. Como é possível transmitir um lote de vários documentos para a API de Índice de uma só vez, o limite de tamanho depende praticamente de quantos documentos estão no lote. Para um lote com um único documento, o tamanho máximo de documentos é de 16 MB de JSON.

Para reduzir o tamanho do documento, lembre-se de excluir dados não consultáveis da solicitação. Imagens e outros dados binários não podem ser diretamente consultados e não devem ser armazenados no índice. Para integrar dados que não podem ser consultados aos resultados da pesquisa, defina um campo não pesquisável que armazene uma referência uma URL para o recurso.

## <a name="indexer-limits"></a>Limites de indexador

Tempos de execução máximo existem para fornecer o equilíbrio e estabilidade para o serviço como um todo, mas conjuntos de dados maiores, talvez seja necessário indexação mais tempo do que o máximo permitido. Se um trabalho de indexação não puder ser concluído no tempo máximo permitido, tente executá-lo de forma agendada. O agendador mantém monitora o status da indexação. Se um trabalho de indexação agendado for interrompido por algum motivo, o indexador poderá continuar de onde parou na próxima execução agendada.


| Resource | Gratuito&nbsp;<sup>1</sup> | Básico&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Indexadores máximos |3 |5 ou 15|50 |200 |200 |N/D |10 |10 |
| Máximo de fontes de dados |3 |5 ou 15 |50 |200 |200 |N/D |10 |10 |
| Número máximo de conjuntos de habilidades <sup>4</sup> |3 |5 ou 15 |50 |200 |200 |N/D |10 |10 |
| Carga de indexação máxima por invocação |10.000 documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |Limitado apenas pelo máximo de documentos |N/D |Sem limite |Sem limite |
| Agenda mínimo | 5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos | 5 minutos |
| Tempo de execução máximo <sup>5</sup> | 1 a 3 minutos |24 horas |24 horas |24 horas |24 horas |N/D  |24 horas |24 horas |
| Tempo de execução máximo de conjuntos de habilidades da pesquisa cognitiva ou de indexação de blobs com análise de imagens <sup>5</sup> | 3 a 10 minutos |2 horas |2 horas |2 horas |2 horas |N/D  |2 horas |2 horas |
| Indexador de blob: tamanho máximo do blob, MB |16 |16 |128 |256 |256 |N/D  |256 |256 |
| Indexador de blob: número máximo de caracteres de conteúdo extraído de um blob |32.000 |64.000 |4&nbsp;milhões |4&nbsp;milhões |4&nbsp;milhões |N/D |4&nbsp;milhões |4&nbsp;milhões |

<sup>1</sup> Os serviços gratuitos têm um tempo máximo de execução do indexador de 3 minutos para fontes do blob e 1 minuto para todas as outras fontes de dados. Para IA indexação que chama em serviços Cognitivos, serviços gratuitos são limitados a 20 transações gratuitas por dia, em que a transação é definida como um documento aprovado com êxito por meio do pipeline enriquecimento.

<sup>2</sup> serviços básicos criados antes de dezembro de 2017 têm limites mais baixos (5 em vez de 15) em indexadores, fontes de dados e conjuntos de habilidades.

<sup>3</sup> Os serviços do S3 HD não incluem suporte ao indexador.

<sup>4</sup> Máximo de 30 habilidades por conjunto de habilidades.

<sup>5</sup> Cargas de trabalho da pesquisa cognitiva e da análise de imagens na indexação de blobs do Azure têm tempos de execução mais curtos do que a indexação de texto normal. A análise de imagens e o processamento de idioma natural fazem uso intensivo dos recursos de computação e consomem uma quantidade desproporcional da capacidade de processamento disponível. O tempo de execução foi reduzido para dar aos outros trabalhos na fila a oportunidade de serem executados.  

## <a name="queries-per-second-qps"></a>Consultas por segundo (QPS)

As estimativas QPS devem ser desenvolvidas independentemente por cada cliente. Tamanho do índice e complexidade, o tamanho da consulta e complexidade e a quantidade de tráfego são determinantes principais de QPS. Não é possível oferecer estimativas significativas quando esses fatores são desconhecidos.

As previsões são mais previsíveis quando calculada em serviços em execução em recursos dedicados (camadas Básico e Standard). No nível padrão, é possível estimar melhor o QPS, porque você tem controle sobre mais parâmetros. Para obter orientação sobre como a estimativa da abordagem, consulte [desempenho e otimização do Azure Search](search-performance-optimization.md).

Para as camadas de armazenamento otimizado, você deve esperar uma taxa de transferência menor de consulta e uma latência maior do que as camadas Standard.  A metodologia para estimar o desempenho de consulta que você enfrentará é o mesmo que as camadas Standard.

## <a name="data-limits-cognitive-search"></a>Limites de dados (pesquisa cognitiva)

Um [pipeline de pesquisa cognitiva](cognitive-search-concept-intro.md) que faz chamadas a um recurso de Análise de Texto para [reconhecimento de entidade](cognitive-search-skill-entity-recognition.md), [extração de frases-chave](cognitive-search-skill-keyphrases.md), [análise de sentimento](cognitive-search-skill-sentiment.md) e [detecção de idioma](cognitive-search-skill-language-detection.md) está sujeito a limites de dados. O tamanho máximo de um registro deve ser 50.000 caracteres conforme medido por `String.Length`. Se você precisar interromper o backup de seus dados antes de enviá-lo ao analisador de sentimentos, use a [habilidade Text Split](cognitive-search-skill-textsplit.md).

## <a name="api-request-limits"></a>Limites de solicitação de API
* Máximo de 16 MB por solicitação <sup>1</sup>
* Comprimento máximo da URL de 8 KB
* Máximo de 1000 documentos por lote de carregamentos, mesclagens ou exclusões de índice
* Máximo de 32 campos na cláusula $orderby
* O tamanho máximo do termo de pesquisa é de 32.766 bytes (32 KB menos 2 bytes) de texto codificado em UTF-8

<sup>1</sup> no Azure Search, o corpo de uma solicitação está sujeito a um limite superior de 16 MB, impondo um limite prático no conteúdo de campos individuais ou Coleções que, caso contrário, não são limitadas por limites teóricos (consulte [Tipos de dados com suporte](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para obter mais informações sobre restrições e composição de campos).

## <a name="api-response-limits"></a>Limites de resposta da API
* Máximo de 1000 documentos retornados por página de resultados da pesquisa
* Máximo de 100 sugestões retornadas por solicitação de Sugerir API

## <a name="api-key-limits"></a>Limites de chave de API
Chaves de API são usadas para autenticação de serviço. Há dois tipos. Chaves de administração são especificadas no cabeçalho da solicitação e concedem acesso completo de leitura/gravação ao serviço. Chaves de consulta são somente leitura, especificadas na URL, e, geralmente, são distribuídas para aplicativos cliente.

* Máximo de duas chaves de administração por serviço
* Máximo de 50 chaves de consulta por serviço
