---
title: "Visão geral da CDN do Azure | Microsoft Docs"
description: "Saiba o que é a CDN (Rede de Distribuição de Conteúdo) do Azure e como usá-la para fornecer conteúdo de alta largura de banda armazenando em cache blobs e conteúdo estático."
services: cdn
documentationcenter: 
author: dksimpson
manager: akucer
editor: 
ms.assetid: 866e0c30-1f33-43a5-91f0-d22f033b16c6
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/10/2017
ms.author: v-semcev
ms.openlocfilehash: cdcf07b6af2bd915345361c0bda2dcd9abe5486e
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="overview-of-the-azure-content-delivery-network"></a>Visão geral da Rede de Distribuição de Conteúdo do Azure

A CDN (Rede de Distribuição de Conteúdo) do Azure armazena em cache conteúdo Web estático em locais estrategicamente posicionados para fornecer taxa de transferência máxima para o fornecimento seguro de conteúdo aos usuários. A CDN oferece aos desenvolvedores uma solução global de fornecimento rápido de conteúdo de alta largura de banda armazenando em cache o conteúdo em nós físicos em todo o mundo. 

> [!NOTE]
> Este artigo descreve a CDN do Azure, como ela funciona, e os recursos de cada produto da CDN do Azure. Para ignorar essas informações e exibir um tutorial sobre como criar um ponto de extremidade CDN, consulte [Guia de Introdução à CDN do Azure](cdn-create-new-endpoint.md). Para ver uma lista de locais de nó CDN atuais, confira [Locais POP da CDN (Rede de Distribuição de Conteúdo) do Azure](cdn-pop-locations.md).

As vantagens do uso da CDN para armazenar ativos de site da Web em cache incluem:

* Melhor desempenho e experiência aprimorada de usuário para usuários finais, especialmente ao se usar aplicativos em que várias viagens de ida e volta são necessárias para carregar o conteúdo.
* Grande dimensionamento para lidar melhor com alta carga instantânea, como no início de um evento de lançamento de produto.
* Distribuição de solicitações de usuário e fornecimento de conteúdo de servidores de borda, assim menos tráfego é enviado à origem.

## <a name="how-it-works"></a>Como ele funciona
![Visão geral da CDN](./media/cdn-overview/cdn-overview.png)

1. Uma usuária (Brenda) solicita um arquivo (também chamado de ativo) usando uma URL com um nome de domínio especial, como `<endpointname>.azureedge.net`. O DNS encaminha a solicitação para ode local de Ponto de Presença (POP) com o melhor desempenho, que é geralmente o POP geograficamente mais próximo ao usuário.
2. Se os servidores de borda no POP não tiverem o arquivo em seu cache, o servidor de borda solicitará o arquivo da origem.  A origem pode ser um Aplicativo Web do Azure, o Serviço de Nuvem do Azure, a conta de Armazenamento do Azure ou qualquer servidor Web acessível publicamente.
3. A origem retorna o arquivo ao servidor de borda, incluindo os cabeçalhos HTTP opcionais que descrevem a TTL (Vida Útil) do arquivo.
4. O servidor de borda armazena o arquivo em cache e o retorna à solicitante original (Brenda).  O arquivo permanece armazenado em cache no servidor de borda até que o TTL expire.  Se a origem não especificar uma TTL, a TTL padrão será de sete dias.
5. Usuários adicionais podem solicitar o mesmo arquivo usando essa mesma URL e também podem ser direcionados para esse mesmo POP.
6. Se a TTL do arquivo ainda não tiver expirado, o servidor de borda retornará os arquivos do cache. Esse processo resulta em uma experiência de usuário mais rápida e responsiva.

## <a name="azure-cdn-features"></a>Recursos da Azure CDN
Há três produtos Azure CDN: **Azure CDN Standard do Akamai**, **Azure CDN Standard da Verizon** e **Azure CDN Premium da Verizon**.  A tabela a seguir lista os recursos disponíveis para cada produto.

|  | Akamai Standard | Verizon Standard | Verizon Premium |
| --- | --- | --- | --- |
| __Recursos de Desempenho e Otimizações__ |
| [Aceleração Dinâmica do Site](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de Site Dinâmico - Compactação de Imagem Adaptável](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only) | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de Site Dinâmico - Pré-busca de objeto](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only) | **&#x2713;**  |  |  |
| [Otimização do streaming de Vídeo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization) | **&#x2713;**  | \* |  \* |
| [Otimização de arquivos grandes](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization) | **&#x2713;**  | \* |  \* |
| [Balanceamento de Carga do Servidor Global (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Limpeza rápida](cdn-purge-endpoint.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Pré-carregamento de ativos](cdn-preload-endpoint.md) | |**&#x2713;** |**&#x2713;** |
| Configurações de cache/cabeçalho (usando [regras de cache](cdn-caching-rules.md)) |**&#x2713;** |**&#x2713;** | |
| Configurações de cache/cabeçalho (usando o [mecanismo de regras](cdn-rules-engine.md)) | | |**&#x2713;** |
| [Cache de cadeia de caracteres de consulta](cdn-query-string.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Pilha dupla IPv4/IPv6 |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Suporte do HTTP/2](cdn-http2.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| __Segurança__ |
| Suporte ao protocolo HTTPS com o ponto de extremidade CDN |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS de domínio personalizado](cdn-custom-ssl.md) | |**&#x2713;** |**&#x2713;** |
| [Suporte a nome de domínio personalizado](cdn-map-content-to-custom-domain.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtragem geográfica](cdn-restrict-access-by-country.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Autenticação de token](cdn-token-auth.md)|  |  |**&#x2713;**| 
| [Proteção DDOS](https://www.us-cert.gov/ncas/tips/ST04-015) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| __Análises e Relatórios__ |
| [Logs de diagnóstico do Azure](cdn-azure-diagnostic-logs.md) | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Relatórios de núcleo da Verizon](cdn-analyze-usage-patterns.md) | |**&#x2713;** |**&#x2713;** |
| [Relatórios personalizados da Verizon](cdn-verizon-custom-reports.md) | |**&#x2713;** |**&#x2713;** |
| [Relatórios avançados de HTTP](cdn-advanced-http-reports.md) | | |**&#x2713;** |
| [Estatísticas em tempo real](cdn-real-time-stats.md) | | |**&#x2713;** |
| [Desempenho do nó de borda](cdn-edge-performance.md) | | |**&#x2713;** |
| [Alertas em tempo real](cdn-real-time-alerts.md) | | |**&#x2713;** |
| __Facilidade de uso__ |
| Fácil integração com os serviços do Azure, como [Armazenamento](cdn-create-a-storage-account-with-cdn.md), [Serviços de Nuvem](cdn-cloud-service-with-cdn.md), [Aplicativos Web](../app-service/app-service-web-tutorial-content-delivery-network.md) e [Serviços de Mídia](../media-services/media-services-portal-manage-streaming-endpoints.md) |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gerenciamento via [API REST](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) ou [PowerShell](cdn-manage-powershell.md). |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Mecanismo de distribuição de conteúdo personalizável e baseado em regras](cdn-rules-engine.md) | | |**&#x2713;** |
| Redirecionamento/reconfiguração de URL (usando o [mecanismo de regras](cdn-rules-engine.md)) | | |**&#x2713;** |
| Regras de dispositivo móvel (usando o [mecanismo de regras](cdn-rules-engine.md)) | | |**&#x2713;** |

\*A Verizon dá suporte à entrega de arquivos grandes e mídia diretamente via Distribuição na Web Geral.


> [!TIP]
> Há algum recurso que você gostaria de ver na CDN do Azure?  [Forneça comentários](https://feedback.azure.com/forums/169397-cdn)! 
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para começar a usar a CDN, consulte [Introdução à CDN do Azure](cdn-create-new-endpoint.md).

Se você for um cliente CDN, poderá gerenciar seus pontos de extremidade de CDN por meio do [Portal do Microsoft Azure](https://portal.azure.com) ou do [PowerShell](cdn-manage-powershell.md).

Para ver a CDN em ação, confira o [vídeo da sessão Build 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Saiba como automatizar a CDN do Azure com [.NET](cdn-app-dev-net.md) ou [Node.js](cdn-app-dev-node.md).

Para obter informações sobre preços, consulte [Preços de Rede de Distribuição de Conteúdo](https://azure.microsoft.com/pricing/details/cdn/).

