<properties 
	pageTitle="Adicione o Application Insights SDK ao seu projeto Web" 
	description="Analise o uso, disponibilidade e desempenho de seu local ou um aplicativo Web do Microsoft Azure com o Application Insights." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-02-03" 
	ms.author="awills"/>

# Application Insights - Iniciar o monitoramento de integridade e uso do seu aplicativo

*O Application Insights está em visualizar.*

O Application Insights permite que você monitore seu aplicativo ao vivo para:

* **Disponibilidade** - Testaremos suas URLs a cada poucos minutos em todo o mundo.
* **Desempenho**   - detecte e diagnostique problemas de desempenho e exceções.
* **Uso** - Descubra o que os usuários estão fazendo com o seu aplicativo, para que você possa torná-lo melhor para eles.

Para modos alternativos de fazê-lo, leia [Introdução ao Application Insights][start].

## <a name="add"></a>Adicionar o Application Insights ao seu projeto

Você precisará do [Visual Studio 2013 Atualização 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (ou posterior) e uma conta no [Microsoft Azure](http://azure.com).

### Se é um novo projeto...

Quando criar um novo projeto no Visual Studio 2013, certifique-se de que o Application Insights está selecionado. 


![Create an ASP.NET project](./media/app-insights-start-monitoring-app-health-usage/appinsights-01-vsnewp1.png)

Se esta é sua primeira vez, você será solicitado a fazer logon ou se inscrever na visualização do Microsoft Azure. (ela é separada de sua conta do Visual Studio Online)

Use **Configurar definições** se você quer que o recurso do Azure tenha um nome diferente que o do seu projeto, ou se você quer que o recurso apareça no mesmo grupo como um projeto diferente. 

*Sem opção de Application Insights? Verifique se você está usando o Visual Studio 2013 Atualização 3, se as Ferramentas do Application Insights estão habilitadas nas Extensões e Atualizações e se você está criando um projeto da web, Windows Store ou Windows Phone.*

### ...ou então, se é um projeto existente

Clique com o botão direito do mouse no projeto no Gerenciador de Soluções, e selecione Adicionar Application Insights.

![Choose Add Application Insights](./media/app-insights-start-monitoring-app-health-usage/appinsights-03-addExisting.png)

*Há mais uma etapa, se quiser configurar a análise de uso da web, mas vamos ver alguns dados primeiro...*


### <a name="run"></a>2. Execute seu projeto

Execute seu aplicativo com F5 e experimente - abra páginas diferentes.

No Visual Studio, você verá uma contagem de eventos que foram recebidas.

![](./media/app-insights-start-monitoring-app-health-usage/appinsights-09eventcount.png)

### <a name="monitor"></a>3. Veja os dados de monitoramento

Abra o Application Insights de seu projeto.

![Right-click your project and open the Azure portal](./media/app-insights-start-monitoring-app-health-usage/appinsights-04-openPortal.png)


Procure pelos dados nos blocos de **Integridade do aplicativo**. Primeiro, você apenas verá um ou dois pontos. Por exemplo:

![Click through to more data](./media/app-insights-start-monitoring-app-health-usage/appinsights-41firstHealth.png)

Clique em qualquer bloco para ver mais detalhes. Você pode alterar o que vê nos relatórios. [Saiba mais sobre configuração de relatórios de Integridade do Aplicativo.][perf]


### <a name="deploy"></a>4. Implante seu aplicativo

Implante seu aplicativo e assista a acumulação de dados.



Uma vez que você tem um aplicativo ao vivo, [configure os testes na Web][availability] para certificar-se de que ele continuará ao vivo. 

![Example application monitor in Application Insights](./media/app-insights-start-monitoring-app-health-usage/appinsights-00-appblade.png)

### Deseja alterar o nome do seu aplicativo no portal?

Você pode alterar o recurso para o qual o seu projeto envia telemetria. 

(O "recurso" é a folha de Ideias de Aplicativo nomeado, onde seus resultados aparecem. Você pode colocar mais do que um recurso em um grupo - por exemplo, se você tem vários projetos que formam parte de um aplicativo de negócio). 

No Gerenciador de Soluções, clique com o botão direito do mouse em ApplicationInsights.config e escolha **Atualizar Application Insights**. Isso abre um assistente onde você pode escolher um recurso existente diferente, ou criar um novo.

Em seguida, volte para o portal e exclua o recurso antigo.

## <a name="video"></a>Vídeo

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a>Próximas etapas

[Acompanhar uso do seu aplicativo web][usage]

[Monitore o desempenho dos aplicativos web][perf]

[Capturar e pesquisar por logs de diagnóstico][diagnostic]

[Solucionar problemas][qna]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]



<!--HONumber=46--> 
 