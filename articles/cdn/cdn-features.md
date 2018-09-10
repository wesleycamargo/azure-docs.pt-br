---
title: Comparar recursos do produto CDN (Rede de Distribuição de Conteúdo) do Azure| Microsoft Docs
description: Saiba mais sobre os recursos para os quais cada produto CDN do Azure oferece suporte.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/09/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 7ec4961a78b2207c9990b787c4a0f9cc9c2e7931
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237107"
---
# <a name="compare-azure-cdn-product-features"></a>Comparar recursos do produto CDN do Azure

A CDN (Rede de Distribuição de Conteúdo) do Azure inclui quatro produtos: **Azure CDN Standard da Microsoft** (em versão prévia), **Azure CDN Standard da Akamai**, **Azure CDN Standard da Verizon** e **Azure CDN Premium da Verizon**. 

Para saber mais sobre como migrar um perfil do **Azure CDN Standard da Verizon** para **Azure CDN Premium da Verizon**, veja [Migrar um perfil do Azure CDN de Standard Verizon para Premium Verizon](cdn-migrate.md).

A tabela a seguir compara os recursos disponíveis em cada produto.

| **Recursos e otimizações de desempenho** | **Microsoft Standard (versão prévia)** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** |
| --- | --- | --- | --- | --- |
| [Aceleração de site dinâmica](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  |  | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de site dinâmico – compactação de imagem adaptável](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de site dinâmico – pré-busca de objeto](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only)  |  | **&#x2713;**  |  |  |
| [Otimização do streaming de Vídeo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Otimização de arquivos grandes](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [GSLB (Balanceamento de carga do servidor global)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Limpeza rápida](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Pré-carregamento de ativos](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Configurações de cache/cabeçalho (usando [regras de cache](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Configurações de cache/cabeçalho (usando o [mecanismo de regras](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Cache de cadeia de caracteres de consulta](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Pilha dupla IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Suporte do HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Segurança** | **Microsoft Standard (versão prévia)** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| Suporte ao protocolo HTTPS com o ponto de extremidade CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS de domínio personalizado](cdn-custom-ssl.md)  | **&#x2713;** | |**&#x2713;** |**&#x2713;** |
| [Suporte a nome de domínio personalizado](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtragem geográfica](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Autenticação de token](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Proteção DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Traga o seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  |  |  |
||||
| **Análise e relatórios** | **Microsoft Standard (versão prévia)** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| [Logs de diagnóstico do Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Relatórios de núcleo da Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Relatórios personalizados da Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Relatórios avançados de HTTP](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Estatísticas em tempo real](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Desempenho do nó de borda](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Alertas em tempo real](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Facilidade de uso** | **Microsoft Standard (versão prévia)** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| Fácil integração com serviços do Azure como [Armazenamento](cdn-create-a-storage-account-with-cdn.md), [Aplicativos Web](cdn-add-to-web-app.md) e [Serviços de Mídia](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gerenciamento via [API REST](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) ou [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Mecanismo de distribuição de conteúdo personalizável e baseado em regras](cdn-rules-engine.md)  |  | | |**&#x2713;** |
| Redirecionamento/reconfiguração de URL (usando o [mecanismo de regras](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Regras de dispositivo móvel (usando o [mecanismo de regras](cdn-rules-engine.md))  |  | | |**&#x2713;** |

\* A Microsoft e a Verizon oferecem suporte à entrega de arquivos e mídias grandes diretamente via otimização geral de entrega na Web.



