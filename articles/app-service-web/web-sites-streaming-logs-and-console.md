<properties 
	pageTitle="Logs de streaming e console" 
	description="Logs de streaming e visão geral do console" 
	authors="btardif" 
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
	ms.date="08/10/2015" 
	ms.author="byvinyal"/>

#Logs de streaming e o console

### Logs de streaming ###

O Portal do Microsoft Azure fornece um visualizador de log de streaming integrado que permite que você exiba os eventos de rastreamento de seus aplicativos Web do Serviço de Aplicativo do Azure em tempo real.

A configuração disso exige algumas etapas simples:

- Escrever rastreamentos no código
- Habilitar o Diagnóstico de Aplicativo no Portal do Azure
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

Em um aplicativo node. js, você pode escrever esse código para obter o mesmo resultado:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Como habilitar e exibir os logs de streaming ###

![][BrowseSitesScreenshot] Os diagnósticos são habilitados de acordo com cada aplicativo Web. No [portal](https://portal.azure.com), clique no botão **Procurar (1)** na barra de menus à esquerda e, em seguida, clique em **Aplicativos Web (2)** para obter a **lista (3)** de todos os aplicativos Web.

Clique no nome do aplicativo Web que você deseja configurar para navegar até ele.
  
![][DiagnosticsLogs] Em seguida, clique em **Configurações (1)** > **Logs de diagnóstico (2)** e **ative** o **Registro em Log do Aplicativo (Sistema de Arquivos)(3)**. A opção **Nível** permite alterar o nível de severidade de rastreamentos a serem capturados. Você deverá defini-lo como **Detalhado** se estiver tentando se familiarizar com o recurso porque isso garantirá que todas as instruções de rastreamento sejam registradas.

Clique em **SALVAR** na parte superior da lâmina, e você está pronto para exibir logs.

**OBSERVAÇÃO:** quanto mais alto o **nível de severidade**, mais recursos serão consumidos para log e mais rastreamentos mais serão exibidos. Verifique se está definido para o nível apropriado ao usar esse recurso para um alto tráfego / site de produção.

![][StreamingLogsScreenshot] Para exibir os logs em fluxo de dentro do portal, clique em **Ferramentas (1)** > **Fluxo de Log(2)**. Se o aplicativo Web estiver gravando ativamente instruções de rastreamento, você deverá vê-las na janela resultante quase em tempo real.

## Console ##

O Portal do Azure dá ao console acesso ao ambiente do aplicativo Web. É possível explorar o sistema de arquivos do aplicativo Web e executar scripts do powershell/cmd. Você está vinculado às mesmas permissões definidas como o código do aplicativo Web em execução durante a execução dos comandos do console. Você não poderá acessar os diretórios protegidos ou executar scripts que exigem permissões elevadas.

![][ConsoleScreenshot] Para chegar ao console, procure um aplicativo Web conforme descrito na seção acima. Clique em **Ferramentas**>**Console** e o console será aberto.

Para se familiarizar com o console, tente comandos básicos como estes:



`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````



<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

<!---HONumber=Oct15_HO3-->