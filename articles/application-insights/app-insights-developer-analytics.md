<properties
	pageTitle="Análise para o Desenvolvedor"
	description="DevOps com Visual Studio, Application Insights e HockeyApp"
	authors="alancameronwills"
	services="application-insights"
    documentationCenter=""
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="05/18/2016"
	ms.author="awills"/>

# Análise para o desenvolvedor com o Application Insights e o HockeyApp

*O Application Insights está em modo de visualização.*

Muitos projetos operam um rápido ciclo de [DevOps](https://en.wikipedia.org/wiki/DevOps). Eles criam e distribuem seus aplicativos, obtêm comentários sobre o seu desempenho e sobre o que os usuários fazem com ele e, em seguida, usam esse conhecimento para planejar futuros ciclos de desenvolvimento.

Para monitorar o uso e o desempenho, é importante ter telemetria do aplicativo dinâmico, bem como os comentários dos usuários.

Muitos sistemas são criados de vários componentes: um serviço Web, processadores de back-end ou armazenamentos de dados e software de cliente executado no navegador do usuário ou como um aplicativo em um telefone ou em outro dispositivo. A telemetria desses componentes diferentes deve ser obtida em conjunto.

Algumas versões restringiram a distribuição a testadores designados; também organizamos o pré-lançamento (testes de novos recursos com público restrito) e testes A|B (testes paralelos de interface do usuário alternativa).

O gerenciamento de distribuições e a integração de monitoramento em vários componentes de cliente e de servidor não são tarefas triviais. Esse processo é uma parte essencial da arquitetura do aplicativo: não é possível criar um sistema desse tipo sem um ciclo de desenvolvimento iterativo e boas ferramentas de monitoramento.

Neste artigo, vamos examinar como os aspectos de monitoramentos do ciclo de DevOps se ajusta a outras partes do processo.

Se você quiser examinar um exemplo específico, há [um estudo de caso interessante](http://aka.ms/mydrivingdocs) com vários componentes de cliente e de servidor.

## Um ciclo de DevOps

As Ferramentas de Análise para o Desenvolvedor e do Visual Studio oferecem uma experiência de DevOps bem integrada. Por exemplo, aqui está um ciclo típico para um aplicativo Web (que poderia ser Java, Node.js ou ASP.NET):

![Ciclo de devops de aplicativo Web](./media/app-insights-developer-analytics/040.png)

* Um desenvolvedor faz check-in no repositório de código ou mescla no branch principal. Nesta ilustração, o repositório é o Git, mas também poderia ser o [Controle de Versão do Team Foundation](https://www.visualstudio.com/docs/tfvc/overview).
* As alterações disparam um teste de unidade e de compilação. O serviço de compilação pode estar no [Visual Studio Team Services ou em seu equivalente local, o Team Foundation Server](https://www.visualstudio.com/docs/vsts-tfs-overview).
* Um teste de unidade e de compilação bem-sucedido pode [disparar uma implantação automática](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition). O host de aplicativo Web pode ser seu próprio servidor Web ou o Microsoft Azure.
* A telemetria do aplicativo dinâmico é enviada para o [Application Insights](app-insights-overview.md), tanto do servidor como [de navegadores cliente](app-insights-javascript.md). Lá, você pode analisar o desempenho dos padrões de uso e do aplicativo. As poderosas [ferramentas de pesquisa](app-insights-analytics.md) ajudam você a diagnosticar problemas. Os [alertas](app-insights-alerts.md) garantem que você fique sabendo de um problema assim que ele ocorre.
* Seu próximo ciclo de desenvolvimento é informado por sua análise da telemetria dinâmica.

### Aplicativos da área de trabalho e de dispositivo

Para aplicativos da área de trabalho e de dispositivo, a parte de distribuição do ciclo é ligeiramente diferente, porque nós não estamos apenas carregando um ou dois servidores. Em vez disso, um teste de unidade e de compilação bem-sucedido pode [disparar o carregamento no HockeyApp](https://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs). O HockeyApp supervisiona a distribuição para sua equipe de usuários de teste (ou para o público em geral, se você preferir).


![Ciclo de devops de dispositivo](./media/app-insights-developer-analytics/030.png)

O HockeyApp também coleta dados de desempenho e de uso, nos formatos:

* Comentários textuais do usuário com capturas de tela
* Relatórios de falha
* Telemetria personalizada codificada por você.

Novamente, o ciclo de DevOps é concluído quando você faz seus planos de desenvolvimento futuros de acordo com os comentários obtidos.


## Configuração da Análise para o Desenvolvedor

Para cada componente do aplicativo - móvel ou Web ou da área de trabalho - as etapas são basicamente as mesmas. Para muitos tipos de aplicativo, o Visual Studio executa automaticamente algumas dessas etapas.

1. Adicione o SDK adequado ao seu aplicativo. Para aplicativos de dispositivo, será o HockeyApp, e para os serviços da Web, será o Application Insights. Cada um tem diversas variantes para diferentes plataformas. (Também é possível usar o SDK para aplicativos da área de trabalho, embora o HockeyApp seja recomendável).
2. Registre seu aplicativo no Application Insights ou no portal do HockeyApp, dependendo do SDK usado. Aqui você verá a análise do seu aplicativo dinâmico. Você obtém uma chave de instrumentação ou a ID que será configurada em seu aplicativo para que o SDK saiba para onde enviar a telemetria.
3. Adicione código personalizado (se desejado) para registrar em log eventos ou métricas, para ajudá-lo com o diagnóstico ou para analisar o desempenho ou o uso. Há muito monitoramento interno, portanto você não precisará disso em seu primeiro ciclo.
3. Para aplicativos de dispositivo:
 * Carregue uma compilação de depuração no HockeyApp. Dali, você pode distribuí-lo a uma equipe de usuários de teste. Sempre que você carregar as compilações subsequentes, a equipe será notificada.
 * Quando você configura seu serviço de compilação contínuo, crie uma definição de lançamento que use a etapa de plug-in para carregar no HockeyApp.

### Análise e exportação de telemetria do HockeyApp

Você pode investigar a telemetria personalizada e de log do HockeyApp usando os recursos de análise e exportação contínua do Application Insights ao [configurar uma ponte](app-insights-hockeyapp-bridge-app.md).



## Próximas etapas
 
Veja as instruções detalhadas para diferentes tipos de aplicativo:

* [Aplicativo Web ASP.NET](app-insights-asp-net.md)
* [Aplicativo Web Java](app-insights-java-get-started.md)
* [Aplicativo Web Node.js](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Aplicativo iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Aplicativo Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Aplicativo Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [Aplicativo Universal do Windows](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Aplicativo do Windows Phone 8 e 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Aplicativo do Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

<!---HONumber=AcomDC_0907_2016-->