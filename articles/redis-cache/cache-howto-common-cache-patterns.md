<properties 
   pageTitle="Padrões de cache comuns com o Cache Redis do Azure" 
   description="Saiba onde e por que usar o Cache Redis do Azure" 
   services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd" 
   ms.date="05/21/2015"
   ms.author="riande"/>

# Padrões de cache comuns com o Cache Redis do Azure

Esta página lista os benefícios mais comuns do uso de um cache.

## Otimizando o acesso a dados com um cache

O uso de um cache pode acelerar drasticamente o acesso a dados em comparação com a busca em um repositório de dados. Um cache fornece alta taxa de transferência e baixa latência. Buscando dados ativos do cache, você não apenas acelera seu aplicativo, mas também pode reduzir a carga de acesso a dados e aumentar sua capacidade de resposta para outras consultas. O armazenamento de informações em um cache ajuda a economizar recursos e aumenta a escalabilidade à medida que as demandas sobre o aplicativo aumentam. Seu aplicativo será muito mais responsivo a cargas intermitentes quando ele puder buscar dados de um cache de forma eficiente.

## Estado de sessão distribuído
Embora evitar usar o estado de sessão seja considerado uma prática recomendada, alguns aplicativos podem ter realmente um benefício de desempenho/complexidade reduzida em comparação com o uso de dados de sessão, enquanto outros aplicativos exigem diretamente o estado de sessão. O padrão no provedor de memória para o estado da sessão não permite expansão (executando várias instâncias do site). O provedor de estado de sessão do SQL Server do ASP.NET permitirá que vários sites usem o estado de sessão, mas ela acarreta um custo de alta latência em comparação com um provedor na memória. O provedor de cache de estado de sessão do Redis é uma alternativa de baixa latência que é muito fácil de configurar e instalar. Se seu aplicativo usar apenas uma quantidade limitada de estado de sessão, você poderá usar a maior parte do cache para cache de dados e uma pequena quantidade para estado de sessão.

## Sobrevivendo o tempo de inatividade do serviço (fallback de cache)
 Armazenando dados em cache, o aplicativo poderá sobreviver a falhas do sistema, como latência de rede, problemas de serviço Web e falhas de hardware. Muitas vezes, é melhor fornecer dados armazenados em cache até que o serviço Web ou banco de dados se recupere, em vez de deixar que o aplicativo falhe completamente.

## Próximas etapas
Para saber mais sobre como usar o Cache Redis do Azure:
 
- [Documentos do Cache Redis do Azure ](http://azure.microsoft.com/documentation/services/cache/): essa página fornece vários links práticos para usar o cache Redis do Azure.
- [Aplicativo de filme MVC com Cache Redis do Azure em 15 minutos](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/): a postagem do blog fornece um início rápido para usar o cache Redis do Azure em um aplicativo ASP.NET MVC.
- [Como usar o Estado de Sessão do ASP.NET com Sites do Azure](../app-service-web/web-sites-dotnet-session-state-caching.md): esse tópico explica como usar o Serviço de Cache Redis do Azure para o estado de sessão.

<!---HONumber=July15_HO1-->