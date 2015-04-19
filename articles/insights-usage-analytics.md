<properties 
	pageTitle="Como usar análises do usuário final" 
	description="Análises do usuário final para sites do Microsoft Azure." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-09" 
	ms.author="awills"/>

# Análises de sites do Microsoft Azure

Você imagina quantos usuários visitaram seu site?  Você imagina qual é o tempo de carga médio de páginas ou quais navegadores estão sendo usados?  Inserindo algumas linhas de script nas suas páginas da web, você pode coletar dados sobre como seu site é usado por seus clientes. 

*Você pode fazer isso para sites não Azure também: [Monitorar o uso de aplicativos Web com o Application Insights](app-insights-web-track-usage.md).*

![End User Analytics](./media/insights-usage-analytics/Insights_ConfiguredExperience.png)

## Como configurar as análises do usuário final

1. Clique na parte da lâmina **Site** que diz **Análises do usuário final**
2. Na lâmina **Configuração**, selecione o script de instrumentação inteiro e copie-o.  
    ![Configuration](./media/insights-usage-analytics/Insights_CopyCode.png)
3. Cole o script em cada uma de suas páginas da web, imediatamente antes do fechamento da marca </head>. É uma boa ideia inserir o script em todas as suas páginas da web. Se você estiver usando o ASP.NET, poderá fazer isso inserindo o script na página mestra do seu aplicativo.
4. Implantar e usar o aplicativo web. A informações das análises de uso começarão a aparecer cerca de cinco a 10 minutos depois.

## Explorando os dados

A parte da sessão Navegadores permite analisar para ver os diferentes navegadores e as versões dos navegadores.

![Browsers](./media/insights-usage-analytics/Insights_Browsers.png)

A parte Análises mostra:

- Uma divisão dos diferentes tipos de dispositivo incluindo Desktop e Celular.
- Suas cinco páginas e gráficos com o maior carregamento de página na última semana.  O número de sessões e exibições também está disponível  
    ![Top Pages](./media/insights-usage-analytics/Insights_TopPages.png)
- As páginas mais lentas da semana anterior para que você possa aprimorá-las para atender às suas metas e objetivos de negócios


<!--HONumber=46--> 
