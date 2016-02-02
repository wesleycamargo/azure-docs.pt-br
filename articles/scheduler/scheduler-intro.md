<properties
 pageTitle="O que é o Agendador do Azure? | Microsoft Azure"
 description="O Agendador do Azure permite que você descreva declarativamente ações a serem executadas na nuvem. Em seguida, ele agenda e executa essas ações automaticamente."
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="12/04/2015"
 ms.author="krisragh"/>

# O que é o Agendador do Azure?

O Agendador do Azure permite que você descreva declarativamente ações a serem executadas na nuvem. Em seguida, ele agenda e executa essas ações automaticamente. O Agendador do Azure faz isso usando [o portal do Azure](scheduler-get-started-portal.md), código, a [API REST](https://msdn.microsoft.com/library/dn528946) ou o Azure PowerShell.

O Agendador cria, mantém e invoca o trabalho agendado. O Agendador não hospeda qualquer carga de trabalho ou executar qualquer código. Ele apenas _invoca_ código hospedado em outro lugar—no Azure, no local ou em outro provedor. Ele invoca via HTTP, HTTPS ou uma fila de armazenamento.

O Agendador agenda [trabalhos](scheduler-concepts-terms.md), mantém um histórico do trabalho de resultados de execução que alguém pode revisar, e agenda de forma determinista e confiável agenda cargas de trabalho a serem executadas. Trabalhos Web do Azure (parte do recurso de aplicativos Web no serviço de aplicativo do Azure) e outro recursos de agendamento do Azure usam o Agendador em segundo plano. A [API REST do Agendador](https://msdn.microsoft.com/library/dn528946) ajuda a gerenciar a comunicação para essas ações. Dessa forma, o Agendador oferece suporte para [agendas complexas e recorrência avançadas](scheduler-advanced-complexity.md) facilmente.

Há vários cenários em que o Agendador pode ser usado. Por exemplo:

+ _Ações do aplicativo recorrente_: coleta periódica de dados do Twitter no feed.
+ _Manutenção diária:_ redução diária de logs, realização de backups e outras tarefas de manutenção. Por exemplo, um administrador pode escolher realizar o backup de seu banco de dados à 1h da manhã, todos os dias, pelos próximos nove meses.

O Agendador permite criar, atualizar, excluir, exibir e gerenciar trabalhos e [coleções de trabalhos](scheduler-concepts-terms.md) programaticamente, usando scripts e no portal.

## Consulte também

 [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)

 [Introdução à utilização do Agendador no Portal do Azure](scheduler-get-started-portal.md)

 [Planos e Cobrança no Agendador do Azure](scheduler-plans-billing.md)

 [Como criar agendas complexas e recorrência avançada com o Agendador do Azure](scheduler-advanced-complexity.md)

 [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)

 [Alta disponibilidade e confiabilidade do Agendador do Azure](scheduler-high-availability-reliability.md)

 [Limites, padrões e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Agendador do Azure](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0128_2016-->