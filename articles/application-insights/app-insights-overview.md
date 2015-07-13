<properties 
	pageTitle="O que é o Application Insights?" 
	description="Acompanhe o uso e o desempenho do seu aplicativo Web ou de dispositivo ativo. Detecte, faça triagem e diagnostique problemas. Monitore e melhore continuamente o sucesso com seus usuários." 
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
	ms.date="04/22/2015" 
	ms.author="awills"/>
 
# O que é o Application Insights?

O Application Insights do Visual Studio permite acompanhar o desempenho e o uso de seu aplicativo Web ou de dispositivo ativo.

* [Detecte, faça a triagem e diagnostique][detect] problemas de desempenho e corrija-os antes que a maioria dos usuários os perceba.
 *  Alertas sobre alterações de desempenho ou falhas.
 *  Métricas para ajudar a diagnosticar problemas de desempenho, como tempos de resposta, uso da CPU e rastreamento de dependências.
 *  Testes de disponibilidade para aplicativos Web.
 *  Alertas e relatórios de falhas e exceções
 *  Pesquisa de log de diagnóstico poderosa (incluindo rastreamentos de log de suas estruturas de logs favoritas).
* [Melhore continuamente o aplicativo][knowUsers] compreendendo o que os usuários fazem com ele. 
 * Contagens de exibições de páginas, usuários novos e que estão retornando e outras análises de uso principal
 * Acompanhe seus próprios eventos para avaliar o os padrões de uso e o sucesso de cada recurso.

## Como ele funciona?

Você instala um pequeno SDK em seu aplicativo e configura uma conta no portal do Application Insights. O SDK monitora seu aplicativo e envia os dados de telemetria ao portal. O portal mostra gráficos estatísticos e fornece ferramentas de pesquisa poderosas para ajudá-lo a diagnosticar quaisquer problemas.

![O SDK do Application Insights em seu aplicativo envia telemetria ao recurso do Application Insights no portal do Azure.](./media/app-insights-overview/01-scheme.png)

O SDK tem vários módulos que coletam telemetria, por exemplo, para contar sessões, usuários e desempenho. Você também pode escrever seu próprio código personalizado para enviar dados de telemetria ao portal. A telemetria personalizada é particularmente útil para acompanhar histórias de usuários: você pode contar eventos, como cliques de botão, realização de metas específicas ou erros do usuário.

Para servidores ASP.NET e aplicativos Web do Azure, você também pode instalar o [Status Monitor][redfield], que tem dois usos. Ele permite que você:

* Monitore um aplicativo Web sem recriá-lo ou reinstalá-lo.
* Acompanhe chamadas para módulos dependentes.

## Com que tipos de aplicativos ele pode trabalhar?

Atualmente há SDKs para:

* Aplicativos Web
 * [ASP.NET][greenbrown] no Azure ou em seu servidor IIS
 * [Java][java] no JRE 
 * [Páginas da Web][client]: HTML+JavaScript
* Aplicativos de dispositivos
 * [Windows][windows]
 * [iOS][ios]
 * [Android][android]
 * Cordova
 * [Outras plataformas][platforms]


## Como usá-lo?

* [Detectar, fazer triagem e diagnosticar problemas][detect]
* [Analisar o uso de seu aplicativo][knowUsers]


## Do que preciso para usá-lo?

* Uma assinatura do Microsoft Azure. O Application Insights é um dos muitos serviços de nuvem do Azure, que também incluem sites da Web, bancos de dados, VMs e muito mais. (Porém, você pode usar o Application Insights para monitorar qualquer aplicativo; seu aplicativo não precisa ser executado no Azure.) 

 * Sua organização pode ter uma conta.


## Como começar?

Escolha sua plataforma no menu Iniciar, à esquerda.

Em todos os casos, o procedimento básico é:

1. Criar um recurso do Application Insights no [Azure][portal] (e obter sua chave de instrumentação).
2. Instrumentar seu aplicativo com o SDK adequado (e configurá-lo com a chave de instrumentação).
3. Executar seu aplicativo no modo de depuração ou em tempo real.
4. Ver os resultados em seu recurso no [Azure][portal].

Em alguns casos, um plug-in está disponível para seu IDE (como o Visual Studio ou o Eclipse), executando as duas primeiras etapas para você. Porém, você sempre pode realizar o procedimento manualmente.

Se seu aplicativo for um site ou serviço, há algumas adições e variações opcionais no procedimento básico:

* Adicione um SDK ao aplicativo no servidor e ao [dispositivo][windows] ou [página da Web][client] do cliente. Os dados de telemetria são mesclados no portal, assim, você pode correlacionar eventos nas duas extremidades.
* Configure testes da Web para monitorar a disponibilidade de seu site de pontos em todo o mundo.
* Instrumente um aplicativo do lado do servidor já ativo sem recriá-lo ou reimplantá-lo. Isso está disponível para [servidores IIS][redfield] e [aplicativos Web do Azure][azure].
* Monitore chamadas de dependência que seu aplicativo faz a outros componentes, como bancos de dados ou por meio de APIs REST. Disponível para [servidores IIS][redfield] e [aplicativos Web do Azure][azure].


<!--Link references-->

[android]: app-insights-android.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[ios]: app-insights-ios.md
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=62-->