---
title: Visão geral dos Serviços de Mídia v3 do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral de alto nível dos Serviços de Mídia e fornece links para artigos com mais detalhes.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: serviços de mídia do azure, transmissão, difusão, ao vivo, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 06/14/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 5205a6746f6a698768a60375e2e77db9cb535a71
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971900"
---
# <a name="what-is-azure-media-services-v3"></a>O que são os Serviços de Mídia v3 do Azure?

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Versão 2 - GA](../previous/media-services-overview.md)
> * [Versão 3 – Versão prévia](media-services-overview.md)

> [!NOTE]
> A versão mais recente do Azure Media Services está em versão prévia e pode ser chamada de v3.

Os Serviços de Mídia do Azure são uma plataforma baseada em nuvem que permite a criação de soluções que apresentem uma transmissão de vídeo com qualidade de difusão, aumentem a acessibilidade e a distribuição, analisem o conteúdo e muito mais. Se você for um desenvolvedor de aplicativos, call center, órgão governamental, empresa de entretenimento, os Serviços de Mídia podem ajudar a criar aplicativos que oferecem experiências de mídia de qualidade superior para um público amplo nos dispositivos móveis e navegadores mais populares da atualidade. 

## <a name="what-can-i-do-with-media-services"></a>O que posso fazer com os Serviços de Mídia?

Os Serviços de Mídia permitem criar uma série de fluxos de trabalho de mídia na nuvem. Veja a seguir alguns exemplos do que pode ser feito com os Serviços de Mídia.  

* Fornecer vídeos em vários formatos, para que eles possam ser reproduzidos em uma série de navegadores e dispositivos. Para a entrega de vídeos sob demanda e transmissões ao vivo a vários clientes (dispositivos móveis, TV, PC, etc.) o conteúdo de vídeo e áudio precisa ser codificado e embalado de forma apropriada. Para ver como fornecer e transmitir esse tipo de conteúdo, confira [Início Rápido: Codificar e transmitir arquivos](stream-files-dotnet-quickstart.md).
* Transmita eventos esportivos ao vivo para um público amplo online, como partidas de futebol, basebol, esportes universitários e escolares, e muito mais. 
* Transmita reuniões públicas e eventos como fóruns, reuniões da câmara de vereadores e órgãos legislativos.
* Analise o conteúdo do áudio ou vídeo gravado. Por exemplo, para alcançar maior satisfação dos clientes, as organizações podem extrair a conversão de fala em texto e criar índices de pesquisa e painéis. Em seguida, elas podem extrair inteligência sobre reclamações comuns, fontes de reclamações e outros dados relevantes. 
* Crie um serviço de assinatura de vídeo e transmita conteúdo protegido por DRM quando um cliente (por exemplo, um estúdio de cinema) precisar restringir o acesso e uso de um trabalho protegido por direitos autorais.
* Forneça conteúdo offline para reprodução em aviões, trens e automóveis. Talvez um cliente precise baixar o conteúdo no telefone ou tablet para reprodução quando ele estiver desconectado da rede.
* Adicione subtítulos e legendas a vídeos para atender a um público mais amplo (por exemplo, pessoas com deficiência auditiva ou que desejam acompanhar a leitura em um idioma diferente). 
* Implemente uma plataforma de vídeo educacional de aprendizado eletrônico com os Serviços de Mídia do Azure e a [API de Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) para fazer a conversão de fala em texto de legenda, traduzir para vários idiomas, etc.
* Habilite a CDN do Microsoft Azure para conseguir um grande dimensionamento para lidar melhor com alta carga instantânea (por exemplo, no início de um evento de lançamento de produto). 

## <a name="v3-capabilities"></a>Funcionalidades da v3

A v3 é baseada em uma superfície de API unificada que expõe a funcionalidade de operações e gerenciamento compilada no Azure Resource Manager. 

Esta versão fornece os seguintes recursos:  

* **Transformações** que ajudam a definir fluxos de trabalho simples de processamento de mídia ou tarefas de análise. A Transformação é uma receita para processar os seus arquivos de áudio e vídeo. Em seguida, você pode aplicá-la repetidamente para processar todos os arquivos em sua biblioteca de conteúdos, enviando trabalhos para a Transformação.
* **Trabalhos** para processar (codificar ou analisar) seus vídeos. Um conteúdo de entrada pode ser especificado em um trabalho, usando URLs de HTTP(s), URLs de SAS ou caminhos para arquivos localizados no Armazenamento de Blobs do Azure. 
* **Notificações** que monitoram o andamento ou estado do trabalho ou eventos iniciar/parar e de erro do Canal ao Vivo. As notificações são integradas com o sistema de notificação de Grade de Eventos do Azure. Você pode se inscrever com facilidade para eventos em vários recursos nos Serviços de Mídia do Microsoft Azure. 
* Os modelos de **Gerenciamento de Recursos do Azure** podem ser usados para criar e implantar as Transformações, Pontos de Extremidade de Transmissão, Canais e muito mais.
* O **Controle de acesso baseado em função** pode ser definido a nível de recurso, permitindo bloquear o acesso a recursos específicos, como Transformações, Canais e muito mais.
* **SDKs de Clientes** em várias linguagens: .NET, .NET core, Python, Go, Java e Node.js.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Nomes de recursos do Serviços de Mídia do Azure v3 (por exemplo, ativos, trabalhos, transformações) estão sujeitos a restrições de nomenclatura do Azure Resource Manager. De acordo com o Azure Resource Manager, os nomes dos recursos são sempre exclusivos. Desse modo, é possível usar qualquer cadeia de caracteres de identificador exclusivo (por exemplo, GUIDs) para os nomes dos recursos. 

Nomes de recurso dos Serviços de Mídia não podem incluir: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', o caractere de aspas simples ou quaisquer caracteres de controle. Todos os outros caracteres são permitidos. O comprimento máximo de um nome de recurso é de 260 caracteres. 

Para obter mais informações sobre a nomenclatura do Azure Resource Manager, consulte: [Requisitos de nomenclatura](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) e [Convenções de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="how-can-i-get-started-with-v3"></a>Como posso começar a v3?

Como desenvolvedor, você pode usar a [API REST](https://go.microsoft.com/fwlink/p/?linkid=873030) dos Serviços de Mídia ou bibliotecas de cliente que permitam interagir com a API REST, para criar, gerenciar e manter fluxos de trabalho de mídia personalizados com facilidade. Você pode encontrar um exemplo da REST do Postman [aqui](https://github.com/Azure-Samples/media-services-v3-rest-postman). Você também pode usar a [API REST baseada no Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates).

A Microsoft gera e oferece suporte às seguintes bibliotecas de cliente: 

|Biblioteca do cliente|Exemplos|
|---|---|
|[SDK da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)|[Exemplos da CLI do Azure](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)|
|[SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0)|[Amostras do .NET](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials)|
|[SDK do .NET Core](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0) (escolha a guia **.NET CLI**)|[Exemplos do .NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials)|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/mediaservices)||
|[SDK do Node.js](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/index?view=azure-node-latest)|[Exemplos do Node.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials)|
|[SDK do Python](https://pypi.org/project/azure-mgmt-media/1.0.0rc1/)||
|[SDK do Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/mediaservices/mgmt/2018-03-30-preview/media)||

Os Serviços de Mídia fornecem [arquivos do Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media) que podem ser usados para gerar SDKs para a sua linguagem/tecnologia preferida.  

## <a name="next-steps"></a>Próximas etapas

Para ver como é fácil começar a codificar e transmitir arquivos de vídeo, confira [Arquivos de transmissão](stream-files-dotnet-quickstart.md). 

