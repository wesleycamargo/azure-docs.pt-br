<properties 
	pageTitle="Solucionar problemas dos Serviços BizTalk usando logs de operação | Microsoft Azure" 
	description="Solucionar problemas dos Serviços BizTalk usando logs de operação. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/07/2015" 
	ms.author="mandia"/>


# Serviços BizTalk: solução de problemas usando logs de operação

## O que são os Logs de operação
Logs de operação é um recurso dos Serviços de Gerenciamento disponível no Portal de Gerenciamento do Azure que permite exibir os logs históricos de operações executadas nos serviços do Azure, incluindo os Serviços BizTalk. Isso permite exibir dados históricos relacionados às operações de gerenciamento em sua assinatura do Serviço BizTalk de até 180 dias.

> [AZURE.NOTE]Esse recurso captura apenas os logs de operações de gerenciamento nos Serviços BizTalk, por exemplo, quando o serviço foi iniciado, sofreu backup etc. Essas operações são acompanhadas independentemente se são executadas no Portal de Gerenciamento do Azure ou usando as [APIs REST do Serviço BizTalk](http://msdn.microsoft.com/library/azure/dn232347.aspx). Para obter uma lista completa das operações que são acompanhadas usando os Serviços de Gerenciamento, consulte [Operações acompanhadas usando os Serviços de Gerenciamento do Azure](#bizops).<br/><br/> Isso não captura os logs de atividades relacionadas ao tempo de execução do Serviço BizTalk (como mensagens processadas pelas pontes etc.) Para exibir esses logs, use a exibição Acompanhamento do portal de Serviços BizTalk. Para obter mais informações, consulte [Acompanhando mensagens](http://msdn.microsoft.com/library/azure/hh949805.aspx).

## Exibir os logs de operações dos Serviços BizTalk
1. No Portal de Gerenciamento do Azure, clique em **Serviços de Gerenciamento ** e, em seguida, clique na guia **Logs de Operação**.
2. Você pode filtrar os logs com base em diferentes parâmetros, como assinatura, intervalo de dados, tipo de serviço (por exemplo, Serviços do BizTalk), nome do serviço ou status da operação (Obteve êxito, Com falha)
3. Clique na marca de seleção para exibir a lista filtrada. A ilustração a seguir mostra as atividades relacionadas ao testbiztalkservice: ![Exibir logs de operação][ViewLogs] 
4. Para ver mais sobre uma operação específica, selecione a linha e clique em **Detalhes** na barra de tarefas na parte inferior da página.


## <a name="bizops"></a>Operações acompanhadas usando os Serviços de Gerenciamento do Azure
A tabela a seguir lista as operações que são acompanhadas usando os Serviços de Gerenciamento do Azure:

Nome de operação | Tarefa
--- | ---
CreateBizTalkService | Operação para criar um novo Serviço BizTalk
DeleteBizTalkService | Operação para excluir um novo Serviço BizTalk
RestartBizTalkService | Operação para reiniciar um Serviço BizTalk
StartBizTalkService | Operação para iniciar um Serviço BizTalk
StopBizTalkService | Operação para parar um Serviço BizTalk
DisableBizTalkService | Operação para desabilitar um Serviço BizTalk
EnableBizTalkService | Operação para habilitar um Serviço BizTalk
BackupBizTalkService | Operação para fazer backup de um Serviço BizTalk
RestoreBizTalkService | Operação para restaurar um Serviço BizTalk de um backup especificado
SuspendBizTalkService | Operação para suspender um Serviço BizTalk
ResumeBizTalkService | Operação para continuar um Serviço BizTalk
ScaleBizTalkService | Operação para ampliar ou reduzir um Serviço BizTalk
ConfigUpdateBizTalkService | Operação para atualizar a configuração de um Serviço BizTalk
ServiceUpdateBizTalkService | Operação para fazer upgrade ou downgrade um Serviço BizTalk Service para outra versão
PurgeBackupBizTalkService | Operação para limpar backups do Serviço BizTalk fora do período de retenção


## Consulte também
- [Fazer o backup do Serviço BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restaurar o Serviço BizTalk do backup](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Serviços BizTalk: gráfico das edições Developer, Basic, Standard e Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Serviços BizTalk: provisionamento usando o Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Serviços BizTalk: gráfico do status do provisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Serviços BizTalk: guias Painel, Monitor e Escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Serviços BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [Serviços BizTalk: nome e chave do emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Como começar a usar o SDK dos Serviços BizTalk do Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
 

<!---HONumber=August15_HO7-->