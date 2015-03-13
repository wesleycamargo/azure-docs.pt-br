<properties 
	pageTitle="Logs de streaming e console" 
	description="Logs de streaming e visão geral do console" 
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor="" 
	services="web-sites" 
	documentationCenter=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="adamab"/>

#Logs de streaming e o console

### Logs de streaming ###

O Portal de Visualização do Microsoft Azure fornece um visualizador de log de streaming integrado que permite exibir eventos de rastreamento dos sites em tempo real.  

A configuração disso exige algumas etapas simples:

- Escrever rastreamentos no código
- Habilitar o Diagnóstico de Aplicativo dentro do Portal de Visualização do Azure
- Clicar na parte de logs de streaming na lâmina do site

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

A classe de rastreamento está no namespace System. Diagnostics.

Em um aplicativo node. js, você pode escrever esse código para obter o mesmo resultado:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Como habilitar e exibir os logs de streaming ###

O diagnóstico é habilitado por site.  Dentro do [portal](https://portal.azure.com), clique no botão **Procurar** na barra de menus à esquerda e, em seguida, clique em **Sites** para obter a lista de todos os sites.  

![][BrowseSitesScreenshot]

Clique no nome do site que você deseja configurar.  Em seguida, clique na parte chamada **LOGS DE DIAGNÓSTICO** e deixe a configuração do **Registro em Log do Aplicativo (Sistema de Arquivos)** em **ON**.  Em seguida, a opção **Nível** é exibida, permitindo que você altere o nível de severidade de rastreamentos para capturar.  Você deverá defini-lo como **Detalhado** se estiver tentando se familiarizar com o recurso porque isso garantirá que todas as instruções de rastreamento sejam registradas.

Clique em **SALVAR** na parte superior da lâmina, e você está pronto para exibir logs.

Para exibir os logs de streaming dentro do portal, clique na parte **LOGS DE STREAMING** na lâmina do site.  Se o site estiver escrevendo ativamente instruções de rastreamento, você deverá vê-las na janela resultante em tempo quase real.

![][StreamingLogsScreenshot]

## Console ##

O Portal de Visualização do Azure dá ao console acesso ao ambiente do site. É possível explorar o sistema de arquivos do site e executar scripts do powershell/cmd.  Você está vinculado às mesmas permissões definidas como o código do site em execução durante a execução dos comandos do console. Você não poderá acessar os diretórios protegidos ou executar scripts que exigem permissões elevadas.  

Para chegar ao console, procure um site conforme descrito na seção acima.  Clique na parte **Console**, e o console abrirá.

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

<!--HONumber=46--> 
