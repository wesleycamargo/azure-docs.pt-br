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
 
# Notas de versão do SDK do Application Insights para .NET

O [SDK do Application Insights para .NET](app-insights-start-monitoring-app-health-usage.md) envia telemetria sobre seu aplicativo ao vivo para o [Application Insights](http://azure.microsoft.com/services/application-insights/), no qual você pode analisar seu uso e o desempenho.


#### Como instalar o SDK em seu aplicativo

Consulte [Introdução ao Application Insights para .NET](app-insights-start-monitoring-app-health-usage.md).

#### Como atualizar para o SDK mais recente 

* Após a atualização, será necessário mesclar novamente todas as personalizações feitas em ApplicationInsights.config. Se não tiver certeza se você personalizou, crie um novo projeto, adicione o Application Insights a ele e compare o seu arquivo .config com o novo projeto. Anote as diferenças.
* No Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto e escolha **Gerenciar pacotes NuGet**.
* Defina o filtro para mostrar os pacotes instalados. 
* Selecione **Microsoft.ApplicationInsights.Web** e escolha **Atualizar**. (Isso também vai atualizar todos os pacotes dependentes).
* Compare o ApplicationInsights.config com a cópia antiga. A maioria das alterações que você verá são porque removemos alguns módulos e deixamos os outros parametrizáveis. Reaplique as personalizações feitas no arquivo antigo.
* Recompile sua solução.

## Versão 0.17
- Dependência removida do NuGet EventSource para os aplicativos do framework 4.5.
- Cookies de sessão e do usuário anônimos não serão gerados no lado do servidor. Os módulos de telemetria ```WebSessionTrackingTelemetryModule``` e ```WebUserTrackingTelemetryModule``` não têm mais suporte e foram removidos do arquivo ApplicationInsights.config. Os cookies do SDK do JavaScript serão respeitados.
- O canal de persistência otimizado para cenários de carga alta é usado para o SDK web. Corrigido problema "Espiral morte". Espiral da morte é uma condição que ocorre quando o pico da contagem de itens de telemetria excede bastante o limite de limitação no ponto de extremidade, o que levará a uma repetição depois de determinado tempo e será limitado durante a repetição novamente.
- O modo de desenvolvedor é otimizado para produção. Se deixado por engano, ele não ocasionará em uma sobrecarga grande como antes da tentativa de obter informações adicionais de saída.
- O modo de desenvolvedor, por padrão, será habilitado somente quando o aplicativo estiver sob o depurador. Você pode substituí-lo usando a ```DeveloperMode``` propriedade da```ITelemetryChannel``` interface.

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

 

<!---HONumber=58_postMigration-->