---
title: Integre os registros de recursos do Azure nos seus sistemas SIEM | Microsoft Docs
description: "Saiba mais sobre a integração de log do Azure, seus principais recursos e como ela funciona."
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 434961f7d25195eee3310461a3a97e03ffdd8917
ms.lasthandoff: 03/15/2017


---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Introdução à integração de log do Microsoft Azure
Saiba mais sobre a integração de log do Azure, seus principais recursos e como ela funciona.

## <a name="overview"></a>Visão geral
PaaS (plataforma como serviço) e IaaS (infraestrutura como serviço) hospedadas no Azure geram uma grande quantidade de dados em logs de segurança. Esses logs contêm informações importantes que podem fornecer inteligência e entendimentos significativos sobre as violações de política, as ameaças internas e externas, os problemas de conformidade normativa e as anomalias na rede, no host e na atividade do usuário.

A integração do log do Azure é uma solução gratuita que permite integrar logs brutos dos recursos do Azure a sistemas SIEM (Gerenciamento de Informações e Eventos de Segurança) locais. A integração do log do Azure coleta o Diagnóstico do Azure nas máquinas virtuais do Windows *(WAD)*, nos Logs de Atividade do Azure, nos alertas da central de segurança do Azure e nos logs do Provedor de Recursos do Azure. Esta integração fornece um painel unificado para todos os seus ativos, locais ou na nuvem, para que você possa agregar, correlacionar, analisar e emitir alertas de eventos de segurança.

![Integração de log do Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quais logs posso integrar?
O Azure produz um log abrangente para cada um de seus serviço. Esses logs são categorizados por esses tipos principais:

* **Logs de Controle/Gerenciamento**, que oferecem visibilidade nas operações CRIAR, ATUALIZAR e EXCLUIR do Azure Resource Manager. Os Logs de Atividades do Azure são um exemplo desse tipo de log.
* **Logs do plano de dados**, que oferecem visibilidade nos eventos gerados como parte do uso de um recurso do Azure. Os exemplos desse tipo de log são os logs de eventos de Sistema, Segurança e Aplicativo do Windows em uma máquina virtual, bem como os Logs de Diagnóstico configurados por meio do Azure Monitor
* **Eventos processados**, que fornecem os eventos/alertas analisados processados em seu nome. Alguns exemplos desse tipo são os Alertas da Central de Segurança do Azure, nos quais a Central de Segurança do Azure processou e analisou sua assinatura e forneceu alertas de segurança muito concisos

A integração do log do Azure atualmente dá suporte à integração dos Logs de Atividades do Azure, ao Log de Eventos do Windows da máquina virtual do Windows em sua assinatura do Azure, aos alertas da Central de Segurança do Azure, aos Logs de Diagnóstico do Azure, bem como aos logs de auditoria do Azure Active Directory.

A tabela a seguir explica a categoria de log e os detalhes de integração do SIEM

| Tipo de log  |JSON de suporte ao Log Analytics (Splunk, ELK)| ArcSight  | QRadar  |   
|---|---|---|---|
|  Logs de auditoria do AAD |  sim | Exige a criação de um arquivo analisador de JSON do FlexConnector. Consulte a documentação do ArcSight para obter mais informações  |  Você deve criar uma extensão de origem do log. Consulte a documentação do QRadar para obter mais informações. |  
| Logs de atividade  | Sim  |  O arquivo analisador de JSON do FlexConnector está disponível no Centro de Download junto com o download de integração de log do Azure |  [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (enviar por Syslog) |  
| Alertas do ASC  | Sim  |  Exige a criação de um arquivo analisador de JSON do FlexConnector. Consulte a documentação do ArcSight para obter mais informações. | [QRadar DSM](https://www.ibm.com/support/knowledgecenter/SSKMKU/com.ibm.dsm.doc/c_dsm_guide_microsoft_azure_overview.html) (enviar pelo Syslog)   |   
| Logs de diagnóstico (logs de recurso) | Sim | Precisa que o usuário final crie o arquivo do analisador JSON FlexConnector. Consulte a documentação do ArcSight para saber como fazer isso. | Você deve criar uma extensão de origem do log. Consulte a documentação do QRadar para obter mais informações |
| Logs da VM | Sim, por meio de eventos encaminhados e não por meio do JSON | Sim, por meio de eventos encaminhados | Sim, por meio de eventos encaminhados |

Integração de Log do Azure é uma solução gratuita – você não precisa pagar pelos binários da Integração de Log do Azure. No entanto, haverá custos relacionados ao armazenamento do Azure necessários para as informações de arquivo de log.

Se você tiver dúvidas sobre a integração do Log do Azure, envie um email para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Próximas etapas
Neste documento, você foi apresentado à integração de log do Azure. Para saber mais sobre a integração de log do Azure e os tipos de logs com suporte, confira o seguinte:

* [Integração de log do Microsoft Azure para os logs do Azure](https://www.microsoft.com/download/details.aspx?id=53324) – visite o Centro de Download para obter os detalhes, os requisitos de sistema e as instruções de instalação da integração de log do Azure.
* [Introdução à integração do log do Azure](security-azure-log-integration-get-started.md) — este tutorial explica as etapas de instalação da integração do log do Azure, bem como a integração de logs do armazenamento WAD do Azure, Logs de Atividades do Azure, alertas da Central de Segurança do Azure e logs de auditoria do Azure Active Directory.
* [Integrar os logs de diagnóstico](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/25/integrate-azure-logs-streamed-to-event-hubs-to-siem/) – Esta postagem de blog fornece as etapas para integrar os logs de diagnóstico usando a integração de log do Azure
* [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – esta postagem de blog mostra a você como configurar a integração de log do Azure para trabalhar com as soluções de parceiros Splunk, HP ArcSight e IBM QRadar.
* [Alertas de atividade e ASC pelo syslog para o QRadar](https://blogs.msdn.microsoft.com/azuresecurity/2016/09/24/integrate-azure-logs-to-qradar/) – Esta postagem de blog fornece as etapas para enviar alertas de atividade e da Central de Segurança do Azure pelo syslog para o QRadar
* [Perguntas frequentes sobre o log de integração do Azure](security-azure-log-integration-faq.md) – encontre as respostas para as perguntas frequentes sobre a integração de log do Azure.
* [Integração dos alertas da Central de Segurança com a Integração de Log do Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) – este documento mostra como sincronizar os alertas da Central de Segurança, juntamente com os eventos de segurança de máquina virtual coletados pelo Diagnóstico do Azure e pelos Logs de Auditoria do Azure, com o Log Analytics ou com a solução SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png

