<properties 
	pageTitle="Notas de versão do Application Insights para Windows" 
	description="As atualizações mais recentes para o SDK do Windows Store." 
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
	ms.date="09/18/2015" 
	ms.author="sergkanz"/>
 
# Notas de versão do SDK do Application Insights Windows Phone e Store

O [SDK do Application Insights](app-insights-windows-get-started.md) envia telemetria sobre seu aplicativo ao vivo para o [Application Insights](https://azure.microsoft.com/services/application-insights/), no qual você pode analisar seu uso e o desempenho.


#### Como instalar o SDK em seu aplicativo

Consulte [Introdução aos aplicativos do Application Insights para Windows Phone e Store](app-insights-windows-get-started.md).

#### Como atualizar para o SDK mais recente 

* Faça uma cópia do ApplicationInsights.config, para manter todas as personalizações que você fez.
* No Gerenciador de Soluções, clique com o botão direito do mouse no seu projeto e escolha **Gerenciar pacotes NuGet**.
* Defina o filtro para mostrar os pacotes instalados. 
* Selecione os pacotes instalados do Application Insights e escolha Atualizar.
* Compare as versões nova e antiga do ApplicationInsights.config. Mescle novamente as personalizações feitas na versão antiga.
* Recompile sua solução.

## Versão 1.1.1

### SDK do Windows

- Correção de uma parada durante uma falha ao usar o SDK do Silverlight do Windows Phone. Após essa alteração, qualquer falha que ocorra após cerca de 2 segundos após a chamada para WindowsAppInitialier.InitializeAsync(...) será mantida em disco e enviada na próxima vez que o aplicativo for iniciado. Se ocorrer uma falha antes de cerca de 2 segundos após a chamada, ela será ignorada.  
- Defina as dependências do NuGet para uma versão específica do Núcleo e Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3).   

### SDK principal

- O núcleo é gerenciado no GitHub. As notas de versão futura do SDK do Núcleo podem ser encontradas [no GitHub](http://github.com/Microsoft/ApplicationInsights-dotnet/releases)

## Versão 1.1

### SDK principal

- Agora o SDK apresenta um novo tipo de telemetria ```DependencyTelemetry``` que contém informações sobre a chamada de dependência do aplicativo
- O novo método ```TelemetryClient.TrackDependency``` permite enviar informações sobre chamadas de dependência do aplicativo

## Versão 1.0.0

### SDK do Aplicativo do Windows

- Nova inicialização para aplicativos do Windows. A nova classe `WindowsAppInitializer` com o método `InitializeAsync()` permite a inicialização bootstrap da coleção do SDK. Essa alteração permite controle mais preciso e melhorias significativas de desempenho na inicialização de aplicativo pela técnica ApplicationInsights.config anterior.
- DeveloperMode não é mais definido automaticamente. Para alterar o comportamento de DeveloperMode, você deve especificar no código.
- O pacote NuGet não injeta mais ApplicationInsights.config. É recomendável usar o novo WindowsAppInitializer ao adicionar manualmente o pacote NuGet.
- O ApplicationInsights.config lê apenas `<InstrumentationKey>`; todas as outras configurações são ignoradas em detrimento das configurações de WindowsAppInitializer.
- Store Market será coletado automaticamente pelo SDK.
- Muitas correções de bugs, melhorias de estabilidade e aprimoramentos de desempenho.

### SDK principal

- O arquivo ApplicationInsights.config não é mais obrigatório. E não é adicionado pelo pacote NuGet. A configuração pode ser totalmente especificada no código.
- O pacote NuGet não adicionará mais um arquivo de destino à sua solução. Isso remove a configuração automática de DeveloperMode durante uma compilação de depuração. O DeveloperMode deve ser definido manualmente no código.

## Versão 0.17

### SDK do Aplicativo do Windows

- O SDK do Aplicativo do Windows agora oferece suporte a Aplicativos Universais do Windows criados no Preview técnico do Windows 10 e com o VS 2015 RC.

### SDK principal

- TelemetryClient é padronizado para inicializar com o InMemoryChannel.
- Nova API adicionada, `TelemetryClient.Flush()`. Esse método Flush acionará um carregamento de bloqueio imediato de toda a telemetria registrada no cliente. Isso permite o disparo manual do carregamento antes do desligamento do processo.
- O pacote do NuGet adicionou um destino .Net 4.5. Esse destino não tem dependências externas (dependências de EventSource e BCL removidas).

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

<!---HONumber=AcomDC_0128_2016-->