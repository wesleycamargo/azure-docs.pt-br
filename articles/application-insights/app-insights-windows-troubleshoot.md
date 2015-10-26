<properties 
	pageTitle="Solucionar problemas do Application Insights em dispositivos Windows" 
	description="Guia de solução de problemas e perguntas e respostas." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/17/2015" 
	ms.author="awills"/>
 
# Solução de problemas e Perguntas e Respostas do Application Insights para dispositivos Windows

Dúvidas ou problemas com o [Visual Studio Application Insights no Windows][windows]? Aqui estão algumas dicas.



## Sem dados 

*Adicionei o Application Insights com êxito e executei meu aplicativo, mas nunca vi dados no portal.*

* Espere um minuto e clique em Atualizar. Atualmente, a atualização não é automática.
* Verifique se você tem uma chave de instrumentação definida no arquivo ApplicationInsights.config e se ela é igual à chave no portal do Application Insights. Para ver a chave, clique em Essentials na folha de visão geral.
* Verifique se seu aplicativo [solicita acesso de rede de saída](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx).
* Há um firewall entre o dispositivo emulador ou de teste e o portal do Application Insights? Talvez você tenha que abrir as portas TCP 80 e 443 para tráfego de saída de dc.services.visualstudio.com e f5.services.visualstudio.com.
* No painel inicial do Microsoft Azure, veja o mapa de status de serviço. Se houver indicações de alerta, espere até que elas tenham voltado a OK; então, feche e abra novamente a folha do Application Insights de seu aplicativo.


#### Eu costumava ver os dados, mas eles foram interrompidos

* Verifique o [blog de status](http://blogs.msdn.com/b/applicationinsights-status/).
* Você atingiu sua cota mensal de pontos de dados? Abra Configurações/Cota e Preços para descobrir. Nesse caso, você pode atualizar seu plano ou então pagar por capacidade adicional. Consulte o [esquema de preços](http://azure.microsoft.com/pricing/details/application-insights/).


## Como adicionar o Application Insights a um Aplicativo Universal?

Adicione manualmente os pacotes do NuGet para cada projeto de dispositivo em sua solução. Consulte [Guia de Introdução – Aplicativos universais][universal].

## Desabilitando a telemetria

*Como desabilitar a coleta da telemetria?*

No código:

    TelemetryConfiguration config = TelemetryConfiguration.Active;
    config.TrackingIsDisabled = true;

## Alterando o recurso de destino

*Como alterar o recurso do Application Insights ao qual meu projeto envia dados?*

Na nova folha de visão geral do Application Insights, abra Essentials e copie a chave de instrumentação.

Cole a chave no arquivo de ApplicationInsights.config no nó `<InstrumentationKey>`.

Ou então, se você quiser alterar destinos no tempo de execução, use:

     var telemetry = new TelemetryClient();
     telemetry.Context.InstrumentationKey = newKey;
    
## Como monitorar um aplicativo de cliente-servidor?

Existem duas maneiras de fazer isso:

* Crie dois recursos do Application Insights (com chaves de instrumentação diferentes), para o cliente e o servidor. Porém, crie-os no mesmo grupo de recursos do Azure. Assim, é fácil alternar entre um e outro.
* Use um recurso de Application Insights e coloque sua chave de instrumentação nos projetos de cliente e servidor. Em seguida, você pode correlacionar métricas e eventos das duas origens.

Para ajudar a correlacionar eventos no cliente e no servidor, gere uma id de operação para cada solicitação. Transmita-a entre o cliente e o servidor e adicione-a à telemetria nas duas extremidades:

    telemetry.Context.OperationId = opid;


## A tela inicial do Azure

*Estou examinando o [portal do Azure](http://portal.azure.com). O mapa me diz algo sobre meu aplicativo?*

Não, ele mostra a integridade dos servidores do Azure em todo o mundo.

*No painel inicial do Azure (tela inicial), como localizar dados sobre meu aplicativo?*

Supondo que você [já tenha configurado seu aplicativo para o Application Insights][windows], clique em Procurar, selecione Application Insights e selecione o recurso criado para seu aplicativo. Para acessar essa opção mais rapidamente no futuro, você pode fixar o recurso no painel inicial.

## Retenção de dados 

*Por quanto tempo os dados são mantidos no portal? É seguro?*

Consulte [Privacidade e retenção de dados][data].

## Próximas etapas

*Configurei o Application Insights para meu aplicativo de servidor Java. O que mais posso fazer?*

* [Monitorar a disponibilidade de suas páginas da Web][availability]
* [Monitorar o uso da página da Web][usage]
* [Acompanhar o uso e diagnosticar problemas em seus aplicativos de dispositivos][platforms]
* [Escrever código para acompanhar o uso de seu aplicativo][track]
* [Capturar logs de diagnóstico][javalogs]


## Obter ajuda

* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[universal]: app-insights-windows-get-started.md#universal
[usage]: app-insights-web-track-usage.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Oct15_HO3-->