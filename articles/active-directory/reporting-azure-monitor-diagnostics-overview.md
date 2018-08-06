---
title: Logs de atividades do Azure Active Directory no Azure Monitor (versão prévia) | Microsoft Docs
description: Visão geral dos logs de atividades do Azure Active Directory no Azure Monitor (versão prévia)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0d88aef46761e8c7d8217f04befec88816587c03
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358241"
---
# <a name="azure-ad-activity-logs-in-azure-monitor-preview"></a>Logs de atividades do Azure AD no Azure Monitor (versão prévia)

Agora você pode encaminhar os logs de atividades do Azure AD (Azure Active Directory) para sua própria conta de armazenamento ou Hub de Eventos usando o Azure Monitor. Com a versão prévia pública dos logs do Azure Active Directory no Azure Monitor, você pode:

* Arquivar trilhas de auditoria de uma conta de armazenamento do Azure, o que permite manter os dados por um longo tempo.
* Transmita seus logs de auditoria para um hub de eventos do Azure para análise por meio de ferramentas populares de SIEM (Gerenciamento de eventos e informações de segurança), como Splunk e QRadar.
* Integre as trilhas de auditoria às suas próprias soluções de log personalizadas transmitindo-as para um hub de eventos.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>Relatórios com suporte

Você pode encaminhar logs de atividades de auditoria e de entradas para sua conta de armazenamento do Azure, um hub de eventos ou uma solução personalizada usando esse recurso. 

* **Trilhas de auditoria**: o [relatório de atividade das trilhas de auditoria](active-directory-reporting-activity-audit-logs.md) dá acesso ao histórico de todas as tarefas executadas em seu locatário.
* **Logs de entrada**: com o [relatório de atividades de entrada](active-directory-reporting-activity-sign-ins.md), você pode determinar quem realizou as tarefas indicadas pelo relatório das trilhas de auditoria.

> [!NOTE]
> Não há suporte para logs de atividades de auditoria e entradas relacionados ao B2C no momento.
>

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse recurso, você precisa de:

* Uma assinatura do Azure. Se não tiver uma assinatura do Azure, você poderá [inscrever-se em uma avaliação gratuita](https://azure.microsoft.com/free/).
* [Licença](https://azure.microsoft.com/pricing/details/active-directory/) do Azure AD Gratuito, Básico, Premium 1 ou Premium 2 para acessar os logs do Azure AD no portal do Azure. 

Dependendo do destino de encaminhamento dos dados da trilha de auditoria, você precisa do seguinte:

* Uma conta de armazenamento do Azure para a qual você tem permissões *ListKeys*. Recomendamos que você use uma conta de armazenamento geral e não uma conta do Armazenamento de blobs. Para obter informações sobre preços de armazenamento, confira a [Calculadora de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Um namespace dos Hubs de Eventos do Azure para integração com soluções de terceiros.

> [!NOTE]
> Para acessar os logs de atividades do Azure AD, você precisará ser um *Administrador Global* ou um *Administrador de Segurança* no locatário do Azure AD.
>

## <a name="cost-considerations"></a>Considerações de custo

Se você já tiver uma licença do Azure AD, precisará de uma assinatura do Azure para configurar o hub de eventos e a conta de armazenamento. A assinatura do Azure é fornecida sem custo, mas você precisará pagar para utilizar os recursos do Azure, incluindo a conta de armazenamento usada para arquivamento e o hub de eventos usado para streaming. A quantidade de dados e o custo incorrido podem variar bastante dependendo do tamanho do locatário. 

### <a name="storage-size-for-activity-logs"></a>Tamanho do armazenamento para logs de atividades

Cada evento de trilha de auditoria usa cerca de 2 KB de armazenamento de dados. Para um locatário com 100 mil usuários, o que poderia gerar cerca de 1,5 milhão de eventos por dia, seria necessário cerca de 3 GB de armazenamento de dados por dia. Já que as gravações ocorrem em lotes de aproximadamente cinco minutos, é possível estimar aproximadamente 9 mil operações de gravação por mês. 

A tabela a seguir contém uma estimativa de custo, dependendo do tamanho do locatário, para uma conta de armazenamento de uso geral v2 no Oeste dos EUA com pelo menos um ano de retenção. Para criar uma estimativa mais precisa para o volume de dados que você prevê em seu aplicativo, use a [calculadora de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). 

| Categoria do log | Número de usuários | Eventos por dia | Volume de dados por mês (est.) | Custo por mês (est.) | Custo por ano (est.) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Audit | 100.000 | 1,5&nbsp;milhão | 90 GB | US$ 1,93 | US$ 23,12 |
| Audit | 1.000 | 15.000 | 900 MB | US$ 0,02 | US$ 0,24 |
| Entradas | 1.000 | 34.800 | 4 GB | US$ 0,13 | US$ 1,56 |
| Entradas | 100.000 | 15&nbsp;milhões | 1.7 TB | US$ 35,41 | US$ 424,92 | 


### <a name="event-hub-messages-for-activity-logs"></a>Mensagens do hub de eventos para logs de atividade

Eventos são colocados em lotes com intervalos de cinco minutos, aproximadamente, e enviados como uma única mensagem que contém todos os eventos nesse período de tempo. Uma mensagem no hub de eventos tem um tamanho máximo de 256 KB, e se o tamanho total de todas as mensagens no período exceder esse volume, várias mensagens serão enviadas. 

Por exemplo, cerca de 18 eventos por segundo ocorrem normalmente para um locatário grande de mais de 100 mil usuários, uma taxa que equivale a 5.400 eventos a cada cinco minutos. Como os logs de auditoria têm cerca de 2 KB por evento, isso equivale a 10.8 MB de dados. Portanto, 43 mensagens são enviadas ao hub de eventos naquele intervalo de cinco minutos. 

A tabela a seguir contém custos estimados por mês para um hub de eventos básico no Oeste dos EUA, dependendo do volume de dados do evento. Para calcular uma estimativa precisa para o volume de dados previsto para seu aplicativo, use a [calculadora de preços do Hub de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

| Categoria do log | Número de usuários | Eventos por segundo | Eventos por intervalo de cinco minutos | Volume por intervalo | Mensagens por intervalo | Mensagens por mês | Custo por mês (est.) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Audit | 100.000 | 18 | 5.400 | 10.8 MB | 43 | 371.520 | US$ 10,83 |
| Audit | 1.000 | 0,1 | 52 | 104 KB | 1 | 8.640 | US$ 10,80 |
| Entradas | 1.000 | 178 | 53.400 | 106.8&nbsp;MB | 418 | 3.611.520 | US$ 11,06 |  


## <a name="frequently-asked-questions"></a>Perguntas frequentes

Esta seção responde a perguntas frequentes e discute problemas conhecidos com os logs do Azure AD no Azure Monitor.

**P: por onde devo começar?** 

**R**: este artigo aborda o que é necessário para implantar esse recurso. Depois de atender aos pré-requisitos, vá para os tutoriais que podem ajudar a configurar e encaminhar os logs a um hub de eventos.

---

**P: quais logs estão incluídos?**

**R:** tanto os logs de atividades quanto os de auditoria estão disponíveis para encaminhamento por meio desse recurso, mas os eventos de auditoria relativos a B2C não estão incluídos no momento. Para descobrir quais tipos de logs e quais logs baseados em recursos têm suporte no momento, leia o [Esquema de trilha de auditoria](reporting-azure-monitor-diagnostics-audit-log-schema.md) e o [Esquema de log de entrada](reporting-azure-monitor-diagnostics-sign-in-log-schema.md). 

---

**P: quanto tempo demora para os logs correspondentes aparecerem nos Hubs de Eventos após a ação?**

**R**: os logs devem aparecer em seu hub de eventos de dois a cinco minutos depois que a ação é executada. Para obter mais informações sobre Hubs de Eventos, confira [O que são os Hubs de Eventos do Azure?](../event-hubs/event-hubs-about.md)

---

**P: quanto tempo demora para os logs correspondentes aparecerem nas contas de armazenamento após a ação?**

**R**: para contas de armazenamento do Azure, a latência fica entre 5 e 15 minutos depois que a ação é executada.

---

**P: quanto custará para armazenar meus dados?**

**R**: os custos de armazenamento dependem do tamanho dos logs e do período de retenção que você escolher. Para obter uma lista dos custos estimados para locatários, o que depende do volume de logs gerados, vá para a seção [Tamanho de armazenamento para logs de atividades](https://review.docs.microsoft.com/en-us/azure/active-directory/reporting-azure-monitor-diagnostics-overview?branch=pr-en-us-47660#storage-size-for-activity-logs).

---

**P: quanto custará para transmitir meus dados por stream para um hub de eventos?**

**R:** o custo de streaming depende do número de mensagens recebidas por minuto. Este artigo discute como os custos são calculados e lista as estimativas de custo, que se baseiam no número de mensagens. 

---

**P: quais ferramentas de SIEM têm suporte atualmente?** 

**R:** atualmente, o Azure Monitor tem suporte do Splunk, do QRadar e do Sumo Logic. No entanto, o Splunk é a única ferramenta de SIEM com suporte aos logs do Azure AD. Para obter mais informações sobre como funcionam os conectores, consulte [Transmitir dados de monitoramento do Azure por stream a um hub de eventos para consumo por uma ferramenta externa](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

---

**P: posso acessar os dados de um hub de eventos sem usar uma ferramenta de SIEM externa?** 

**R**: Sim. Para acessar os logs do aplicativo personalizado, você pode usar a [API dos Hubs de Eventos](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md). 

---


## <a name="next-steps"></a>Próximas etapas

* [Arquivar os logs de atividades em uma conta de armazenamento](reporting-azure-monitor-diagnostics-azure-storage-account.md)
* [Encaminhar logs de atividades para um hub de eventos](reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [Saiba mais sobre os Logs de Diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
