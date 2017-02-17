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
ms.date: 01/23/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: b5edb42f7fac6943a72e02a85a4cbc32300b9f38
ms.openlocfilehash: 62b5469d06974dfbe1b243091d34e1141416c971


---
# <a name="introduction-to-microsoft-azure-log-integration-preview"></a>Introdução à integração de log do Microsoft Azure (Preview)
Saiba mais sobre a integração de log do Azure, seus principais recursos e como ela funciona.

## <a name="overview"></a>Visão geral
PaaS (plataforma como serviço) e IaaS (infraestrutura como serviço) hospedadas no Azure geram uma grande quantidade de dados em logs de segurança. Esses logs contêm informações importantes que podem fornecer inteligência e entendimentos significativos sobre as violações de política, as ameaças internas e externas, os problemas de conformidade normativa e as anomalias na rede, no host e na atividade do usuário.

A integração do log do Azure permite que você integre registros brutos de recursos do Azure em seus sistemas de SIEM (Segurança da Informação e Gerenciamento de Evento) locais. A integração do log do Azure coleta o Diagnóstico do Azure nas máquinas virtuais do Windows *(WAD)*, nos Logs de Atividade do Azure, nos alertas da central de segurança do Azure e nos logs do Provedor de Recursos do Azure. Esta integração fornece um painel unificado para todos os seus ativos, locais ou na nuvem, para que você possa agregar, correlacionar, analisar e emitir alertas de eventos de segurança.

![Integração de log do Azure][1]

## <a name="what-logs-can-i-integrate"></a>Quais logs posso integrar?
O Azure produz um log abrangente para cada um de seus serviço. Esses logs são categorizados pelos dois tipos principais:

* **Logs de Controle/Gerenciamento**, que oferecem visibilidade nas operações CRIAR, ATUALIZAR e EXCLUIR do Azure Resource Manager. Os Logs de Auditoria do Azure são um exemplo desse tipo de log.
* **Logs do plano de dados**, que oferecem visibilidade nos eventos gerados como parte do uso de um recurso do Azure. Os exemplos desse tipo de log são os Logs do Sistema de Eventos do Windows, de Segurança e Aplicativo em uma máquina virtual.

A integração do log do Azure atualmente dá suporte para a integração dos Logs de Auditoria do Azure, aos logs de máquina virtual e aos alertas da Central de Segurança do Azure.

Se você tiver dúvidas sobre a integração do Log do Azure, envie um email para [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Próximas etapas
Neste documento, você foi apresentado à integração de log do Azure. Para saber mais sobre a integração de log do Azure e os tipos de logs com suporte, confira o seguinte:

* [Integração de Log do Microsoft Azure para os logs do Azure (Preview)](https://www.microsoft.com/download/details.aspx?id=53324) – Centro de Download para obter os detalhes, os requisitos de sistema e as instruções de instalação da integração de log do Azure.
* [Introdução à Integração de log do Azure](security-azure-log-integration-get-started.md) – este tutorial orienta você durante a instalação da integração de log do Azure e na integração dos Logs de Armazenamento do Azure, dos Logs de Auditoria do Azure e dos alertas da Central de Segurança.
* [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – esta postagem de blog mostra a você como configurar a integração de log do Azure para trabalhar com as soluções de parceiros Splunk, HP ArcSight e IBM QRadar.
* [Perguntas frequentes sobre o log de integração do Azure](security-azure-log-integration-faq.md) – encontre as respostas para as perguntas frequentes sobre a integração de log do Azure.
* [Integração dos alertas da Central de Segurança com a Integração de Log do Azure](../security-center/security-center-integrating-alerts-with-log-integration.md) – este documento mostra como sincronizar os alertas da Central de Segurança, juntamente com os eventos de segurança de máquina virtual coletados pelo Diagnóstico do Azure e pelos Logs de Auditoria do Azure, com o Log Analytics ou com a solução SIEM.
* [Novos recursos para o Diagnóstico do Azure e para os Logs de Auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – esta postagem de blog apresenta os Logs de Auditoria do Azure e outros recursos que ajudam você a obter ideias sobre as operações de seus recursos do Azure.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png



<!--HONumber=Jan17_HO4-->


