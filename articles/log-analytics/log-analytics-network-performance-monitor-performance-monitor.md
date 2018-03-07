---
title: "Recurso de Monitor de Desempenho na solução Monitor de Desempenho de Rede no Azure Log Analytics | Microsoft Docs"
description: "O recurso de Monitor de Desempenho no Monitor de Desempenho de Rede ajuda a monitorar a conectividade de rede entre vários pontos em sua rede, como implantações de nuvem e localizações locais, vários data centers e filiais, vários aplicativos/microserviços multiníveis críticos à missão."
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
ms.openlocfilehash: a90ab3bc857b704d9d94daf96d17611844abb1a6
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution---performance-monitoring"></a>Solução do Monitor de Desempenho de Rede - Monitoramento de Desempenho

O recurso de Monitor de Desempenho no [Monitor de Desempenho de Rede](log-analytics-network-performance-monitor.md) ajuda a monitorar a conectividade de rede entre vários pontos em sua rede, como implantações de nuvem e localizações locais, vários data centers e filiais, vários aplicativos/microserviços multiníveis críticos à missão. Com o Monitor de desempenho, você pode detectar problemas de rede antes de seus usuários reclamarem. As principais vantagens são: 

- Monitorar perda e latência entre vários sub-redes e definir alertas 
- Monitorar todos os caminhos (incluindo caminhos redundantes) na rede 
- Solucionar problemas de rede transitórios e pontuais, que são difíceis de replicar 
- Determinar um segmento específico na rede, que é responsável pelo desempenho degradado 
- Monitorar a integridade da rede, sem a necessidade de SNMP 


![Monitor de Desempenho de Rede](media/log-analytics-network-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Configuração
Para abrir a configuração do Monitor de Desempenho de Rede, abra a [solução Monitor de Desempenho de Rede](log-analytics-network-performance-monitor.md) e clique no botão **Configurar**.

![Configure o Monitor de Desempenho de Rede](media/log-analytics-network-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Criar novas redes

Uma rede em NPM é um contêiner lógico para sub-redes. Ele ajuda você a organizar o monitoramento da infraestrutura de rede de acordo com suas necessidades de monitoramento. Você pode criar uma Rede com um nome amigável e adicionar sub-redes a ele de acordo com a sua lógica de negócios. Por exemplo, você pode criar uma rede chamada Londres e adicionar todas as sub-redes em seu datacenter de Londres, ou criar uma rede chamada *ContosoFrontEnd* e adicionar todas as sub-redes que atendem ao front-end do aplicativo chamado Contoso para essa rede. A solução cria automaticamente uma rede padrão que contém todas as sub-redes descobertas no seu ambiente. Sempre que você criar uma rede, adicione uma sub-rede para ela. Essa sub-rede será removida da rede padrão. Se você excluir uma rede, todas as suas sub-redes serão retornadas automaticamente para a rede padrão. Assim, a rede Padrão age como o contêiner para todas as sub-redes que não estão contidas em nenhuma rede definida pelo usuário. Você não pode editar nem excluir a rede padrão. Ela sempre permanece no sistema. No entanto, você pode criar tantas redes personalizadas quantas forem necessárias. Na maioria dos casos, as sub-redes na organização serão organizadas em mais de uma rede e você deve criar uma ou mais redes para agrupar as sub-redes para sua lógica de negócios.

Para criar uma nova rede:


1. Clique na **guia Redes**.
1. Clique em  **Adicionar rede** e digite o nome e a descrição da rede. 
2. Selecione uma ou mais sub-redes e clique em **Adicionar**. 
3. Clique em **Salvar** para salvar a configuração. 


### <a name="create-monitoring-rules"></a>Crie regras de monitoramento 

O Monitor de Desempenho gera eventos de integridade quando o limite do desempenho de conexões de rede entre duas sub-redes ou entre duas redes é ultrapassado. Esses limites podem ser aprendidos automaticamente pelo sistema ou você pode fornecer limites personalizados. O sistema cria automaticamente uma regra Padrão, que gera um evento de integridade sempre que a perda ou a latência entre qualquer par de links de rede/sub-rede viola o limite aprendido pelo sistema. Isso ajudará a solução a monitorar a infraestrutura de rede enquanto você não tiver criado nenhuma regra de monitoramento explicitamente. Se a **regra padrão** estiver habilitada, todos os nós enviarão transações sintéticas para todos os outros nós que você tiver habilitado para monitoramento. A regra padrão é útil com redes pequenas, por exemplo, em um cenário no qual você tem um pequeno número de servidores que executam um microsserviço e você deseja garantir que o todos os servidores tenham conectividade entre si. 

>[!NOTE]
> É altamente recomendável que você desabilite a **regra padrão** e cria regras de monitoramento personalizadas, especialmente com grandes redes em que você está usando um grande número de nós para monitoramento. Isso reduzirá o tráfego gerado pela solução e ajudará você a organizar o monitoramento da rede. 

Crie regras de monitoramento de acordo com a sua lógica de negócios. Por exemplo, se você quiser monitorar o desempenho da conectividade de rede de dois escritórios para a matriz e, em seguida, agrupar todas as sub-redes no escritório 1 na rede O1, todas as sub-redes no escritório 2 na rede O2 e todas as sub-redes na matriz para a rede H. Crie duas regras de monitoramento – uma entre O1 e H e a outra entre O2 e H. 

Para criar regras de monitoramento personalizadas:

1. Clique em **Adicionar Regra** na guia **Monitor** e digite o nome da regra e uma descrição. 
2. Selecione nas listas o par de links de rede ou sub-rede para monitorar. 
3. Primeiro, selecione a rede na qual a(s) primeira(s) sub-rede(s) de seu interesse está(ão) contida(s), no menu suspenso de rede, e selecione a(s) sub-rede(s) no menu suspenso da sub-rede correspondente. Selecione **Todas as sub-redes** se desejar monitorar todas as sub-redes em um link de rede. Da mesma forma, selecione outra(s) sub-rede(s) de seu interesse. Você pode clicar em **Adicionar Exceção** para excluir o monitoramento de links de sub-rede específicos da seleção feita. 
4. Escolha entre os protocolos TCP e ICMP para executar transações sintéticas. 
5. Se não quiser criar eventos de integridade para os itens que você selecionou, desmarque **Ativar o monitoramento de integridade nos links cobertos por essa regra**. 
6. Escolha as condições de monitoramento. Você pode definir limites personalizados para geração de eventos de integridade digitando os valores de limite. Sempre que o valor da condição ultrapassar o limite selecionado para o par de rede/sub-rede selecionado, será gerado um evento de integridade. 
7. Clique em **Salvar** para salvar a configuração. 

Depois de salvar uma regra de monitoramento, você pode integrar essa regra ao Gerenciamento de Alertas clicando em **Criar Alerta**. Uma regra de alerta é criada automaticamente com a consulta de pesquisa e outros parâmetros necessários são preenchidos automaticamente. Usando uma regra de alerta, você pode receber alertas baseados em email, além de alertas existentes em NPM. Os alertas também podem disparar ações corretivas com runbooks ou podem integrar soluções de gerenciamento de serviço existentes usando webhooks. Você pode clicar em **Gerenciar Alerta** para editar as configurações de alerta. 

Agora você pode criar mais regras de Monitor de Desempenho ou mover para o painel de solução para começar a usar o recurso 

### <a name="choose-the-protocol"></a>Escolha o protocolo

O Monitor de Desempenho de Rede (NPM) usa transações sintéticas para calcular métricas de desempenho de rede como perda de pacote e latência de link. Para entender isso melhor, considere um agente NPM conectado a uma extremidade de um link de rede. Esse agente NPM envia pacotes de teste para um segundo agente NPM conectado a outra extremidade da rede. O segundo agente responde com pacotes de resposta. Esse processo é repetido algumas vezes. Medindo o número de respostas e o tempo necessário para receber cada resposta, o primeiro agente NPM avalia a latência de link e os descartes de pacotes. 

O formato, o tamanho e a sequência desses pacotes são determinados pelo protocolo que você escolheu ao criar regras de monitoramento. Com base no protocolo dos pacotes, os dispositivos de rede intermediários (roteadores, comutadores etc.) podem processar esses pacotes de maneira diferente. Consequentemente, a opção de protocolo afeta a precisão dos resultados. E a escolha de protocolo também determina se será necessário executar alguma etapa manual após implantar a solução NPM. 

O NPM permite que você escolha entre os protocolos TCP e ICMP para executar transações sintéticas. Se você escolher o ICMP quando criar uma regra de transação sintética, os agentes NPM usarão mensagens de eco ICMP para calcular a latência de rede e a perda de pacote. O Eco ICMP usa a mesma mensagem enviada pelo utilitário Ping convencional. Quando você usa o TCP como protocolo, os agentes NPM enviam um pacote TCP SYN pela rede. Isso é seguido pela conclusão de um handshake TCP e, em seguida, a remoção da conexão usando pacotes RST. 

Antes de escolher um protocolo para uso, considere as seguintes informações: 

**Descobrindo várias rotas de rede.**  O TCP é mais preciso ao descobrir várias rotas e exige menos agentes em cada sub-rede. Por exemplo, um ou dois agentes que usem TCP podem descobrir todos os caminhos redundantes entre sub-redes. Entretanto, você precisa de vários agentes usando o ICMP para alcançar resultados semelhantes. Usando o ICMP e, se você tiver um número de rotas entre duas sub-redes, precisará de mais de 5N agentes na sub-rede de origem ou de destino. 

**Precisão dos resultados.** Roteadores e comutadores tendem a atribuir a prioridade mais baixa para pacotes de eco ICMP em comparação com pacotes TCP. Em determinadas situações, quando os dispositivos de rede estão sobrecarregados, os dados obtidos pelo TCP refletem mais a perda e a latência apresentadas pelos aplicativos. Isso ocorre porque a maioria do tráfego do aplicativo flui pelo TCP. Nesses casos, o ICMP fornece resultados menos precisos em comparação ao TCP. 

**Configuração do firewall.** O protocolo TCP exige que os pacotes TCP sejam enviados a uma porta de destino. A porta padrão usada por agentes NPM é 8084, mas você pode alterar isso quando configurar agentes. Portanto, você precisa garantir que seus firewalls de rede ou regras de NSG (no Azure) estejam permitindo o tráfego na porta. Você também precisa certificar-se de que o firewall local nos computadores em que os agentes são instalados esteja configurado para permitir o tráfego nesta porta. Você pode usar scripts do PowerShell para configurar regras de firewall em computadores que executam o Windows, no entanto, você precisa configurar manualmente o firewall da rede. Por outro lado, o ICMP não funciona usando a porta. Na maioria dos cenários de negócios, o tráfego do ICMP é permitido por meio de firewalls para que você use ferramentas de diagnóstico de rede como o utilitário Ping. Portanto, se você puder executar Ping de um computador para o outro, poderá usar o protocolo ICMP sem ter que configurar firewalls manualmente. 

>[!NOTE] 
> Alguns firewalls podem bloquear o ICMP, o que pode levar à retransmissão resultando em um grande número de eventos em seu sistema de gerenciamento de evento e informações de segurança. Verifique se o protocolo que você escolher não está bloqueado por um firewall de rede/NSG, caso contrário o NPM não é capaz de monitorar o segmento de rede. Por isso, recomendamos que você use TCP para monitoramento. Você deve usar ICMP nos cenários em que não é possível usar o TCP, como quando: 
>
> - Você está usando nós de cliente com base em Windows, já que soquetes brutos TCP não são permitidos no cliente Windows
> - Seu firewall de rede/NSG bloqueia o TCP
> - Como mudar o protocolo 

Se você optar por usar o ICMP durante a implantação, poderá mudar para TCP a qualquer momento, editando a regra de monitoramento padrão:

1. Navegue até **Desempenho da Rede** > **Monitorar** > **Configurar** > **Monitorar** e, em seguida, clique em  **Regra padrão**. 
2. Role até a seção **Protocolo** e selecione o protocolo que você deseja usar. 
3. Clique em **Salvar** para aplicar a configuração. 

Mesmo se a regra padrão for usar um protocolo específico, você poderá criar novas regras com um protocolo diferente. Você pode até mesmo criar uma combinação de regras em que algumas das regras usem ICMP e a outra use TCP. 

## <a name="walkthrough"></a>Passo a passo 

Agora que você leu sobre o Monitor de Desempenho, vejamos uma investigação simples sobre a causa raiz de um evento de integridade.  

No painel de solução, você observará que há um evento de integridade - um link de rede não está íntegro. Você decide investigar o problema e clica no bloco **Links de rede que estão sendo monitorados**.

Na página de busca detalhada, você observa que o link de rede **DMZ2 DMZ1** não está íntegro. Você clica no link **Exibir links de sub-rede** para este link de rede. 


A página de busca detalhada mostra todos os links de sub-rede no link de rede **DMZ2-DMZ1**. Você observará que, para ambos os links de sub-rede, a latência ultrapassou o limite, tornando o link de rede não íntegro. Você também pode ver as tendências de latência de ambos os links de sub-rede. Você pode usar o controle de seleção de tempo do grafo para se concentrar no intervalo de tempo necessário. Você pode ver a hora do dia em que a latência atingiu seu pico. Você pode pesquisar mais tarde os logs desse período de tempo para investigar o problema. Clique em **Exibir links do nó** para continuar com o drill down. 
 
 ![Links de sub-rede](media/log-analytics-network-performance-monitor/subnetwork-links.png) 

De forma semelhante à página anterior, a página de busca detalhada do link de sub-rede específico lista seus links de nós constituintes. Você pode executar ações semelhantes aqui, como fez na etapa anterior. Clique em **Exibir topologia** para exibir a topologia entre os dois nós. 
 
 ![Links de nó](media/log-analytics-network-performance-monitor/node-links.png) 

Todos os caminhos entre os dois nós selecionados são criados em gráfico no mapa de topologia. Você pode visualizar a topologia de salto a salto das rotas entre dois nós no mapa de topologia. Isso lhe dá uma visão clara de quantas rotas existem entre os dois nós e quais caminhos os pacotes de dados estão adotando. Os gargalos de desempenho de rede são marcados em vermelho. Você pode localizar uma conexão de rede ou um dispositivo de rede com defeito observando elementos em vermelho no mapa de topologia. 

 ![Painel de Topologia](media/log-analytics-network-performance-monitor/topology-dashboard.png) 

A perda, a latência e o número de saltos em cada caminho podem ser analisados no painel **Ação**. Use a barra de rolagem para exibir os detalhes dos caminhos não íntegros. Use os filtros para selecionar os caminhos com o nó íntegro, de forma que somente a topologia para os caminhos selecionados seja criada em gráfico. Você pode usar a roda do mouse para ampliar ou reduzir o mapa de topologia. 

Na imagem abaixo, você pode ver claramente a causa das áreas problemáticas da seção específica da rede, observando os caminhos e saltos na cor vermelha. Clicar em um nó no mapa de topologia revela as propriedades do nó, incluindo o FQDN e o endereço IP. Clicar em um salto mostra o endereço IP do nó. 
 
![Painel de Topologia](media/log-analytics-network-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>Próximas etapas
* [Pesquisar logs](log-analytics-log-searches.md) para exibir registros de dados de desempenho de rede detalhados.
