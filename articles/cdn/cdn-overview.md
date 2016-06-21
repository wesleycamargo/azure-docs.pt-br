<properties
	pageTitle="Visão geral da CDN do Azure"
	description="Saiba o que é a CDN (Rede de Distribuição de Conteúdo) do Azure e como usá-la para fornecer conteúdo de alta largura de banda armazenando em cache blobs e conteúdo estático."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="06/06/2016"
	ms.author="casoper"/>

# Visão geral da CDN (Rede de Distribuição de Conteúdo) do Azure

> [AZURE.NOTE] Este documento descreve o que é a CDN (Rede de Distribuição de Conteúdo) do Azure, como ela funciona e os recursos de cada produto CDN do Azure. Se você quiser ignorar essas informações e ir diretamente para um tutorial sobre como criar um ponto de extremidade CDN, veja [Usando o Azure CDN](cdn-create-new-endpoint.md). Para obter uma lista de locais de nó CDN atuais, confira [Locais POP da CDN (Rede de Distribuição de Conteúdo) do Azure](cdn-pop-locations.md).

A CDN (Rede de Distribuição de Conteúdo) do Azure armazena em cache conteúdo Web estático em locais estrategicamente posicionados para fornecer taxa de transferência máxima para o fornecimento de conteúdo aos usuários. A CDN oferece aos desenvolvedores uma solução global de fornecimento de conteúdo de alta largura de banda armazenando em cache o conteúdo em nós físicos em todo o mundo.

As vantagens do uso da CDN para armazenar ativos de site da Web em cache incluem:

- Melhor desempenho e experiência de usuário para usuários finais, especialmente ao se usar aplicativos em que várias viagens de ida e volta são necessárias para carregar o conteúdo.
- Grande dimensionamento para lidar melhor com alta carga instantânea, como no início de um evento de lançamento de produto.
- Distribuindo-se solicitações de usuário e fornecendo-se conteúdo de servidores de borda, menos tráfego é enviado à origem.


## Como ele funciona

![Visão geral da CDN](./media/cdn-overview/cdn-overview.png)

1. Uma usuária (Brenda) solicita um arquivo (também chamado de ativo) usando uma URL com um nome de domínio especial, como `<endpointname>.azureedge.net`. O DNS roteia a solicitação para o local POP (Ponto de Presença) de melhor desempenho . Normalmente, esse é o POP geograficamente mais próximo ao usuário.

2. Se os servidores de borda no POP não tiverem o arquivo em seu cache, o servidor de borda solicitará o arquivo da origem. A origem pode ser um Aplicativo Web do Azure, o Serviço de Nuvem do Azure, a conta de Armazenamento do Azure ou qualquer servidor Web acessível publicamente.

3. A origem retorna o arquivo ao servidor de borda, incluindo os cabeçalhos HTTP opcionais que descrevem a TTL (Vida Útil) do arquivo.

4. O servidor de borda armazena o arquivo em cache e o retorna à solicitante original (Brenda). O arquivo permanecerá em cache no servidor de borda até que a TTL expire. Se a origem não especificar uma TTL, a TTL padrão será de sete dias.

5. Usuários adicionais (como Pedro) podem solicitar o mesmo arquivo usando essa mesma URL e também podem ser direcionados para esse mesmo POP.

6. Se a TTL do arquivo ainda não tiver expirado, o servidor de borda retornará os arquivos do cache. Isso resulta em uma experiência de usuário mais rápida e responsiva.


## Recursos da Azure CDN

Há três produtos Azure CDN: **Azure CDN Standard do Akamai**, **Azure CDN Standard da Verizon** e **Azure CDN Premium da Verizon**. A tabela a seguir lista os recursos disponíveis para cada produto.

| | Akamai Standard | Verizon Standard | Verizon Premium |
|-------|-----------------|------------------|-----------------|
| Fácil integração com os serviços do Azure, como [Armazenamento](cdn-create-a-storage-account-with-cdn.md), [Serviços de Nuvem](cdn-cloud-service-with-cdn.md), [Aplicativos Web](../app-service-web/cdn-websites-with-cdn.md) e [Serviços de Mídia](../media-services/media-services-manage-origins.md#enable_cdn) | **&#x2713;** | **&#x2713;** | **&#x2713;**|
| Suporte para HTTPS | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| Balanceamento de carga | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| Proteção DDOS | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| Pilha dupla IPv4/IPv6 | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Suporte a nome de domínio personalizado](cdn-map-content-to-custom-domain.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Cache de cadeia de caracteres de consulta](cdn-query-string.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Filtragem de país](cdn-restrict-access-by-country.md) | | **&#x2713;** | **&#x2713;** |
| [Limpeza rápida](cdn-purge-endpoint.md) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Pré-carregamento de ativos](cdn-preload-endpoint.md) | | **&#x2713;** | **&#x2713;** |
| [Análise principal](cdn-analyze-usage-patterns.md) | | **&#x2713;** | **&#x2713;** |
| [Gerenciamento por meio da API REST](https://msdn.microsoft.com/library/mt634456.aspx) | **&#x2713;** | **&#x2713;** | **&#x2713;** |
| [Mecanismo de distribuição de conteúdo personalizável e baseado em regras](cdn-rules-engine.md) | | | **&#x2713;** |
| [Relatórios avançados de HTTP](cdn-advanced-http-reports.md) | | | **&#x2713;** |
| [Estatísticas em tempo real](cdn-real-time-stats.md) | | | **&#x2713;** |

>[AZURE.TIP] Há algum recurso que você gostaria de ver na CDN do Azure? [Forneça comentários](https://feedback.azure.com/forums/169397-cdn).

## Próximas etapas

Para começar a usar a CDN, confira [Usar a CDN do Azure](./cdn-create-new-endpoint.md).

Se você for um cliente CDN, poderá gerenciar seus pontos de extremidade de CDN por meio do [Portal do Microsoft Azure](https://portal.azure.com).

Para ver a CDN em ação, confira o [vídeo da sessão Build 2016](https://azure.microsoft.com/documentation/videos/build-2016-leveraging-the-new-azure-cdn-apis-to-build-wicked-fast-applications/).

Para obter informações sobre preços, confira [Preços da CDN](https://azure.microsoft.com/pricing/details/cdn/).

<!---HONumber=AcomDC_0615_2016-->