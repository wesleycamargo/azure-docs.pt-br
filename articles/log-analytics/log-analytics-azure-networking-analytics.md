---
title: Solução de Análise de Rede do Azure no Log Analytics | Microsoft Docs
description: Você pode usar a solução de Análise de Rede do Azure no Log Analytics para examinar logs de grupo de segurança de rede do Azure e logs do Gateway de Aplicativo do Azure.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: richrund

---
# <a name="azure-networking-analytics-(preview)-solution-in-log-analytics"></a>Solução de Análise de Rede do Azure (Visualização) no Log Analytics
> [!NOTE]
> Esta é uma [solução de visualização](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).
> 
> 

Você pode usar a solução de Análise de Rede do Azure no Log Analytics para examinar logs de grupo de segurança de rede do Azure e logs do Gateway de Aplicativo do Azure.

Você pode habilitar o registro em log para logs de Gateway de Aplicativo do Azure e grupos de segurança de rede do Azure. Esses logs são gravados no armazenamento de blobs, em que eles podem então ser indexados pelo Log Analytics para pesquisa e análise.

Nos Gateways de Aplicativo, há suporte para os seguintes logs:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog

Nos grupos de segurança de rede, há suporte para os seguintes logs:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

## <a name="install-and-configure-the-solution"></a>Instale e configure a solução
Use as instruções a seguir para instalar e configurar a solução de Análise de Rede do Azure:

1. Habilite o log de diagnóstico para os recursos que você deseja monitorar:
   * [Application Gateway](../application-gateway/application-gateway-diagnostics.md)
   * [Grupo de segurança de rede](../virtual-network/virtual-network-nsg-manage-log.md)
2. Configure o Log Analytics para ler os logs do Armazenamento de Blobs usando o processo descrito em [Arquivos JSON no armazenamento de blobs](log-analytics-azure-storage-json.md).
3. Habilite a solução de Análise de Rede do Azure usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).  

Se você não habilitar o log de diagnóstico para um tipo de recurso específico, as folhas de painel para esse recurso ficarão em branco.

## <a name="review-azure-networking-analytics-data-collection-details"></a>Examine os detalhes de coleta de dados da Análise de Rede do Azure
A solução de Análise de Rede do Azure coleta logs de diagnóstico do Armazenamento de Blobs do Azure para Gateways de Aplicativo do Azure e grupos de segurança de rede.
Nenhum agente é necessário para a coleta de dados.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para a Análise de Rede do Azure.

| Plataforma | Agente direto | Agente do SCOM (System Center Operations Manager) | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | Frequência de coleta |
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

<!--HONumber=Oct16_HO2-->


