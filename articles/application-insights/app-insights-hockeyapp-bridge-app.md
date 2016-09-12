<properties 
	pageTitle="Gerenciamento dos dados do HockeyApp no Application Insights | Microsoft Azure" 
	description="Analise o uso e o desempenho de seu aplicativo do Azure com o Application Insights." 
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
	ms.date="08/25/2016" 
	ms.author="awills"/>

#  Exploração de dados do HockeyApp no Application Insights

O [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) é a plataforma recomendada para o monitoramento de aplicativos móveis e de desktop dinâmico. Do HockeyApp, você pode enviar telemetria personalizada e de rastreamento para monitorar o uso e ajudar no diagnóstico (além de obter dados de falha). Esse fluxo de telemetria pode ser consultado usando o poderoso recurso [Analytics](app-insights-analytics.md) do [Visual Studio Application Insights](app-insights-overview.md). Além disso, você pode [exportar a telemetria personalizada e de rastreamento](app-insights-export-telemetry.md). Para habilitar esses recursos, você configura uma ponte que retransmite os dados do HockeyApp ao Application Insights.


## O aplicativo de ponte HockeyApp

O aplicativo de ponte HockeyApp é o principal recurso que permite a você acessar seus dados do HockeyApp no Application Insights por meio dos recursos Analytics e Exportação Contínua. Todos os dados coletados pelo HockeyApp após a criação do aplicativo de ponte HockeyApp estarão acessíveis a partir desses recursos. Vamos ver como configurar esses aplicativos de ponte.

No HockeyApp, abra Configurações de Conta, [Tokens de API](https://rink.hockeyapp.net/manage/auth_tokens). Crie um novo token ou reutilize um existente. Os direitos mínimos necessários são "somente leitura". Faça uma cópia do token da API.

![Obter um token da API do HockeyApp](./media/app-insights-hockeyapp-bridge-app/01.png)

Abra o portal do Microsoft Azure e [crie um recurso do Application Insights](app-insights-create-new-resource.md). Defina o Tipo de Aplicativo como "Aplicativo de ponte HockeyApp":

![Novo recurso do Application Insights](./media/app-insights-hockeyapp-bridge-app/02.png)

Você não precisa definir um nome - isso será definido automaticamente a partir do nome do HockeyApp.

Os campos da ponte HockeyApp aparecem.

![Inserir campos de ponte](./media/app-insights-hockeyapp-bridge-app/03.png)

Insira o token do HockeyApp que você anotou anteriormente. Essa ação preenche o menu suspenso "Aplicativo HockeyApp" com todos os seus aplicativos do HockeyApp. Selecione o que você deseja usar e conclua o restante dos campos.

Abra o novo recurso.

Observe que os dados demoram um pouco para começar a fluir.

![Recurso do Application Insights aguardando os dados](./media/app-insights-hockeyapp-bridge-app/04.png)

É isso! Daqui em diante, todos os dados coletados em seu aplicativo instrumentado pelo HockeyApp agora também estão disponíveis para você nos recursos Analytics e Exportação Contínua do Application Insights.

Vamos analisar rapidamente cada um desses recursos agora disponíveis para você.

## Análise

O Analytics é uma ferramenta eficiente para consultas ad hoc de seus dados, permitindo que você diagnostique e analise a telemetria e descubra rapidamente as causas raiz e os padrões.


![Análise](./media/app-insights-hockeyapp-bridge-app/05.png)


* [Saiba mais sobre o Analytics](app-insights-analytics-tour.md)
* [Vídeo de introdução](https://channel9.msdn.com/events/Build/2016/T666)
* [Vídeo de conceitos avançados](https://channel9.msdn.com/Events/Build/2016/P591)


## Exportação contínua

A Exportação Contínua permite que você exporte os dados para um contêiner do Armazenamento de Blobs do Azure. Isso será muito útil se você precisar manter seus dados por mais tempo que o período de retenção atualmente oferecido pelo Application Insights. Você pode manter os dados no armazenamento de blobs, processá-los em um banco de dados SQL ou a solução de data warehouse preferencial.

[Saiba mais sobre a Exportação Contínua](app-insights-export-telemetry.md)


## Próximas etapas

* [Aplicar a análise aos seus dados](app-insights-analytics-tour.md)

<!---HONumber=AcomDC_0831_2016-->