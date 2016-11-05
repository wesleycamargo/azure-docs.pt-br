---
title: Limites e padrões do Agendador
description: Limites e padrões do Agendador
services: scheduler
documentationcenter: .NET
author: krisragh
manager: dwrede
editor: ''

ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: krisragh

---
# Limites e padrões do Agendador
## Aceleradores, limites, padrões e cotas do Agendador
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## O cabeçalho x-ms-request-id
Cada solicitação feita no serviço de Agendador retorna um cabeçalho de resposta chamado **x-ms-request-id**. Esse cabeçalho contém um valor opaco que identifica exclusivamente a solicitação.

Se uma solicitação estiver falhando consistentemente e você tiver verificado que a solicitação foi formulada corretamente, você poderá usar esse valor para relatar o erro à Microsoft. Em seu relatório, inclua o valor de x-ms-request-id, a hora aproximada na qual a solicitação foi feita, o identificador da assinatura, a coleção de trabalhos e/ou o trabalho e o tipo de operação para o qual a solicitação realizou uma tentativa.

## Consulte também
 [O que é o Agendador?](scheduler-intro.md)

 [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)

 [Introdução à utilização do Agendador no Portal do Azure](scheduler-get-started-portal.md)

 [Planos e Cobrança no Agendador do Azure](scheduler-plans-billing.md)

 [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)

 [Alta disponibilidade e confiabilidade do Agendador do Azure](scheduler-high-availability-reliability.md)

 [Autenticação de saída do Agendador do Azure](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0824_2016-->