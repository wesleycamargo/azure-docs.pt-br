---
title: Solucionar problemas dos Serviços BizTalk usando logs de operação | Microsoft Docs
description: Solucionar problemas dos Serviços BizTalk usando logs de operação. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 1081a9c6-58cc-4a76-8ac8-11e5e7a6ea27
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 35c30ec97d4c033a687204b926d2f686c52be069
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916012"
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>Serviços BizTalk: Solução de problemas usando os logs de operação.

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="what-are-the-operation-logs"></a>O que são os Logs de operação
Logs de Operação é um recurso dos Serviços de Gerenciamento que permite exibir os logs históricos de operações executados nos serviços do Azure, incluindo os Serviços BizTalk. Isso permite exibir dados históricos relacionados às operações de gerenciamento em sua assinatura do Serviço BizTalk de até 180 dias.

> [!NOTE]
> Esse recurso captura apenas os logs de operações de gerenciamento nos Serviços BizTalk, por exemplo, quando o serviço foi iniciado, sofreu backup etc. Essas operações são controladas usando os [APIs de REST do Serviço BizTalk](/previous-versions/azure/reference/dn232347(v=azure.100)). Para obter uma lista completa das operações que são acompanhadas usando os Serviços de Gerenciamento, consulte [Operações Acompanhadas Usando os Serviços de Gerenciamento do Azure](#bizops).<br/><br/>
> Isso não captura os logs de atividades relacionadas ao tempo de execução do Serviço BizTalk (como mensagens processadas pelas pontes etc.) Para exibir esses logs, use a exibição Acompanhamento do portal de Serviços BizTalk. Para obter mais informações, consulte [Acompanhando as Mensagens](/previous-versions/azure/hh949805(v=azure.100)).
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>Exibir os logs de operações dos Serviços BizTalk
1. No portal, selecione **Serviços de Gerenciamento**, em seguida, selecione a guia **Logs de Operação**.
2. Você pode filtrar os logs com base em diferentes parâmetros, como assinatura, intervalo de datas, tipo de serviço (por exemplo, Serviços BizTalk), nome do serviço ou status da operação (Êxito, Falha).
3. Clique na marca de seleção para exibir a lista filtrada. A ilustração a seguir mostra as atividades relacionadas ao testbiztalkservice:  ![Exibir logs de operação][ViewLogs] 
4. Para ver mais sobre uma operação específica, selecione a linha e clique em **Detalhes** na barra de tarefas na parte inferior da página.

## <a name="bizops"></a>Operações acompanhadas usando os Serviços de Gerenciamento do Azure
A tabela a seguir lista as operações que são acompanhadas usando os Serviços de Gerenciamento do Azure:

| Nome de operação | Tarefa |
| --- | --- |
| CreateBizTalkService |Operação para criar um novo Serviço BizTalk |
| DeleteBizTalkService |Operação para excluir um novo Serviço BizTalk |
| RestartBizTalkService |Operação para reiniciar um Serviço BizTalk |
| StartBizTalkService |Operação para iniciar um Serviço BizTalk |
| StopBizTalkService |Operação para parar um Serviço BizTalk |
| DisableBizTalkService |Operação para desabilitar um Serviço BizTalk |
| EnableBizTalkService |Operação para habilitar um Serviço BizTalk |
| BackupBizTalkService |Operação para fazer backup de um Serviço BizTalk |
| RestoreBizTalkService |Operação para restaurar um Serviço BizTalk de um backup especificado |
| SuspendBizTalkService |Operação para suspender um Serviço BizTalk |
| ResumeBizTalkService |Operação para continuar um Serviço BizTalk |
| ScaleBizTalkService |Operação para ampliar ou reduzir um Serviço BizTalk |
| ConfigUpdateBizTalkService |Operação para atualizar a configuração de um Serviço BizTalk |
| ServiceUpdateBizTalkService |Operação para fazer upgrade ou downgrade um Serviço BizTalk Service para outra versão |
| PurgeBackupBizTalkService |Operação para limpar backups do Serviço BizTalk fora do período de retenção |

## <a name="see-also"></a>Veja também
* [Backup do serviço BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Restaurar serviço BizTalk do Backup](https://go.microsoft.com/fwlink/p/?LinkID=325582)
* [Serviços BizTalk: Developer, Basic, Standard e Premium gráfico de edições](https://go.microsoft.com/fwlink/p/?LinkID=302279)
* [Serviços BizTalk: Provisionamento](https://go.microsoft.com/fwlink/p/?LinkID=302280)
* [Serviços BizTalk: Gráfico de Status de provisionamento](https://go.microsoft.com/fwlink/p/?LinkID=329870)
* [Serviços BizTalk: Guias painel, Monitor e escala](https://go.microsoft.com/fwlink/p/?LinkID=302281)
* [Serviços BizTalk: Limitação](https://go.microsoft.com/fwlink/p/?LinkID=302282)
* [Serviços BizTalk: Nome do emissor e chave do emissor](https://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Como faço para começar a usar o SDK dos Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

