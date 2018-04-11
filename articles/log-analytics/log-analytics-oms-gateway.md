---
title: Conectar computadores usando o Gateway do OMS | Microsoft Docs
description: Conecte seus dispositivos e computadores monitorados pelo Operations Manager ao Gateway do OMS para enviar dados à Automação do Azure e ao serviço do Log Analytics quando eles não tiverem acesso à internet.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: magoedte
ms.openlocfilehash: 66e5444f5346a44cfc8a43cf2b43dbaeacffedc9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="connect-computers-without-internet-access-by-using-the-oms-gateway"></a>Conectar computadores sem acesso à internet usando o Gateway do OMS
Este documento descreve como configurar a comunicação com a Automação do Azure e Log Analytics usando o Gateway do OMS quando os computadores monitorados pelo Operations Manager ou computadores conectados diretamente não tem acesso à internet. O Gateway do OMS é um proxy de encaminhamento de HTTP que dá suporte ao túnel HTTP usando o comando HTTP CONNECT. Ele pode coletar dados e enviá-lo para a Automação do Azure e o Log Analytics em nome dos computadores sem acesso à internet.  

O Gateway do OMS dá suporte a:

* Runbook Workers Híbridos da Automação do Azure  
* Computadores Windows com o Microsoft Monitoring Agent conectado diretamente a um espaço de trabalho do Log Analytics
* Computadores Linux com o agente do OMS para Linux conectado diretamente a um espaço de trabalho do Log Analytics  
* System Center Operations Manager 2012 SP1 com UR7, Operations Manager 2012 R2 com UR3, Operations Manager 2016 e grupo de gerenciamento do Operations Manager 1801 integrado ao Log Analytics  

Se suas políticas de segurança de TI não permitirem que determinados computadores em sua rede se conectem à Internet, como dispositivos de ponto de venda ou servidores que dão suporte a serviços de TI, mas você precisar se conectar à Automação do Azure ou Log Analytics para gerenciá-los e monitorá-los, eles podem ser configurados para se comunicarem diretamente com o Gateway do OMS.

 Se esses computadores estão configurados com o agente do OMS para se conectar diretamente a um espaço de trabalho do Log Analytics, todos os computadores se comunicam com o Gateway do OMS. O Gateway de OMS transfere dados dos agentes para o serviço diretamente. Ele não analisa os dados enquanto está em trânsito.

Quando um grupo de gerenciamento do Operations Manager é integrado com o Log Analytics, os servidores de gerenciamento podem ser configurados para se conectar ao Gateway do OMS para receber informações de configuração e enviar os dados coletados. Os agentes do Operations Manager enviam alguns dados, como alertas, avaliação de configuração, espaço de instância e dados de capacidade para o servidor de gerenciamento. Outros dados de alto volume, como logs do IIS, informações de desempenho e eventos de segurança, são enviados diretamente ao Gateway do OMS.  

Se você tiver um ou mais servidores de gateway do Operations Manager implantados na rede de perímetro ou em outra rede isolada para monitorar os sistemas não confiáveis, eles não poderão se comunicar com um gateway do OMS. Os servidores do Operations Manager Gateway só podem se reportar a um servidor de gerenciamento. 

Quando um grupo de gerenciamento do Operations Manager é configurado para se comunicar com o Gateway do OMS, as informações de configuração de proxy são distribuídas automaticamente a todos os computadores gerenciados por agente configurados para coletar dados para o Log Analytics, mesmo que a configuração esteja vazia.    

Para fornecer alta disponibilidade a grupos do Operations Management que se comunicam com o Log Analytics pelo Gateway do OMS ou diretamente conectados, você pode usar balanceamento de carga de rede para redirecionar e distribuir o tráfego em vários servidores de Gateway do OMS. Se um servidor de gateway falhar, o tráfego será redirecionado para outro nó disponível.  

É recomendável que você instale o agente do OMS no computador que está executando o software do Gateway do OMS para monitorá-lo e analisar os dados de desempenho ou de evento. Além disso, o agente ajuda o Gateway do OMS a identificar os pontos de extremidade de serviço com que precisa se comunicar.

Todos os agentes devem ter conectividade de rede com seus gateways para que possam transferir dados automaticamente para dentro e fora do gateway. Não é recomendável instalar um gateway em um controlador de domínio.

O diagrama a seguir mostra o fluxo de dados dos agentes diretos para a Automação do Azure e Log Analytics usando o servidor de gateway. A configuração de proxy do agente deve usar a mesma porta que o Gateway do OMS usa para se comunicar com o serviço.  

![Diagrama de comunicação direta do agente com os serviços](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

O diagrama a seguir mostra o fluxo de dados de um grupo de gerenciamento do Operations Manager para o Log Analytics.   

![Diagrama Comunicação do Operations Manager com o Log Analytics](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>pré-requisitos

Quando você designar um computador para executar o Gateway do OMS, verifique se ele tem os seguintes componentes:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .NET Framework 4.5
* Mínimo de processador de 4 núcleos e 8 GB de memória 

### <a name="language-availability"></a>Disponibilidade de idiomas

O Gateway do OMS está disponível nos seguintes idiomas:

- Chinês (simplificado)
- Chinês (tradicional)
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
O Gateway de OMS dá suporte apenas ao protocolo TLS (Transport Layer Security) 1.0, 1.1 e 1.2.  Ele não oferece suporte a protocolo SSL (Secure Sockets Layer).

### <a name="supported-number-of-agent-connections"></a>Número de conexões de agente com suporte
A tabela a seguir destaca o número de agentes com suporte que estão se comunicando com um servidor de gateway. Esse suporte é baseado em agentes que carregam cerca de 200 KB de dados a cada 6 segundos. O volume de dados por agente testado é de aproximadamente 2,7 GB por dia.

|Gateway |Número aproximado de agentes com suporte|  
|--------|----------------------------------|  
|- CPU: CPU Intel XEON E5-2660 v3 com 2 núcleos de 2,6 GHz<br> - Memória: 4 GB<br> - Largura de banda de rede: 1 Gbps| 600|  
|- CPU: CPU Intel XEON E5-2660 v3 com 4 núcleos de 2,6 GHz<br> - Memória: 8 GB<br> - Largura de banda de rede: 1 Gbps| 1000|  

## <a name="download-the-oms-gateway"></a>Baixar o Gateway do OMS

Há duas maneiras de obter a versão mais recente do arquivo de configuração de Gateway do OMS.

1. Baixe-o do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

2. Faça o download no Portal do Azure. Depois de entrar no Portal do Azure, execute as seguintes etapas:  

   1. Navegue pela lista de serviços e selecione **Log Analytics**.  
   2. Selecione um espaço de trabalho.
   3. Na folha do seu espaço de trabalho, em **Geral**, selecione **Início Rápido**.
   4. Em **Escolher uma fonte de dados para conectar ao espaço de trabalho**, selecione **Computadores**.
   5. No painel **Agente Direto**, selecione **Fazer o download do Gateway do OMS**.
   
    ![Baixar o Gateway do OMS](./media/log-analytics-oms-gateway/download-gateway.png)

-- OU -- 

   1. Na folha do seu espaço de trabalho em **Configurações**, selecione **Configurações avançadas**.
   2. Selecione **Fontes Conectadas** > **Servidores Windows**. Em seguida, selecione **Fazer o download do Gateway do OMS**.

## <a name="install-the-oms-gateway"></a>Instalar o Gateway do OMS

Para instalar um gateway, execute as etapas a seguir. Se você instalou a versão anterior, chamada *Encaminhador do Log Analytics*, ele será atualizado para esta versão.  

1. Na pasta de destino, selecione **OMS Gateway.msi**.
2. Sobre o **boas-vindas** página, selecione **próxima**.

 ![Assistente de Instalação do Gateway](./media/log-analytics-oms-gateway/gateway-wizard01.png)
  
3. Na página **Contrato de Licença**, selecione **Aceito os termos do Contrato de Licença**. Em seguida, selecione **Avançar**.

4. Na página **Porta e endereço de proxy**:

   a. Digite o número da porta TCP a ser usada para o gateway. A instalação configura uma regra de entrada com esse número de porta no firewall do Windows. O valor padrão é 8080. O intervalo válido do número de porta é de 1 a 65535. Se a entrada não estiver dentro desse intervalo, uma mensagem de erro será exibida.

   b. Como opção, se o servidor onde o gateway estiver instalado precisar se comunicar por meio de um proxy, digite o proxy ao qual gateway precisa se conectar. Um exemplo seria `http://myorgname.corp.contoso.com:80`, conforme mostrado na seguinte captura de tela. Se você deixar esse campo em branco, o gateway tenta se conectar diretamente à internet.  Se o servidor proxy exigir autenticação, insira um nome de usuário e senha.
   
    ![Configuração de proxy do Assistente de Gateway](./media/log-analytics-oms-gateway/gateway-wizard02.png)

4. Selecione **Avançar**.

5. Se o Microsoft Update não estiver habilitado, a página Microsoft Update será exibida e você poderá optar por habilitá-lo. Faça uma seleção e selecione **Avançar**. Caso contrário, prossiga para a próxima etapa.

6. Na página **Pasta de Destino**, deixe a pasta padrão como **C:\ProgramFiles\OMS Gateway** ou digite o local em que deseja instalar o gateway do OMS. Em seguida, selecione **Avançar**.

7. Na página **Pronto para instalar**, selecione **Instalar**. O Controle de Conta de Usuário pode aparecer solicitando permissão para instalação. Se você receber uma solicitação de permissão, selecione **Sim**.

8. Após a conclusão da instalação, selecione **Concluir**. Para verificar se o serviço está em execução, abra o snap-in services.msc e verifique se o **Gateway do OMS** aparece na lista de serviços e seu status é **Em execução**.

    ![Serviços – Gateway do OMS](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Configurar o balanceamento de carga de rede 
Você pode configurar o gateway para alta disponibilidade usando o balanceamento de carga de rede (NLB). Use o balanceamento de carga de rede da Microsoft ou balanceadores de carga com base em hardware.  O balanceador de carga gerencia o tráfego redirecionando as conexões solicitadas dos servidores de gerenciamento do Operations Manager ou de agentes do OMS entre seus nós. Se um servidor de gateway falhar, o tráfego será redirecionado para outros nós.

Para saber como projetar e implantar um cluster de balanceamento de carga de rede Windows Server 2016, confira [Balanceamento de carga de rede](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  As etapas a seguir descrevem como configurar uma cluster de balanceamento de carga de rede Microsoft Network.  

1.  Entre com uma conta administrativa no servidor Windows que seja membro do cluster NLB.  

2.  No Gerenciador de servidores, abra o Gerenciador de balanceamento de carga de rede. Selecione **ferramentas**e, em seguida, selecione **Gerenciador de balanceamento de carga de rede**.

3. Para conectar um servidor do Gateway do OMS ao Microsoft Monitoring Agent instalado, clique com o botão direito no endereço IP do cluster e selecione **Adicionar Host ao Cluster**.

 ![Gerenciador de Balanceamento de Carga de Rede -- Adicionar Host ao Cluster](./media/log-analytics-oms-gateway/nlb02.png)

4. Digite o endereço IP do servidor de gateway ao qual deseja se conectar.

    ![Gerenciador de Balanceamento de Carga de Rede – Adicionar Host ao Cluster: Conectar](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-the-oms-agent-and-the-operations-manager-management-group"></a>Configurar agente do OMS e grupo de gerenciamento do Operations Manager
As seções a seguir neste artigo incluem as etapas sobre como configurar agentes do OMS, um grupo de gerenciamento do Operations Manager ou Hybrid Runbook Workers da Automação do Azure conectados diretamente ao Gateway do OMS para se comunicar com a Automação do Azure ou o Log Analytics.  

Para entender os requisitos e as etapas para instalar o agente do OMS em computadores com Windows que se conectam diretamente ao Log Analytics, consulte [Coletar dados de computadores em seu ambiente com o Log Analytics](log-analytics-concept-hybrid.md#prerequisites).

 Para computadores Linux, veja [Conectar computadores Linux ao Log Analytics](log-analytics-quick-collect-linux-computer.md). Para obter informações relacionadas para o Hybrid Runbook Worker de automação, consulte [implantar Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-the-standalone-oms-agent"></a>Configurar agente do OMS autônomo
Para obter informações sobre como configurar um agente para usar um servidor proxy (que nesse caso é o gateway), consulte [Coletar dados de computadores em seu ambiente com o Log Analytics](log-analytics-concept-hybrid.md#prerequisites). Se você implantou vários servidores gateway por trás de um balanceador de carga de rede, a configuração de proxy de agente do OMS é o endereço IP virtual do NLB:

![Propriedades do Microsoft Monitoring Agent – configurações de proxy](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="configure-operations-manager-all-agents-use-the-same-proxy-server"></a>Configurar o Operations Manager: todos os agentes usam o mesmo servidor proxy
Configure o Operations Manager para adicionar o servidor de gateway.  A configuração do proxy do Operations Manager é aplicada automaticamente a todos os agentes que se reportam ao Operations Manager, mesmo se a configuração está vazia.  

Para usar o Gateway do OMS para dar suporte ao Operations Manager, os seguintes componentes devem estar em vigor:

* Microsoft Monitoring Agent (versão do agente **8.0.10900.0** ou posterior) instalado no servidor do gateway e configurado para os espaços de trabalho do Log Analytics com os quais quer se comunicar.

* Um gateway que tenha conectividade com a internet ou uma conexão a um servidor proxy que tenha.

> [!NOTE]
> Se você não especificar um valor para o gateway, os valores em branco serão enviados por push a todos os agentes.
> 

Se esta for a primeira vez em que o grupo de gerenciamento do Operations Manager está sendo registrado com um espaço de trabalho do Log Analytics, a opção para especificar a configuração de proxy para o grupo de gerenciamento não estará disponível no console de operações. 

O grupo de gerenciamento deve ser registrado com êxito com o serviço antes que essa opção esteja disponível. Atualize a configuração de proxy do sistema usando Netsh no mesmo sistema no qual você está executando o console de operações todos os servidores de gerenciamento no grupo de gerenciamento.

1. Abra um prompt de comandos com privilégios elevados.

    a. Vá para **Iniciar**. Em seguida, digite **cmd**.
    
    b. Clique com botão direito em **Prompt de comando**. Em seguida, selecione **Executar como administrador**.
    
2. Insira o seguinte comando e selecione **Inserir**:

    `netsh winhttp set proxy <proxy>:<port>`

Depois de concluir a integração com o Log Analytics, você poderá remover a alteração executando `netsh winhttp reset proxy`. Em seguida, use a opção **Configurar servidor de proxy** no console de operações para especificar o servidor de Gateway do OMS. 

1. Abra o console do Operations Manager. Em **Operations Management Suite**, selecione **Conexão**. Em seguida, selecione **Configurar servidor proxy**.

    ![Operations Manager -- Configurar o servidor proxy](./media/log-analytics-oms-gateway/scom01.png)

2. Selecione **usar um servidor proxy para acessar o Operations Management Suite**. Insira o endereço IP do servidor de Gateway do OMS ou o endereço IP virtual do NBL. Inicie com o prefixo `http://`.

    ![Operations Manager – endereço do servidor proxy](./media/log-analytics-oms-gateway/scom02.png)

3. Selecione **Concluir**. O grupo de gerenciamento do Operations Manager agora está configurado para se comunicar por meio do servidor de gateway para o serviço de Log Analytics.

### <a name="configure-operations-manager-specific-agents-use-proxy-server"></a>Configurar o Operations Manager: agentes específicos usam servidor proxy
Para ambientes grandes ou complexos, você talvez queira que servidores (ou grupos) específicos usem o servidor de gateway do OMS. Para esses servidores, você não pode atualizar o agente do Operations Manager diretamente, pois esse valor é substituído pelo valor global do grupo de gerenciamento. Em vez disso, substitua a regra que é usada para enviar esses valores.  

> [!NOTE] 
> Essa mesma técnica de configuração pode ser usada para permitir o uso de vários servidores de gateway do OMS no seu ambiente. Por exemplo, você pode exigir que os servidores de gateway de OMS sejam especificados com base em cada região.
>  

1. Abra o console do Operations Manager e selecione o espaço de trabalho **Criação**.

2. No espaço de trabalho de Criação, selecione **Regras**. Selecione o botão **Escopo** na barra de ferramentas do Operations Manager. Se esse botão não estiver disponível, verifique se você tem um objeto, não uma pasta, selecionado no painel **Monitoramento**. A caixa de diálogo **Delimitar Objetos do Pacote de Gerenciamento** exibe uma lista de objetos, grupos ou classes comuns de destino. 

3. No campo **Procurar**, digite **Serviço de Integridade**. Em seguida, selecione-o na lista. Selecione **OK**.  

4. Pesquise a regra **Regra de Configuração de Proxy do Advisor**. Na barra de ferramentas do console de operações, selecione **Substitui**. Em seguida, selecione **Substituir a um objeto específico da classe Rule\For: Serviço de Integridade**. 

5. Em seguida, selecione um objeto específico da lista. 

6. (Opcional) Crie um grupo personalizado que contenha o objeto de serviço de integridade dos servidores ao qual você deseja aplicar essa substituição. Em seguida, você pode aplicar a substituição para esse grupo.

7. Na caixa de diálogo **Propriedades da Substituição**, clique para marcar a coluna **Substituir** ao lado do parâmetro **WebProxyAddress**.  No **Valor de Substituição**, insira a URL do servidor de gateway do OMS garantindo que se inicia com o prefixo `http://`.  

    >[!NOTE]
    > Você não precisa habilitar a regra. Ela está sendo gerenciada automaticamente com uma substituição contida no pacote de gerenciamento Microsoft System Center Advisor Secure Reference Override direcionado ao grupo do servidor de monitoramento do Microsoft System Center Advisor.
    >   

8. Selecione um pacote de gerenciamento da lista **Selecionar pacote de gerenciamento de destino** ou crie um novo pacote de gerenciamento sem lacre selecionando **Novo**. 

9. Quando você concluir as alterações, selecione **OK**. 

### <a name="configure-the-oms-gateway-for-automation-hybrid-runbook-workers"></a>Configurar o Gateway do OMS para Hybrid Runbook Worker de Automação
Se você tiver Hybrid Runbook Workers de Automação no ambiente, as etapas a seguir fornecem soluções alternativas temporárias manuais para configurar o Gateway do OMS que dê suporte a eles.

Nas etapas a seguir, você precisa saber em que região do Azure a conta de Automação reside. Para encontrar o local, execute as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Selecione o serviço de Automação do Azure.
3. Selecione a conta de Automação do Azure apropriada.
4. Em **Local**, exiba a região da conta.

    ![Portal do Azure – local da conta de Automação](./media/log-analytics-oms-gateway/location.png)  

Use as seguintes tabelas para identificar a URL de cada local:

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

Se o computador for registrado automaticamente como um Hybrid Runbook Worker para aplicação de patch usando a solução Gerenciamento de Atualizações, siga estas etapas:

1. Adicione as URLs de serviço de dados de tempo de execução do trabalho à lista Hosts Permitidos no Gateway do OMS. Por exemplo, digite o seguinte: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

2. Reinicie o serviço do Gateway do OMS usando o seguinte cmdlet do PowerShell: `Restart-Service OMSGatewayService`

Se você integrar seu computador à Automação do Azure usando o cmdlet de registro do Hybrid Runbook Worker, siga estas etapas:

1. Adicione a URL do registro de serviço do agente à lista Hosts Permitidos no Gateway do OMS. Por exemplo, digite o seguinte: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`

2. Adicione as URLs de serviço de dados de tempo de execução do trabalho à lista Hosts Permitidos no Gateway do OMS. Por exemplo, digite o seguinte: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`

3. Reinicie o serviço do Gateway do OMS: `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets úteis do PowerShell
Os cmdlets podem ajudar na conclusão de tarefas que são necessárias para atualizar as definições de configuração do Gateway do OMS. Antes de você usá-los, certifique-se de executar as etapas a seguir:

1. Instalar o Gateway do OMS (MSI).
2. Abra uma janela do console do PowerShell.
3. Importe o módulo digitando este comando: `Import-Module OMSGateway`.
4. Se nenhum erro tiver ocorrido na etapa anterior, o módulo foi importado com êxito, e os cmdlets poderão ser usados. Digite `Get-Module OMSGateway`.
5. Depois de fazer as alterações usando os cmdlets, lembre-se de reiniciar o serviço de Gateway do OMS.

Se você receber um erro na etapa 3, o módulo não foi importado. O erro pode ocorrer quando o PowerShell não é capaz de encontrar o módulo. É possível encontrá-lo no caminho de instalação do Gateway do OMS: *C:\Arquivos de Programas\Microsoft OMS Gateway\PowerShell*.

| **Cmdlet** | **Parâmetros** | **Descrição** | **Exemplo** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Chave |Obtém a configuração do serviço |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Chave (obrigatória) <br> Valor |Altera a configuração do serviço |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Obtém o endereço do proxy de retransmissão (upstream) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Endereço<br> Nome de Usuário<br> Senha |Define o endereço (e a credencial) do proxy da retransmissão (upstream) |1. Define um proxy de retransmissão e uma credencial:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Define um proxy de resposta que não precise de autenticação: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Desmarca a configuração do proxy de retransmissão:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Obtém o host atualmente permitido (somente o host permitido configurado localmente; isso não inclui hosts permitidos baixados automaticamente) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (obrigatório) |Adiciona o host à lista de permissões |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (obrigatório) |Remove o host da lista de permissões |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Adiciona o assunto do certificado do cliente à lista de permissões |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Remove o assunto do certificado do cliente da lista de permissões |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Obtém os assuntos do certificado do cliente atualmente permitidos (somente os assuntos permitidos configurados localmente; isso não inclui assuntos permitidos baixados automaticamente) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>solução de problemas
Para coletar eventos registrados pelo gateway, você precisará ter também o agente do OMS instalado.

![Visualizador de Eventos -- Log do Gateway do OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**IDs e descrições dos eventos de Gateway do OMS**

A tabela a seguir mostra as IDs e descrições de eventos do log do Gateway do OMS:

| **ID** | **Descrição** |
| --- | --- |
| 400 |Qualquer erro de aplicativo que não tenha uma ID específica. |
| 401 |Configuração incorreta. Por exemplo: listenPort = "texto" em vez de um número inteiro. |
| 402 |Exceção ao analisar mensagens de handshake TLS. |
| 403 |Rede de rede. Por exemplo: não é possível se conectar ao servidor de destino. |
| 100 |Informações gerais. |
| 101 |Serviço iniciado. |
| 102 |Serviço interrompido. |
| 103 |Comando HTTP CONNECT recebido do cliente. |
| 104 |Não é um comando HTTP CONNECT. |
| 105 |O servidor de destino não está na lista de permissões ou a porta de destino não é segura (443). <br> <br> Verifique se o agente MMA no servidor do Gateway e os agentes que se comunicam com o Gateway estão conectados ao mesmo espaço de trabalho do Log Analytics. |
| 105 |ERRO TcpConnection – Certificado de cliente inválido: CN=Gateway <br><br> Verifique se: <br>    <br> - Você está usando um Gateway com o número de versão 1.0.395.0 ou superior. <br> - O agente MMA no servidor do Gateway e os agentes que se comunicam com o gateway estão conectados ao mesmo espaço de trabalho do Log Analytics. |
| 106 |O Gateway de OMS só dá suporte ao protocolo TLS 1.0, 1.1 e 1.2.  Ele não oferece suporte ao protocolo SSL. Para qualquer versão do protocolo TLS/SSL sem suporte, o OMS Gateway gera a ID de evento 106.|
| 107 |A sessão TLS foi verificada. |

**Contadores de desempenho a serem coletados**

A tabela a seguir mostra os contadores de desempenho disponíveis para o Gateway do OMS. Você pode adicionar os contadores usando o Monitor de Desempenho.

| **Nome** | **Descrição** |
| --- | --- |
| Gateway do OMS/Conexão de Cliente Ativo |Número de conexões de rede de cliente ativo (TCP) |
| Gateway do OMS/Contagem de Erro |Número de erros |
| Gateway do OMS/Cliente Conectado |Número de clientes conectados |
| Gateway do OMS/Contagem de Rejeição |Número de rejeições devido a qualquer erro de validação de TLS |

![Contadores de desempenho do Gateway OMS](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Obter assistência
Quando estiver conectado ao portal do Azure, você poderá criar uma solicitação de assistência com o Gateway do OMS, ou qualquer outro serviço do Azure ou recurso de um serviço.

Para solicitar assistência, selecione o ponto e interrogação no canto superior direito do portal. Em seguida, selecione **Nova solicitação de suporte**. Conclua o novo formulário de solicitação de suporte.

![Nova solicitação de suporte](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>Próximas etapas
[Adicionar fontes de dados](log-analytics-data-sources.md) para coletar dados de suas fontes conectadas e armazená-los no seu espaço de trabalho do Log Analytics.
