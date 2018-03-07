---
title: "Solução de Monitor de Desempenho de Rede no Azure | Microsoft Docs"
description: O Monitor de Desempenho de Rede no Azure ajuda a monitorar o desempenho de suas redes quase em tempo real para detectar e localizar gargalos de desempenho de rede.
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
ms.openlocfilehash: 399fe552d5c7d9a96cdabc2a1dfafe99635d4a61
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Solução do Monitor de Desempenho de Rede no Azure

![Símbolo do Monitor de Desempenho de Rede](./media/log-analytics-network-performance-monitor/npm-symbol.png)


O Monitor de Desempenho de Rede (NPM) é uma solução de monitoramento de rede híbrida baseada em nuvem que ajuda a monitorar o desempenho da rede entre vários pontos em sua infraestrutura de rede, monitorar a conectividade de rede a pontos de extremidade de serviço/aplicativo e monitorar o desempenho do Azure ExpressRoute.  

O NPM detecta problemas de tráfego de rede, como blackholing, erros de roteamento e problemas que os métodos de monitoramento de rede convencionais não são capazes de detectar. A solução gera alertas, notifica quando um limite é violado em um link de rede, garante a detecção oportuna de problemas de desempenho de rede e localiza a origem do problema para determinado segmento de rede ou dispositivo. 

O NPM oferece três recursos abrangentes: 

[Monitor de Desempenho](log-analytics-network-performance-monitor-performance-monitor.md): monitore a conectividade de rede entre vários pontos em sua rede, como implantações de nuvem e localizações locais, vários data centers e filiais, vários aplicativos/microserviços multiníveis críticos à missão. Com o Monitor de Desempenho, você pode detectar problemas de rede antes de seus usuários reclamarem.  

[Monitor de Ponto de Extremidade de serviço](log-analytics-network-performance-monitor-service-endpoint.md): você pode monitorar a conectividade dos seus usuários aos serviços importantes, determinar qual infraestrutura é o caminho e onde gargalos ocorrem na rede. Saiba quais são as interrupções antes dos usuários e veja a localização exata dos problemas ao longo de seu caminho de rede. 

Esse recurso ajuda você a executar testes baseados em http, HTTPS, TCP e ICMP para monitorar quase em tempo real ou historicamente a disponibilidade e o tempo de resposta do serviço e a contribuição da rede para a perda de pacotes e a latência. Com o mapa de topologia de rede, você pode isolar gargalos de rede identificando pontos problemáticos que ocorrem ao longo do caminho de rede do nó ao serviço, com dados de latência em cada salto. Com testes internos, monitore a conectividade de rede ao Office 365 e ao Dynamics CRM sem nenhuma configuração prévia. Com essa funcionalidade, você pode monitorar a conectividade de rede a qualquer ponto de extremidade compatível com TCP, como sites, SaaS, aplicativos PaaS, bancos de dados SQL, etc.  

[Monitor do ExpressRoute](log-analytics-network-performance-monitor-expressroute.md): monitore desempenho e conectividade ponta a ponta entre as filiais e o Azure por meio do Azure ExpressRoute.  
 

## <a name="set-up-and-configure"></a>Instalar e configurar

### <a name="install-and-configure-agents"></a>Instalar e configurar agentes 

Use os processos básicos para instalar agentes em [Conectar computadores com Windows à Análise de Log](log-analytics-windows-agents.md) e [Conectar o Operations Manager à Análise de Log](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Onde instalar os agentes 

**Monitor de desempenho:** instale agentes do OMS em pelo menos um nó conectado a cada sub-rede na qual você deseja monitorar a conectividade de rede a outras sub-redes.  

Para monitorar um link de rede, você deve instalar agentes em ambas as extremidades do link.  Se você não tiver certeza sobre a topologia da rede, instale os agentes em servidores com cargas de trabalho críticas entre os quais você deseja monitorar o desempenho da rede. Por exemplo, se você quiser monitorar a conexão de rede entre um servidor Web e um servidor que executa o SQL, instale um agente em ambos os servidores. Os agentes monitoram a conectividade de rede (links) entre os hosts, não os próprios hosts. 

**Monitor de Ponto de Extremidade de serviço:** instale o agente do OMS em cada nó no qual você deseja monitorar a conectividade de rede para o ponto de extremidade de serviço. Por exemplo, se você pretende monitorar a conectividade de rede ao Office 365 do seu site do office O1, O2 e O3, instale o agente do OMS em pelo menos um nó cada em O1, O2 e O3. 

**Monitor do ExpressRoute:** instale pelo menos um agente do OMS em sua rede virtual do Azure e pelo menos um agente em sua sub-rede local, a qual é conectada por meio do emparelhamento privado do ExpressRoute.  

### <a name="configure-oms-agents-for-monitoring"></a>Configurearagentes do OMS para monitoramento  

O NPM usa transações sintéticas para monitorar o desempenho da rede entre os agentes de origem e de destino. A solução oferece a opção de TCP e ICMP como protocolo para o monitoramento de recursos do Monitor de Desempenho e Monitor de Ponto de Extremidade de Serviço, enquanto o TCP é usado para o Monitor do ExpressRoute. Certifique-se de que o firewall permita a comunicação entre os agentes do OMS que estão sendo usados para o monitoramento no protocolo que você escolheu para monitoramento.  

**Protocolo TCP:** se você tiver escolhido TCP como protocolo para monitoramento, abra a porta de Firewall nos agentes que está sendo usada para os recursos do Monitor de Desempenho e Monitor do ExpressRoute, para garantir que os agentes possam se conectar entre si. Para isso, execute o script do PowerShell EnableRules.ps1 sem parâmetros em uma janela do PowerShell com privilégios administrativos.  

O script cria chaves do registro necessárias para a solução e cria regras de firewall do Windows para permitir que os agentes criem conexões TCP entre si. As chaves do Registro criadas pelo script também podem especificar se é preciso registrar os logs de depuração e o caminho para o arquivo de log. Também é definida a porta TCP de agente usada para comunicação. Os valores dessas chaves são definidos automaticamente pelo script. Portanto, você não deve alterar manualmente as chaves. A porta aberta por padrão é 8084. Você pode usar uma porta personalizada fornecendo o parâmetro portNumber para o script. No entanto, a mesma porta deve ser usada em todos os computadores em que o script é executado. 

>[!NOTE]
> O script configura apenas o firewall do windows localmente. Se tiver um firewall de rede, você deverá verificar se ele está permitindo o tráfego destinado à porta TCP usada pelo NPM 

>[!NOTE]
> Você não precisa executar o script do PowerShell EnableRules.ps1 para o Monitor de Ponto de Extremidade de Serviço 

 

**Protocolo ICMP** - se você escolheu ICMP como protocolo para monitoramento, habilite as seguintes regras de firewall para usar de forma confiável o ICMP: 

 
```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```
 

### <a name="configure-the-solution"></a>Configurar a solução 

1. Adicione a solução de Monitor de Desempenho de Rede ao seu espaço de trabalho do [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md). 
2. Abra seu espaço de trabalho do Log Analytics e clique no bloco **Visão geral**.  
3. Clique no bloco **Monitor de Desempenho de Rede** com a mensagem *A solução requer configuração adicional*.

    ![Bloco NPM](media/log-analytics-network-performance-monitor/npm-config.png)

3. Na página **Instalação**, você verá a opção para instalar agentes do OMS e configurar os agentes para monitoramento no modo de exibição **Configurações comuns**. Conforme explicado acima, se você já instalou e configurou os agentes do OMS, clique no modo de exibição **Instalação** para configurar o recurso que você está interessado em usar.  

    **Modo de exibição de Monitor de Desempenho** - escolha qual protocolo deve ser usado para transações sintéticas na regra de monitor de desempenho Padrão e clique em Salvar e continuar. Essa seleção de protocolo só se mantém em relação à regra padrão gerada pelo sistema e você precisará escolher o protocolo toda vez que criar uma regra de Monitor de Desempenho explicitamente. Você sempre pode mover para as configurações de regra Padrão na guia do Monitor de Desempenho (que aparece depois de concluir a configuração do dia 0) e alterar o protocolo mais tarde. Caso você não esteja interessado no recurso de Monitor de Desempenho, você pode desabilitar a regra padrão nas configurações de regra Padrão na guia do Monitor de Desempenho. 

    ![Configuração do NPM](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
    **Modo de exibição de Monitor de Ponto de Extremidade de Serviço** - o recurso fornece testes internos pré-configurados para monitorar a conectividade de rede dos seus agentes ao Office 365 e ao Dynamcis365. Escolha os serviços do Office 365 e Dynamcis365 que você esteja interessado em monitorar marcando a caixa de seleção ao lado deles. Escolha os agentes que você deseja monitorar clicando no botão Adicionar Agentes. Se você não quiser usar esse recurso ou quiser configurá-lo mais tarde, poderá optar por ignorar e clicar diretamente em **Salvar** e **Continuar** sem escolher nenhuma opção.  

    ![Configuração do NPM](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

    **Modo de exibição do Monitor do ExpressRoute** - Clique no botão **Descobrir Agora** para descobrir todos os emparelhamentos privados ExpressRoute que estão conectados às VNETs na assinatura do Azure vinculada a este espaço de trabalho do Log Analytics.  


    >[!NOTE] 
    > A solução atualmente descobre apenas emparelhamentos privados do ExpressRoute. 

    >[!NOTE] 
    > Somente os emparelhamentos privados são descobertos que estão conectados às VNETs associadas à assinatura associada a este espaço de trabalho do Log Analytics. Se o ExpressRoute for conectado às VNETs fora da assinatura vinculada a este espaço de trabalho, você precisará criar um espaço de trabalho do Log Analytics nessas assinaturas e usar o NPM para monitorar os emparelhamentos. 

    ![Configuração do NPM](media/log-analytics-network-performance-monitor/npm-express-route.png)

    Quando a descoberta estiver concluída, os emparelhamentos privados descobertos serão listados em uma tabela.  

    ![Configuração do NPM](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
    O monitoramento para esses emparelhamentos estão inicialmente no estado desabilitado. Clique em cada emparelhamento que você esteja interessado em monitorar e configure o monitoramento deles no modo de exibição do lado direito (RHS).  Clique no botão Salvar para salvar a configuração. Consulte [Configurar monitoramento do ExpressRoute]() para saber mais.  

    Quando a instalação estiver concluída, demora entre 30 minutos e uma hora para que os dados sejam preenchidos. Enquanto a solução está agregando dados de sua rede, você verá *A solução requer configuração adicional* no bloco de visão geral do NPM. Depois que os dados são coletados e indexados, o bloco de visão geral muda e informa o resumo da integridade de sua rede. Você pode optar por editar o monitoramento de nós nos quais agentes do OMS estão instalados, bem como as sub-redes descobertas no seu ambiente 

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Editar configurações de monitoramento para sub-redes e nós 

Todas as sub-redes com pelo menos um agente instalado são listadas na guia Sub-redes na página de configuração. 


Para habilitar ou desabilitar o monitoramento de sub-redes específicas 

1. Marque ou desmarque a caixa ao lado de  **ID da sub-rede** e verifique se **Usar para monitoramento** está marcado ou desmarcado, conforme apropriado. Você pode marcar ou desmarcar várias sub-redes. Quando desabilitado, as sub-redes não são monitoradas, pois os agentes serão atualizados para parar de executar o ping para outros agentes. 
2. Escolha os nós que você deseja monitorar em uma sub-rede específica, selecionando a sub-rede na lista e movendo os nós necessários entre as listas que contêm nós não monitorados e monitorados. Você pode adicionar uma **descrição personalizada** à sub-rede. 
3. Clique em **Salvar** para salvar a configuração. 

#### <a name="choose-nodes-to-monitor"></a>Escolher nós para monitorar

Todos os nós que têm um agente instalado neles estão listados na guia **Nós**. 

1. Marque ou desmarque os nós que você deseja monitorar ou parar de monitorar. 
2. Clique em **Usar para Monitoramento** ou desmarque essa opção, conforme apropriado. 
3. Clique em **Salvar**. 


Configure o(s) recurso(s) que lhe interessa(m): 
- Configurar [Monitor de Desempenho](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Configurar [Monitor de Ponto de Extremidade de Serviço](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Configurar [Monitor do ExpressRoute](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Detalhes da coleta de dados
O Monitor de Desempenho de Rede usa pacotes de handshake TCP SYN-SYNACK-ACK quando TCP é escolhido e rICMP ECHO ICMP ECHO REPLY quando ICMP é escolhido como o protocolo para coletar informações de latência e de perda. O traceroute também é usado para obter informações sobre a topologia.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para o Monitor de Desempenho de Rede.

| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Mensagens de handshakes TCP/ECO ICMP a cada 5 segundos, dados enviados a cada 3 minutos |
 

 
A solução usa transações sintéticas para avaliar a integridade da rede. Os agentes do OMS instalados em vários pontos da rede trocam pacotes TCP ou Eco ICMP (dependendo do protocolo selecionado para monitoramento) uns com os outros. No processo, os agentes aprendem qual é o tempo de ida e volta e a perda de pacotes, se houver. Periodicamente, cada agente também realiza uma rota de rastreamento para outros agentes para localizar todas as várias rotas da rede que deve ser testadas. Usando esses dados, os agentes podem deduzir a latência de rede e os valores de perda de pacotes. Os testes são repetidos a cada cinco segundos, e os dados são agregados por um período de três minutos pelos agentes antes de serem carregados no serviço Log Analytics. 



>[!NOTE]
> Embora os agentes se comuniquem uns com os outros com frequência, não geram tráfego de rede significativo durante a realização de testes. Os agentes utilizam somente pacotes de handshake TCP SYN-SYNACK-ACK para determinar a perda e latência. Não são trocados pacotes de dados. Durante esse processo, os agentes se comunicam entre si somente quando necessário, e a topologia de comunicação de agente é otimizada para reduzir o tráfego de rede.

## <a name="using-the-solution"></a>Usando a solução 

### <a name="npm-overview-tile"></a>Bloco Visão Geral do NPM 

Depois que você habilita a solução de Monitor de Desempenho de Rede, o bloco da solução na página de Visão Geral fornece uma visão geral da integridade da rede. 

 ![Bloco Visão Geral do NPM](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Painel do Monitor de Desempenho de Rede 

A página **Eventos de Integridade de Rede Principais** fornece uma lista de alertas e eventos de integridade mais recentes no sistema e o tempo desde que o evento esteve ativo. Um alerta ou evento de integridade é gerado sempre que o valor da métrica escolhida (perda, latência, tempo de resposta ou utilização de largura de banda) para a regra de monitoramento excede o limite. 

A página  **Monitor de Desempenho** apresenta um resumo da integridade dos links de Rede e links de Sub-rede que estão sendo monitorados pela solução. O bloco Topologia informa o número de caminhos de rede que estão sendo monitorados em sua rede. Ao clicar nesse bloco diretamente, você é direcionado para o modo de exibição Topologia. 

A página  **Monitor de Ponto de Extremidade de Serviço** apresenta um resumo da integridade de diferentes testes criados por você. O bloco Topologia informa o número de pontos de extremidade que estão sendo monitorados. Ao clicar nesse bloco diretamente, você é direcionado para o modo de exibição Topologia.

A página  **Monitor do ExpressRoute** apresenta um resumo da integridade de várias conexões de emparelhamento de ExpressRoute monitoradas pela solução. O bloco Topologia informa o número de caminhos de rede pelo(s) circuito(s) do ExpressRoute que estão sendo monitorados em sua rede. Ao clicar nesse bloco diretamente, você é direcionado para o modo de exibição Topologia.

A página  **Consultas Comuns** contém um conjunto de consultas de pesquisa que buscam diretamente os dados de monitoramento de rede brutos. Você pode usar essas consultas como um ponto de partida para criar suas próprias consultas para relatórios personalizados. 

![Painel do NPM](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Fazer busca detalhada para profundidade 

Você pode clicar em vários links do painel da solução para fazer drill down em qualquer área de interesse. Por exemplo, ao ver um alerta ou um link de rede não íntegro no painel, você pode clicar para investigar mais. Você será levado a uma página que lista todos os links de sub-rede do link de rede específico. Você poderá ver o status de integridade, latência e perda de cada link de sub-rede e descobrir rapidamente quais links de sub-rede estão causando o problema. Você pode clicar em **Exibir links de nó** para ver todos os links de nó do link de sub-rede não íntegro. Em seguida, você pode ver links de nós individuais e encontrar os links de nó não íntegros. 

Você pode clicar em **Exibir topologia** para exibir a topologia de salto a salto das rotas entre os nós de origem e de destino. As rotas não íntegras são mostradas em vermelho e você pode exibir a latência que cada sal acrescentou para que você possa identificar rapidamente o problema de uma parte específica da rede. 

 

### <a name="network-state-recorder"></a>Gravador de Estado da Rede 

Cada modo de exibição exibe um instantâneo da integridade da rede em um ponto específico no tempo. Por padrão, o estado mais recente é mostrado. A barra na parte superior da página mostra o ponto no tempo para que o estado está sendo exibido. Você pode optar por voltar no tempo e exibir o instantâneo da integridade da rede, clicando na barra em Ações. Você também pode optar por habilitar ou desabilitar a atualização automática para qualquer página enquanto exibe o estado mais recente. 

 ![Gravador de Estado da Rede](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Gráficos de tendência 

Em cada nível que fizer uma busca detalhada, você poderá ver a tendência da métrica aplicável – perda, latência, tempo de resposta, utilização de largura de banda. Você pode alterar o intervalo de tempo da tendência usando o controle de tempo na parte superior do gráfico. 

Os gráficos de tendência mostram uma perspectiva histórica da métrica de desempenho. Alguns problemas de rede são temporários por natureza e seriam difíceis de detectar apenas examinando o estado atual da rede. Isso ocorre porque problemas podem surgir rapidamente e desaparecem antes que alguém perceba, apenas para aparecer em um momento posterior. Esses problemas transitórios também podem ser difíceis para os administradores de aplicativos, pois os problemas geralmente surgem como aumentos inexplicáveis de tempo de resposta, mesmo quando todos os componentes do aplicativo parecem ser executados normalmente. 

Você pode facilmente detectar esses tipos de problemas examinando um gráfico de tendência, em que o problema aparece como um aumento repentino na latência de rede ou perda de pacotes. Em seguida, você pode investigar o problema usando o gravador de estado da rede para exibir o instantâneo de rede e a topologia referentes a esse ponto no tempo quando o problema ocorreu. 

 
![Gráficos de tendência](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapa de topologia 

O NPM mostra a topologia de salto a salto de rotas entre o ponto de extremidade de origem e o de destino, em um mapa de topologia interativo. Você pode exibir o mapa de topologia, basta clicar no bloco **Topologia** no painel da solução ou clicar no link **Exibir topologia** nas páginas de detalhamento.  

O mapa de topologia exibe quantas rotas existem entre a origem e o destino e quais caminhos os pacotes de dados seguem. A latência que cada salto de rede acrescentou também pode ser vista. Todos os caminhos para os quais a latência total do caminho está acima do limite (definido na regra de monitoramento correspondente) são mostrados em vermelho.  

Ao clicar em um nó ou passar o mouse sobre ele no mapa de topologia, você verá as propriedades do nó, como o FQDN e o endereço IP. Clique em um salto para ver seu endereço IP. Você pode identificar o salto de rede problemático observando a latência acrescentada por ele. Você pode optar por filtrar rotas específicas usando os filtros no painel de ação recolhível. Você também pode simplificar as topologias de rede, ocultando os nós intermediários usando o controle deslizante no painel de ações. Você pode ampliar ou reduzir o mapa de topologia usando a roda do mouse. 

Observe que a topologia mostrada no mapa é a topologia de camada 3 e não contém conexões e dispositivos de camada 2. 

 
![Mapa de topologia](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Pesquisa do Log Analytics 

Todos os dados que são graficamente expostos por meio do painel do NPM e das páginas de busca detalhada também estão disponíveis de forma nativa na [pesquisa do Log Analytics](log-analytics-log-search-new.md). Você pode executar análises interativas de dados no repositório, correlacionar dados de origens diferentes, criar alertas personalizados, criar exibições personalizadas e exportar os dados para Excel, Power BI ou um link compartilhável. A área Consultas Comuns no painel tem algumas consultas úteis que você pode usar como ponto de partida para criar suas próprias consultas e relatórios. 

 

## <a name="provide-feedback"></a>Fornecer comentários 

**UserVoice** - Você pode postar suas ideias para recursos do Monitor de Desempenho de Rede nas quais você deseja trabalhar. Visite nossa  [página UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

**Junte-se a nossa coorte** - Estamos sempre interessados em novos clientes para a nossa coorte. Como parte do coorte, você consegue acesso antecipado a novos recursos e ajuda a melhorar o Monitor de Desempenho de Rede. Se estiver interessado em participar, preencha esta  [pesquisa rápida](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Próximas etapas 
- Saiba mais sobre o [Monitor de Desempenho](log-analytics-network-performance-monitor-performance-monitor.md), o [Monitor de Ponto de Extremidade de Serviço](log-analytics-network-performance-monitor-performance-monitor.md) e o [Monitor do ExpressRoute](log-analytics-network-performance-monitor-expressroute.md). 
