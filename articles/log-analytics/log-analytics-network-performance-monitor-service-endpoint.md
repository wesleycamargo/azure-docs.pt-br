---
title: "Solução Monitor de Desempenho de Rede no Azure Log Analytics | Microsoft Docs"
description: "O recurso de Gerenciador de Ponto de Extremidade de Serviço no Monitor de Desempenho de Rede permite monitorar a conectividade de rede para qualquer ponto de extremidade que tem uma porta TCP aberta."
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
ms.openlocfilehash: ba19a4fc24668bff27c961b5b415f840d1132a34
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="service-endpoint-monitor"></a>Monitor de Ponto de Extremidade de Serviço

O recurso de [Monitor de Ponto de Extremidade de Serviço no Monitor](log-analytics-network-performance-monitor.md) de Desempenho de Rede permite monitorar a conectividade de rede para qualquer ponto de extremidade que tem uma porta TCP aberta. Esses pontos de extremidade incluem sites, aplicativos SaaS, os aplicativos PaaS e bancos de dados SQL. 

Você pode executar as seguintes funções com **Monitor de Ponto de Extremidade de Serviço**: 

- Monitorar a conectividade de rede para seus aplicativos e serviços de rede (como o Office 365, Dynamics CRM, linha interna de aplicativos de negócios, o banco de dados SQL, etc) de vários escritórios/filiais 
- Testes internos para monitorar a conectividade de rede para pontos de extremidade do Office 365 e Dynamics365 
- Determinar o tempo de resposta, latência de rede, perda de pacotes apresentados ao se conectar ao ponto de extremidade 
- Determinar se o desempenho insatisfatório do aplicativo é devido à rede ou devido a algum problema no final do provedor de aplicativo 
- Identifica pontos de acesso na rede que podem estar causando o desempenho insatisfatório do aplicativo exibindo a latência contribuída por cada salto em um mapa de topologia. 


![Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Configuração 
Para abrir a configuração do Monitor de Desempenho de Rede, abra a [solução Monitor de Desempenho de Rede](log-analytics-network-performance-monitor.md) e clique no botão **Configurar**.

![Configure o Monitor de Desempenho de Rede](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-oms-agents-for-the-monitoring"></a>Configure agentes do OMS para o monitoramento.  
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

Comece criando os testes para monitorar a conectividade de rede aos pontos de extremidade de serviço 

1. Clique na guia **Monitor de Ponto de Extremidade de Serviço**.
2. Clique em **Adicionar teste** e insira o nome do teste e a descrição. 
3. Selecione o tipo de teste:<br>Selecione o **teste da Web** se você estiver monitorando conectividade a um serviço que responde às solicitações HTTP/S, como outlook.office365.com, bing.com.<br>Selecione o **teste de rede** se você estiver monitorando conectividade a um serviço que responde à solicitação TCP, mas não responde à solicitação HTTP/S, como um SQL server, servidor FTP, porta SSH etc. 
4. Se você não quiser executar medidas de rede (latência de rede, perda de pacotes, descoberta de topologia), em seguida, desmarque a caixa de texto. Recomendamos que você mantenha marcado para obter o máximo benefício da capacidade. 
5. Insira o endereço IP/FQDN/URL de destino para o qual você deseja monitorar a conectividade de rede.  
6. Insira o número da porta do serviço de destino. 
7. Insira a frequência em que você deseja executar o teste. 
8. Selecione os nós do qual você deseja monitorar a conectividade de rede para o serviço. 

    >[!NOTE]
    > Para nós de servidor Windows, o recurso usa solicitações com base em TCP para executar as medidas de rede. Para nós de clientes Windows, o recurso usa solicitações com base em ICMP para executar as medidas de rede. Em alguns casos, o aplicativo de destino bloqueia solicitação com base em ICMP de entrada devido a que quando os nós são baseados no cliente Windows a solução não é capaz de realizar as medidas de rede. Portanto, é recomendável usar nós de servidores Windows nesses casos. 

9. Se não quiser criar eventos de integridade para os itens que você selecionou, desmarque **Ativar o monitoramento de integridade nos destinos cobertos por esse teste**. 
10. Escolha as condições de monitoramento. Você pode definir limites personalizados para geração de eventos de integridade digitando os valores de limite. Sempre que o valor da condição ultrapassar o limite selecionado para o par de rede/sub-rede selecionado, será gerado um evento de integridade. 
11. Clique em **Salvar** para salvar a configuração. 

 ![Configuração do Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Passo a passo 

Mover para a exibição de painel de Monitoramento do Desempenho de Rede e observe a página **Monitor de Ponto de Extremidade de Serviço** para obter um resumo da integridade de diferentes testes que você criou.  

![Página Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

Clique no bloco para fazer busca detalhada e exibir detalhes dos testes na página de **Testes**. Na tabela LHS, você pode exibir a integridade de point-in-time e o valor do tempo de resposta do serviço, latência de rede e perda de pacotes para todos os testes. Você pode usar o controle de Gravador de Estado da Rede para exibir o instantâneo de rede em outro momento no passado. Clique no teste na tabela que você deseja investigar. Você pode exibir as tendências históricas de valores de perda, latência e tempo de resposta dos gráficos no painel de RHS. Clique no link de detalhes de teste para exibir o desempenho de cada nó. 

![Testes de Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

No modo de exibição **nós de teste**, você pode observar a conectividade de rede de cada nó. Clique no nó com a degradação de desempenho.  Este é o nó de onde o aplicativo é observado em execução lenta. 

Determine se o desempenho insatisfatório do aplicativo é devido à rede ou devido a algum problema no final do provedor do aplicativo, observando a correlação entre o tempo de resposta do aplicativo e a latência de rede. 

**Problema de aplicativo:** se há um aumento no tempo de resposta, mas a latência de rede é consistente, isso sugere que a rede está funcionando corretamente e o problema é devido a um problema no final do aplicativo.  

![Problema de aplicativo do Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

**Problema de Rede:** se um aumento no tempo de resposta é acompanhado de um aumento correspondente na latência de rede, isso sugere que o aumento no tempo de resposta é devido a um aumento na latência de rede.  

![Problema de Rede de Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

Depois de determinar que o problema é devido à rede, você pode clicar no link de exibição **Topologia** para identificar o salto problemático no Mapa de Topologia. Por exemplo, na imagem abaixo, você verá que dos 105ms de latência total entre o nó e o ponto de extremidade do aplicativo, 96ms é devido ao salto marcado em vermelho. Depois de identificar o salto problemático, você pode adotar uma ação corretiva.  

![Testes de Monitor de Ponto de Extremidade de Serviço](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostics 

Se você observar uma anormalidade, siga estas etapas:

Se o tempo de resposta do serviço, perda de rede e latência são mostrados como NA, isso pode ser devido a um ou mais dos seguintes motivos:
- O aplicativo está inoperante.
- O nó que está sendo usado para verificar a conectividade de rede para o serviço está inoperante.
- O destino inserido na configuração de teste está incorreto.
- O nó não tem nenhuma conectividade de rede.

Se um tempo de resposta do serviço válido é exibido mas a perda de rede e latência são mostrados como NA, isso pode ser devido a um ou mais dos seguintes motivos:
- Se o nó usado para verificar a conectividade de rede para o serviço for máquina cliente Windows, o serviço de destino está bloqueando as solicitações ICMP ou um firewall de rede está bloqueando as solicitações ICMP provenientes do nó.
- A caixa de seleção para **Executar medidas de rede** foi desmarcada na configuração de teste. 

Se o tempo de resposta do serviço for NA, mas perda da rede e latência são válidas, isso sugere que o serviço de destino não é um aplicativo da web. Edite a configuração de teste e escolha o tipo de teste como teste de rede em vez de teste da web. 

Se o aplicativo está sendo executado lentamente, você deve determinar se o desempenho insatisfatório do aplicativo é devido à rede ou devido a algum problema no final do provedor de aplicativo.


## <a name="next-steps"></a>Próximas etapas
* [Pesquisar logs](log-analytics-log-searches.md) para exibir registros de dados de desempenho de rede detalhados.
