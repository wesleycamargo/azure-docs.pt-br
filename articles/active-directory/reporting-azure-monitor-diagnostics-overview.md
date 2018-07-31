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
ms.openlocfilehash: 9b594360bc760fa9eec8ab9900c3aadcb10e9db6
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240094"
---
# <a name="azure-active-directory-activity-logs-in-azure-monitor-preview"></a>Logs de atividades do Azure Active Directory no Azure Monitor (versão prévia)

Agora você pode usar os logs de atividades do Azure AD usando o Azure Monitor em sua própria conta de armazenamento ou Hub de Eventos. Com a versão prévia pública dos logs do Azure Active Directory no Azure Monitor, você pode:

* Arquivar trilhas de auditoria de uma conta de armazenamento do Azure, podendo manter os dados por um longo tempo
* Transmitir trilhas de auditoria por streaming para um hub de eventos do Azure para análise usando ferramentas de SIEM populares, como o Splunk e o QRadar
* Integrar as trilhas de auditoria às suas próprias soluções de log personalizado, transmitindo-os para um hub de eventos

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>Relatórios com suporte

Você pode encaminhar logs de atividades de auditoria e de entradas para sua conta de armazenamento do Azure, um Hub de Eventos ou uma solução personalizada usando esse recurso. 

* **Trilhas de auditoria**: o [relatório de atividade das trilhas de auditoria](active-directory-reporting-activity-audit-logs.md) fornece acesso ao histórico de todas as tarefas executadas em seu locatário.
* **Entradas**: com o [relatório de atividades de entrada](active-directory-reporting-activity-sign-ins.md), você pode determinar quem realizou as tarefas indicadas pelo relatório das trilhas de auditoria.

> [!NOTE]
> Não há suporte para logs de atividades de auditoria e entradas relacionados ao B2C no momento.
>

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse recurso, você precisa de:

* Uma assinatura do Azure. Se não tiver uma assinatura do Azure, você poderá [assinar uma versão de avaliação gratuita](https://azure.microsoft.com/free/).
* [Licença](https://azure.microsoft.com/pricing/details/active-directory/) do Azure AD Gratuito, Básico, Premium 1 ou Premium 2para acessar os logs do Azure AD no portal do Azure. 

Dependendo do destino de roteamento dos dados da trilha de auditoria, você precisa de:

* Uma conta de armazenamento do Azure para a qual você tem permissões *ListKeys*. Recomendamos que você use uma conta de armazenamento geral e não uma conta de armazenamento de blobs. Para obter informações sobre preços de armazenamento, confira a [Calculadora de preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Namespace dos hubs de eventos do Azure, para integrar soluções de terceiros.

> [!NOTE]
> Você precisa ser um *Administrador Global* ou um *Administrador de Segurança* no locatário do Azure AD para acessar os logs de atividades do Azure AD.
>

## <a name="cost-considerations"></a>Considerações de custo

Se você já tiver uma licença do Azure AD, precisará de uma assinatura do Azure para configurar o hub de eventos e a conta de armazenamento. A assinatura do Azure é fornecida sem custo, mas você precisará pagar para utilizar os recursos do Azure, incluindo a conta de armazenamento usada para arquivamento e o hub de eventos usado para streaming. A quantidade de dados e o custo incorrido vai variar bastante dependendo do tamanho do locatário. 

### <a name="storage-size-for-activity-logs"></a>Tamanho do armazenamento para logs de atividades

Cada evento de trilha de auditoria consome cerca de 2 KB de armazenamento de dados. Portanto, para um locatário com 100.000 usuários, o que poderia gerar cerca de 1,5 milhão de eventos por dia, seria necessário cerca de 3 GB de armazenamento de dados por dia. Já que as gravações ocorrem em lotes de aproximadamente cinco minutos, é possível estimar aproximadamente 9 mil operações de gravação por mês. A tabela a seguir contém uma estimativa aproximada de custo, dependendo do tamanho do locatário, para uma conta de armazenamento de uso geral v2 no Oeste dos EUA com pelo menos um ano de retenção. Use a [calculadora de preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) para criar uma estimativa mais precisa para o volume de dados que você prevê em seu aplicativo. Cada evento de trilha de auditoria consome cerca de 2 KB de armazenamento de dados. Portanto, para um locatário com 100.000 usuários, o que poderia gerar cerca de 1,5 milhão de eventos por dia, seria necessário cerca de 3 GB de armazenamento de dados por dia. Já que as gravações ocorrem em lotes de aproximadamente cinco minutos, é possível estimar aproximadamente 9 mil operações de gravação por mês. A tabela a seguir contém uma estimativa aproximada de custo, dependendo do tamanho do locatário, para uma conta de armazenamento de uso geral v2 no Oeste dos EUA com pelo menos um ano de retenção. Use a [calculadora de preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) para criar uma estimativa mais precisa para o volume de dados que você prevê em seu aplicativo. 

| Categoria do log | Número de usuários | Número de eventos/dia | Volume aproximado dos dados por mês | Custo aproximado por mês | Custo aproximado por ano |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Audit | 100.000 | 1,5 milhão | 90 GB | US$ 1,93 | US$ 23,12 |
| Audit | 1000 | 15000 | 900 MB | US$ 0,02 | US$ 0,24 |
| Entradas | 1000 | 34800 | 4 GB | US$ 0,13 | US$ 1,56 |
| Entradas | 100.000 | 15 milhões | 1.7 TB | US$ 35,41 | US$ 424,92 | 


### <a name="event-hub-messages-for-activity-logs"></a>Mensagens do hub de eventos para logs de atividade

Eventos são colocados em lotes com intervalos de cinco minutos, aproximadamente, e enviados como uma única mensagem contendo todos os eventos nesse período de tempo. Uma mensagem no hub de eventos tem um tamanho máximo de 256 KB, e se o tamanho de todas as mensagens no período exceder esse volume, várias mensagens serão enviadas. 

Por exemplo, normalmente há cerca de 18 eventos por segundo em um locatário grande com mais de 100 mil usuários, o que corresponde a 5400 eventos a cada 5 minutos. Como as trilhas de auditoria têm cerca de 2 KB por evento, isso equivale a 10.8 MB de dados e, portanto, 43 mensagens serão enviadas para o hub de eventos naquele intervalo de 5 minutos. A tabela a seguir contém um custo aproximado de um hub de eventos básico no Oeste dos EUA, dependendo do volume de dados do evento. Use a [Calculadora de preços do Hub de Eventos](https://azure.microsoft.com/pricing/details/event-hubs/) para calcular uma estimativa precisa para o volume de dados previsto para seu aplicativo.

| Categoria do log | Número de usuários | Número de eventos/segundo | Número de eventos por intervalo de 5 minutos | Volume por intervalo | Número de mensagens por intervalo | Número de mensagens por mês | Custo aproximado por mês |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Audit | 100.000 | 18 | 5400 | 10.8 MB | 43 | 371.520 | US$ 10,83 |
| Audit | 1000 | 0,1 | 52 | 104 KB | 1 | 8640 | US$ 10,8 |
| Entradas | 1000 | 178 | 53400 | 106.8 MB | 418 | 3.611.520 | US$ 11,06 |  


## <a name="frequently-asked-questions"></a>Perguntas frequentes

Esta seção contém perguntas frequentes e problemas conhecidos com os logs do Azure Active Directory no Azure Monitor.

**P: por onde devo começar?** 

**R:** comece com a [Visão Geral](reporting-azure-monitor-diagnostics-overview.md) para ter uma ideia sobre o que é necessário para implantar esse recurso. Quando você estiver familiarizado com os pré-requisitos, confira os tutoriais para configurar e encaminhar os logs aos Hubs de Eventos.

---

**P: quais logs estão incluídos?**

**R:** tanto os logs de entrada quanto os de auditoria estão disponíveis para roteamento por meio desse recurso, mas os eventos de auditoria relativos a B2C não estão incluídos no momento. Leia o [esquema de trilha de auditoria](reporting-azure-monitor-diagnostics-audit-log-schema.md) e o [esquema de log de entrada ](reporting-azure-monitor-diagnostics-sign-in-log-schema.md) para descobrir quais tipos de logs e quais logs baseados em recursos têm suporte no momento. 

---

**P: quanto tempo demora para os logs correspondentes aparecerem nos Hubs de Eventos após a ação?**

**R:** os logs devem aparecer nos Hubs de Eventos entre dois e cinco minutos após a ação. Para obter mais informações sobre hubs de eventos, confira [O que são hubs de eventos?](/azure/event-hubs/event-hubs-what-is-event-hubs.md)

---

**P: quanto tempo demora para os logs correspondentes aparecerem nas contas de armazenamento após a ação?**

**R:** em caso de contas de armazenamento do Azure, a latência é entre 5 e 15 minutos após a ação.

---

**P: quanto custará para armazenar meus dados?**

**R:** o custo de armazenamento depende do tamanho dos logs e do período de retenção escolhido. Para obter uma estimativa aproximada dos custos para locatários, dependendo do volume de logs gerados, confira a [Visão Geral](reporting-azure-monitor-diagnostics-overview.md).

---

**P: quanto custará para transmitir meus dados por stream para os Hubs de Eventos?**

**R:** o custo de streaming depende do número de mensagens recebidas por minuto. Leia a [Visão Geral](reporting-azure-monitor-diagnostics-overview.md) para saber mais sobre como o custo é calculado e para encontrar uma estimativa aproximada com base no número de mensagens. 

---

**P: quais ferramentas de SIEM têm suporte atualmente?** 

**R:** atualmente, o Azure Monitor tem suporte do Splunk, do QRadar e do Sumologic. No entanto, o Splunk é a única ferramentas de SIEM que dá suporte a logs do Azure Active Directory. Para obter mais informações sobre como funcionam os conectores, consulte [Transmitir dados de monitoramento do Azure por stream a um hub de eventos para consumo por uma ferramenta externa](/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs).

---

**P: posso acessar os dados do Hub de Eventos sem usar uma ferramenta de SIEM externa?** 

**R:** sim, você pode usar a [API do Hub de Eventos](/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph) para acessar os logs do aplicativo personalizado. 

---


## <a name="next-steps"></a>Próximas etapas

* [Arquivar os logs de atividade em uma conta de armazenamento do Azure](reporting-azure-monitor-diagnostics-azure-storage-account.md)
* [Direcionar logs de atividades para um Hub de Eventos](reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [Saiba mais sobre os Logs de Diagnóstico do Azure](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)