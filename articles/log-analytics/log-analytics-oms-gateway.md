---
title: Conectar computadores offline para o Operations Management Suite usando o OMS Gateway | Microsoft Docs
description: "Conecte computadores offline que são gerenciados pelo Operations Management Suite e monitorados pelo System Center Operations Manager por meio do Gateway do OMS para enviar dados para o serviço Operations Management Suite."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: magoedte; banders
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19bf9b3d65210458e4f018dc591e35e59287cd8e
ms.lasthandoff: 04/03/2017

---

# <a name="connect-offline-computers-to-operations-management-suite-by-using-oms-gateway"></a>Conectar computadores offline para o Operations Management Suite usando o Gateway do OMS

Computadores que são gerenciados pelo Operations Management Suite (OMS) e monitorados pelo System Center Operations Manager (Operations Manager) podem enviar dados para o serviço Operations Management Suite quando não tiverem acesso à Internet. O Gateway do OMS é um proxy de encaminhamento de HTTP que dá suporte ao túnel HTTP usando o comando HTTP CONNECT. Gateway do OMS pode coletar dados e enviá-lo para o serviço Operations Management Suite em nome dos computadores offline.  

Você pode usar o Gateway de OMS com:

* Hybrid Runbook Worker da Automação do Azure.  
* Computadores com Windows que possuem o agente de monitoramento da Microsoft e que estão diretamente conectado a um espaço de trabalho do Operations Management Suite.
* System Center Operations Manager 2012 SP1 com 7 Update Rollup (UR), System Center Operations Manager 2012 R2 com UR3 ou um grupo de gerenciamento do System Center Operations Manager 2016 é integrado com o Operations Management Suite.  

Algumas políticas de segurança de TI não permitem que computadores em rede para se conectar à Internet. Isso pode ser verdadeiro para dispositivos do ponto de venda (PDV), ou para servidores que oferecem suporte a serviços de TI. Mas, você precisa conectar os computadores para o Operations Management Suite para gerenciar e monitorá-los. Você pode configurar computadores offline para se comunicar diretamente com o Gateway do OMS. O gateway recebe dados de configuração e encaminhamento em nome dos computadores offline.  

Se você configurar computadores que possuem o agente do Operations Management Suite para se conectar diretamente a um espaço de trabalho do Operations Management Suite, todos os computadores em vez disso, se comuniquem por meio do gateway do OMS. O gateway transfere dados dos agentes, diretamente ao Operations Management Suite. O gateway não analisa os dados em trânsito.

Quando você integra um grupo de gerenciamento do Operations Manager com o Operations Management Suite, você pode configurar servidores de gerenciamento para se conectar ao gateway do OMS. Os servidores de recebem informações de configuração e, em seguida, enviam os dados coletados, dependendo da solução que você configurou. Os agentes do Operations Manager enviam alguns dados, como alertas, avaliação de configuração, espaço de instância e dados de capacidade para o servidor de gerenciamento. Outros dados de alto volume, como logs do IIS, eventos de desempenho e de segurança, são enviados diretamente ao Gateway do OMS. 

Um servidor de gateway do Operations Manager é implantado em uma rede de perímetro (também conhecido como *DMZ*, *zona desmilitarizada*, e *sub-rede filtrada*) ou outra rede isolada para monitorar os sistemas não confiáveis não pode se comunicar com o gateway do OMS. Os servidores de gateway do Operations Manager só podem se reportar a um servidor de gerenciamento. Quando você configura um grupo de gerenciamento do Operations Manager com o gateway do OMS, as informações de configuração de proxy são distribuídas automaticamente a todos os computadores gerenciados por agente configurados para coletar dados para o Log Analytics. Informações de configuração de proxy são distribuídas automaticamente mesmo se a configuração está vazia.    

Para fornecer alta disponibilidade para agentes conectados diretamente ou grupos de gerenciamento de operações que se comunicam com o Operations Management Suite por meio do gateway, você pode usar o (NLB) de balanceamento de carga de rede. O NLB redireciona e distribui o tráfego entre vários servidores gateway. Se um servidor de gateway falhar, o tráfego será redirecionado para outro nó disponível.  

Recomendamos que você instale o agente do Operations Management Suite no computador que está executando o software de gateway do OMS. Dessa forma, você pode monitorar o gateway do OMS e analisar dados de eventos ou desempenho. O agente também ajuda o gateway do OMS a identificar os pontos de extremidade de serviço com que precisa se comunicar.

Cada agente deve ter conectividade de rede com seu gateway para que os agentes possam transferir dados automaticamente para dentro e fora do gateway. É recomendável que você não instale o gateway em um controlador de domínio.

O diagrama a seguir mostra o fluxo de dados de agentes diretos para o Operations Management Suite por meio do servidor de gateway. Configuração de proxy para agentes deve corresponder à porta em que o gateway do OMS é configurado para se comunicar com o Operations Management Suite.  

![comunicação do agente direto com o diagrama Operations Management Suite](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

O diagrama a seguir mostra o fluxo de dados de um grupo de gerenciamento do Operations Manager para o Operations Management Suite.   

![Comunicação do Gerenciador de operações com o diagrama Operations Management Suite](./media/log-analytics-oms-gateway/oms-omsgateway-opsmgrconnect.png)

## <a name="prerequisites"></a>Pré-requisitos

Um computador que você designa para executar o Gateway do OMS deve atender esses requisitos:

* Sistema operacional Windows 10, Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 ou Windows Server 2008
* Microsoft .NET Framework 4.5
* Mínima de 4 núcleos processador
* Mínimo de 8 GB de memória 

### <a name="language-availability"></a>Disponibilidade de idiomas

O Gateway do OMS está disponível nos seguintes idiomas:

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

## <a name="download-oms-gateway"></a>Baixar o Gateway do OMS

Você tem três opções para obter a versão mais recente do gateway do OMS:

* Baixe-o do [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

* Baixe-o do portal do Operations Management Suite. Após entrar no seu espaço de trabalho do Operations Management Suite:

    1. Vá até **Configurações** > **Fontes Conectadas** > **Servidores Windows**. 
    2. Selecione **Baixar Gateway do OMS**.

* Baixe-o do [portal do Azure](https://portal.azure.com). Depois de entrar:  

   1. Na lista de serviços, selecione **Log Analytics**.  
   2. Selecione um espaço de trabalho.
   3. Na folha do seu espaço de trabalho, em **Geral**, selecione **Início Rápido**.
   4. Em **Escolher uma fonte de dados para conectar ao espaço de trabalho**, selecione **Computadores**.
   5. Na folha **Agente Direto**, selecione **Baixar Gateway do OMS**.<br><br> ![Baixar Gateway do OMS](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-oms-gateway"></a>Instalar o Gateway do OMS

Para instalar o gateway, conclua as etapas a seguir. 

> [!NOTE]
> Se você tiver uma versão anterior do gateway (anteriormente chamado de encaminhador do Log Analytics) instalada, a versão anterior é atualizada para a versão atual do Gateway do OMS.
>

1. Na pasta de destino, clique duas vezes em **OMS Gateway.msi**.
2. Sobre o **boas-vindas** página, selecione **próxima**.<br><br> ![Assistente de Instalação do Gateway](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. Sobre o **contrato de licença** page para concordar com os termos de licença de Software Microsoft, selecione **aceito os termos do contrato de licença**. 
4. Na página **Porta e endereço de proxy**:

   1. Insira o número da porta TCP a ser usado para o gateway. A instalação configura uma regra de entrada com esse número de porta no Firewall do Windows. O valor padrão é 8080.
      O intervalo válido do número de porta é de 1 a 65535. Se a entrada não estiver dentro desse intervalo, uma mensagem de erro será exibida.
   2. Como opção, se o servidor onde o gateway estiver instalado precisar se comunicar por meio de um proxy, insira o endereço do proxy onde o gateway precisa para se conectar. Por exemplo, **http://myorgname.corp.contoso.com:80**. Se a caixa de endereço de proxy estiver vazia, o gateway tenta se conectar diretamente à Internet. Se o servidor proxy exigir autenticação, insira um nome de usuário e senha.<br><br> ![Configuração de proxy do Assistente de Gateway](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
5. Se o Microsoft Update não estiver habilitado no computador, o **Microsoft Update** página será exibida. Você pode optar por habilitá-lo nesta página. Caso contrário, prossiga para a próxima etapa.
6. Na página **Pasta de Destino**, deixe a pasta padrão C:\Arquivos de Programas\OMS Gateway ou insira o local em que deseja instalar o gateway.
7. Na página **Pronto para instalar**, selecione **Instalar**. Pode aparecer uma caixa de diálogo que solicita permissão para instalar. Selecione **Sim**.
8. Selecione **Concluir**. Para verificar se o serviço está em execução, abra o snap-in Services.msc e verifique se o **Gateway do OMS** aparece na lista de serviços. O status deve ser **executando**.<br><br> ![Verificar serviços para OMS Gateway](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="set-up-network-load-balancing"></a>Configurar o balanceamento de carga de rede 
Você pode configurar o gateway do OMS para alta disponibilidade usando o NLB. Você pode usar o recurso de balanceamento de carga de rede no Windows Server ou balanceadores de carga com base em hardware. Para gerenciar o tráfego, o balanceador de carga redireciona conexões solicitados de agentes do Operations Management Suite ou servidores de gerenciamento do Operations Manager em seus nós. Se um servidor de gateway falhar, o tráfego será redirecionado para outros nós.

Para saber como projetar e implantar um cluster de Balanceamento de Carga de Rede do Windows Server 2016, confira [Balanceamento de Carga de Rede](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  

Para configurar um cluster de balanceamento de carga de rede do Windows Server, conclua as seguintes etapas:  

1. Usando uma conta de administrador, entre para o Windows server que seja membro do cluster de balanceamento de carga de rede.  
2. No Gerenciador de servidores, abra o Gerenciador de balanceamento de carga de rede.
3. Selecione **ferramentas**e, em seguida, selecione **Gerenciador de balanceamento de carga de rede**.
4. Para conectar um servidor do Gateway do OMS ao Microsoft Monitoring Agent instalado, clique com o botão direito no endereço IP do cluster e selecione **Adicionar Host ao Cluster**.<br><br> ![Gerenciador de Balanceamento de Carga de Rede: Adicionar Host ao Cluster](./media/log-analytics-oms-gateway/nlb02.png)<br> 
5. Digite o endereço IP do servidor de gateway que deseja conectar.<br><br> ![Gerenciador de Balanceamento de Carga de Rede – Adicionar Host ao Cluster, Conectar](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="set-up-the-operations-management-suite-agent-and-an-operations-manager-management-group"></a>Configurar o agente do Operations Management Suite e um grupo de gerenciamento do Operations Manager
Nesta seção, descreveremos como configurar agentes do Operations Management Suite diretamente conectados e um grupo de gerenciamento do Operations Manager. Pode também configurar o Hybrid Runbook Worker da Automação do Azure para usar o gateway do OMS para se comunicar com o Operations Management Suite.  

Para saber mais sobre os requisitos e as etapas para instalar o agente do Operations Management Suite em computadores com Windows que estão conectados diretamente ao Operations Management Suite, veja [Conectar computadores com Windows ao Operations Management Suite](log-analytics-windows-agents.md). Para computadores Linux, veja [Conectar computadores Linux ao Operations Management Suite](log-analytics-linux-agents.md). 

### <a name="set-up-the-operations-management-suite-agent-and-operations-manager-to-use-the-oms-gateway-as-a-proxy-server"></a>Configurar o agente do Operations Management Suite e o Operations Manager para usar o gateway do OMS como um servidor proxy

### <a name="set-up-a-standalone-operations-management-suite-agent"></a>Configurar um agente do Operations Management Suite autônomo
Para saber mais sobre como configurar um agente para usar um servidor proxy, veja [Definir configurações de proxy e firewall com o Microsoft Monitoring Agent](log-analytics-proxy-firewall.md). Nesse caso, o servidor proxy é o gateway. Se você tiver implantado vários servidores gateway por trás de um balanceador de carga de rede, a configuração de proxy de agente do Operations Management Suite é o endereço IP virtual do balanceador de carga de rede:<br><br> ![Propriedades do Microsoft Monitoring Agent: configurações de proxy](./media/log-analytics-oms-gateway/nlb04.png)

### <a name="set-up-operations-manager-all-agents-use-the-same-proxy-server"></a>Configurar o Operations Manager (todos os agentes usam o mesmo servidor proxy)
Configure o Operations Manager para adicionar o servidor de gateway. A configuração do proxy do Operations Manager é aplicada automaticamente a todos os agentes que se reportam ao Operations Manager, mesmo se a configuração está vazia.

Para usar o gateway do OMS com o Operations Manager, você deve atender aos seguintes requisitos:

* Microsoft Monitoring Agent (agent versão **8.0.10900.0** e versões posteriores) deve ser instalado no servidor gateway. Ele deve ser configurado para os espaços de trabalho de agente Operations Management Suite que você deseja se comunicar.
* O gateway deve ter conectividade com a Internet ou estar conectado a um servidor proxy que tenha.

> [!NOTE]
> Se você não especificar um valor para o gateway, os valores em branco serão enviados por push a todos os agentes.

Para conectar o servidor do Operations Manager para seu espaço de trabalho de agente do Operations Management Suite:

1. No console do Operations Manager, vá para **Operations Management Suite** > **Conexão** > **configurar servidor Proxy**.<br><br> ![Operations Manager: configurar o servidor proxy](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. Selecione **usar um servidor proxy para acessar o Operations Management Suite**. Insira o endereço IP do servidor de gateway do OMS, ou o endereço IP virtual do balanceador de carga de rede. Inicie com o prefixo **http://**.<br><br> ![Operations Manager: endereço do servidor proxy](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. Selecione **Concluir**. 

### <a name="set-up-operations-manager-specific-agents-use-the-proxy-server"></a>Configurar o Operations Manager (agentes específicos usam servidor proxy)
Para ambientes grandes ou complexos, você talvez queira que servidores (ou grupos) específicos usem o servidor de gateway do OMS. Para esses servidores, é possível atualizar o agente do Operations Manager diretamente. O valor é substituído pelo valor global do grupo de gerenciamento. Em vez disso, substitua a regra que é usada para enviar esses valores.

> [!NOTE] 
> Você pode usar a mesma técnica de configuração para executar vários servidores de gateway do OMS no seu ambiente. Por exemplo, você pode exigir que os servidores de gateway de OMS sejam especificados com base em cada região.

1. No console do System Center Operations Manager, selecione o **criação** espaço de trabalho.  
2. Selecione **regras**e na barra de ferramentas de gerenciamento de operações do System Center, selecione o **escopo** botão. Se esse botão não estiver disponível, verifique se você tem um objeto, e não uma pasta, selecionado no painel **Monitoramento**. A caixa de diálogo **Delimitar Objetos do Pacote de Gerenciamento** exibe uma lista de objetos, grupos ou classes comuns de destino. 
3. No **procure** , digite **serviço de integridade**. Selecione-o na lista. Selecione **OK**.  
4. Na barra de ferramentas do Operations Manager console, pesquise a regra **regra de configuração de Proxy do Advisor**. Selecione **substituições**e, em seguida, aponte para **substituir a um objeto específico da classe Rule\For: serviço de integridade**. Selecione um objeto específico da lista. Opcionalmente, você pode criar um grupo personalizado que possui o objeto de serviço de integridade dos servidores que você deseja aplicar essa substituição é para. Em seguida, aplica a substituição para esse grupo.
5. No **propriedades da substituição** caixa de diálogo, em seguida o **WebProxyAddress** parâmetro, selecione o **substituir** coluna. No **valor de substituição** , digite a URL do servidor de gateway do OMS. Inicie com o prefixo **http://**.

   >[!NOTE]
   > Você não precisa habilitar a regra. A regra é gerenciada automaticamente com uma substituição no pacote de gerenciamento do System Center Advisor Secure referência substituir. O pacote de gerenciamento destina-se o grupo do System Center Advisor monitoramento de servidor.
   > 

6. Para especificar um pacote de gerenciamento, siga um destes procedimentos:
    * No **selecionar o pacote de gerenciamento de destino** , selecione um pacote de gerenciamento.
    * Para criar um pacote de gerenciamento sem lacre, novo, selecione **novo**. 
7. Selecione **OK**. 

### <a name="set-up-automation-hybrid-workers"></a>Configurar workers híbridos da automação
Se você tiver o runbook workers híbridos da automação do Azure em seu ambiente, você pode criar uma solução alternativa manual, temporária para que o gateway oferece suporte a eles.

Para as etapas a seguir, você precisa saber em que região do Azure a conta de Automação reside. Para encontrar o local:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Selecione o serviço de Automação do Azure.
3. Selecione a conta de automação do Azure relevante.
4. Em **Local**, veja a região.<br><br> ![Portal do Azure: local da conta de Automação](./media/log-analytics-oms-gateway/location.png)  

Use as seguintes tabelas para identificar a URL de cada local:

**URLs de serviço de dados de tempo de execução do trabalho**

| Local | URL |
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

| Local | URL |
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

Se o computador for automaticamente registrado como um hybrid runbook worker para aplicação de patch usando a solução Gerenciamento de Atualizações, conclua estas etapas:

1. Adicione as URLs de serviço de dados do Tempo de Execução do Trabalho à lista **Hosts Permitidos** no gateway do OMS. Por exemplo:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Reinicie o serviço do Gateway do OMS usando o seguinte cmdlet do Azure PowerShell: `Restart-Service OMSGatewayService`

Se o computador estiver integrado à Automação do Azure usando o cmdlet de registro do Hybrid Runbook Worker, conclua estas etapas:

1. Adicione a URL do registro de serviço do agente à lista **Hosts Permitidos** no gateway do OMS. Por exemplo: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Adicione as URLs de serviço de dados do Tempo de Execução do Trabalho à lista **Hosts Permitidos** no gateway do OMS. Por exemplo:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Reinicie o serviço do gateway do OMS:  `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets úteis do PowerShell
Cmdlets pode ajudá-lo a atualizar as configurações de gateway do OMS. Antes de usar cmdlets do PowerShell, não se esqueça:

1. Instale o Gateway do OMS (MSI).
2. Abra uma janela do console do PowerShell.
3. Para importar o módulo, insira este comando: `Import-Module OMSGateway` Se nenhum erro ocorrer, o módulo foi importado com êxito e você pode usar os cmdlets. 
4. Digite `Get-Module OMSGateway`.
5. Depois de fazer as alterações usando os cmdlets, reinicie o serviço de gateway do OMS.

Se você receber um erro na etapa 3, o módulo não foi importado. Esse erro pode ocorrer se o PowerShell não pode localizar o módulo. O módulo está listado no caminho de instalação do gateway: C:\Arquivos de Programas\Microsoft OMS Gateway\PowerShell.

| Cmdlet | parâmetros | Descrição | Exemplos |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |Chave (obrigatória) <br> Valor |Altera a configuração de serviço |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |Chave |Obtém a configuração do serviço |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |Endereço <br> Nome de Usuário <br> Senha |Define o endereço (e as credenciais) do proxy da retransmissão (upstream) |1. Defina um proxy de resposta e a credencial: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2. Defina um proxy de resposta que não precise de autenticação: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3. Desmarque a configuração do proxy de resposta, isto é, não há necessidade de um proxy de resposta: `Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |Obtém o endereço do proxy de retransmissão (upstream) |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |Host (obrigatório) |Adiciona o host à lista de permissões |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |Host (obrigatório) |Remove o host da lista de permissões |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |Obtém o host atualmente permitido (somente o host permitido configurado localmente; isso não inclui hosts permitidos baixados automaticamente) |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Adiciona o assunto do certificado do cliente à lista de permissões |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Remove o assunto do certificado do cliente da lista de permissões |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |Obtém os assuntos do certificado do cliente atualmente permitidos (somente os assuntos permitidos configurados localmente; isso não inclui assuntos permitidos baixados automaticamente) |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshooting"></a>Solucionar problemas
Para coletar eventos registrados pelo gateway, você precisa ter o agente do Operations Management Suite instalado.<br><br> ![Visualizador de Eventos: Log do gateway do OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**IDs e descrições dos eventos de gateway do OMS**

A tabela a seguir mostra as IDs e descrições de eventos do log do gateway do OMS.

| ID | Descrição |
| --- | --- |
| 400 |Qualquer erro de aplicativo que não tenha uma ID específica |
| 401 |Configuração incorreta. Por exemplo: listenPort = "\<texto\>" em vez de um número inteiro |
| 402 |Exceção ao analisar mensagens de handshake Transport Layer Security (TLS) |
| 403 |Rede de rede. Por exemplo: não é possível se conectar ao servidor de destino |
| 100 |Informações gerais |
| 101 |Serviço iniciado |
| 102 |Serviço interrompido |
| 103 |Comando HTTP CONNECT recebido do cliente |
| 104 |Não é um comando HTTP CONNECT |
| 105 |O servidor de destino não está na lista de permissões ou a porta de destino não é segura (443) <br> <br> Verifique se o agente Microsoft Monitoring Agent no servidor do gateway e os agentes que se comunicam com o gateway estão conectados ao mesmo espaço de trabalho do Log Analytics. |
| 105 |ERRO TcpConnection – Certificado de cliente inválido: CN=Gateway <br><br> Verifique se: <br>    <br> Você está usando a versão de Gateway OSM 1.0.395.0 ou uma versão posterior. <br> &#149; O agente Microsoft Monitoring Agent no servidor do gateway e os agentes que se comunicam com o gateway estão conectados ao mesmo espaço de trabalho do Log Analytics. |
| 106 |Qualquer motivo pelo qual a sessão TLS é suspeita e rejeitada |
| 107 |A sessão TLS foi verificada |

**Contadores de desempenho a serem coletados**

A tabela a seguir lista os contadores de desempenho disponíveis para o gateway do OMS. Você pode adicionar os contadores usando o Monitor de desempenho do Windows.

| Nome | Descrição |
| --- | --- |
| Gateway do OMS/Conexão de Cliente Ativo |Número de conexões de rede de cliente ativo (TCP) |
| Gateway do OMS/Contagem de Erro |Número de erros |
| Gateway do OMS/Cliente Conectado |Número de clientes conectados |
| Gateway do OMS/Contagem de Rejeição |Número de rejeições devido a qualquer erro de validação de TLS |

![Contadores de desempenho do Gateway OMS](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Obter assistência
No portal do Azure, quando estiver conectado, você poderá criar uma solicitação de assistência com o gateway do OMS, ou qualquer outro serviço do Azure ou recurso de serviço.
Para solicitar assistência, selecione o ponto e interrogação no canto superior direito do portal e selecione **Nova solicitação de suporte**. Conclua o novo formulário de solicitação de suporte.

![Nova solicitação de suporte](./media/log-analytics-oms-gateway/support.png)

Você também pode deixar comentários sobre o Operations Management Suite ou Log Analytics no [Fórum de comentários do Microsoft Azure](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Próximas etapas
Para coletar dados das fontes conectadas em seu espaço de trabalho do Operations Management Suite, você pode [adicionar fontes de dados](log-analytics-data-sources.md). Você pode armazenar a data no repositório Operations Management Suite.

