<properties
 pageTitle="O que é o Agendador?"
 description=""
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
 ms.date="05/12/2015"
 ms.author="krisragh"/>

# O que é o Agendador?

O Agendador do Azure permite que você descreva declarativamente ações a serem executadas na nuvem. Em seguida, ele agenda e executa essas ações automaticamente. O Agendador do Azure faz isso usando o [portal do Azure](scheduler-get-started-portal.md), código, a [API REST](https://msdn.microsoft.com/library/dn528946) ou o PowerShell.

O Agendador do Azure mantém, gerencia, agenda e invoca o trabalho agendado. O Agendador do Azure não hospeda qualquer carga de trabalho ou executar qualquer código. Ele apenas _invoca_ código hospedado em outro lugar; esse código pode estar hospedado no Azure, no local ou em outro provedor. Ele invoca via HTTP, HTTPS ou uma fila de armazenamento.

O Agendador do Azure agenda trabalhos, mantém um histórico do trabalho de resultados de execução que alguém pode interrogar e agendar de forma determinista e confiável agenda cargas de trabalho a serem executadas. Os Serviços Móveis do Azure, os Scripts Agendados, os Trabalhos Web de Aplicativos Web do Azure e outros recursos de agendamento do Azure usam o Agendador do Azure nos bastidores. A [API REST do Agendador](https://msdn.microsoft.com/library/dn528946) ajuda a gerenciar a comunicação para essas ações. Dessa forma, o Agendador oferece suporte a [recorrência fácil de agendas complexas e avançadas](scheduler-advanced-complexity.md).

Há vários cenários em que o Agendador do Azure pode ser usado. Por exemplo:

+ _Ações do aplicativo recorrente_: coleta periódica de dados do Twitter e reunião dos dados no feed.
+ _Manutenção diária_: limpeza diária de logs, execução de backups e outras tarefas de manutenção. Por exemplo, um administrador poderá optar por fazer o backup de seu banco de dados à 01:00 todos os dias nos próximos 9 meses.

O Agendador permite criar, atualizar, excluir, exibir e gerenciar [“coleções de trabalhos” e “trabalhos”](scheduler-concepts-terms.md) programaticamente, usando scripts e no portal.

## Consulte também

 [Conceitos, terminologia e hierarquia de entidades do Agendador](scheduler-concepts-terms.md)

 [Introdução à utilização do Agendador no Portal de Gerenciamento](scheduler-get-started-portal.md)

 [Planos e cobrança no Agendador do Azure](scheduler-plans-billing.md)

 [Como criar agendas complexas e recorrência avançada com o Agendador do Azure](scheduler-advanced-complexity.md)

 [Referência da API REST do Agendador](https://msdn.microsoft.com/library/dn528946)

 [Referência de cmdlets do PowerShell do Agendador](scheduler-powershell-reference.md)

 [Alta disponibilidade e confiabilidade do Agendador](scheduler-high-availability-reliability.md)

 [Limites, padrões e códigos de erro do Agendador](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Agendador](scheduler-outbound-authentication.md)
 

<!---HONumber=58_postMigration-->