---
title: "Azure CosmosDB: unidades de solicitação por minuto (RU/m) | Microsoft Docs"
description: "Saiba como reduzir custos utilizando unidades de solicitação por minuto."
services: cosmos-db
documentationcenter: 
author: arnomicrosoft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: acomet
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 0c597fa4afa816f9731edb744ae494b6ef928b9e
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017


---
# <a name="request-units-per-minute-in-azure-cosmos-db"></a>Unidades de solicitação por minuto no BD Cosmos do Azure

O BD Cosmos do Azure foi criado para ajudá-lo a obter um desempenho rápido e previsível e a dimensionar continuamente conforme o crescimento de seu aplicativo. É possível provisionar a taxa de transferência em um contêiner do BD Cosmos com granularidades por segundo e por minuto (RU/m). A taxa de transferência provisionada com granularidade por minuto é usada para gerenciar picos inesperados na carga de trabalho que ocorrem com granularidade por segundo. 

Este artigo fornece uma visão geral de como o provisionamento da Unidade de Solicitação por Minuto (RU/m) funciona. A meta do provisionamento de RU/m é fornecer desempenho previsível diante de necessidades imprevisíveis (especialmente se você precisar executar análises de seus dados operacionais) e de cargas de trabalho que apresentam picos. Queremos que nossos clientes consumam mais da taxa de transferência que eles provisionam para que possam dimensionar rapidamente com tranquilidade.

Depois de ler este artigo, você poderá responder as seguintes perguntas:

* Como funciona uma Unidade de Solicitação por minuto?
* Qual é a diferença entre a Unidade de Solicitação por Minuto e a Unidade de Solicitação por Segundo?
* Como provisionar RU/m?
* Em que cenário eu devo considerar o provisionamento de Unidades de Solicitação por Minuto?
* Como usar as métricas do portal para otimizar meu custo e meu desempenho?
* Definir qual tipo de solicitação pode consumir seu orçamento para RU/m?

## <a name="provisioning-request-units-per-minute-rum"></a>Provisionamento de unidades de solicitação por minuto (RU/m)

Quando provisiona o BD Cosmos do Azure com granularidade de segundo (RU/s), você tem a garantia de que a solicitação será bem-sucedida com baixa latência se a taxa de transferência não tiver ultrapassado a capacidade provisionada dentro desse segundo. Com a RU/m, a granularidade é de minutos, com a garantia de que a solicitação terá êxito dentro desse minuto. Em comparação com sistemas de intermitência, garantimos que o desempenho obtido será previsível e você poderá fazer planos com base nele.

A forma como o provisionamento por minuto funciona é simples:

* A unidade RU/m é cobrada por hora, além da RU/s. Para obter mais detalhes, visite a [página de preços](https://aka.ms/acdbpricing) do BD Cosmos do Azure.
* A RU/m pode ser habilitada no nível da coleção. Isso pode ser feito por meio dos SDKs (Node.js, Java ou .Net) ou por meio do portal (também inclui cargas de trabalho da API do MongoDB)
* Quando a RU/m está habilitada, para cada 100 RU/s provisionadas, também são provisionadas 1.000 RU/m (a taxa é de 10 vezes)
* Em um determinado segundo, uma unidade de solicitação consome o provisionamento de RU/m somente se você tiver ultrapassado seu provisionamento por segundo dentro desse segundo
* Quando o período de 60 segundos (UTC) termina, o provisionamento por minuto é recarregado
* A RU/m pode ser habilitada apenas para coleções com provisionamento máximo de 5.000 RU/s por partição. Se dimensionar suas necessidades de taxa de transferência e tiver um alto nível de provisionamento por partição, você receberá uma mensagem de aviso

Abaixo, temos um exemplo concreto, em que um cliente pode provisionar 10kRU/s com 100kRU/m, economizando 73% dos custos em comparação com o provisionamento para o pico (com 50kRU/s) por um período de 90 segundos em uma coleção que tem 10.000 RU/s e 100.000 RU/m provisionadas:

* 1º segundo: o orçamento de RU/m está definido como 100.000
* 3º segundo: durante esse segundo, o consumo de unidades de solicitação foi de 11.010 RUs; 1.010 RUs acima do provisionamento de RU/s. Portanto, 1.010 RUs são deduzidas do orçamento de RU/m. 98.990 RUs estão disponíveis para os próximos 57 segundos no orçamento de RU/m
* 29º segundo: durante esse segundo, um grande aumento aconteceu (mais de 4 vezes maior que o provisionamento por segundo) e o consumo de unidades de solicitação foi de 46.920 RUs. 36.920 RUs são deduzidas do orçamento de RU/m, que caiu de 92.323 RUs (28º segundo) para 55.403 RUs (29º segundo)
* 61º segundo: o orçamento de RU/m volta para 100.000 RUs.
 
![Gráfico mostrando o consumo e o provisionamento do BD Cosmos do Azure](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute.png)

## <a name="specifying-request-unit-capacity-with-rum"></a>Especificando a capacidade da unidade de solicitação com RU/m

Ao criar uma coleção do BD Cosmos do Azure, você especifica o número de unidades de solicitação por segundo (RU por segundo) que deseja reservar para a coleção. Você também pode decidir se deseja adicionar a RU por minuto. Isso pode ser feito usando o Portal ou o SDK. 

### <a name="through-the-portal"></a>Usando o Portal

Para habilitar ou desabilitar a RU por minuto, basta um clique ao provisionar uma coleção. 

 ![Captura de tela mostrando como definir a RU/m no Portal do Azure](./media/request-units-per-minute/azure-cosmos-db-request-unit-per-minute-portal.png)

### <a name="through-the-sdk"></a>Usando o SDK
Primeiro, é importante observar que a RU/m só está disponível para os seguintes SDKs:

* .Net 1.14.0
* Java 1.11.0
* Node.js 1.12.0
* Python 2.2.0

Veja um trecho de código para criar uma coleção com 3.000 unidades de solicitação por segundo e 30.000 unidades de solicitação por minuto usando o SDK do .NET:

```csharp
// Create a collection with RU/m enabled
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

// Set the throughput to 3,000 request units per second which will give you 30,000 request units per minute as the RU/m budget
await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 3000, OfferEnableRUPerMinuteThroughput = true });
```

Veja um trecho de código para alterar a taxa de transferência de uma coleção para 5.000 unidades de solicitação por segundo sem provisionar a RU por minuto usando o SDK do .NET:

```csharp
// Get the current offer
Offer offer = client.CreateOfferQuery()
    .Where(r => r.ResourceLink == collection.SelfLink)    
    .AsEnumerable()
    .SingleOrDefault();

// Set the throughput to 5000 request units per second without RU/m enabled (the last parameter to OfferV2 constructor below)
OfferV2 offerV2 = new OfferV2(offer, 5000, false);

// Now persist these changes to the database by replacing the original resource
await client.ReplaceOfferAsync(offerV2);
```

## <a name="good-fit-scenarios"></a>Cenários adequados

Nesta seção, fornecemos uma visão geral dos cenários em que é adequado habilitar as unidades de solicitação por minuto.

**Ambiente de desenvolvimento e teste:** adequado. Durante a fase de desenvolvimento, se você estiver testando seu aplicativo com cargas de trabalho diferentes, a RU/m pode fornecer flexibilidade neste estágio. Ao mesmo tempo, o [emulador](local-emulator.md) é uma ótima ferramenta gratuita para testar o BD Cosmos do Azure. No entanto, se quiser começar em um ambiente de nuvem, você terá grande flexibilidade com a RU/m para suas necessidades de desempenho ad hoc. Você passará mais tempo desenvolvendo e menos tempo se preocupando com as necessidades de desempenho no início. É recomendável começar com o provisionamento mínimo de RU/s e habilitar a RU/m.

**Necessidades imprevisíveis de granularidade por minuto, com picos de uso:** adequado – economia de 25 a 75%. Já vimos uma grande melhoria com relação à RU/m e a maioria dos cenários de produção está nesse grupo. Se tiver uma carga de trabalho de IoT que apresenta picos algumas vezes em um minuto e, se tiver consultas em execução quando o sistema faz uma inserção em massa ao mesmo tempo, você precisará de capacidade extra para lidar com esses picos. Recomendamos otimizar suas necessidades de recursos aplicando nossa abordagem passo a passo abaixo.

 ![Gráfico mostrando o consumo de solicitação com granularidade de 5 minutos](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-consumption.png)
 
 *Figura – benchmark de consumo de RU*

**Tranquilidade:** adequado – economia de 10 a 20%. Às vezes, você só quer ter tranquilidade e não se preocupar com possíveis picos e limitações. Este recurso é o ideal para você. Nesse caso, recomendamos habilitar a RU/m e reduzir ligeiramente seu provisionamento por segundo. Este caso é diferente do acima, pois você não tentará otimizar agressivamente o provisionamento. Trata-se de uma abordagem de "limitação zero" que você tem em mente.

Operações críticas com necessidades ad hoc: às vezes, é recomendável permitir que apenas operações críticas tenham acesso ao orçamento de RU/m para que o orçamento não seja consumido por operações ad hoc ou operações menos importantes. Isso pode ser definido com facilidade na seção abaixo.

## <a name="using-the-portal-metrics-to-optimize-cost-and-performance"></a>Usando as métricas do portal para otimizar o custo e o desempenho

**Nas próximas semanas, desenvolveremos mais o conteúdo sobre o monitoramento do consumo de RUs por minuto para otimizar suas necessidades de taxa de transferência.**

Por meio das métricas do portal, você pode quantos segundos de RU regulares são consumidos, em comparação com os minutos de RU. Monitorar essas métricas deve ajudá-lo a otimizar o provisionamento. 

É recomendável uma abordagem passo a passo para usar a RU/m a seu favor. Para cada etapa, você deve ter uma visão geral do consumo de RU que representa um ciclo completo de sua carga de trabalho (ele pode ser de horas, dias ou até mesmo semanas) e deve ter informações sobre a utilização daquilo que provisionar.

O princípio por trás dessa abordagem é fazer com que o provisionamento de taxa de transferência seja o mais próximo possível de um ponto de provisionamento que corresponda aos critérios de desempenho abaixo. 

![Gráfico mostrando o consumo de solicitação com granularidade de 5 minutos](./media/request-units-per-minute/azure-cosmos-db-request-units-per-minute-adjust-provisioning.png)
 
Para compreender o ponto de provisionamento ideal para sua carga de trabalho, você precisa compreender:

* Padrões de consumo: nenhum pico, picos frequentes ou picos prolongados? Picos pequenos (média de 2 vezes), medianos ou grandes (média de mais de 10 vezes)?
* Porcentagem de solicitações limitadas: você se sente confortável se tiver um pouco de limitação? Em caso afirmativo, quanto? 

Depois de identificar quais são suas metas, você poderá se aproximar do provisionamento ideal.

Para ajudá-lo, queremos fornecer orientações gerais de como otimizar o provisionamento com base em seu consumo de RU/m. Estas orientações não se aplicam a todos os tipos de cargas de trabalho, mas são baseadas em conhecimentos sobre o modo de versão prévia particular. Essas linhas de base poderão ser alteradas conforme aprendermos mais:

|% de utilização da RU/m|Grau de utilização da RU/m|Ações recomendadas para provisionamento|
|---|---|---|
|0-1%|Subutilização|Diminuir RU/s para consumir mais RU/m|
|1-10%|Uso íntegro|Manter o mesmo nível de provisionamento|
|Acima de 10%|Utilização excessiva|Aumentar RU/s para depender menos de RU/m|

## <a name="select-which-operations-can-consume-the-rum-budget"></a>Selecionar quais operações podem consumir o orçamento de RU/m

No nível da solicitação, você pode também habilitar/desabilitar o orçamento de RU/m para atender à solicitação, independentemente do tipo de operação. Se o orçamento de RUs/s regular provisionado for consumido e a solicitação não puder consumir o orçamento de RU/m, essa solicitação será limitada. Por padrão, qualquer solicitação será atendida pelo orçamento de RU/m se o orçamento de RU/m para taxa de transferência estiver ativado. 

Veja um trecho de código para desabilitar o orçamento de RU/m usando a API do DocumentDB para operações CRUD e de consulta.

```csharp
// In order to disable any CRUD request for RU/m, set DisableRUPerMinuteUsage to true in RequestOptions
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    new Document { Id = "Cosmos DB" },
    new RequestOptions { DisableRUPerMinuteUsage = true });
// In order to disable any query request for RU/m, set DisableRUPerMinuteOnRequest to true in RequestOptions
FeedOptions feedOptions = new FeedOptions();
feedOptions.DisableRUPerMinuteUsage = true;
var query = client.CreateDocumentQuery<Book>(
    UriFactory.CreateDocumentCollectionUri("db", "container"),
    "select * from c",feedOptions).AsDocumentQuery();
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, descrevemos como o particionamento funciona no BD Cosmos do Azure, como você pode criar coleções particionadas e como pode escolher uma boa chave de partição para seu aplicativo.

* Executar testes de desempenho e escala com o BD Cosmos do Azure. Consulte [Teste de desempenho e escala com o BD Cosmos do Azure](performance-testing.md) para obter um exemplo.
* Introdução à codificação com os [SDKs](documentdb-sdk-dotnet.md) ou a [API REST](/rest/api/documentdb/).
* Saiba mais sobre a [taxa de transferência provisionada](request-units.md) no BD Cosmos do Azure 


