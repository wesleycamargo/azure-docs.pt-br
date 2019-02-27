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
ms.openlocfilehash: 0ad829b976d8b712ee8027c89fb618c6c07de1bc
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429002"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Alerta "Os dados do serviço de integridade não estão atualizados"

## <a name="overview"></a>Visão geral
Os agentes das máquinas locais que o Azure AD Connect Health monitora periodicamente carregam dados para o Azure AD Connect Health Service. Se o serviço não receber dados de um agente, as informações apresentadas no portal serão obsoletas. Para destacar o problema, o serviço irá gerar o alerta **Os dados do serviço de integridade não estão atualizados**. Ele será gerado se o serviço não tiver recebido dados nas últimas duas horas.  

* O alerta de status de **Aviso** aciona se o Connect Health não receber elementos de dados parciais enviados pelo servidor por duas horas. O alerta de status de aviso não dispara notificações por email ao administrador do locatário.
* O alerta de status de **Erro** aciona se o Connect Health não receber nenhum elemento de dados enviado do servidor por duas horas. O alerta de status de erro dispara notificações por email ao administrador do locatário.


## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas 

> [!IMPORTANT] 
> Esse alerta segue a [política de retenção de dados](reference-connect-health-user-privacy.md#data-retention-policy) do Connect Health

* Certifique-se de que os serviços de agente do Azure AD Connect Health estejam sendo executados na máquina. Por exemplo, o Connect Health para AD FS deve ter três serviços.  
  ![Verifique o Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Certifique-se de analisar e atender a [ seção de requisitos](how-to-connect-health-agent-install.md#requirements).
* Use a [ferramenta de teste de conectividade](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) para descobrir problemas de conectividade.
* Se você tiver um proxy HTTP, siga estas [etapas de configuração](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 

A folha de detalhes do alerta lista os elemento(s) de dados ausente(s) de um servidor. A tabela a seguir ajudará a resumir ainda mais o problema. 
### <a name="connect-health-for-sync"></a>Connect Health para sincronização

| Elementos de dados | Etapas para solucionar problemas |
| --- | --- | 
| PerfCounter | - [Conectividade de saída com os pontos de extremidade de serviço do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [A inspeção de SSL para tráfego de saída está filtrada ou desabilitada](https://technet.microsoft.com/library/ee796230.aspx) <br /> - [Portas de firewall no servidor que executa o agente](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
| AadSyncService-SynchronizationRules, <br /> AadSyncService-Connectors, <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | - [Conectividade de saída com os pontos de extremidade de serviço do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Portas de firewall no servidor que executa o agente](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) | 

### <a name="connect-health-for-adfs"></a>Connect Health para ADFS

Etapas adicionais para validar para o AD FS e seguir o fluxo de trabalho em [Ajuda do AD FS](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

| Elementos de dados | Etapas para solucionar problemas |
| --- | --- | 
| PerfCounter, TestResult | - [Conectividade de saída com os pontos de extremidade de serviço do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br />- [A inspeção de SSL para tráfego de saída está filtrada ou desabilitada](https://technet.microsoft.com/library/ee796230.aspx) <br />-  [Portas de firewall no servidor que executa o agente](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
|  Adfs-UsageMetrics | Conectividade de saída com base em endereços IP, consulte os [Intervalos de IP do Azure](https://www.microsoft.com/download/details.aspx?id=41653) | 

### <a name="connect-health-for-adds"></a>Connect Health para ADDS

| Elementos de dados | Etapas para solucionar problemas |
| --- | --- | 
| PerfCounter, Adds-TopologyInfo-Json, Common-TestData-Json | - [Conectividade de saída com os pontos de extremidade de serviço do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) <br /> -  [Portas de firewall no servidor que executa o agente](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |


## <a name="next-steps"></a>Próximas etapas
* [Perguntas frequentes do Azure AD Connect Health](reference-connect-health-faq.md)
