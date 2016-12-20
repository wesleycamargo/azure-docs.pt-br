---
title: "Política de armazenamento em cache de CDN na extensão dos Serviços de Mídia"
description: "Este tópico fornece uma visão geral de uma política de armazenamento em cache de CDN na extensão dos Serviços de Mídia."
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: 
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d8e5fa4f3de86be0a8ef65971e42c8b20f61f0b2


---
# <a name="cdn-caching-policy-in-media-services-extension"></a>Política de armazenamento em cache de CDN na extensão dos Serviços de Mídia
Os Serviços de Mídia do Azure fornecem streaming adaptável e download progressivo com base em HTTP. Streaming com base em HTTP é altamente escalonável, com os benefícios do armazenamento em cache em camadas proxy e CDN, bem como armazenamento em cache no lado do cliente. Pontos de extremidade de streaming fornecem recursos de streaming gerais e também a configuração de cabeçalhos de cache HTTP. Pontos de extremidade de streaming definem o Controle de Cache HTTP: cabeçalhos idade máxima e Vencimento. Saiba mais sobre os cabeçalhos de cache HTTP em [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Cabeçalhos de armazenamento em cache padrão
Por padrão, os pontos de extremidade de streaming aplicam cabeçalhos de cache de 3 dias para dados de streaming sob demanda (fragmentos/partes reais de mídia) e manifesto (lista de reprodução). Para transmissão ao vivo, os pontos de extremidade de streaming aplicam cabeçalhos de cache de 3 dias para solicitações de dados (fragmentos/partes reais de mídia) e cabeçalho de cache de 2 segundos para solicitações de manifesto (lista de reprodução). Quando os programa ao vivo são transformados em sob demanda (arquivamento dinâmico) os cabeçalhos de cache de streaming sob demanda são aplicados.

## <a name="azure-cdn-integration"></a>Integração da CDN com o Azure
Os Serviços de Mídia do Azure fornecem [CDN integrada](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) para pontos de extremidade de streaming. Cabeçalhos do controle de cache são aplicados da mesma forma que os pontos de extremidade de streaming para pontos de extremidade de streaming habilitados para CDN. O Azure CDN usa os valores de cache configurados para o ponto de extremidade de streaming para definir o tempo de vida dos objetos armazenados internamente em cache e também usa esse valor para definir os cabeçalhos de cache de entrega. Ao usar os pontos de extremidades de streaming habilitados para CDN, não é recomendável definir valores pequenos para o cache. A definição de valores pequenos diminuirá o desempenho e reduzirá o benefício da CDN. Não é permitido definir cabeçalhos de cache menores do que 600 segundos para pontos de extremidades de streaming habilitados para CDN.

> [!IMPORTANT]
> Integração dos Serviços de Mídia do Azure com a CDN do Azure é implementada na **CDN do Azure da Verizon**.  Para usar a **CDN do Azure da Akamai** para Serviços de Mídia do Azure, é preciso [configurar o ponto de extremidade manualmente](cdn-create-new-endpoint.md).  Para obter mais informações sobre os recursos da CDN do Azure, consulte [Visão geral da CDN](cdn-overview.md).
> 
> 

## <a name="configuring-cache-headers-with-azure-media-services"></a>Configurando os cabeçalhos de cache com os Serviços de Mídia do Azure
Você pode usar o Portal de Gerenciamento do Azure ou as APIs de Serviços de Mídia do Azure para configurar os valores de cabeçalho de cache.

1. Para configurar cabeçalhos de cache usando o Portal de Gerenciamento, consulte [Como gerenciar pontos de extremidade de streaming](../media-services/media-services-portal-manage-streaming-endpoints.md) , seção Configurando o ponto de extremidade de streaming.
2. API REST dos Serviços de Mídia do Azure, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. SDK do .NET dos Serviços de Mídia do Azure, [Propriedades de StreamingEndpointCacheControl](http://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Ordem de precedência de configuração do cache
1. O valor de cache configurado dos Serviços de Mídia do Azure substitui o valor padrão.
2. Se não houver configuração manual, os valores padrão serão aplicados.
3. Por padrão, os cabeçalhos de cache de 2 segundos são aplicados ao manifesto (lista de reprodução) do streaming dinâmico, independentemente da configuração de Mídia do Azure ou do Armazenamento do Azure, e não é possível substituir esse valor.




<!--HONumber=Nov16_HO3-->


