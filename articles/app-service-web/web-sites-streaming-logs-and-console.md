---
title: Logs de streaming e console
description: "Logs de streaming e visão geral do console"
author: btardif
manager: wpickett
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3e50a287-781b-4c6a-8c53-eec261889d7a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7704270d7bba54577f61f3f349dc69dc6ddf08c6


---
# <a name="streaming-logs-and-the-console"></a>Logs de streaming e o console
## <a name="streaming-logs"></a>Logs de streaming
O **portal do Azure** fornece um visualizador de log de streaming integrado que permite exibir os eventos de rastreamento de aplicativos do **Serviço de Aplicativo** em tempo real.  

A configuração desse recurso exige algumas etapas simples:

* Escrever rastreamentos no código
* Habilitar os **Logs de Diagnóstico** para seu aplicativo
* Exiba a transmissão da interface de usuário interna **Logs de Streaming** no **portal do Azure**.

### <a name="how-to-write-traces-in-your-code"></a>Como escrever rastreamentos no código
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

Em um aplicativo node.js, você pode escrever esse código para obter o mesmo resultado:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### <a name="how-to-enable-and-view-the-streaming-logs"></a>Como habilitar e exibir os logs de streaming
Os Diagnósticos do ![][BrowseSitesScreenshot] são habilitados de acordo com cada aplicativo. Comece navegando até o site em que deseja habilitar esse recurso.  

![][DiagnosticsLogs] No menu de configurações, role para baixo até a seção **Monitoramento** e clique em **(1) Logs de Diagnóstico**. Em seguida, **(2) habilite** **Log de Aplicativo (Filesystem)** ou **Log de Aplicativo (blob)** A opção **Nível** permite alterar o nível de severidade dos rastreamentos a serem capturados. Se estiver apenas tentando se familiarizar com o recurso, defina o nível para **Detalhado** a fim de garantir que todos os demonstrativos de rastreamento sejam coletados.

Clique em **SALVAR** na parte superior da lâmina, e você está pronto para exibir logs.

> [!NOTE]
> Quanto mais alto o **nível de severidade**, mais recursos serão consumidos para registro e mais rastreamentos serão produzidos. Certifique-se de que o **nível de severidade** esteja configurado com o detalhamento correto para um site de produção ou tráfego alto. 
> 
> 

![][StreamingLogsScreenshot] Para exibir os **logs de streaming** de dentro do portal do Azure, clique em **(1) Fluxo de Log** também na seção **Monitoramento** do menu de configurações. Se o aplicativo estiver gravando ativamente os demonstrativos de rastreamento, você deverá vê-los na **(2) interface de usuário Logs de Streaming** quase em tempo real.

## <a name="console"></a>Console
O **portal do Azure** fornece ao console acesso ao seu aplicativo. É possível explorar o sistema de arquivos do aplicativo e executar scripts powershell/cmd. Você é vinculado pelas mesmas permissões definidas que seu código de aplicativo em execução ao executar comandos do console. O acesso a diretórios protegidos ou a execução de scripts que exigem permissões elevadas é bloqueado.  

![][ConsoleScreenshot] No menu de configurações, role para baixo até a seção **Ferramentas de Desenvolvimento** e clique em **(1) Console**, e a interface de usuário **(2) Console** será aberta à direita.

Para se familiarizar com o **console**, tente comandos básicos como:

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



<!--HONumber=Nov16_HO3-->


