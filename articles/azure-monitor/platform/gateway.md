---
title: Conectar computadores usando o gateway do Log Analytics | Microsoft Docs
description: Conecte seus dispositivos e computadores monitorados pelo Operations Manager usando o gateway do Log Analytics para enviar dados para a automação do Azure e o serviço Log Analytics quando eles não têm acesso à internet.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: magoedte
ms.openlocfilehash: 47b589d32accc4a699e7260b9e4b2de4cca58f2b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369608"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>Conectar computadores sem acesso à internet usando o gateway do Log Analytics no Azure Monitor

>[!NOTE]
>Como o Microsoft Operations Management Suite (OMS) faz a transição para o Microsoft Azure Monitor, a terminologia está mudando. Este artigo refere-se ao Gateway do OMS como o gateway do Azure Log Analytics. 
>

Este artigo descreve como configurar a comunicação com a automação do Azure e o Azure Monitor, usando o gateway do Log Analytics quando os computadores que estão conectados diretamente ou que são monitorados pelo Operations Manager tem sem acesso à internet. 

O gateway do Log Analytics é um proxy de encaminhamento de HTTP que dá suporte a túnel HTTP usando o comando HTTP CONNECT. Este gateway pode coletar dados e enviá-lo para a automação do Azure e um espaço de trabalho do Log Analytics no Azure Monitor em nome dos computadores que não estão conectados à internet.  

O gateway do Log Analytics dá suporte a:

* Relatório até o mesmo Log Analytics quatro agentes de espaço de trabalho que estão por trás dele e que estão configurados com Hybrid Runbook Workers da automação do Azure.  
* Computadores Windows no qual o Microsoft Monitoring Agent é conectado diretamente a um espaço de trabalho do Log Analytics no Azure Monitor.
* Computadores com Linux no qual um agente do Log Analytics para Linux é conectado diretamente a um espaço de trabalho do Log Analytics no Azure Monitor.  
* System Center Operations Manager 2012 SP1 com UR7, Operations Manager 2012 R2 com UR3 ou um grupo de gerenciamento no Operations Manager 2016 ou posterior que esteja integrado com o Log Analytics.  

Algumas políticas de segurança não permitem a conexão de internet para computadores da rede. Esses computadores não conectadas pode ser o ponto de dispositivos de venda (PDV) ou servidores que oferecem suporte a serviços de TI, por exemplo. Para se conectar a esses dispositivos a automação do Azure ou um espaço de trabalho do Log Analytics para que você possa gerenciar e monitorá-los, configurá-los para se comunicar diretamente com o gateway do Log Analytics. O gateway do Log Analytics pode receber informações de configuração e encaminhar os dados em seu nome. Se os computadores estão configurados com o agente do Log Analytics para se conectar diretamente a um espaço de trabalho do Log Analytics, os computadores em vez disso, se comunicar com o gateway do Log Analytics.  

O Log Analytics gateway transfere dados dos agentes para o serviço diretamente. Ele não analisa os dados em trânsito.

Quando um grupo de gerenciamento do Operations Manager é integrado com o Log Analytics, os servidores de gerenciamento podem ser configurados para se conectar ao gateway do Log Analytics para receber informações de configuração e enviar os dados coletados, dependendo da solução que você tiver habilitado .  Agentes do Operations Manager enviam alguns dados para o servidor de gerenciamento. Por exemplo, agentes podem enviar alertas do Operations Manager, os dados de avaliação de configuração, dados de espaço de instância e dados de capacidade. Outros dados de alto volume, como logs de serviços de informações da Internet (IIS), dados de desempenho e eventos de segurança, são enviados diretamente ao gateway do Log Analytics. 

Se um ou mais servidores de Gateway do Operations Manager são implantados para monitorar os sistemas não confiáveis em uma rede de perímetro ou em uma rede isolada, esses servidores não podem se comunicar com um gateway do Log Analytics.  Servidores do Operations Manager Gateway podem relatar somente a um servidor de gerenciamento.  Quando um grupo de gerenciamento do Operations Manager é configurado para se comunicar com o gateway do Log Analytics, as informações de configuração de proxy é automaticamente distribuídas a todos os computadores gerenciados por agente que está configurado para coletar dados de log do Azure Monitor mesmo se a configuração está vazia.    

Para fornecer alta disponibilidade para diretamente conectado ou grupos de gerenciamento de operações que se comunicam com um espaço de trabalho do Log Analytics por meio do gateway, use a rede balanceamento de carga (NLB) para redirecionar e distribuir tráfego entre vários servidores de gateway. Dessa forma, se um servidor de gateway falhar, o tráfego será redirecionado para outro nó disponível.  

O computador que executa o gateway do Log Analytics requer que o agente do Windows do Log Analytics identificar os pontos de extremidade de serviço que precisa se comunicar com o gateway. O agente também precisa direcionar o gateway para relatar para espaços de trabalho do mesmos que os agentes ou grupo de gerenciamento do Operations Manager por trás do gateway são configuradas com. Essa configuração permite que o gateway e o agente se comunique com o seu espaço de trabalho atribuído.

Um gateway pode ser multihomed para até quatro espaços de trabalho. Isso é o número total de espaços de trabalho que dá suporte a um agente do Windows.  

Cada agente deve ter conectividade de rede para o gateway para que os agentes automaticamente podem transferir dados para e do gateway. Evite instalar o gateway em um controlador de domínio.

O diagrama a seguir mostra os dados que fluem dos agentes diretos, por meio do gateway, a automação do Azure e o Log Analytics. A configuração de proxy do agente deve corresponder à porta que o gateway do Log Analytics está configurado com.  

![Diagrama de comunicação do agente direto com os serviços](./media/gateway/oms-omsgateway-agentdirectconnect.png)

O diagrama a seguir mostra o fluxo de dados de um grupo de gerenciamento do Operations Manager para o Log Analytics.   

![Diagrama de comunicação do Operations Manager com o Log Analytics](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>Configurar seu sistema

Computadores designados para executar o gateway do Log Analytics devem ter a seguinte configuração:

* Windows 10, Windows 8.1 ou Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 ou Windows Server 2008
* Microsoft .NET Framework 4.5
* Pelo menos 4 núcleos processador e 8 GB de memória 
* Um [agente do Log Analytics para Windows](agent-windows.md) que é configurado para o relatório para o mesmo espaço de trabalho dos agentes que se comunicam por meio do gateway

### <a name="language-availability"></a>Disponibilidade de idiomas

O gateway do Log Analytics está disponível nestes idiomas:

- Chinês (Simplificado)
- Chinês (Tradicional)
- Tcheco
- Holandês
- Inglês
- Francês
- Alemão
- Húngaro
- Italiano
- Japonês
- Coreano
- Polonês
- Português (Brasil)
- Português (Portugal)
- Russo
- Espanhol (internacional)

### <a name="supported-encryption-protocols"></a>Protocolos de criptografia com suporte
O gateway do Log Analytics suporta apenas segurança TLS (Transport Layer) 1.0, 1.1 e 1.2.  Ele não dá suporte a Secure Sockets Layer (SSL).  Para garantir a segurança dos dados em trânsito para o Log Analytics, configure o gateway para usar pelo menos TLS 1.2. Versões mais antigas do TLS ou SSL são vulneráveis. Embora elas atualmente permitem compatibilidade com versões anteriores, evite usá-las.  

Para obter mais informações, examine [Enviando dados com segurança usando o TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12). 

### <a name="supported-number-of-agent-connections"></a>Número de conexões de agente com suporte
A tabela a seguir mostra aproximadamente quantos agentes podem se comunicar com um servidor de gateway. Suporte é baseado em agentes que carregam cerca de 200 KB de dados a cada 6 segundos. Para cada agente testado, o volume de dados é aproximadamente 2,7 GB por dia.

|Gateway |Agentes com suporte (aproximado)|  
|--------|----------------------------------|  
|CPU: Intel Xeon Processador 2660 E5 v3 \@ núcleos de 2,6 GHz 2<br> Memória: 4 GB<br> Largura de banda de rede: 1 Gbps| 600|  
|CPU: Intel Xeon Processador 2660 E5 v3 \@ núcleos de 2,6 GHz de 4<br> Memória: 8 GB<br> Largura de banda de rede: 1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Fazer download do gateway do Log Analytics

Obter a versão mais recente do arquivo de instalação do gateway do Log Analytics de qualquer uma [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=54443) ou o portal do Azure.

Para obter o gateway do Log Analytics do portal do Azure, siga estas etapas:

1. Navegue pela lista de serviços e selecione **Log Analytics**. 
1. Selecione um workspace.
1. Na folha do seu workspace, em **Geral**, selecione **Início Rápido**. 
1. Em **Escolher uma fonte de dados para conectar ao workspace**, selecione **Computadores**.
1. No **agente direto** folha, selecione **gateway de baixar o Log Analytics**.
 
   ![Captura de tela das etapas para baixar o gateway do Log Analytics](./media/gateway/download-gateway.png)

ou o 

1. Na folha do seu workspace em **Configurações**, selecione **Configurações avançadas**.
1. Vá para **fontes conectadas** > **servidores Windows** e selecione **gateway baixar o Log Analytics**.

## <a name="install-the-log-analytics-gateway"></a>Instalar o gateway do Log Analytics

Para instalar um gateway, siga estas etapas.  (Se você instalou uma versão anterior, chamada de encaminhador do Log Analytics, ele será atualizado para esta versão.)

1. Na pasta de destino, clique duas vezes em **Log Analytics gateway.msi**.
1. Sobre o **boas-vindas** página, selecione **próxima**.

   ![Página de captura de tela de boas-vindas do Assistente de instalação do Gateway](./media/gateway/gateway-wizard01.png)

1. Sobre o **contrato de licença** página, selecione **aceito os termos do contrato de licença** para concordar com os termos de licença de Software da Microsoft e, em seguida, selecione **próxima**.
1. Na página **Porta e endereço de proxy**:

    a. Insira o número da porta TCP a ser usado para o gateway. A instalação usa esse número de porta para configurar uma regra de entrada no Firewall do Windows.  O valor padrão é 8080.
      O intervalo válido do número da porta é de 1 a 65535. Se a entrada não estiver dentro desse intervalo, uma mensagem de erro será exibida.

   b. Se o servidor onde o gateway estiver instalado precisar se comunicar por meio de um proxy, insira o endereço do proxy onde o gateway precisa para se conectar. Por exemplo, insira: `http://myorgname.corp.contoso.com:80`.  Se você deixar esse campo em branco, o gateway tentará se conectar diretamente à internet.  Se o servidor proxy exigir autenticação, insira um nome de usuário e senha.

   c. Selecione **Avançar**.

   ![Captura de tela da configuração do proxy do gateway](./media/gateway/gateway-wizard02.png)

1. Se você não tiver habilitado do Microsoft Update, a página do Microsoft Update será exibida e você pode optar por habilitá-lo. Faça uma seleção e, em seguida, selecione **próxima**. Caso contrário, prossiga para a próxima etapa.
1. Sobre o **pasta de destino** página, deixe a pasta padrão C:\Program c:\programfiles\oms Gateway ou digite o local onde você deseja instalar o gateway. Em seguida, selecione **Avançar**.
1. Na página **Pronto para instalar**, selecione **Instalar**. Se o controle de conta de usuário solicita permissão para instalar, selecione **Sim**.
1. Após a conclusão da instalação, selecione **concluir**. Para verificar se o serviço está em execução, abra o snap-in Services. msc e verifique **Gateway do OMS** aparece na lista de serviços e seu status é **executando**.

   ![Captura de tela de serviços locais, mostrando que o Gateway do OMS está em execução](./media/gateway/gateway-service.png)


## <a name="configure-network-load-balancing"></a>Configurar o balanceamento de carga de rede 
Você pode configurar o gateway para alta disponibilidade usando o balanceamento de carga (NLB) usando a Microsoft [balanceamento de carga rede (NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing), [Azure Load Balancer](../../load-balancer/load-balancer-overview.md), ou balanceadores de carga com base em hardware. O balanceador de carga gerencia o tráfego redirecionando as conexões solicitadas dos servidores de gerenciamento do Operations Manager ou de agentes do Log Analytics entre seus nós. Se um servidor de Gateway falhar, o tráfego será redirecionado para outros nós.

### <a name="microsoft-network-load-balancing"></a>Balanceamento de carga de rede da Microsoft
Para saber como projetar e implantar um cluster de balanceamento de carga de rede Windows Server 2016, confira [Balanceamento de carga de rede](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing). As etapas a seguir descrevem como configurar uma cluster de balanceamento de carga de rede Microsoft Network.  

1. Entre no servidor Windows que seja membro do cluster NLB com uma conta administrativa.  
2. Abra o Gerenciador de Balanceamento de Carga de Rede no gerenciador de servidores, clique em **Ferramentas**e clique em **Gerenciador de Balanceamento de Carga de Rede**.
3. Para conectar um servidor do gateway do Log Analytics ao Microsoft Monitoring Agent instalado, clique com o botão direito no endereço IP do cluster e clique em **Adicionar Host ao Cluster**. 

    ![De Gerenciador de balanceamento de carga de Rede Adicionar Host ao Cluster](./media/gateway/nlb02.png)
 
4. Digite o endereço IP do servidor de gateway que deseja conectar. 

    ![Gerenciador de Balanceamento de Carga de Rede – Adicionar Host ao Cluster: Connect](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer
Para saber como projetar e implantar um balanceador de carga do Azure, consulte [o que é o balanceador de carga do Azure?](../../load-balancer/load-balancer-overview.md). Para implantar um balanceador de carga básico, siga as etapas descritas nesta [quickstart](../../load-balancer/quickstart-create-basic-load-balancer-portal.md) excluindo as etapas descritas na seção **criar servidores de back-end**.   

> [!NOTE]
> Configurar o balanceador de carga do Azure usando o **SKU básico**, exige que máquinas virtuais do Azure pertencer a um conjunto de disponibilidade. Para saber mais sobre os conjuntos de disponibilidade, consulte [gerenciar a disponibilidade das máquinas de virtuais do Windows no Azure](../../virtual-machines/windows/manage-availability.md). Para adicionar máquinas virtuais existentes a um conjunto de disponibilidade, consulte [definida do Azure Resource Manager VM do conjunto de disponibilidade](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4).
> 

Depois que o balanceador de carga é criado, um pool de back-end precisa ser criado, que distribui o tráfego para um ou mais servidores de gateway. Siga as etapas descritas na seção do artigo de início rápido [criar recursos para o balanceador de carga](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer).  

>[!NOTE]
>Ao configurar a investigação de integridade, ele deve ser configurado para usar a porta TCP do servidor de gateway. A investigação de integridade adiciona ou remove os servidores de gateway da rotação do balanceador de carga com base na resposta às verificações de integridade dinamicamente. 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Configurar o agente do Log Analytics e o grupo de gerenciamento do Operations Manager
Nesta seção, você verá como configurar agentes conectados diretamente ao Log Analytics, um grupo de gerenciamento do Operations Manager ou Hybrid Runbook Workers da automação do Azure com o gateway do Log Analytics para se comunicar com a automação do Azure ou Log Analytics.  

### <a name="configure-a-standalone-log-analytics-agent"></a>Configurar um agente de Log Analytics autônomo
Ao configurar o agente do Log Analytics, substitua o valor do servidor proxy com o endereço IP do servidor de gateway do Log Analytics e seu número de porta. Se você tiver implantado vários servidores gateway por trás de um balanceador de carga, a configuração de proxy do agente do Log Analytics é o endereço IP virtual do balanceador de carga.  

>[!NOTE]
>Para instalar o agente do Log Analytics no gateway e em computadores Windows que se conectam diretamente ao Log Analytics, consulte [computadores Windows de se conectar ao serviço Log Analytics no Azure](agent-windows.md). Para conectar computadores Linux, consulte [configurar um agente de Log Analytics para computadores Linux em um ambiente híbrido](../../azure-monitor/learn/quick-collect-linux-computer.md). 
>

Depois de instalar o agente no servidor gateway, configurá-lo para relatar para o espaço de trabalho ou agentes de espaço de trabalho que se comunicam com o gateway. Se o agente do Windows do Log Analytics não estiver instalado no gateway, o evento 300 é gravado no log de eventos do Gateway do OMS, que indica que o agente deve ser instalado. Se o agente estiver instalado, mas não configurado para relatar para o mesmo espaço de trabalho dos agentes que se comunicam por meio dele, o evento 105 é gravado no mesmo log, indicando que o agente no gateway precisa ser configurado para se reportar ao mesmo espaço de trabalho dos agentes que co mmunicate com o gateway.

Depois de concluir a configuração, reinicie o serviço de Gateway do OMS para aplicar as alterações. Caso contrário, o gateway rejeitará os agentes que tentam se comunicar com o Log Analytics e relatarão 105 de evento no log de eventos do Gateway do OMS. Isso também acontece quando você adicionar ou remover um espaço de trabalho da configuração do agente no servidor de gateway.   

Para obter informações relacionadas para o Hybrid Runbook Worker de automação, consulte [automatizar recursos em seu datacenter ou nuvem usando o Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Configurar o Operations Manager, em que todos os agentes usam o mesmo servidor proxy
A configuração do proxy do Operations Manager é aplicada automaticamente a todos os agentes que se reportam ao Operations Manager, mesmo se a configuração está vazia.  

Para usar o Gateway do OMS para dar suporte ao Operations Manager, você deve ter:

* Microsoft Monitoring Agent (versão 8.0.10900.0 ou posterior) instalado no servidor de Gateway do OMS e configurado com os espaços de trabalho do Log Analytics mesmos se seu grupo de gerenciamento está configurado para se reportar a.
* Conectividade com a Internet. Como alternativa, o Gateway do OMS deve estar conectado a um servidor proxy que está conectado à internet.

> [!NOTE]
> Se você não especificar nenhum valor para o gateway, os valores em branco são enviados por push a todos os agentes.
>

Se o grupo de gerenciamento do Operations Manager está sendo registrado com um espaço de trabalho do Log Analytics pela primeira vez, você não verá a opção de especificar a configuração de proxy para o grupo de gerenciamento no console de operações. Essa opção está disponível somente se o grupo de gerenciamento tiver sido registrado com o serviço.  

Para configurar a integração, atualize a configuração de proxy do sistema usando Netsh no sistema em que você está executando o console de operações e em todos os servidores de gerenciamento no grupo de gerenciamento. Siga estas etapas:

1. Abra um prompt de comando elevado:

    a. Selecione **inicie** e insira **cmd**.  

   b. Clique com botão direito **Prompt de comando** e selecione **executar como administrador**.  

1. Digite o seguinte comando:

   `netsh winhttp set proxy <proxy>:<port>`

Depois de concluir a integração com o Log Analytics, remova a alteração executando `netsh winhttp reset proxy`. Em seguida, no console de operações, use o **configurar servidor de proxy** opção para especificar o servidor de gateway do Log Analytics. 

1. No console do Operations Manager, sob **Operations Management Suite**, selecione **Conexão**e, em seguida, selecione **configurar servidor Proxy**.

   ![Captura de tela do Operations Manager, mostrando a seleção configurar servidor de Proxy](./media/gateway/scom01.png)

1. Selecione **usar um servidor proxy para acessar o Operations Management Suite** e, em seguida, insira o endereço IP do servidor de gateway do Log Analytics ou o endereço IP virtual do balanceador de carga. Tenha cuidado para começar com o prefixo `http://`.

   ![Captura de tela do Operations Manager, mostrando o proxy do endereço do servidor](./media/gateway/scom02.png)

1. Selecione **Concluir**. O grupo de gerenciamento do Operations Manager agora está configurado para se comunicar por meio do servidor de gateway para o serviço de Log Analytics.

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Configurar o Operations Manager, em que os agentes específicos usam um servidor proxy
Para ambientes grandes ou complexos, você talvez queira apenas servidores específicos (ou grupos) para usar o servidor de gateway do Log Analytics.  Para esses servidores, você não pode atualizar o agente do Operations Manager diretamente porque esse valor é substituído pelo valor global do grupo de gerenciamento.  Em vez disso, substitua a regra usada para enviar esses valores.  

> [!NOTE] 
> Use essa técnica de configuração se você deseja permitir para vários servidores de gateway do Log Analytics em seu ambiente.  Por exemplo, você pode exigir que os servidores de gateway do Log Analytics sejam especificados em uma base regional.
>

Para configurar servidores específicos ou grupos para usar o servidor de gateway do Log Analytics: 

1. Abra o console do Operations Manager e selecione o workspace **Criação** .  
1. No workspace de Criação, selecione **Regras**. 
1. Na barra de ferramentas do Operations Manager, selecione o **escopo** botão. Se esse botão não estiver disponível, verifique se você selecionou um objeto, não uma pasta na **monitoramento** painel. A caixa de diálogo **Delimitar Objetos do Pacote de Gerenciamento** exibe uma lista de objetos, grupos ou classes comuns de destino. 
1. No **procure** , insira **serviço de integridade** e selecione-o na lista. Selecione **OK**.  
1. Pesquise **regra de configuração de Proxy do Advisor**. 
1. Na barra de ferramentas do Operations Manager, selecione **substituições** e, em seguida, aponte para **substituir a um objeto específico da classe Rule\For: Serviço de integridade** e selecione um objeto da lista.  Ou crie um grupo personalizado que contém o objeto de serviço de integridade dos servidores que você deseja aplicar a substituição. Em seguida, aplica a substituição para o seu grupo personalizado.
1. No **propriedades da substituição** diálogo caixa, adicione uma marca de seleção na **substituir** coluna Avançar para o **WebProxyAddress** parâmetro.  No **valor de substituição** , insira a URL do servidor de gateway do Log Analytics. Tenha cuidado para começar com o prefixo `http://`.  

    >[!NOTE]
    > Você não precisa habilitar a regra. Ele tem já gerenciado automaticamente com uma substituição no pacote de gerenciamento Microsoft System Center Advisor Secure Reference Override que tem como destino o grupo Microsoft System Center Advisor monitoramento de servidor.
    > 

1. Selecione um pacote de gerenciamento dos **selecionar pacote de gerenciamento de destino** lista ou crie um novo pacote de gerenciamento sem lacre selecionando **New**. 
1. Quando terminar, selecione **OK**. 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Configurar para Hybrid Runbook Workers da automação
Se você tiver Hybrid Runbook Workers da automação em seu ambiente, siga estas etapas para soluções alternativas temporárias manuais configurar o Gateway do OMS para dar suporte os operadores.

Para seguir as etapas nesta seção, você precisa saber onde reside a conta de automação de região do Azure. Para localizar esse local:

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Selecione o serviço de Automação do Azure.
1. Selecione a conta de Automação do Azure apropriada.
1. Exiba sua região em **Local**.

   ![Captura de tela do local da conta de automação no portal do Azure](./media/gateway/location.png)

Use as tabelas a seguir para identificar a URL para cada local.

**URLs de serviço de dados de tempo de execução do trabalho**

| **Localidade** | **URL** |
| --- | --- |
| Centro-Norte dos EUA |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Europa Ocidental |we-jobruntimedata-prod-su1.azure-automation.net |
| Centro-Sul dos Estados Unidos |scus-jobruntimedata-prod-su1.azure-automation.net |
| Leste dos EUA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Centro do Canadá |cc-jobruntimedata-prod-su1.azure-automation.net |
| Norte da Europa |ne-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste da Ásia |sea-jobruntimedata-prod-su1.azure-automation.net |
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japão |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Austrália |ase-jobruntimedata-prod-su1.azure-automation.net |

**URLs de serviço do agente**

| **Localidade** | **URL** |
| --- | --- |
| Centro-Norte dos EUA |ncus-agentservice-prod-1.azure-automation.net |
| Europa Ocidental |we-agentservice-prod-1.azure-automation.net |
| Centro-Sul dos Estados Unidos |scus-agentservice-prod-1.azure-automation.net |
| Leste dos EUA 2 |eus2-agentservice-prod-1.azure-automation.net |
| Centro do Canadá |cc-agentservice-prod-1.azure-automation.net |
| Norte da Europa |ne-agentservice-prod-1.azure-automation.net |
| Sudeste da Ásia |sea-agentservice-prod-1.azure-automation.net |
| Índia Central |cid-agentservice-prod-1.azure-automation.net |
| Japão |jpe-agentservice-prod-1.azure-automation.net |
| Austrália |ase-agentservice-prod-1.azure-automation.net |

Se o computador é registrado automaticamente como um Hybrid Runbook Worker, use a solução de gerenciamento de atualizações para gerenciar o patch. Siga estas etapas:

1. Adicione as URLs de serviço de dados de tempo de execução do trabalho à lista Hosts Permitidos no gateway do Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Reinicie o serviço do gateway do Log Analytics usando o seguinte cmdlet do PowerShell: `Restart-Service OMSGatewayService`

Se o computador tiver ingressado na automação do Azure usando o cmdlet de registro do Hybrid Runbook Worker, siga estas etapas:

1. Adicione a URL do registro de serviço do agente à lista Hosts Permitidos no gateway do Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Adicione as URLs de serviço de dados de tempo de execução do trabalho à lista Hosts Permitidos no gateway do Log Analytics. Por exemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Reinicie o serviço do gateway do Log Analytics.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets úteis do PowerShell
Você pode usar cmdlets para concluir as tarefas para atualizar definições de configuração do gateway do Log Analytics. Antes de usar os cmdlets, certifique-se a:

1. Instale o gateway do Log Analytics (Microsoft Windows Installer).
1. Abra uma janela do console do PowerShell.
1. Importe o módulo digitando este comando: `Import-Module OMSGateway`
1. Se nenhum erro tiver ocorrido na etapa anterior, o módulo foi importado com êxito, e os cmdlets poderão ser usados. Inserir `Get-Module OMSGateway`
1. Depois de usar os cmdlets para fazer alterações, reinicie o serviço de Gateway do OMS.

Um erro na etapa 3 significa que o módulo não foi importado. O erro pode ocorrer quando o PowerShell não é possível localizar o módulo. Você pode encontrar o módulo no caminho de instalação do Gateway do OMS: *C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parâmetros** | **Descrição** | **Exemplo** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Chave |Obtém a configuração do serviço |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Chave (obrigatória) <br> Valor |Altera a configuração do serviço |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Obtém o endereço do proxy de retransmissão (upstream) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Endereço<br> Nome de Usuário<br> Senha |Define o endereço (e as credenciais) do proxy de retransmissão (upstream) |1. Define um proxy de retransmissão e uma credencial:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Define um proxy de resposta que não precise de autenticação: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Desmarca a configuração do proxy de retransmissão:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Obtém o host atualmente permitido (somente o host permitido configurado localmente, não é automaticamente baixado permitido hosts) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (obrigatório) |Adiciona o host à lista de permissões |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (obrigatório) |Remove o host da lista de permissões |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Adiciona o assunto do certificado do cliente à lista de permissões |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Remove o assunto do certificado do cliente da lista de permissões |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Obtém de assuntos do certificado do cliente atualmente permitidos (somente os assuntos permitidos configurados localmente, não é automaticamente baixados assuntos permitidos) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>solução de problemas
Para coletar eventos registrados pelo gateway, você deve ter instalado o agente de Log Analytics.

![Captura de tela da lista do Visualizador de eventos no log de gateway do Log Analytics](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>IDs de eventos do gateway do log Analytics e descrições

A tabela a seguir mostra as IDs e descrições para eventos de log do gateway do Log Analytics.

| **ID** | **Descrição** |
| --- | --- |
| 400 |Qualquer erro de aplicativo que não tenha nenhuma ID específica. |
| 401 |Configuração incorreta. Por exemplo, listenPort = "text", em vez de um número inteiro. |
| 402 |Exceção ao analisar mensagens de handshake TLS. |
| 403 |Rede de rede. Por exemplo, não é possível se conectar ao servidor de destino. |
| 100 |Informações gerais. |
| 101 |Serviço iniciado. |
| 102 |Serviço interrompido. |
| 103 |Um comando HTTP CONNECT recebido do cliente. |
| 104 |Não é um comando HTTP CONNECT. |
| 105 |Servidor de destino não está na lista de permissões ou porta de destino não é segura (443). <br> <br> Certifique-se de que o agente MMA no servidor do Gateway do OMS e os agentes que se comunicam com o Gateway do OMS estão conectados ao mesmo espaço de trabalho do Log Analytics. |
| 105 |ERRO TcpConnection – Certificado do cliente inválido: CN = Gateway. <br><br> Certifique-se de que você esteja usando o Gateway do OMS versão 1.0.395.0 ou superior. Certifique-se também de que o agente MMA no servidor do Gateway do OMS e os agentes que se comunicam com o Gateway do OMS estão conectados ao mesmo espaço de trabalho do Log Analytics. |
| 106 |Versão do protocolo TLS/SSL sem suporte.<br><br> O gateway do Log Analytics dá suporte a apenas TLS 1.0, TLS 1.1 e 1.2. Ele não oferece suporte ao protocolo SSL.|
| 107 |A sessão TLS foi verificada. |

### <a name="performance-counters-to-collect"></a>Contadores de desempenho para coletar

A tabela a seguir mostra os contadores de desempenho disponíveis para o gateway do Log Analytics. Use o Monitor de desempenho para adicionar os contadores.

| **Nome** | **Descrição** |
| --- | --- |
| Gateway do Log Analytics/Conexão de Cliente Ativo |Número de conexões de rede de cliente ativo (TCP) |
| Gateway do Log Analytics/Contagem de Erros |Número de erros |
| Gateway do Log Analytics/Cliente Conectado |Número de clientes conectados |
| Gateway do Log Analytics/Contagem de Rejeição |Número de rejeições devido a qualquer erro de validação de TLS |

![Interface de gateway de captura de tela do Log Analytics, mostrando os contadores de desempenho](./media/gateway/counters.png)

## <a name="assistance"></a>Assistência
Quando você estiver conectado ao portal do Azure, você pode obter ajuda com o gateway do Log Analytics ou qualquer outro serviço do Azure ou recurso.
Para obter ajuda, selecione o ícone de ponto de interrogação no canto superior direito do portal e selecione **nova solicitação de suporte**. Em seguida, conclua o novo formulário de solicitação de suporte.

![Captura de tela de uma nova solicitação de suporte](./media/gateway/support.png)

## <a name="next-steps"></a>Próximas etapas
[Adicionar fontes de dados](../../azure-monitor/platform/agent-data-sources.md) para coletar dados de fontes conectadas e armazenar os dados em seu espaço de trabalho do Log Analytics.