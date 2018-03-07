---
title: "Solução Monitor de Desempenho de Rede no Azure Log Analytics | Microsoft Docs"
description: O recurso ExpressRoute Manager no Monitor de Desempenho de Rede permite monitorar a conectividade de ponta a ponta e o desempenho entre as filiais e o Azure, por meio do Microsoft Azure ExpressRoute.
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 405bcd7d69e93f838d35b61be489464fcf55ee88
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2018
---
# <a name="expressroute-manager"></a>ExpressRoute Manager

O recurso ExpressRoute Manager no [Monitor de Desempenho de Rede](log-analytics-network-performance-monitor.md) permite monitorar a conectividade de ponta a ponta e o desempenho entre as filiais e o Azure, por meio do Microsoft Azure ExpressRoute. As principais vantagens são: 

- Detecção automática de circuitos ExpressRoute associados à sua assinatura 
- Acompanhamento de utilização de largura de banda, perda e latência no circuito, emparelhamento e nível de VNet para o ExpressRoute 
- Descoberta da topologia de rede dos circuitos do ExpressRoute 

![ExpressRoute Monitor](media/log-analytics-network-performance-monitor/expressroute-intro.png)

## <a name="configuration"></a>Configuração 
Para abrir a configuração do Monitor de Desempenho de Rede, abra a [solução Monitor de Desempenho de Rede](log-analytics-network-performance-monitor.md) e clique no botão **Configurar**.

### <a name="configure-nsg-rules"></a>Configurar regras de NSG 
Para servidores no Azure que estão sendo usados para o monitoramento via NPM, você deverá configurar as regras do NSG (grupo de segurança de rede) para permitir o tráfego TCP na porta usada pelo NPM para transações sintéticas. A porta padrão é 8084. Isso permite que o agente do OMS instalado na VM do Azure se comunique com um agente de monitoramento local. 

Para obter mais informações sobre o NSG, consulte  [Grupos de Segurança de Rede](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

>[!NOTE]
> Certifique-se de que você tenha instalado os agentes (o agente do servidor local e o agente do servidor do Azure) e executado o script do PowerShell EnableRules.ps1 antes de continuar com esta etapa. 

 
### <a name="discover-expressroute-peering-connections"></a>Descobrir conexões de emparelhamento do ExpressRoute 
 
1. Clique na exibição **Emparelhamentos do ExpressRoute**.  
2. Clique no botão **Descobrir Agora** para descobrir todos os emparelhamentos privados ExpressRoute que estão conectados às VNETs na assinatura do Azure vinculada a este espaço de trabalho do Log Analytics.  

>[!NOTE]  
> A solução atualmente descobre apenas emparelhamentos privados do ExpressRoute. 

>[!NOTE]  
> Somente os emparelhamentos privados são descobertos que estão conectados às VNETs associadas à assinatura associada a este espaço de trabalho do Log Analytics. Se o ExpressRoute for conectado às VNETs fora da assinatura vinculada a este espaço de trabalho, você precisará criar um espaço de trabalho do Log Analytics nessas assinaturas e usar o NPM para monitorar os emparelhamentos. 

 ![Configuração do ExpressRoute Monitor](media/log-analytics-network-performance-monitor/expressroute-configure.png)
 
 Quando a descoberta estiver concluída, as conexões de emparelhamento privadas descobertas serão listadas em uma tabela. O monitoramento para esses emparelhamentos inicialmente estará no estado desabilitado. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Habilitar o monitoramento das conexões de emparelhamento do ExpressRoute 

1. Clique na conexão de emparelhamento privado que você está interessado em monitorar.  
2. No painel de RHS, clique na caixa de seleção para **Monitorar esse Emparelhamento**. 
3. Se você pretender criar eventos de integridade para essa conexão, verifique **Habilitar Monitoramento de Integridade para esse emparelhamento**. 
4. Escolha as condições de monitoramento. Você pode definir limites personalizados para geração de eventos de integridade digitando os valores de limite. Sempre que o valor da condição ultrapassar o limite selecionado para a conexão de emparelhamento, será gerado um evento de integridade. 
5. Clique em **Adicionar Agentes** para escolher os agentes de monitoramentos que você pretende usar para monitorar essa conexão de emparelhamento. Você precisa se certificar de adicionar agentes nas extremidades da conexão, pelo menos um agente na VNET do Azure conectado a esse emparelhamento, bem como pelo menos um agente local conectado a esse emparelhamento. 
6. Clique em **Salvar** para salvar a configuração. 

![Configuração do ExpressRoute Monitor](media/log-analytics-network-performance-monitor/expressroute-configure-discovery.png)


Depois de habilitar as regras e selecionar os valores e os agentes que deseja monitorar, há uma espera de aproximadamente 30 a 60 minutos para os valores começarem a preencher e os blocos de **Monitoramento do ExpressRoute** se tornarem disponíveis. Depois de ver os blocos de monitoramentos, seus circuitos do ExpressRoute e os recursos de conexão estarão sendo monitorados pelo NPM. 

>[!NOTE]
> Esse recurso funciona de modo confiável em espaços de trabalho que atualizaram para a nova linguagem de consulta.  

## <a name="walkthrough"></a>Passo a passo 

O painel Monitor de Desempenho de Rede mostra uma visão geral da integridade de circuitos do ExpressRoute e das conexões de emparelhamento. 

![ExpressRoute do Painel do NPM](media/log-analytics-network-performance-monitor/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Lista de circuitos 

Para ver uma lista de todos os circuitos do ExpressRoute monitorados, clique no bloco de circuitos do ExpressRoute. É possível selecionar um circuito e exibir seu estado de integridade, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência. Os gráficos são interativos. É possível selecionar uma janela de tempo personalizada para plotar os gráficos. Você pode arrastar o mouse sobre uma área do gráfico para ampliar e ver os pontos de dados refinados. 

![Lista de Circuitos do ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-circuits.png) 

### <a name="trend-of-loss-latency-and-throughput"></a>Tendência de Perda, Latência e Taxa de Transferência 

A largura de banda, latência e perda de gráficos são interativas. Você pode aplicar o zoom em qualquer seção destes gráficos usando os controles do mouse. Também é possível ver a largura de banda, a latência e a perda de dados para outros intervalos ao clicar em Data/Hora, localizado abaixo do botão Ações no canto superior esquerdo. 

![Latência do ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-latency.png) 

### <a name="peerings-list"></a>Lista de emparelhamentos 

Clicar no bloco **Emparelhamentos Privados** no painel exibe uma lista de todas as conexões com redes virtuais sobre emparelhamento privado. Aqui, é possível selecionar uma conexão de rede virtual e exibir seu estado de integridade, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência. 

![Emparelhamentos do ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologia de circuito 

Para exibir a topologia de circuito, clique no bloco **Topologia**. Isso leva você para a exibição de topologia do circuito ou emparelhamento selecionado. O diagrama de topologia fornece a latência de cada segmento na rede e cada salto de camada 3 é representado por um nó do diagrama. Clicar em um salto revela mais detalhes sobre ele. É possível aumentar o nível de visibilidade para incluir saltos locais ao mover a barra do controle deslizante para baixo dos **Filtros**. Mover a barra do controle deslizante para a esquerda ou direita amplia/reduz o número de saltos no grafo de topologia. A latência em cada segmento é visível, o que permite o isolamento mais rápido de segmentos de alta latência na sua rede. 

![Topologia do ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-topology.png)  

### <a name="detailed-topology-view-of-a-circuit"></a>Exibição de topologia detalhada de um circuito 

Essa exibição mostra as conexões de VNet. 

![VNet do ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-vnet.png)
 

### <a name="diagnostics"></a>Diagnostics 

O Monitor de Desempenho de Rede ajuda a diagnosticar vários problemas de conectividade do circuito. Alguns dos problemas estão listados abaixo 

**O circuito está inoperante.** O NPM notifica assim que a conectividade entre seus recursos locais e as VNETs do Azure é perdida. Isso ajuda você a tomar medidas proativas antes de receber escalonamentos de usuário e reduzir o tempo de inatividade 

![Circuito do ExpressRoute inoperante](media/log-analytics-network-performance-monitor/expressroute-circuit-down.png)
 

**O tráfego não flui pelo circuito pretendido.** O NPM pode notificá-lo sempre que o tráfego inesperadamente não estiver fluindo pelo circuito do ExpressRoute pretendido. Isso poderá acontecer se o circuito estiver inoperante e o tráfego estiver fluindo pela rota de backup ou se houver um problema de roteamento. Essas informações ajudam a gerenciar proativamente quaisquer problemas de configuração em suas políticas de roteamentos e verificar se a rota mais ideal e segura é usada. 

 

**O tráfego não flui pelo circuito primário.** O recurso notifica quando o tráfego está fluindo pelo circuito do ExpressRoute secundário. Embora você não vá enfrentar nenhum problema de conectividade nesse caso, solucionar os problemas proativamente com o circuito primário o deixará mais bem preparado. 

 
![Fluxo de tráfego do ExpressRoute](media/log-analytics-network-performance-monitor/expressroute-traffic-flow.png)


**Degradação devido à utilização de pico.** Você pode correlacionar a tendência de utilização de largura de banda com a tendência de latência para identificar se a degradação da carga de trabalho do Azure é devido a um pico de utilização de largura de banda ou não e tomar as devidas providências.  

![ExpressRoute Monitor](media/log-analytics-network-performance-monitor/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Próximas etapas
* [Pesquisar logs](log-analytics-log-searches.md) para exibir registros de dados de desempenho de rede detalhados.
