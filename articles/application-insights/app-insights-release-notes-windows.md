<properties 
	pageTitle="Notas de versão do Application Insights" 
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
	ms.date="06/18/2015" 
	ms.author="sergkanz"/>
 
# Notas de versão do SDK do Application Insights Windows Phone e Store

O [SDK do Application Insights](app-insights-windows-get-started.md) envia telemetria sobre seu aplicativo ao vivo para o [Application Insights](http://azure.microsoft.com/services/application-insights/), no qual você pode analisar seu uso e o desempenho.


#### Como instalar o SDK em seu aplicativo

Consulte [Introdução aos aplicativos do Application Insights para Windows Phone e Store](app-insights-windows-get-started.md).

#### Como atualizar para o SDK mais recente 

* Faça uma cópia do ApplicationInsights.config, para manter todas as personalizações que você fez.
* No Gerenciador de Soluções, clique com o botão direito do mouse no seu projeto e escolha **Gerenciar pacotes NuGet**.
* Defina o filtro para mostrar os pacotes instalados. 
* Selecione os pacotes instalados do Application Insights e escolha Atualizar.
* Compare as versões nova e antiga do ApplicationInsights.config. Mescle novamente as personalizações feitas na versão antiga.
* Recompile sua solução.


## Versão 0.16 

Visualização 2015-04-28

- O SDK agora dá suporte à plataforma de destino DNX para habilitar o monitoramento de aplicativos de [estrutura de Núcleo .NET](http://www.dotnetfoundation.org/NETCore5).
- Instâncias do ```TelemetryClient``` não armazenam mais em cache a Chave de Instrumentação. Agora, se a chave de instrumentação não tiver sido definida no ```TelemetryClient``` explicitamente, a ```InstrumentationKey``` retornará nulo. Ela corrige um problema quando você define a ```TelemetryConfiguration.Active.InstrumentationKey``` depois que alguma telemetria já foi coletada; módulos de telemetria como o coletor de dependências, a coleta de dados de solicitações da Web e o coletor de contadores de desempenho usarão a nova chave de instrumentação.

## Versão 0.15

- A nova propriedade ```Operation.SyntheticSource``` já está disponível no ```TelemetryContext```. Agora, você pode marcar os itens de telemetria como "não é tráfego de um usuário real" e especificar como esse tráfego foi gerado. Por exemplo, definindo essa propriedade, você pode distinguir o tráfego de sua automação de teste do tráfego de teste de carga.
- A lógica de canal foi movida para o NuGet separado chamado Microsoft.ApplicationInsights.PersistenceChannel. O canal padrão agora é chamado de InMemoryChannel
- O novo método ```TelemetryClient.Flush``` permite liberar itens de telemetria do buffer de forma síncrona

## Versão 0.13

Não há notas de versão disponíveis para versões anteriores.

<!---HONumber=62-->