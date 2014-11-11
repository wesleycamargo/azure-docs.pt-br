<properties linkid="biztalk-troubleshoot-using-ops-logs" urlDisplayName="BizTalk Services: Troubleshoot using operation logs" pageTitle="BizTalk Services: Troubleshoot using ops logs | Azure" metaKeywords="" description="BizTalk Services: Troubleshoot using ops logs" metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Troubleshoot using ops logs" authors="mandia"  solutions="" writer="nitinme" manager="dwrede" editor="cgronlun"  />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# Serviços do BizTalk: Solução de problemas usando os logs de operação.

Os Logs de Operação são um recurso dos Serviços de Gerenciamento disponível no Portal de Gerenciamento do Azure que permite exibir os logs históricos de operações executadas nos serviços do Azure, incluindo o Serviço BizTalk. Isso permite exibir dados históricos relacionados às operações de gerenciamento em sua assinatura do Serviço BizTalk por um período de até 180 dias.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
<p>Esse recurso captura apenas logs de opera&ccedil;&otilde;es de gerenciamento nos Servi&ccedil;os do BizTalk, como quando o servi&ccedil;o foi iniciado, o backup foi feito etc. Essas opera&ccedil;&otilde;es s&atilde;o acompanhadas independentemente de serem executadas no Portal de Gerenciamento do Azure ou usando as <a href="http://msdn.microsoft.com/pt-BR/library/windowsazure/dn232347.aspx">APIs REST do Servi&ccedil;o BizTalk</a>. Para obter uma lista completa das opera&ccedil;&otilde;es que s&atilde;o acompanhadas usando os Servi&ccedil;os de Gerenciamento, consulte <a href="#bizops">Opera&ccedil;&otilde;es acompanhadas usando os Servi&ccedil;os de Gerenciamento do Azure</a>.</p>
<p>Isso n&atilde;o captura os logs de atividades relacionadas ao tempo de execu&ccedil;&atilde;o do Servi&ccedil;o BizTalk (como mensagens processadas pelas pontes etc.) Para exibir esses logs, voc&ecirc; deve usar a exibi&ccedil;&atilde;o Acompanhamento do portal dos Servi&ccedil;os BizTalk. Para obter mais informa&ccedil;&otilde;es, consulte <a HREF="http://msdn.microsoft.com/library/windowsazure/hh949805.aspx">Acompanhando mensagens</a>.</p>
</div>

## <a name="viewlogs"></a>Exibir os logs de operações dos Serviços BizTalk

1.  No Portal de Gerenciamento do Azure, clique em Serviços de Gerenciamento e, em seguida, clique na guia Logs de Operação.
2.  Você pode filtrar os logs com base em vários parâmetros, como assinatura, intervalo de dados, tipo de serviço (por exemplo, Serviços do BizTalk), nome do serviço ou status (da operação, por exemplo, Obteve êxito, Com falha)
3.  Clique na marca de seleção para exibir a lista filtrada. A ilustração a seguir mostra as atividades relacionadas ao testbiztalkservice.
    ![Ver logs de operação][Ver logs de operação]
4.  Para ler mais sobre uma operação específica, selecione a linha e clique em **Detalhes** na parte inferior da página.

## <a name="bizops"></a>Operações acompanhadas usando os Serviços de Gerenciamento do Azure

A tabela a seguir lista as operações que são acompanhadas usando os Serviços de Gerenciamento do Azure.

<table border="1" cellpadding="5">
<tr>
<td>CreateBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para criar um novo Servi&ccedil;o BizTalk</td> 
</tr> 
<tr>
<td>DeleteBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para excluir um novo Servi&ccedil;o BizTalk</td>  
</tr> 
<tr>
<td>RestartBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para reiniciar um Servi&ccedil;o BizTalk</td> 
</tr>
<tr>
<td>StartBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para iniciar um Servi&ccedil;o BizTalk</td> 
</tr>
<tr>
<td>StopBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para parar um Servi&ccedil;o BizTalk</td> 
</tr>
<tr>
<td>DisableBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para desabilitar um Servi&ccedil;o BizTalk</td> 
</tr>
<tr>
<td>EnableBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para habilitar um Servi&ccedil;o BizTalk</td> 
</tr>
<tr>
<td>BackupBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para fazer backup de um Servi&ccedil;o BizTalk</td> 
</tr>
<tr>
<td>RestoreBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para restaurar um Servi&ccedil;o BizTalk de um backup especificado</td> 
</tr>
<tr>
<td>SuspendBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para suspender um Servi&ccedil;o BizTalk</td> 
</tr>
<tr>
<td>ResumeBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para continuar um Servi&ccedil;o BizTalk</td> 
</tr>
<tr>
<td>ScaleBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para ampliar ou reduzir um Servi&ccedil;o BizTalk</td> 
</tr>
<tr>
<td>ConfigUpdateBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para atualizar a configura&ccedil;&atilde;o de um Servi&ccedil;o BizTalk</td> 
</tr>
<tr>
<td>ServiceUpdateBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para fazer upgrade ou downgrade um Servi&ccedil;o BizTalk Service para outra vers&atilde;o</td> 
</tr>
<tr>
<td>PurgeBackupBizTalkService</td> 
<td align="left">Opera&ccedil;&atilde;o para limpar backups do Servi&ccedil;o BizTalk fora do per&iacute;odo de reten&ccedil;&atilde;o</td> 
</tr>
</table>

## Consulte também

-   [Fazer o backup do Serviço BizTalk][Fazer o backup do Serviço BizTalk]
-   [Restaurar o Serviço BizTalk do backup][Restaurar o Serviço BizTalk do backup]
-   [Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium][Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium]
-   [Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure][Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure]
-   [Serviços do BizTalk: Gráfico de status do provisionamento][Serviços do BizTalk: Gráfico de status do provisionamento]
-   [Serviços do BizTalk: guias Painel, Monitoramento e Escala][Serviços do BizTalk: guias Painel, Monitoramento e Escala]
-   [Serviços do BizTalk: limitação][Serviços do BizTalk: limitação]
-   [Serviços do BizTalk: nome e chave do emissor][Serviços do BizTalk: nome e chave do emissor]
-   [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)][Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)]

  [Ver logs de operação]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
  [Fazer o backup do Serviço BizTalk]: http://go.microsoft.com/fwlink/p/?LinkID=325584
  [Restaurar o Serviço BizTalk do backup]: http://go.microsoft.com/fwlink/p/?LinkID=325582
  [Serviços do BizTalk: tabela de edições Developer, Basic, Standard e Premium]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Serviços do BizTalk: provisionamento usando o Portal de Gerenciamento do Azure]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Serviços do BizTalk: Gráfico de status do provisionamento]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Serviços do BizTalk: guias Painel, Monitoramento e Escala]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [Serviços do BizTalk: limitação]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [Serviços do BizTalk: nome e chave do emissor]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Como começar a usar o SDK dos Serviços BizTalk do Azure (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/p/?LinkID=302335
