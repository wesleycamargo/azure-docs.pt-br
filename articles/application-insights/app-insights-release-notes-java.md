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
	ms.author="awills"/>
 
# Notas de versão do SDK do Application Insights para Java

O [SDK do Application Insights para Java](app-insights-java-get-started.md) envia a telemetria sobre seu aplicativo ao vivo para o [Application Insights](http://azure.microsoft.com/services/application-insights/), no qual você pode analisar seu uso e o desempenho.

#### Como instalar o SDK em seu aplicativo

Consulte [Introdução ao SDK para Java](app-insights-java-get-started.md).

#### Como atualizar para o SDK mais recente 

Após a atualização, você precisará mesclar de novo todas as personalizações feitas em ApplicationInsights.xml. Faça uma cópia dele para comparar com o novo arquivo.

*Se você estiver usando o Maven ou Gradle*

1. Se você tiver especificado um número de versão específico no pom.xml ou build.gradle, atualize-o.
2. Atualize as dependências do seu projeto.

*Caso contrário*

* Baixe a versão mais recente da [Bibliotecas do Azure para Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html) e substitua as antigas. 
 
Compare o ApplicationInsights.xml antigo e novo. Muitas das alterações que você vê são porque nós adicionamos e removemos módulos. Reaplique todas as personalizações que você fez.

## Versão 0.9.6
- Tornar o SDK do Java compatível com o servlet v2.5 e HttpClient pre-v4.3
- Adicionando suporte a interceptores de Java EE
- Removendo dependências redundantes do appender Logback

## Versão 0.9.5  

- Correção para um problema no qual eventos personalizados não estão correlacionados com usuários/sessões devido a erros de análise de cookie.  
- Lógica aperfeiçoada para resolver o local do arquivo de configuração ApplicationInsights.xml.
- Removido o acompanhamento de sessões e usuários (isso será apenas feito pelos SDKs do cliente).

## Versão 0.9.4

- Suporte à coleta de contadores de desempenho de computadores com o Windows de 32 bits.
- Suporte a acompanhamento manual de dependências usando uma nova API do método ```trackDependency```.
- Capacidade de marcar um item de telemetria como sintético adicionando uma propriedade ```SyntheticSource``` ao item relatado.
 

<!---HONumber=58_postMigration-->