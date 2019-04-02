---
title: Azure AD Connect Health - Alerta "Os dados do serviço de integridade não estão atualizados" | Microsoft Docs
description: Este documento descreve a causa do alerta "Os dados do serviço de integridade não estão atualizados" e como solucioná-lo.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16794dfdcdc6ed9c2effe412237d2681fca4f394
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803282"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Alerta "Os dados do serviço de integridade não estão atualizados"

## <a name="overview"></a>Visão geral
Os agentes das máquinas locais que o Azure AD Connect Health monitora periodicamente carregam dados para o Azure AD Connect Health Service. Se o serviço não receber dados de um agente, as informações apresentadas no portal serão obsoletas. Para destacar o problema, o serviço irá gerar o alerta **Os dados do serviço de integridade não estão atualizados**. Ele é gerado quando o serviço não recebeu dados concluídos nas últimas duas horas.  

* O **aviso** alerta de status será acionado se o serviço de integridade recebeu apenas **parcial** tipos de dados enviados do servidor nas últimas duas horas. Alerta de status de aviso não dispara notificações por email para destinatários configurados. 
* O **erro** alerta de status será acionado se o serviço de integridade não tenha recebido quaisquer tipos de dados do servidor nas últimas duas horas. Gatilhos de alerta de status de erro de notificações por email aos destinatários configurados.

O serviço obtém os dados dos agentes que estão em execução nas máquinas locais. Dependendo do tipo de serviço, a tabela a seguir lista os agentes que são executados no computador, o que fazer, bem como os tipos de dados que são gerados pelo serviço. Em alguns casos, há vários serviços envolvidos no processo, portanto, qualquer um deles pode ser o culpado. 

## <a name="understanding-the-alert"></a>Noções básicas sobre o alerta
A folha de detalhes do alerta indica a hora quando o alerta for gerado e detectado pela última vez. O alerta é gerado/re-evaluated por um processo em segundo plano que é executado a cada duas horas. No exemplo a seguir, o alerta inicial foi gerado em 03/10 às 9:00: 59. Ele continua a existir até mesmo em 03/12 às 10h quando o alerta foi avaliado novamente.
A folha também fornece detalhes sobre a hora em que um determinado tipo de dados pela última vez foi recebido pelo serviço de integridade. 
 
 ![Detalhes do alerta do Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Abaixo está o mapa de tipos de serviço e tipo de dados necessário correspondente.

| Tipo de serviço | Agente (nome de serviço do Windows) | Finalidade | Tipo de dados gerado  |
| --- | --- | --- | --- |  
| Azure AD Connect (sincronização) | Serviço de Informações do Azure AD Connect Health para Sincronização | Coletar informações específicas do AAD Connect (conectores, a sincronização de regras etc.) | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Serviço de Monitoramento do Azure AD Connect Health para Sincronização | Coletar contadores de desempenho (AAD Connect específico), os rastreamentos ETW, arquivos | Contador de Desempenho |
| AD DS | Serviço do Insights do AD DS do Azure AD Connect Health | Executar testes sintéticos, coletar informações sobre a topologia, metadados de replicação |  -Adiciona-TopologyInfo-Json <br /> -Common-TestData-Json (cria os resultados do teste)   | 
|  | Serviço de Monitoramento do AD DS do Azure AD Connect Health | Coletar arquivos de rastreamentos ETW, contadores de Perf (específico do ADDS) | -Contador de desempenho  <br /> -Common-TestData-Json (carrega os resultados do teste)  |
| AD FS | Serviço de diagnóstico do AD FS do Azure AD Connect Health | Executar testes sintéticos | TestResult (cria os resultados do teste) | 
| | Serviço do Insights do AD FS do Azure AD Connect Health  | Coletar métricas de uso do ADFS | Adfs-UsageMetrics |
| | Serviço de Monitoramento do AD FS do Azure AD Connect Health | Coletar arquivos de rastreamentos ETW, contadores de Perf (específico do AD FS) | TestResult (carrega os resultados do teste) |

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas 

As etapas necessárias para diagnosticar o problema é fornecido abaixo. O primeiro é um conjunto de verificações básicas que são comuns a todos os tipos de serviço. A tabela a seguir que lista as etapas específicas para cada tipo de serviço e o tipo de dados. 

> [!IMPORTANT] 
> Esse alerta segue a [política de retenção de dados](reference-connect-health-user-privacy.md#data-retention-policy) do Connect Health

* Verifique se que as versões mais recentes dos agentes estão instaladas. Modo de exibição [histórico de lançamento](reference-connect-health-version-history.md). 
* Certifique-se de que os serviços de agentes do Azure AD Connect Health são **executando** na máquina. Por exemplo, o Connect Health para AD FS deve ter três serviços.
  ![Verifique se o Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Certifique-se de analisar e atender a [ seção de requisitos](how-to-connect-health-agent-install.md#requirements).
* Use a [ferramenta de teste de conectividade](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) para descobrir problemas de conectividade.
* Se você tiver um proxy HTTP, siga estas [etapas de configuração](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Próximas etapas
Se qualquer uma das etapas acima identificado um problema, corrigi-lo e aguarde até que o alerta resolver. O processo de alerta em segundo plano é executado a cada 2 horas, por isso levará até 2 horas para resolver o alerta. 

* [Política de retenção de dados do Azure AD Connect Health](reference-connect-health-user-privacy.md#data-retention-policy)
* [Perguntas frequentes do Azure AD Connect Health](reference-connect-health-faq.md)
