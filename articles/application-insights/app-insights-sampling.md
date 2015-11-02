<properties 
	pageTitle="Amostragem de telemetria no Application Insights" 
	description="Como manter o volume de telemetria sob controle." 
	services="application-insights" 
    documentationCenter="windows"
	authors="vgorbenko" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="awills"/>

#  Amostragem no Application Insights

*O Application Insights está em modo de visualização.*


A amostragem é uma opção no Application Insights que permite coletar e armazenar um conjunto reduzido de telemetria, ao mesmo tempo que mantém uma análise estatisticamente correta dos dados do aplicativo. Você normalmente a usaria para reduzir o tráfego e evitar a [limitação](app-insights-pricing.md#data-rate). Os dados são filtrados de forma que os itens relacionados são permitidos, para que você possa realizar investigações de diagnóstico com um conjunto reduzido de dados. Os lados do cliente e do servidor são coordenados automaticamente para filtrar itens relacionados. Quando as contagens de métrica são apresentadas a você no portal, elas são normalizadas novamente para levar em conta a amostragem, a fim de minimizar qualquer efeito sobre as estatísticas.


A amostragem está atualmente na versão Beta e pode ser alterada no futuro.

## Configurando a amostragem para o seu aplicativo

A amostragem está atualmente disponível para o SDK do ASP.NET ou para [qualquer página da Web](#other-web-pages).

### Servidor ASP.NET
Para configurar a amostragem em seu aplicativo, insira o seguinte trecho de código ao método `Application_Start()` em Global.asax.cs:

```C#

    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    // This configures sampling percentage at 10%:
    TelemetryConfiguration.Active.TelemetryChannel = new TelemetryChannelBuilder().UseSampling(10.0).Build();
```

> [AZURE.NOTE]Para o percentual de amostragem, escolha um percentual que esteja próximo a 100/N, em que N é um inteiro. Por exemplo, os valores válidos incluem 50 (=1/2), 33,33 (=1/3), 25 (=1/4), 20 (=1/5) e assim por diante. Atualmente, a amostragem não dá suporte a outros valores.

### Páginas da Web com JavaScript

Você pode configurar páginas da Web para a amostragem em qualquer servidor. Para servidores ASP.NET, configure os lados do cliente e do servidor.

Ao [configurar as páginas da Web para o Application Insights](app-insights-javascript.md), modifique o trecho de código que você receber do portal do Application Insights. (No ASP.NET, você o encontrará em \_Layout.cshtml.) Insira uma linha como `samplingPercentage: 10,` antes da chave de instrumentação:

    <script>
	var appInsights= ... 
	}({ 

	samplingPercentage: 10, 

	instrumentationKey:...
	}); 
	
	window.appInsights=appInsights; 
	appInsights.trackPageView(); 
	</script> 

Lembre-se de fornecer o mesmo percentual de amostragem no JavaScript fornecido no lado do servidor.

[Saiba mais sobre a API](app-insights-api-custom-events-metrics.md)


## Quando usar a amostragem?

A amostragem não é necessária para a maioria dos aplicativos de pequeno e médio porte. As informações de diagnóstico mais úteis e as estatísticas mais precisas são obtidas por meio da coleta de dados em todas as atividades de usuário.

 
As principais razões pelas quais você usaria a amostragem são:


* O serviço Application Insights remove (“limita”) os pontos de dados quando o aplicativo envia uma taxa muito alta de telemetria em um curto intervalo de tempo. 
* Você deseja permanecer dentro da [cota](app-insights-pricing.md) de pontos de dados para o tipo de preço. 
* Para reduzir o tráfego de rede da coleção de telemetria. 

## Como funciona a amostragem?

Do ponto de vista do aplicativo, a amostragem é um recurso do SDK do Application Insights. Especifique o percentual de todos os pontos de dados que deve ser enviado ao serviço Application Insights. Na versão 2.0.0 do SDK do Application Insights, é possível controlar o percentual de amostragem por meio do seu código. (Além disso, as versões futuras do SDK permitirão a configuração do percentual de amostragem no arquivo ApplicationInsights.config.)

O SDK decide quais itens de telemetria serão removidos e quais serão mantidos. A decisão de amostragem baseia-se em várias regras que visam preservar todos os pontos de dados inter-relacionados intactos, mantendo uma experiência de diagnóstico no Application Insights que seja acionável e confiável mesmo com um conjunto reduzido de dados. Por exemplo, se para uma solicitação com falha seu aplicativo enviar itens de telemetria adicionais (como exceção e rastreamentos registrados desta solicitação), a amostragem não dividirá essa solicitação e outra telemetria. Ela mantém ou remove todos juntos. Como resultado, quando você examinar os detalhes da solicitação no Application Insights, é possível ver sempre a solicitação junto com seus itens de telemetria associados.

Para aplicativos que definem o “usuário” (ou seja, a maioria dos aplicativos Web típicos), a decisão de amostragem é baseada no hash da ID de usuário, o que significa que toda a telemetria para qualquer usuário específico é preservada ou removida. Para os tipos de aplicativos que não definem usuários (como serviços Web), a decisão de amostragem baseia-se na ID de operação da solicitação. Por fim, para os itens de telemetria que não têm uma ID de usuário nem de operação definida (por exemplo, itens telemetria relatados dos threads assíncronos sem contexto HTTP), a amostragem apenas captura um percentual dos itens de telemetria de cada tipo.

Ao apresentar a telemetria de volta para você, o serviço Application Insights ajusta as métricas pelo mesmo percentual de amostragem usado no momento da coleta, para compensar os pontos de dados ausentes. Portanto, ao examinar a telemetria no Application Insights, os usuários veem aproximações estatisticamente corretas que estão muito próximas aos números reais.

A precisão da aproximação depende principalmente do percentual de amostragem configurado. Além disso, a precisão aumenta para os aplicativos que lidam com um grande volume de solicitações geralmente semelhantes de uma grande quantidade de usuários. Por outro lado, para aplicativos que não funcionam com uma carga significativa, a amostragem não é necessária, pois esses aplicativos geralmente podem enviar toda a sua telemetria, ao mesmo tempo que permanecem dentro da cota, sem causar perda de dados da limitação.

Observe que o Application Insights não realiza a amostragem dos tipos de telemetria Métricas e Sessões, já que para esses tipos, uma redução na precisão pode ser altamente indesejável.

## Amostragem e o SDK do JavaScript

O SDK do lado do cliente (JavaScript) participa da amostragem em conjunto com o SDK do lado do servidor. As páginas instrumentadas enviarão apenas a telemetria do lado do cliente dos mesmos usuários para os quais o lado do servidor tomou sua decisão de “amostragem”. Essa lógica é projetada para manter a integridade da sessão do usuário nos lados do cliente e do servidor. Como resultado, em qualquer item de telemetria específico no Application Insights é possível encontrar todos os outros itens de telemetria para esse usuário ou sessão.

*Minha telemetria do lado do cliente e do servidor não mostra exemplos coordenados como descrito acima.*

* Verifique se você habilitou a amostragem tanto no servidor quanto no cliente.
* Certifique-se de que a versão do SDK é 2.0 ou superior.
* Verifique se você definiu o mesmo percentual de amostragem no cliente e no servidor.


## Perguntas frequentes 

*Por que a amostragem não se trata apenas de “coletar X% de cada tipo de telemetria”?*

 *  Embora essa abordagem de amostragem forneceria uma precisão muito alta em aproximações de métrica, ela interromperia a capacidade de correlacionar dados de diagnóstico por usuário, sessão e solicitação, que são essenciais para o diagnóstico. Portanto, a amostragem funciona melhor com a lógica “coletar todos os itens de telemetria para X% de usuários do aplicativo” ou “coletar toda a telemetria para X% das solicitações do aplicativo”. Para os itens de telemetria não associados às solicitações (como o processamento assíncrono em segundo plano), o fallback é “coletar X% de todos os itens para cada tipo de telemetria”. 

*O percentual de amostragem pode ser alterado com o tempo?*

 * Na implementação de hoje, normalmente o percentual de amostragem após sua configuração na inicialização do aplicativo não seria alterado. Embora você tenha controle sobre o tempo de execução do percentual de amostragem, não há nenhuma maneira de determinar qual percentual de amostragem será ideal e coletará “apenas a quantidade certa de volume de dados” antes que a lógica de limitação seja acionada ou antes que a cota do volume de dados mensal seja atingida. As versões futuras do SDK do Application Insights incluirão uma amostragem adaptável que, imediatamente, ajustará o percentual de amostragem para cima e para baixo, com base no volume atualmente observado da telemetria e de outros fatores. 

*Como saber qual percentual de amostragem funcionará melhor para o meu aplicativo?*

* Hoje, é preciso adivinhar. Analise seu uso atual de telemetria em AI, observe as remoções de dados relacionados à limitação e estime o volume da telemetria coletada. Essas três entradas, junto com o tipo de preço selecionado, sugerirão quanto você talvez deseje reduzir o volume da telemetria coletada. No entanto, uma mudança no padrão do volume telemetria pode invalidar o percentual de amostragem configurado de modo ideal (por exemplo, um aumento no número de usuários). Quando implementada, a amostragem adaptável controlará automaticamente o percentual de amostragem até seu nível ideal, com base no volume de telemetria observado.

*O que acontece se eu configurar o percentual de amostragem com um valor muito baixo?*

* Um percentual de amostragem excessivamente baixo (amostragem superagressiva) reduzirá a precisão das aproximações quando o Application Insights tentar compensar a visualização dos dados para a redução do volume de dados. Além disso, a experiência de diagnóstico pode ser afetada negativamente, já que algumas das solicitações lentas ou raramente com falhas podem ser amostradas.

*O que acontece se eu configurar o percentual de amostragem com um valor muito alto?*

* Configurar um percentual de amostragem muito alto (não agressivo o suficiente) resultará em uma redução insuficiente no volume da telemetria coletada. Ainda pode ocorrer perda de dados de telemetria relacionada à limitação, e o custo do uso do Application Insights pode ser maior do que o planejado devido a encargos excedentes.

*Em quais plataformas posso usar a amostragem?*

* Atualmente, a amostragem está disponível para todas as páginas da Web e para os lados do servidor e do cliente de aplicativos Web do .NET.

*Posso usar a amostragem com aplicativos de dispositivo (Windows Phone, iOS, Android ou aplicativos da área de trabalho)?*

* Não, atualmente, não há suporte para a amostragem de aplicativos do dispositivo. 

<!---HONumber=Oct15_HO4-->