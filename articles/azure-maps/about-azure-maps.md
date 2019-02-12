---
title: Visão geral dos Mapas do Azure | Microsoft Docs
description: Uma introdução aos Mapas do Azure
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ce55185effc67709157ce6219c405bb8e32dd5db
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768570"
---
# <a name="what-is-azure-maps"></a>O que é o Azure Mapas?

Os Azure Mapas são uma coleção de serviços geoespaciais, desafixados pelos mais novos dados de mapeamento disponíveis, fornecendo um contexto geográfico preciso para seus aplicativos Web e móveis. Os Azure Mapas consistem em APIs REST para renderização de **mapas** em vários estilos e imagens de satélite, **pesquisa** de endereços, locais e pontos de interesse em todo o mundo; **roteamento** ponto a ponto, multiponto, otimização de multiponto, isócrono, veículo comercial, influenciado por tráfego e roteamento de matriz; exibição do melhor fluxo de tráfego e incidentes; estabelecimento da localização do usuário por meio da **geolocalização**; e conversão de localização para **fusos horários**, bem como fetch de tempo em uma localização. Além disso, os Azure Mapas oferecem serviços de **delimitação geográfica**, armazenamento de **dados** de mapa – hospedagem das informações de localização no Azure; e **operações espaciais** que fornecem inteligência de localização por meio da análise geoespacial. Os serviços dos Azure Mapas estão disponíveis diretamente como APIs REST ou por meio de nossos robustos **SDK da Web** ou **SDK do Android**. Essas ferramentas permitem que os desenvolvedores desenvolvam e dimensionem rapidamente soluções que integram as informações de localização às soluções do Azure na nuvem do Azure. Inscreva-se hoje mesmo para obter sua [conta dos Azure Mapas](https://azure.microsoft.com/services/azure-maps/) gratuita e começar a desenvolver!

O vídeo a seguir explica os Mapas do Azure com detalhes:

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Controles de Mapeamento

### <a name="web-control"></a>Controle da Web

O controle de Web do Azure Mapas lhe permite personalizar mapas interativos com seu próprio conteúdo e imagens para exibição em seus aplicativos móveis ou Web. Esse controle utiliza WebGL, permitindo que você processe grandes conjuntos de dados com alto desempenho. Desenvolva com o controle usando JavaScript ou TypeScript.

![Controle de Web do Azure Mapas](media/about-azure-maps/Introduction_WebMapControl.png)

## <a name="services-in-azure-maps"></a>Serviços dos Mapas do Azure

Os Mapas do Azure são compostos pelos seis serviços a seguir que podem oferecer contexto geográfico aos aplicativos Azure.

### <a name="render-service"></a>Serviço de Renderização

O Serviço de renderização foi concebido para os desenvolvedores criarem aplicativos Web e móveis baseados em mapeamento. O serviço usa imagens gráficas de varredura de alta qualidade, disponíveis em 19 de níveis de zoom ou imagens de mapa do formato de vetor totalmente personalizável.

![Map.png de Mapas do Azure](media/about-azure-maps/Introduction_Map.png)

O Serviço de renderização agora oferece APIs para permitir que os desenvolvedores trabalhem com imagens de satélite. Para obter mais detalhes, leia as [APIs de renderização dos Mapas do Azure](https://docs.microsoft.com/rest/api/maps/render).

### <a name="route-service"></a>Serviço de Roteamento

O Serviço de roteiros contém cálculos robustos de geometria de infraestrutura do mundo real e instruções para vários modos de transporte. Ele permite que os desenvolvedores calculem as instruções entre vários modos de viagem como carro, caminhão, bicicleta ou caminhada. O serviço também pode considerar entradas como condições de tráfego, restrições de peso ou transporte de material perigoso.

![Route.png de Mapas do Azure](media/about-azure-maps/Introduction_Route.png)

O Serviço de roteiros agora oferece uma versão prévia dos recursos avançados como o processamento em lotes de várias solicitações de roteiros, matrizes de tempo de viagem e a distância entre um conjunto de origens e destinos, além da localização de roteiros ou distâncias que você pode percorrer com base nas exigências de tempo ou de combustível. Para obter detalhes sobre os recursos de roteiro, leia as [APIs de roteiro dos Mapas do Azure](https://docs.microsoft.com/rest/api/maps/route).

### <a name="search-service"></a>Serviço Search

O Serviço de pesquisa foi concebido para que os desenvolvedores procurem por endereços, locais, listagens de negócios por nome ou categoria e outras informações geográficas. O Serviço de pesquisa também pode [inverter os endereços código geográfico](https://en.wikipedia.org/wiki/Reverse_geocoding) e fazer o cruzamento de ruas baseado em latitudes e longitudes.

![Search.png de Mapas do Azure](media/about-azure-maps/Introduction_Search.png)

O Serviço de pesquisa também fornece recursos avançados, como pesquisar ao longo de uma rota, pesquisar em uma área mais ampla, agrupar um grupo de solicitações de pesquisa em lotes e pesquisar uma área maior em vez de um ponto de localização. As APIs de lote e a pesquisa de área estão atualmente em versão prévia. Para obter detalhes sobre os recursos de pesquisa, leia a página [APIs de pesquisa dos Mapas do Azure](https://docs.microsoft.com/rest/api/maps/search).

### <a name="time-zone-service"></a>Serviço de fuso horário

O Serviço de fuso horário permite consultar informações de fuso horário atuais, históricos e informações futuras de fuso horário usando qualquer um dos pares de latitude-longitude ou uma [ID IANA](https://www.iana.org/). O Serviço de Fuso Horário também permite converter IDs de fuso horário do Microsoft Windows em fusos horários IANA, buscar uma diferença de fuso horário em relação ao UTC e obter a hora atual em um fuso horário respectivo. Uma resposta JSON normal para uma consulta para o Serviço de Fuso Horário é semelhante ao exemplo a seguir:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

Para obter detalhes sobre esse serviço, visite a página [APIs de fuso horário dos Mapas do Azure](https://docs.microsoft.com/rest/api/maps/timezone).

### <a name="traffic-service"></a>Serviço de Tráfego

O Serviço de tráfego é um conjunto de serviços Web projetado para os desenvolvedores criarem aplicativos Web e móveis que exigem tráfego. O serviço fornece dois tipos de dados:

* Fluxo de tráfego: velocidades observadas em tempo real e tempos de deslocamento para todas as estradas principais na rede.
* Incidentes de Tráfego: uma visão precisa sobre as obstruções e incidentes de tráfego na malha de ruas.

![Tráfego dos Mapas do Azure](media/about-azure-maps/Introduction_Traffic.png)

Visite a página de [APIs de tráfego dos Mapas do Azure](https://docs.microsoft.com/rest/api/maps/traffic) para obter mais informações.

### <a name="ip-to-location"></a>IP para Local

O serviço IP para Local permite visualizar o código de país de duas letras recuperado para um determinado endereço IP. Esse serviço pode ajudar você a personalizar e aprimorar a experiência do usuário impulsionando o conteúdo do aplicativo personalizado com base na localização geográfica.

Para obter informações sobre as APIs REST para o IP do serviço de Localização, visite a página [APIs de geolocalização do Azure Mapas](https://docs.microsoft.com/rest/api/maps/geolocation).

## <a name="programming-model"></a>Modelo de programação

Os Mapas do Azure foram criados para a mobilidade e podem fornecer aplicativos de plataforma cruzada. Eles usam um modelo de programação de linguagem independente e dão suporte à saída JSON por meio de [APIs REST](https://docs.microsoft.com/rest/api/maps/).

Além disso, os Mapas do Azure oferecem um [controle de mapa de JavaScript](https://docs.microsoft.com/javascript/api/azure-maps-control) conveniente com um modelo de programação simples para um desenvolvimento rápido e fácil de aplicativos Web e móveis.

## <a name="usage"></a>Uso

Para acessar os serviços dos Mapas, basta navegar até o [portal do Azure](https://portal.azure.com) e criar uma conta dos Mapas do Azure.

Os Mapas do Azure usam um esquema de autenticação baseado em chave. Sua conta é fornecida com duas chaves geradas previamente para você. Comece integrando esses recursos de localização em seus aplicativos usando qualquer uma de suas chaves e fazendo uma solicitação para o serviço do Azure Mapas.

## <a name="supported-regions"></a>Regiões com suporte

Atualmente, a API dos Mapas do Azure está disponível em todos os países, exceto nos seguintes:

* Argentina
* China
* Índia
* Marrocos
* Paquistão
* Coreia do Sul

Verifique se a localização do seu endereço IP atual não está em um dos países sem suporte acima.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os novos recursos dos Mapas do Azure:

> [!div class="nextstepaction"]
> [Matriz de roteiro, Isócronas, Pesquisa de IP e muito mais](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/)

Continue para experimentar um aplicativo de exemplo que mostra o serviço:

> [!div class="nextstepaction"]
> [Inicie uma demonstração de mapa de pesquisa interativa](quick-demo-map-app.md)
