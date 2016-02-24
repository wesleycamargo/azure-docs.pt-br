<properties 
	pageTitle="Visão geral da CDN do Azure" 
	description="Saiba o que é a CDN (Rede de Distribuição de Conteúdo) do Azure e como usá-la para fornecer conteúdo de alta largura de banda armazenando em cache blobs e conteúdo estático." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="casoper"/>

# Visão geral da CDN (Rede de Distribuição de Conteúdo) do Azure

A CDN (Rede de Distribuição de Conteúdo) do Azure armazena em cache conteúdo estático e blobs do Azure usados por serviços de nuvem em locais estrategicamente posicionados para fornecer largura de banda máxima para o fornecimento de conteúdo aos usuários.

Se você for um cliente CDN, poderá gerenciar seus pontos de extremidade de CDN por meio do [Portal do Microsoft Azure](https://portal.azure.com).


A CDN oferece aos desenvolvedores uma solução global de fornecimento de conteúdo de alta largura de banda armazenando em cache o conteúdo em nós físicos em todo o mundo. Para obter uma lista atual dos locais de nós CDN, consulte [Locais POP da CDN (Rede de Distribuição de Conteúdo) do Azure](cdn-pop-locations.md).

Os benefícios do uso da CDN para armazenar em cache dados do Azure incluem:

- Melhor desempenho e experiência de usuário para usuários finais que estão longe de uma fonte de conteúdo e estão usando aplicativos em que muitas "viagens à Internet" são necessárias para carregar conteúdo
- Grande escala distribuída para lidar melhor com alta carga instantânea, como no início de um evento de lançamento de produto. 


>[AZURE.IMPORTANT] Quando você cria ou habilita um ponto de extremidade da CDN, pode levar até 90 minutos para que a propagação seja feita no mundo inteiro.
 
Quando uma solicitação de um objeto é feita pela primeira vez à CDN, o objeto é recuperado diretamente do local de origem do objeto. Essa origem pode ser uma conta de armazenamento do Azure, um aplicativo Web, um serviço de nuvem ou qualquer origem personalizada que aceite solicitações da Web públicas. Quando uma solicitação é feita usando a sintaxe da CDN, a solicitação é redirecionada para o ponto de extremidade da CDN mais próximo do local em que a solicitação foi feita para fornecer acesso ao objeto. Se o objeto não for encontrado nesse ponto de extremidade, ele será recuperado do serviço e armazenado em cache no ponto de extremidade, em que uma configuração de TTL (vida útil) será mantida para o objeto armazenado em cache.

## Recursos Standard

A camada CDN Standard inclui os seguintes recursos:

- Fácil integração com serviços do Azure, como [Armazenamento](cdn-create-a-storage-account-with-cdn.md), Aplicativos Web e Serviços de Mídia
- [Cache de cadeia de caracteres de consulta](cdn-query-string.md)
- [Suporte a nome de domínio personalizado](cdn-map-content-to-custom-domain.md)
- [Filtragem de país](cdn-restrict-access-by-country.md)
- [Análise principal](cdn-analyze-usage-patterns.md)
- [Origens de conteúdo personalizado](cdn-how-to-use-cdn.md#caching-content-from-custom-origins)
- [Suporte para HTTPS](cdn-how-to-use-cdn.md#accessing-cached-content-over-https)
- Balanceamento de carga
- Proteção DDOS
- [Limpeza rápida](cdn-purge-endpoint.md)
- [Pré-carregamento de ativos](cdn-preload-endpoint.md)
- [Gerenciamento por meio da API REST](https://msdn.microsoft.com/library/mt634456.aspx)


## Recursos Premium

A camada CDN Premium inclui todos os recursos da camada Standard, além desses recursos adicionais:

- [Mecanismo de distribuição de conteúdo personalizável e baseado em regras](cdn-rules-engine.md)
- [Relatórios avançados de HTTP](cdn-advanced-http-reports.md)
- [Estatísticas em tempo real](cdn-real-time-stats.md)

<!---HONumber=AcomDC_0204_2016-->