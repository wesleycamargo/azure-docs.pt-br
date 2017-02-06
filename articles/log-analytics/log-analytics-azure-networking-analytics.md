---
title: "Solução de Análise de Rede do Azure no Log Analytics | Microsoft Docs"
description: "Você pode usar a solução de Análise de Rede do Azure no Log Analytics para examinar logs de grupo de segurança de rede do Azure e logs do Gateway de Aplicativo do Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/1/2016
ms.author: richrund
translationtype: Human Translation
ms.sourcegitcommit: a86819102797b0e243d28cd9ddb3d2c88c74bfca
ms.openlocfilehash: 7ea593885c1b380236a49ec030c00ad19097e2fa


---
# <a name="azure-networking-analytics-preview-solution-in-log-analytics"></a>Solução de Análise de Rede do Azure (Visualização) no Log Analytics

Você pode usar a solução de Análise de Rede do Azure no Log Analytics para analisar:

* Logs do Gateway de Aplicativo do Azure
* Métricas do Gateway de Aplicativo do Azure e 
* Logs de grupo de segurança de rede do Azure.

> [!NOTE]
> A Análise de Rede do Azure é uma [solução de visualização](log-analytics-add-solutions.md#preview-management-solutions-and-features).
> 
> 

Para usar a solução, habilite os diagnósticos para logs do Gateway de Aplicativo do Azure e para grupos de segurança de rede do Azure e direcione o diagnóstico para um espaço de trabalho do Log Analytics. Não é necessário gravar os logs no Armazenamento de Blobs do Azure.

Nos Gateways de Aplicativo, há suporte para os seguintes logs:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Há suporte para as seguintes métricas nos Gateways de Aplicativo:

* Taxa de transferência de 5 minutos

Nos grupos de segurança de rede, há suporte para os seguintes logs:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter
* NetworkSecurityGroupFlowEvent

## <a name="install-and-configure-the-solution"></a>Instale e configure a solução
Use as instruções a seguir para instalar e configurar a solução de Análise de Rede do Azure:

1. Habilite o log de diagnóstico para os recursos que você deseja monitorar:
   * [Gateway de Aplicativo](../application-gateway/application-gateway-diagnostics.md)
   * [Grupo de segurança de rede](../virtual-network/virtual-network-nsg-manage-log.md)
2. Habilite a solução de Análise de Rede do Azure usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).  

O script do PowerShell a seguir fornece um exemplo de como habilitar o registro em log de diagnóstico para gateways de aplicativo e para grupos de segurança de rede 
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```


Se você não habilitar o registro em log de diagnósticos para um tipo de recurso específico, as folhas de painel para esse recurso ficarão em branco.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Examine os detalhes de coleta de dados da Análise de Rede do Azure
A solução de gerenciamento de Análise de Rede do Azure coleta logs de diagnóstico diretamente dos Gateways de Aplicativo do Azure e dos grupos de segurança de rede. Não é necessário gravar os logs no Armazenamento de Blobs do Azure e nenhum agente é necessário para a coleta de dados.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para a Análise de Rede do Azure.

| Plataforma | Agente direto | Agente do Systems Center Operations Manager | As tabelas | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | Frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| As tabelas |![Não](./media/log-analytics-azure-networking/oms-bullet-red.png) |![Não](./media/log-analytics-azure-networking/oms-bullet-red.png) |![Sim](./media/log-analytics-azure-networking/oms-bullet-green.png) |![Não](./media/log-analytics-azure-networking/oms-bullet-red.png) |![Não](./media/log-analytics-azure-networking/oms-bullet-red.png) |10 minutos |

## <a name="use-azure-networking-analytics"></a>Usar a Análise de Rede do Azure
Depois de instalar a solução, você pode exibir o resumo de erros de cliente e servidor para seus Gateways de Aplicativo monitorado usando o bloco **Análise de Rede do Azure** na página **Visão geral** no Log Analytics.

![imagem do bloco de Análise de Rede do Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking-tile.png)

Depois de clicar no bloco **Visão Geral**, você pode exibir resumos dos seus logs e então aprofundar-se nos detalhes das seguintes categorias:

* Logs de acesso do Gateway de Aplicativo
  * Erros de cliente e servidor nos logs de acesso do Gateway de Aplicativo
  * Solicitações por hora para cada Gateway de Aplicativo
  * Solicitações com falha por hora para cada Gateway de Aplicativo
  * Erros por agente do usuário para Gateways de Aplicativo
* Desempenho do Gateway de Aplicativo
  * Integridade do host para o Gateway de Aplicativo
  * Percentil 95 e máximo para solicitações com falha do Gateway de Aplicativo
* Fluxos bloqueados no grupo de segurança de rede
  * Regras do grupo de segurança de rede com fluxos bloqueados
  * Endereços MAC com fluxos bloqueados
* Fluxos permitidos no grupo de segurança de rede
  * Regras com fluxos permitidos do grupo de segurança de rede
  * Endereços MAC com fluxos permitidos

![imagem do painel de Análise de Rede do Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking01.png)

![imagem do painel de Análise de Rede do Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking02.png)

![imagem do painel de Análise de Rede do Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking03.png)

![imagem do painel de Análise de Rede do Azure](./media/log-analytics-azure-networking/log-analytics-azurenetworking04.png)

### <a name="to-view-details-for-any-log-summary"></a>Para exibir detalhes de qualquer resumo de log
1. Sobre o **Visão Geral**, clique no bloco **Análise de Rede do Azure**.
2. Na painel **Análise de Rede do Azure**, examine as informações resumidas em uma das folhas e, em seguida, clique em uma para exibir informações detalhadas sobre ela na página pesquisa de log.
   
    Em qualquer uma das páginas de pesquisa de log, você pode exibir os resultados por tempo, resultados detalhados e o histórico de pesquisa de log. Você também pode filtrar por facetas para restringir os resultados.

## <a name="next-steps"></a>Próximas etapas
* Use [Pesquisas de log no Log Analytics](log-analytics-log-searches.md) para exibir dados detalhados da Análise de Rede do Azure.




<!--HONumber=Dec16_HO1-->


