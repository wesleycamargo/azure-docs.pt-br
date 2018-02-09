---
title: "Configurar o Monitor de Desempenho de Rede para circuitos do Azure ExpressRoute (versão prévia) | Microsoft Docs"
description: "Configure o NPM para circuitos do Azure ExpressRoute. (Visualização)"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: cherylmc
ms.openlocfilehash: 63160bc8f334b975ade8b35ce809578ad3a5b3fa
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="configure-network-performance-monitor-for-expressroute-preview"></a>Configurar o Monitor de Desempenho de Rede para ExpressRoute (versão prévia)

O Monitor de Desempenho de Rede (NPM) é uma solução de monitoramento de rede baseado em nuvem que monitora a conectividade entre implantações de nuvem do Azure e instalações locais (Filiais etc.). O NPM faz parte do Microsoft Operations Management Suite (OMS). O NPM agora oferece uma extensão para o ExpressRoute, que permite que você monitore o desempenho da rede sobre os circuitos do ExpressRoute, que são configurados para usar o Emparelhamento Privado. Ao configurar o NPM para ExpressRoute, você poderá detectar problemas de rede para identificar e eliminar.

Você pode:

* Monitorar perda e latência entre vários VNets e definir alertas

* Monitorar todos os caminhos (incluindo caminhos redundantes) na rede

* Solucionar problemas de rede transitórios e pontuais que são difíceis de replicar

* Ajudar a determinar um segmento específico na rede que é responsável pelo desempenho degradado

* Obter a taxa de transferência por rede virtual (se você tiver agentes instalados em cada VNet)

* Consulte o estado do sistema do ExpressRoute de um ponto anterior no tempo

## <a name="regions"></a>Regiões com suporte

Você pode monitorar os circuitos ExpressRoute em qualquer parte do mundo usando um espaço de trabalho que está hospedado em uma das seguintes regiões:

* Europa Ocidental 
* Leste dos EUA 
* Sudeste da Ásia 
* Sudeste da Austrália

## <a name="workflow"></a>Fluxo de trabalho

Agentes de monitoramento são instalados em vários servidores, locais e no Azure. Os agentes se comunicam entre si, mas não enviam dados, enviam pacotes de handshake de TCP. A comunicação entre os agentes permite que o Azure mapeie a topologia de rede e o caminho que o tráfego pode levar.

1. Crie um Espaço de trabalho do NPM em uma das [regiões com suporte](#regions).
2. Instalar e configurar agentes de software: 
    * Instale agentes de monitoramento nos servidores locais e nas VMs do Azure.
    * Defina as configurações nos servidores de agente de monitoramento para permitir que os agentes de monitoramentos se comuniquem. (Abra as portas do firewall etc.)
3. Configure as regras de grupo de segurança de rede (NSG) para permitir que o agente de monitoramento instalado nas VMs do Azure se comunique com os agentes de monitoramento local.
4. Solicite a inclusão do seu Espaço de trabalho do NPM na lista de permissões.
5. Configurar monitoramento: detecte automaticamente e gerencie as redes que estão visíveis no NPM.

Se você já estiver usando o Monitor de Desempenho de Rede para monitorar outros objetos ou serviços e já tiver o Espaço de Trabalho em uma das regiões com suporte, pode ignorar as Etapas 1 e 2 e começar a configuração pela Etapa 3.

## <a name="configure"></a>Etapa 1: criar um espaço de trabalho

1. No [Portal do Azure](https://portal.azure.com), pesquise o 'Monitor de Desempenho de Rede' na lista de serviços do **Marketplace**. Na volta, clique para abrir a página **Monitor de Desempenho de Rede**.

  ![portal](.\media\how-to-npm\3.png)<br><br>
2. Na parte inferior da página principal **Monitor de Desempenho de Rede**, clique em **Criar** para abrir a página **Monitor de Desempenho de Rede – Criar nova solução**. Clique em **Espaço de Trabalho do OMS – Selecionar um espaço de trabalho** para abrir a página de Espaços de Trabalho. Clique em **+ Criar Novo Espaço de Trabalho** para abrir a página de Espaço de Trabalho.
3. Na página **Espaço de Trabalho do OMS**, selecione **Criar Novo** e defina as seguintes configurações:

  * Espaço de Trabalho do OMS: digite um nome para o Espaço de Trabalho.
  * Assinatura: se você tiver várias assinaturas, selecione aquela que você deseja associar ao novo Espaço de Trabalho.
  * Grupo de recursos: crie um grupo de recursos ou use um existente.
  * Local – você deve selecionar uma [região com suporte](#regions).
  * Tipo de preço: selecione ‘Gratuito’
  
  >[!NOTE]
  >O circuito ExpressRoute poderia estar em qualquer lugar no mundo e não precisa estar na mesma região que o Espaço de trabalho.
  >


  ![espaço de trabalho](.\media\how-to-npm\4.png)<br><br>
4. Clique em **OK** para salvar e implantar o modelo de configurações. Depois que o modelo for validado, clique em **Criar** para implantar o Espaço de Trabalho.
5. Depois que o Espaço de Trabalho for implantado, navegue até o recurso **NetworkMonitoring(name)** que você criou. Valide as configurações e clique em **Solução requer configuração adicional**.

  ![configuração adicional](.\media\how-to-npm\5.png)
6. Na página **Bem-vindo ao Monitor de Desempenho de Rede**, selecione **Usar TCP para transações sintéticas** e, em seguida, clique em **Enviar**. As transações de TCP são usadas somente para fazer e interromper a conexão. Nenhum dado é enviado por essas conexões de TCP.

  ![TCP para transações sintéticas](.\media\how-to-npm\6.png)

## <a name="agents"></a>Etapa 2: instalar e configurar agentes

### <a name="download"></a>2.1: baixar o arquivo de instalação do agente

1. Na **página de Configuração do Monitor de Desempenho de Rede – Configuração de TCP** para seu recurso, na seção **Instalar Agentes do OMS**, clique no agente que corresponde ao processador do servidor e baixe o arquivo de instalação.

  >[!NOTE]
  >O agente deve ser instalado em um servidor Windows (2008 SP1 ou posterior). Não há suporte para o monitoramento de circuitos do ExpressRoute usando o sistema operacional de área de trabalho do Windows e o sistema operacional Linux. 
  >
  >
2. Em seguida, copie a **ID do Espaço de Trabalho** e a **Chave Primária** para o Bloco de Notas.
3. Na seção **Configurar Agentes**, baixe o Script do Powershell. O script do PowerShell ajuda você a abrir a porta de firewall relevante para as transações de TCP.

  ![Script do PowerShell](.\media\how-to-npm\7.png)

### <a name="installagent"></a>2.2: instale um agente de monitoramento em cada servidor de monitoramento

É recomendável que você instale pelo menos dois agentes em cada lado da conexão de ExpressRoute (ou seja, no local, VNETs do Azure) para redundância. Use as etapas a seguir para instalar os agentes:

1. Execute a **Instalação** para instalar o agente em cada servidor que deseja usar para o monitoramento de ExpressRoute. O servidor utilizado no monitoramento pode ser uma VM ou local e deve ter acesso à Internet. É necessário instalar pelo menos um agente local e um agente em cada segmento de rede que você deseja monitorar no Azure.
2. Na página de **Boas-vindas**, clique em **Avançar**.
3. Na página **Termos de Licença**, leia a licença e clique em **Aceito**.
4. Na página **Pasta de Destino**, altere ou mantenha a pasta de instalação padrão e clique em **Avançar**.
5. Na página **Opções de Instalação do Agente**, é possível escolher a opção de conectar o agente ao Azure Log Analytics (OMS) ou ao Operations Manager. Ou, você poderá deixar as opções em branco se quiser configurar o agente mais tarde. Após fazer suas seleções, clique em **Avançar**.

  * Se você optar por conectar-se ao **Azure Log Analytics (OMS)**, cole a **ID do Espaço de Trabalho** e a **Chave do Espaço de Trabalho** (Chave Primária) que você copiou para o Bloco de Notas na seção anterior. Em seguida, clique em **Avançar**.

    ![ID e a Chave](.\media\how-to-npm\8.png)
  * Se você optar por conectar-se ao **Operations Manager**, na página **Configuração de Grupo de Gerenciamento**, digite o **Nome do Grupo de Gerenciamento**, **Servidor de Gerenciamento** e **Porta do Servidor de Gerenciamento**. Em seguida, clique em **Avançar**.

    ![Operations Manager](.\media\how-to-npm\9.png)
  * Na página **Conta de Ação de Agente**, escolha a conta **Sistema Local** ou a **Conta de Computador Local ou Domínio**. Em seguida, clique em **Avançar**.

    ![Conta](.\media\how-to-npm\10.png)
6. Na página **Pronto para Instalar**, examine suas escolhas e clique em **Instalar**.
7. Na página **Configuração concluída com êxito**, clique em **Concluir**.
8. Após concluir, o Microsoft Monitoring Agent aparecerá no Painel de Controle. É possível examinar sua configuração e verificar se o agente está conectado ao OMS (Operational Insights). Quando conectado ao OMS, o agente exibe uma mensagem dizendo: **o Microsoft Monitoring Agent conectou-se com êxito ao serviço do Microsoft Operations Management Suite**.

### <a name="proxy"></a>2.3: definir configurações de proxy (opcional)

Caso esteja usando um proxy da Web para acessar a Internet, utilize as etapas a seguir para definir configurações de proxy para o Microsoft Monitoring Agent. Execute essas etapas para cada servidor. Se você tiver vários servidores que precisa configurar, talvez seja mais fácil usar um script para automatizar esse processo. Se tiver, consulte [Definir configurações de proxy para o Microsoft Monitoring Agent usando um script](../log-analytics/log-analytics-windows-agent.md).

Para definir configurações de proxy para o Microsoft Monitoring Agent usando o Painel de Controle:

1. Abrir o **Painel de Controle**.
2. Abra o **Microsoft Monitoring Agent**.
3. Clique na guia **Configurações de Proxy** .
4. Selecione **Usar um servidor proxy** e digite a URL e o número da porta, se for necessário. Se o servidor proxy requer autenticação, digite o nome de usuário e senha para acessar o servidor proxy.

  ![proxy](.\media\how-to-npm\11.png)

### <a name="verifyagent"></a>2.4: verifique a conectividade do agente

É possível verificar facilmente se os agentes estão se comunicando.

1. Em um servidor com o agente de monitoramento, abra o **Painel de Controle**.
2. Abra o **Microsoft Monitoring Agent**.
3. Clique na guia **Azure Log Analytics (OMS)**.
4. Na coluna de **Status**, você verá que o agente se conectou com êxito ao serviço do Operations Management Suite.

  ![status](.\media\how-to-npm\12.png)

### <a name="firewall"></a>2.5: abrir as portas do firewall nos servidores do agente de monitoramento

Para usar o protocolo TCP, você deve abrir as portas de firewall para garantir que os agentes de monitoramento possam se comunicar.

É possível executar o script do PowerShell, que cria as chaves do Registro exigidas pelo Monitor de Desempenho de Rede e também cria regras de Firewall do Windows para permitir que os agentes de monitoramento criem conexões TCP entre si. As chaves do Registro criadas pelo script também especificam se é preciso registrar os logs de depuração e o caminho para o arquivo de logs. Também é definida a porta TCP de agente usada para comunicação. Os valores dessas chaves são definidos automaticamente pelo script. Portanto, você não deve alterar manualmente as chaves.

A porta 8084 é aberta por padrão. Você pode usar uma porta personalizada fornecendo o parâmetro 'portNumber' para o script. No entanto, caso faça isso, você deverá especificar a mesma porta para todos os servidores nos quais o script é executado.

>[!NOTE]
>O script do PowerShell 'EnableRules' configura regras de Firewall do Windows somente no servidor em que o script é executado. Se tiver um firewall de rede, você deverá verificar se ele permite o tráfego destinado à porta TCP usada pelo Monitor de Desempenho de Rede.
>
>

Nos servidores do agente, abra uma janela do PowerShell com privilégios administrativos. Execute o script do PowerShell [EnableRules](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) (que você baixou anteriormente). Não use nenhum parâmetro.

  ![PowerShell_Script](.\media\how-to-npm\script.png)

## <a name="opennsg"></a>Etapa 3: configurar regras do grupo de segurança de rede

Para servidores do agente de monitoramento que estão no Azure, você deve configurar regras do NSG (grupo de segurança de rede) para permitir o tráfego TCP em uma porta usada pelo NPM para transações sintéticas. A porta padrão é 8084. Isso permite que um agente de monitoramento instalado na VM do Azure se comunique com um agente de monitoramento local.

Para obter mais informações sobre os NSG, consulte [Grupos de Segurança de Rede](../virtual-network/virtual-networks-create-nsg-arm-portal.md).

>[!NOTE]
>Certifique-se de que você instalou os agentes (o agente do servidor local e o agente do servidor do Azure) e executou o script do PowerShell antes de continuar com esta etapa.
>
>


## <a name="setupmonitor"></a>Etapa 4: configurar NPM para monitoramento de ExpressRoute

>[!WARNING]
>Não continue até que seu Espaço de Trabalho esteja na lista de permissões e que você receba um email de confirmação.
>
>

Depois de concluir as seções anteriores e verificar que você está na lista de permissões, é possível definir o monitoramento.

1. Navegue até o bloco de visão geral do Monitor de Desempenho de Rede ao acessar a página **Todos os Recursos** e ao clicar no Espaço de Trabalho de NPM na lista de permissões.

  ![espaço de trabalho de NPM](.\media\how-to-npm\npm.png)
2. Clique no bloco de visão geral do **Monitor de Desempenho de Rede** para exibir o painel. O painel contém uma página do ExpressRoute, que mostra que o ExpressRoute está em um 'estado não configurado'. Clique em **Instalação de Recurso** para abrir a página de configuração do Monitor de Desempenho de Rede.

  ![configuração de recurso](.\media\how-to-npm\npm2.png)
3. Na página de configuração, navegue até a guia 'Emparelhamentos do ExpressRoute', localizada no painel esquerdo. Clique em **Descobrir Agora**.

  ![descobrir](.\media\how-to-npm\13.png)
4. Quando a descoberta estiver concluída, você verá as regras para o nome do Circuito exclusivo e o nome do VNet. Inicialmente, essas regras estão desabilitadas. Habilite as regras e, em seguida, selecione os agentes de monitoramento e os valores de limite.

  ![regras](.\media\how-to-npm\14.png)
5. Depois de habilitar as regras e selecionar os valores e os agentes que deseja monitorar, há uma espera de aproximadamente 30 a 60 minutos para os valores começarem a preencher e os blocos de **Monitoramento do ExpressRoute** se tornarem disponíveis. Depois de ver os blocos de monitoramentos, seus circuitos do ExpressRoute e os recursos de conexão estarão sendo monitorados pelo NPM.

  ![blocos de monitoramento](.\media\how-to-npm\15.png)

## <a name="explore"></a>Etapa 5: exibir blocos de monitoramento

### <a name="dashboard"></a>Página Monitor de Desempenho de Rede

A página NPM contém uma página para ExpressRoute que mostra uma visão geral da integridade de circuitos e emparelhamentos do ExpressRoute.

  ![painel](.\media\how-to-npm\dashboard.png)

### <a name="circuits"></a>Lista de circuitos

Para ver uma lista de todos os circuitos do ExpressRoute monitorados, clique no bloco **circuitos do ExpressRoute**. É possível selecionar um circuito e exibir seu estado de integridade, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência. Os gráficos são interativos. É possível selecionar uma janela de tempo personalizada para plotar os gráficos. Você pode arrastar o mouse sobre uma área do gráfico para ampliar e ver os pontos de dados refinados.

  ![circuit_list](.\media\how-to-npm\circuits.png)

#### <a name="trend"></a>Tendência de Perda, Latência e Taxa de Transferência

A largura de banda, latência e perda de gráficos são interativas. Você pode aplicar o zoom em qualquer seção destes gráficos usando os controles do mouse. Também é possível ver as largura de banda, latência e perda de dados para outros intervalos ao clicar em **Data/Hora**, localizado abaixo do botão Ações no canto superior esquerdo.

![tendência](.\media\how-to-npm\16.png)

### <a name="peerings"></a>Lista de emparelhamentos

Clicar no bloco **Emparelhamentos Privados** no painel exibe uma lista de todas as conexões com redes virtuais sobre emparelhamento privado. Aqui, é possível selecionar uma conexão de rede virtual e exibir seu estado de integridade, gráficos de tendências para perda de pacotes, utilização de largura de banda e latência.

  ![lista de circuitos](.\media\how-to-npm\peerings.png)

### <a name="topology"></a>Topologia de circuito

Para exibir a topologia de circuito, clique no bloco **Topologia**. Isso leva você para a exibição de topologia do circuito ou emparelhamento selecionado. O diagrama de topologia fornece a latência de cada segmento na rede e cada salto de camada 3 é representado por um nó do diagrama. Clicar em um salto revela mais detalhes sobre ele.
É possível aumentar o nível de visibilidade para incluir saltos locais ao mover a barra do controle deslizante para baixo dos **Filtros**. Mover a barra do controle deslizante para a esquerda ou direita amplia/reduz o número de saltos no grafo de topologia. A latência em cada segmento é visível, o que permite o isolamento mais rápido de segmentos de alta latência na sua rede.

![filtros](.\media\how-to-npm\topology.png)

#### <a name="detailed-topology-view-of-a-circuit"></a>Exibição de topologia detalhada de um circuito

Essa exibição mostra as conexões de VNet.
![topologia detalhada](.\media\how-to-npm\17.png)
