<properties 
	pageTitle="Notas de versão dos adaptadores de log do Application Insights" 
	description="As últimas atualizações." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/21/2015" 
	ms.author="abaranch"/>
 
# Notas de versão para os adaptadores de log do .NET

Se você já usa uma estrutura de registros, como log4net, NLog ou System.Diagnostics.Trace, você pode capturar esses logs e enviá-los para o [Application Insights](https://azure.microsoft.com/services/application-insights/), onde poderá correlacionar os rastreamentos de log com ações do usuário, exceções e outros eventos.


#### Introdução

Consulte [Logs, exceções e diagnóstico personalizado para o ASP.NET no Application Insights](app-insights-search-diagnostic-logs.md).

## Versão 1.2.6

- Correções de bug
- log4Net: colete as propriedades do log4net como propriedades personalizadas. UserName não é mais uma propriedade personalizada (ele é coletado como telemetry.Context.User.Id). O carimbo de data/hora não é mais uma propriedade personalizada.
- NLog: colete as propriedades de NLog como propriedades personalizadas. O SequenceID não é mais uma propriedade personalizada (ele é coletado como telemetry.Sequence). O carimbo de data/hora não é mais uma propriedade personalizada. 

## Versão 1.2.5
- Primeiro abra a versão de software livre: API References Application Insights versão 1.2.3 ou superior.

<!---HONumber=AcomDC_0128_2016-->