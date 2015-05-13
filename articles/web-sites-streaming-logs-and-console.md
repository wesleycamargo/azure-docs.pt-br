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
	ms.date="11/17/2014" 
	ms.author="adamab"/>

#Logs de streaming e o console

### Logs de streaming ###

O Portal do Microsoft Azure fornece um visualizador de log de streaming integrado que permite que você exiba os eventos de rastreamento de seus aplicativos Web do Serviço de Aplicativo do Azure em tempo real.  

A configuração disso exige algumas etapas simples:

- Escrever rastreamentos no código
- Habilitar o Diagnóstico de Aplicativo no Portal do Azure
- Clique na parte de logs de streaming na folha do aplicativo Web

### Como escrever rastreamentos no código ###

É fácil escrever rastreamentos no código.  No C#, é tão fácil quanto escrever o seguinte código:

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

A classe de Rastreamento localiza-se no namespace System.Diagnostics.

Em um aplicativo node. js, você pode escrever esse código para obter o mesmo resultado:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Como habilitar e exibir os logs de streaming ###

Os diagnósticos são habilitados de acordo com cada aplicativo Web.  No [portal](https://portal.azure.com), clique no botão **Procurar** na barra de menus à esquerda e, em seguida, clique em **Aplicativos Web** para obter a lista de todos os aplicativos Web.  

![][BrowseSitesScreenshot]

Clique no nome do aplicativo Web que você deseja configurar.  Em seguida, clique na parte chamada **LOGS DE DIAGNÓSTICO** e deixe a configuração do **Registro em Log do Aplicativo (Sistema de Arquivos)** em **ON**.  Em seguida, a opção **Nível** é exibida, permitindo que você altere o nível de severidade de rastreamentos para capturar.  Você deverá defini-lo como **Detalhado** se estiver tentando se familiarizar com o recurso porque isso garantirá que todas as instruções de rastreamento sejam registradas.

Clique em **SALVAR** na parte superior da lâmina, e você está pronto para exibir logs.

Para exibir os logs de streaming dentro do portal, clique na parte **LOGS DE STREAMING** na folha do aplicativo Web.  Se o aplicativo Web estiver gravando ativamente instruções de rastreamento, você deverá vê-las na janela resultante quase em tempo real.

![][StreamingLogsScreenshot]

## Console ##

O Portal do Azure dá ao console acesso ao ambiente do aplicativo Web. É possível explorar o sistema de arquivos do aplicativo Web e executar scripts do powershell/cmd.  Você está vinculado às mesmas permissões definidas como o código do aplicativo Web em execução durante a execução dos comandos do console. Você não poderá acessar os diretórios protegidos ou executar scripts que exigem permissões elevadas.  

Para chegar ao console, procure um aplicativo Web conforme descrito na seção acima.  Clique na parte **Console**, e o console abrirá.

![][ConsoleScreenshot]

Para se familiarizar com o console, tente comandos básicos como estes:



`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````



<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

<!--HONumber=52-->