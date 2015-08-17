<properties 
	pageTitle="Logs de streaming e console" 
	description="Logs de streaming e visão geral do console"
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="adamab"/>

#Logs de streaming e o console

### Logs de streaming ###

O [portal de visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) fornece um visualizador de log de streaming integrado que permite que você exiba os eventos de rastreamento de seus aplicativos Web do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) em tempo real.

A configuração disso exige algumas etapas simples:

- Escrever rastreamentos no código
- Habilitar o Diagnóstico de Aplicativo dentro do Portal de Visualização do Azure
- Clique na parte de logs de streaming na folha do aplicativo Web

### Como escrever rastreamentos no código ###

É fácil escrever rastreamentos no código. No C#, é tão fácil quanto escrever o seguinte código:

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

A classe de rastreamento está no namespace System. Diagnostics.

Em um aplicativo node.js, você pode escrever esse código para obter o mesmo resultado:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Habilite e exiba os logs de streaming ###

Os diagnósticos são habilitados de acordo com cada aplicativo Web.

1. De dentro do [Portal de Visualização do Azure](https://portal.azure.com), clique em **Procurar** > **Aplicativos Web** para obter a lista de todos os seus aplicativos da web.  

2. Clique no nome do aplicativo Web que você deseja configurar.

3. Clique em **Todas as configurações** > **Logs de diagnóstico**, e coloque o botão **Log de aplicativo (sistema de arquivos)** na configuração **ON**.

4. Em seguida, a opção **Nível** é exibida, permitindo que você altere o nível de severidade de rastreamentos para capturar. Você deverá defini-lo como **Detalhado** se estiver tentando se familiarizar com o recurso porque isso garantirá que todas as instruções de rastreamento sejam registradas.

5. Clique em **Salvar** na parte superior da folha, e você está pronto para exibir logs.

6. Para exibir os logs de streaming de dentro do portal, clique na parte **Logs de streaming** na folha do aplicativo Web. Se o aplicativo Web estiver gravando ativamente instruções de rastreamento, você deverá vê-las na janela resultante quase em tempo real.

![][StreamingLogsScreenshot]

## Acessar o console ##

O Portal de Visualização do Azure dá ao console acesso ao seu aplicativo Web. É possível explorar o sistema de arquivos do aplicativo Web e executar scripts do powershell/cmd. Você está vinculado às mesmas permissões definidas como o código do aplicativo Web em execução durante a execução dos comandos do console. Você não poderá acessar os diretórios protegidos ou executar scripts que exigem permissões elevadas.

1. Navegue até a folha de um aplicativo web, conforme descrito na seção acima.

2. Clique na parte **Console**, e o console abrirá.

![][ConsoleScreenshot]

Para se familiarizar com o console, tente comandos básicos como estes:

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
 

<!---HONumber=August15_HO6-->