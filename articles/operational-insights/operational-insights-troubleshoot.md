<properties 
   pageTitle="Solucionar problemas com Insights Operacionais"
   description="Saiba mais sobre a solução de problemas do Insights Operacionais"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Solucionar problemas com Insights Operacionais
Você pode usar as informações nas seções a seguir para ajudá-lo a solucionar problemas. Se o seu problema não estiver neste artigo, consulte o [blog da equipe do Insights Operacionais](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx).

## Diagnosticar problemas de conexão para Insights Operacionais

Como o Insights Operacionais do Microsoft Azure depende de dados movidos de e para a nuvem, problemas de conexão podem ser debilitantes. Use as informações a seguir para compreender e solucionar seus problemas de conexão.



<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Mensagens de erro</b></td>
		<td><b>Possíveis causas</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>A conectividade com a Internet foi verificada, mas não foi possível estabelecer conexão com o serviço do Insights Operacionais. Tente novamente mais tarde.|</td>
		<td>O serviço do Insights Operacionais está em manutenção. Aguarde até que a manutenção do Insights Operacionais seja feita.<p>Sua rede bloqueou o Insights Operacionais. Entre em contato com o administrador da rede e solicite acesso a Insights Operacionais ou use outro servidor como gateway.</td>
    </tr>
    <tr align="left" valign="top">
		<td>Não foi possível estabelecer conexão com a Internet. Verifique as configurações de proxy.</td>
		<td>Este servidor não está conectado à Internet. Verifique o status de conectividade de Internet e conecte o servidor à Internet.<p>A configuração do proxy não está correta. Consulte <A HREF="operational-insights-proxy-filewall.md"> Configurar proxy e firewall</A> para obter informações sobre como definir ou alterar as configurações de proxy.<p>O servidor proxy requer autenticação. Consulte <A HREF="operational-insights-proxy-filewall.md"> Definir configurações de proxy e firewall</A> para saber mais sobre como configurar o Operations Manager para usar um servidor proxy.</td>
    </tr>
    </tbody>
    </table>

## Solucionar problemas de descoberta do SQL Server

Se você estiver executando o Microsoft SQL Server 2008 R2 e, apesar da implantação do agente do Operations Manager, você não receber alertas para esse servidor, pode haver um problema de descoberta.

Para confirmar se essa é a fonte do seu problema, verifique os dois problemas a seguir:

- No log de eventos do Operations Manager, você verá a ID de Evento 4001. Esse evento indica que há uma classe inválida.

- No SQL Server Configuration Manager, ao exibir Serviços do SQL Server, você recebe a mensagem de erro "Falha na chamada de procedimento remoto. [0x0800706be]"

Se os dois problemas forem verdadeiros, você precisa instalar o SQL Server 2008 R2 Service Pack 2. Para baixar esse service pack, consulte [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) no Centro de Download da Microsoft.

Depois de instalar o service pack, você deve ver dados do Insights Operacionais para o servidor dentro de 24 horas.



<!--HONumber=52--> 