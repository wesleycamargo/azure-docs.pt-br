---
title: Solução Monitor de Desempenho de Rede no Azure Log Analytics | Microsoft Docs
description: Use recurso de Gerenciador de Ponto de Extremidade de Serviço no Monitor de Desempenho de Rede para monitorar a conectividade de rede para qualquer ponto de extremidade que tem uma porta TCP aberta.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: b21d711e59ddc762eaf72f49e501d9f324d75105
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="service-endpoint-monitor"></a>Monitor de Ponto de Extremidade de Serviço

Você pode usar o recurso de Gerenciador de Ponto de Extremidade de Serviço no [Monitor de Desempenho de Rede](log-analytics-network-performance-monitor.md) para monitorar a conectividade de rede para qualquer ponto de extremidade que tem uma porta TCP aberta. Esses pontos de extremidade incluem sites, aplicativos SaaS, os aplicativos PaaS e bancos de dados SQL. 

Você pode executar as seguintes funções com Monitor de Ponto de Extremidade de Serviço: 

- Monitore a conectividade de rede para seus aplicativos e serviços de rede de vários locais ou de ramificação. Aplicativos e serviços de rede incluem o Office 365, Dynamics CRM, aplicativos de linha de negócios internos e bancos de dados SQL.
- Use testes internos para monitorar a conectividade de rede para pontos de extremidade do Office 365 e Dynamics365. 
- Determinar o tempo de resposta, latência de rede e perda de pacotes apresentados ao se conectar ao ponto de extremidade.
- Determinar se o desempenho insatisfatório do aplicativo é devido à rede ou devido a algum problema no final do provedor de aplicativo.
- Identifica pontos de acesso na rede que podem estar causando o desempenho insatisfatório do aplicativo exibindo a latência contribuída por cada salto em um mapa de topologia.


![Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Configuração 
Para abrir a configuração do Monitor de Desempenho de Rede, abra a [solução Monitor de Desempenho de Rede](log-analytics-network-performance-monitor.md) e selecione **Configurar**.

![Configure o Monitor de Desempenho de Rede](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Configurar agentes do Operations Management Suite para monitoramento
Habilite as seguintes regras de firewall em nós usado para o monitoramento para que a solução possa descobrir a topologia de seus nós para o ponto de extremidade de serviço: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action 
```

### <a name="create-service-endpoint-monitor-tests"></a>Crie teste de Monitor de Ponto de Extremidade de Serviço 

Comece criando os testes para monitorar a conectividade de rede aos pontos de extremidade de serviço.

1. Selecione **Monitor de Ponto de Extremidade de Serviço** na guia.
2. Selecione **Adicionar teste** e insira o nome do teste e a descrição. 
3. Selecione o tipo de teste:<br>

    * Selecione o **Web** para monitorar a conectividade a um serviço que responde às solicitações HTTP/S, como outlook.office365.com ou bing.com.<br>
    * Selecione a **Rede** para monitorar a conectividade a um serviço que responde à solicitação TCP, mas não responde à solicitação HTTP/S, como um SQL server, servidor FTP ou porta SSH etc. 
4. Se você não deseja executar medidas de rede, como latência de rede, perda de pacotes e descoberta de topologia, desmarque a caixa de seleção **Executar medidas de rede**. Mantenha selecionado para obter o máximo benefício da funcionalidade. 
5. Em **Destino**, insira o endereço IP/FQDN/URL para o qual você deseja monitorar a conectividade de rede.
6. Em **Número da porta**, insira o número da porta do serviço de destino. 
7. Em **Frequência de Teste**, insira um valor para a frequência com a qual você deseja que o teste execute. 
8. Selecione os nós do qual você deseja monitorar a conectividade de rede para o serviço. 

    >[!NOTE]
    > Para nós de servidor Windows, o recurso usa solicitações com base em TCP para executar as medidas de rede. Para nós de clientes Windows, o recurso usa solicitações com base em ICMP para executar as medidas de rede. Em alguns casos, o aplicativo de destino bloqueia solicitações ICMP quando os nós forem baseados em cliente Windows. A solução não pode realizar medidas de rede. É recomendável usar nós de servidores Windows nesses casos. 

9. Se não quiser criar eventos de integridade para os itens que você selecionou, desmarque **Ativar o Monitoramento de Integridade nos destinos cobertos por esse teste**. 
10. Escolha as condições de monitoramento. Você pode definir limites personalizados para geração de eventos de integridade digitando os valores de limite. Sempre que o valor da condição ultrapassar o limite selecionado para o par de rede ou sub-rede selecionado, será gerado um evento de integridade. 
11. Selecione **Salvar** para salvar a configuração. 

    ![Configurações de teste do Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Passo a passo 

Vá até a visualização do painel do Monitor de Desempenho de Rede. Para obter um resumo da integridade de testes diferentes que você criou, olhe a página **Monitor de Ponto de Extremidade de Serviço**. 

![Página Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

Selecione o bloco para visualizar detalhes dos testes na página de **Testes**. Na tabela à esquerda, você pode exibir a integridade de point-in-time e o valor do tempo de resposta do serviço, latência de rede e perda de pacotes para todos os testes. Use o controle de Gravador de Estado da Rede para exibir o instantâneo de rede em outro momento no passado. Selecione o teste na tabela que você deseja investigar. Nos gráficos no painel à direita, você pode exibir as tendências históricas de valores de perda, latência e tempo de resposta. Selecione o link Clique **Detalhes de Teste** para exibir o desempenho de cada nó.

![Testes de Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

No modo de exibição **nós de teste**, você pode observar a conectividade de rede de cada nó. Selecione o nó que tem a degradação do desempenho. Este é o nó em que o aplicativo é observado em execução lenta.

Determine se o desempenho insatisfatório do aplicativo é devido à rede ou devido a algum problema no final do provedor do aplicativo, observando a correlação entre o tempo de resposta do aplicativo e a latência de rede. 

* **Problema de aplicativo:** um aumento no tempo de resposta, mas a latência de rede é sugere que a rede está funcionando corretamente e o problema é devido a um problema no final do aplicativo. 

    ![Problema de aplicativo do Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

* **Problema de Rede:** um aumento no tempo de resposta é acompanhado de um aumento correspondente na latência de rede que o aumento no tempo de resposta é devido a um aumento na latência de rede. 

    ![Problema de Rede de Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

Depois de determinar que o problema é devido à rede, selecione a exibição **Topologia** para identificar o salto problemático no mapa de topologia. Um exemplo é mostrado na imagem a seguir. Fora dos 105ms de latência total entre o nó e o ponto de extremidade do aplicativo, 96ms é devido ao salto marcado em vermelho. Depois de identificar o salto problemático, você pode adotar uma ação corretiva. 

![Testes de Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostics 

Se você observar uma anormalidade, siga estas etapas:

* Se o tempo de resposta do serviço, perda de rede e latência são mostrados como NA, pode ser devido a um ou mais dos seguintes motivos:

    - O aplicativo está inoperante.
    - O nó usado para verificar a conectividade de rede para o serviço está inoperante.
    - O destino inserido na configuração de teste está incorreto.
    - O nó não tem nenhuma conectividade de rede.

* Se um tempo de resposta do serviço válido é exibido mas a perda de rede e latência são mostrados como NA, pode ser devido a um ou mais dos seguintes motivos:

    - Se o nó usado para verificar a conectividade de rede para o serviço for máquina cliente Windows, o serviço de destino está bloqueando as solicitações ICMP ou um firewall de rede está bloqueando as solicitações ICMP provenientes do nó.
    - A caixa de seleção **Executar medidas de rede** está em branco na configuração de teste. 

* Se o tempo de resposta do serviço for NA, mas a perda da rede e latência são válidas, isso sugere que o serviço de destino não é um aplicativo da web. Edite a configuração de teste e escolha o tipo de teste como **Rede** em vez de **Web**. 

* Se o aplicativo está sendo executado lentamente, determine se o desempenho insatisfatório do aplicativo é devido à rede ou devido a algum problema no final do provedor de aplicativo.


## <a name="next-steps"></a>Próximas etapas
[Pesquisar logs](log-analytics-log-searches.md) para exibir registros de dados de desempenho de rede detalhados.
