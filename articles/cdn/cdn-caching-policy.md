<properties 
	pageTitle="Política de armazenamento em cache de CDN na extensão dos Serviços de Mídia" 
	description="Este tópico fornece uma visão geral de uma política de armazenamento em cache de CDN na extensão dos Serviços de Mídia." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="juliako"/>

#Política de armazenamento em cache de CDN na extensão dos Serviços de Mídia

Os Serviços de Mídia do Azure fornecem streaming adaptável e download progressivo com base em HTTP. Streaming com base em HTTP é altamente escalonável, com os benefícios do armazenamento em cache em camadas proxy e CDN, bem como armazenamento em cache no lado do cliente. Pontos de extremidade de streaming fornecem recursos de streaming gerais e também a configuração de cabeçalhos de cache HTTP. Pontos de extremidade de streaming definem o Controle de Cache HTTP: cabeçalhos idade máxima e Vencimento. Saiba mais sobre os cabeçalhos de cache HTTP em [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

##Cabeçalhos de armazenamento em cache padrão

Por padrão, os pontos de extremidade de streaming aplicam cabeçalhos de cache de 3 dias para dados de streaming sob demanda (fragmentos/partes reais de mídia) e manifesto (lista de reprodução). Para transmissão ao vivo, os pontos de extremidade de streaming aplicam cabeçalhos de cache de 3 dias para solicitações de dados (fragmentos/partes reais de mídia) e cabeçalho de cache de 2 segundos para solicitações de manifesto (lista de reprodução). Quando os programa ao vivo são transformados em sob demanda (arquivamento dinâmico) os cabeçalhos de cache de streaming sob demanda são aplicados.

##Integração da CDN com o Azure

Os Serviços de Mídia do Azure fornecem [CDN integrada](http://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) para pontos de extremidade de streaming. Cabeçalhos do controle de cache são aplicados da mesma forma que os pontos de extremidade de streaming para pontos de extremidade de streaming habilitados para CDN. O Azure CDN usa os valores de cache configurados para o ponto de extremidade de streaming para definir o tempo de vida dos objetos armazenados internamente em cache e também usa esse valor para definir os cabeçalhos de cache de entrega. Ao usar os pontos de extremidades de streaming habilitados para CDN, não é recomendável definir valores pequenos para o cache. A definição de valores pequenos diminuirá o desempenho e reduzirá o benefício da CDN. Não é permitido definir cabeçalhos de cache menores do que 600 segundos para pontos de extremidades de streaming habilitados para CDN.

##Configurando os cabeçalhos de cache com os Serviços de Mídia do Azure

Você pode usar o Portal de Gerenciamento do Azure ou as APIs de Serviços de Mídia do Azure para configurar os valores de cabeçalho de cache.

1. Para configurar cabeçalhos de cache usando o Portal de Gerenciamento, consulte [Como gerenciar pontos de extremidade de streaming](../media-services-manage-origins.md), seção Configurando o ponto de extremidade de streaming.
2. API REST dos Serviços de Mídia do Azure, [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. SDK do .NET dos Serviços de Mídia do Azure, [Propriedades de StreamingEndpointCacheControl](http://go.microsoft.com/fwlink/?LinkId=615302).

##Ordem de precedência de configuração do cache

1. O valor de cache configurado dos Serviços de Mídia do Azure substitui o valor padrão.
2. Se não houver configuração manual, os valores padrão serão aplicados.
3. Por padrão, os cabeçalhos de cache de 2 segundos são aplicados ao manifesto (lista de reprodução) do streaming dinâmico, independentemente da configuração de Mídia do Azure ou do Armazenamento do Azure, e não é possível substituir esse valor.
 

<!---HONumber=AcomDC_1203_2015-->