<properties 
	pageTitle="Monitorar um site do SharePoint com o Application Insights" 
	description="Iniciar o monitoramento de um novo aplicativo com uma nova chave de instrumentação" 
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
	ms.date="03/24/2016" 
	ms.author="awills"/>

# Monitorar um site do SharePoint com o Application Insights


[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

O Application Insights do Visual Studio monitora a disponibilidade, o desempenho e o uso de seus aplicativos. Aqui você aprenderá a configurá-lo para um site do SharePoint.


## Criar um recurso do Application Insights


No [Portal do Azure](https://portal.azure.com), crie um novo recurso do Application Insights. Escolha ASP.NET como o tipo de aplicativo.

![Clique em Propriedades, selecione a chave e pressione ctrl + C](./media/app-insights-sharepoint/01-new.png)


A folha que será aberta é o local em que você verá os dados de uso e desempenho sobre seu aplicativo. Para retornar a ela na próxima vez em que fizer logon no Azure, você deverá encontrar um bloco para ela na tela inicial. Como alternativa, clique em Procurar para localizá-la.
    


## Adicione nosso script a suas páginas da Web

Em Início Rápido, obtenha o script para páginas da Web:

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

Insira o script antes da marca &lt;/head&gt; de cada página que você deseja acompanhar. Se seu site possui uma página mestra, você poderá colocar o script lá. Por exemplo, em um projeto ASP.NET MVC, você deve colocá-lo em View\\Shared\_Layout.cshtml

O script contém a chave de instrumentação que direciona a telemetria para o recurso do Application Insights.

### Adicionar o código às páginas do site

#### Na página mestra

Se você puder editar a página mestra do site, que fornecerá monitoramento para todas as páginas no site.

Confira a página mestra e edite-a usando o SharePoint Designer ou qualquer outro editor.

![](./media/app-insights-sharepoint/03-master.png)


Adicione o código antes da marca </head>.


![](./media/app-insights-sharepoint/04-code.png)

#### Ou em páginas individuais

Para monitorar um conjunto limitado de páginas, adicione o script separadamente a cada página.

Insira uma web part e insira o trecho de código nela.


![](./media/app-insights-sharepoint/05-page.png)


## Exibir dados sobre seu aplicativo

Reimplante o aplicativo.

Retorne à folha de seu aplicativo no [portal do Azure](https://portal.azure.com).

Os primeiros eventos aparecerão na Pesquisa.

![](./media/app-insights-sharepoint/09-search.png)

Se você estiver esperando mais dados, clique em Atualizar depois de alguns segundos.

Na folha visão geral, clique em **análises de uso** para ver gráficos de usuários, sessões e modos de exibição de página:

![](./media/app-insights-sharepoint/06-usage.png)

Clique em qualquer gráfico para ver mais detalhes - exibições de página por exemplo:

![](./media/app-insights-sharepoint/07-pages.png)

Ou Usuários:


![](./media/app-insights-sharepoint/08-users.png)


## Capturando a ID de usuário


O trecho de código de página da Web padrão não captura a ID de usuário do SharePoint, mas você pode fazer isso com uma pequena modificação.


1. Copie a chave de instrumentação do seu aplicativo da lista suspensa Essentials no Application Insights. 


    ![](./media/app-insights-sharepoint/02-props.png)

2. Substitua a chave de instrumentação para 'XXXX' no trecho a seguir.
3. Insira o script em seu aplicativo do SharePoint, em vez de inserir o trecho de código que você obtém do portal.



```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 
  
<script type="text/javascript"> 
var personProperties; 
  
// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 
  
function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 
     
    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 
    
    personProperties = peopleManager.getMyProperties(); 
  
    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 
     
// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 
  
// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## Próximas etapas

* [Testes da web](app-insights-monitor-web-app-availability.md) para monitorar a disponibilidade de seu site.

* [Application Insights](app-insights-overview.md) para outros tipos de aplicativos.



<!--Link references-->


 

<!-----------HONumber=AcomDC_0330_2016-->