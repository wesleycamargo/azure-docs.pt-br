<properties 
	pageTitle="Notas de versão do Application Insights" 
	description="As últimas atualizações." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/21/2015" 
	ms.author="awills"/>
 
# Notas de versão do SDK do Application Insights para Java

[Usando o SDK para Java](app-insights-java-get-started.md)

## Versão 0.9.4

- Suporte à coleta de contadores de desempenho de computadores com o Windows de 32 bits.
- Suporte a acompanhamento manual de dependências usando uma nova API do método ```trackDependency```.
- Capacidade de marcar um item de telemetria como sintético adicionando uma propriedade ```SyntheticSource``` ao item relatado.

<!---HONumber=58-->