---
title: Conectar computadores e dispositivos ao OMS usando o Gateway do OMS | Microsoft Docs
description: "Conecte seus dispositivos gerenciados pelo OMS e computadores monitorados pelo Operations Manager ao Gateway do OMS para enviar dados ao serviço OMS quando eles não tiverem acesso à Internet."
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
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: de2e6d201ba54774697356e1cd55c4881664a401
ms.lasthandoff: 02/28/2017


---
# <a name="connect-computers-and-devices-without-internet-access-to-oms-using-the-oms-gateway"></a>Conectar computadores e dispositivos sem acesso à Internet a OMS usando o Gateway do OMS

Este documento descreve como dispositivos gerenciados pelo OMS e computadores monitorados pelo SCOM (System Center Operations Manager) podem enviar dados ao serviço OMS quando eles não tiverem acesso à Internet. O Gateway do OMS pode coletar os dados e enviá-los ao serviço OMS em seu nome.

O gateway é um proxy de encaminhamento de HTTP que dá suporte ao túnel HTTP usando o comando HTTP CONNECT. O gateway pode manipular até 2.000 dispositivos conectados simultaneamente do OMS quando executado em um servidor com Windows, uma CPU de 4 núcleos e 16 GB.

Por exemplo, sua empresa ou organização de grande porte pode ter servidores com conectividade de rede, mas pode não ter conectividade com a Internet. Em outro exemplo, você pode ter muitos dispositivos POS (ponto de venda) sem ter como monitorá-los diretamente. Ainda, em outro exemplo, o Operations Manager pode usar o Gateway do OMS como um servidor proxy. Nesses exemplos, o Gateway do OMS pode transferir dados dos agentes que estão instalados nesses servidores ou dispositivos POS para o OMS.

Em vez de cada agente enviar dados diretamente ao OMS e exigir uma conexão direta com a Internet, todos os dados dos agentes são enviados por meio de um único computador que tem uma conexão com a Internet. Esse computador é onde você instala e usa o gateway. Nesse cenário, é possível instalar agentes em quaisquer computadores em que deseja coletar dados. O gateway transfere dados dos agentes para o OMS diretamente — o gateway não analisa os dados que são transferidos.

Você precisa instalar o agente do OMS no computador em que o gateway também está instalado. Isso permite que você monitore o Gateway do OMS e analise dados de desempenho ou eventos do servidor em que ele está instalado. Além disso, o agente ajuda o Gateway do OMS a identificar os pontos de extremidade de serviço com que precisa se comunicar.

O gateway deve ter acesso à Internet para carregar dados no OMS. Cada agente também deve ter conectividade de rede com seu gateway para que os agentes possam transferir dados automaticamente para dentro e fora do gateway. Para obter melhores resultados, não instale o gateway em um computador que também seja um controlador de domínio.

Veja um diagrama que mostra o fluxo de dados de agentes diretos para o OMS.

![diagrama do agente direto](./media/log-analytics-oms-gateway/direct-agent-diagram.png)

Veja um diagrama que mostra o fluxo de dados do Operations Manager para o OMS.

![Diagrama do Operations Manager](./media/log-analytics-oms-gateway/scom-mgt-server.png)

## <a name="language-availability"></a>Disponibilidade de idiomas

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

## <a name="download-the-oms-gateway"></a>Baixar o Gateway do OMS

Há três maneiras de obter o arquivo de configuração de Gateway do OMS.

### <a name="microsoft-download-center"></a>Centro de Download da Microsoft

- Baixe a versão mais recente do Gateway do OMS do [Centro de Download da Microsoft](http://download.microsoft.com/download/2/5/C/25CF992A-0347-4765-BD7D-D45D5B27F92C/OMS%20Gateway.msi).

### <a name="oms-portal"></a>Portal do OMS

1.    Faça logon em seu espaço de trabalho do OMS.
2.    Selecione **Configurações** > **Fontes Conectadas** > **Servidores Windows**.
3.    Clique em **Baixar Gateway do OMS**.


### <a name="azure-portal"></a>Portal do Azure

1. Acesse o [Portal do Azure](https://portal.azure.com) e entre, navegue pela lista de serviços e, em seguida, selecione **Log Analytics**.
2. Selecione um espaço de trabalho.
3. Na folha do espaço de trabalho, em **Geral**, clique em **Início Rápido**.
4. Em **Escolher uma fonte de dados para conectar ao espaço de trabalho**, clique em **Computadores**.
4. Na folha **Agente Direto**, clique em **Baixar Gateway do OMS**.  
    ![Baixar Gateway do OMS](./media/log-analytics-oms-gateway/download-gateway.png)


## <a name="install-the-oms-gateway"></a>Instalar o Gateway do OMS
A instalação desse Gateway substitui versões anteriores do Gateway que você instalou (Encaminhador do Log Analytics).

Pré-requisitos: .Net Framework 4.5, Windows Server 2012 R2 SP1 e superior


1. Para iniciar a instalação, clique duas vezes em **OMS Gateway.msi**.
2. Na página de boas-vindas, clique em **Avançar**.  
    ![Assistente de Instalação do Gateway](./media/log-analytics-oms-gateway/gateway-wizard01.png)
3. Na página Contrato de Licença, selecione **Aceito os termos do Contrato de Licença** para aceitar o EULA e clique em **Avançar**.
4. Na página de endereço de proxy e porta:
   1. Digite o número da porta TCP a ser usada para o gateway. A instalação abre esse número de porta no firewall do Windows. O valor padrão é 8080.
      O intervalo válido do número de porta é de 1 a 65535. Se a entrada não estiver dentro desse intervalo, uma mensagem de erro será exibida.
   2. Como opção, se o servidor onde o gateway estiver instalado precisar usar um proxy, digite o endereço do proxy onde o gateway precisa para se conectar. Por exemplo, `http://myorgname.corp.contoso.com:80`. Se estiver em branco, o gateway tentará se conectar à Internet diretamente. Caso contrário, o gateway se conectará ao proxy. Se o servidor proxy exigir autenticação, digite seu nome de usuário e senha.  
       ![Configuração de proxy do Assistente de Gateway](./media/log-analytics-oms-gateway/gateway-wizard02.png)  
   3. Clique em **Próximo**
5. Se as Atualizações da Microsoft não estiverem habilitadas, a página Atualização da Microsoft será exibida, onde você pode optar por habilitar as Atualizações da Microsoft. Faça uma seleção e clique em **Avançar**. Caso contrário, prossiga para a próxima etapa.
6. Na página Pasta de Destino, deixe a pasta padrão c:\ProgramFiles\OMS Gateway ou digite o local em que deseja instalar o gateway e clique em **Avançar**.
7. Na página Pronto para instalar, clique em **Instalar**. Um Controle de Conta de Usuário pode aparecer solicitando permissão para instalação. Nesse caso, clique em **Sim**.
8. Depois que Instalação estiver finalizada, clique em **Concluir**. Para verificar se o serviço está em execução, abra o snap-in services.msc e verifique se o **Gateway do OMS** aparece na lista de serviços.  
    ![Serviços – Gateway do OMS](./media/log-analytics-oms-gateway/gateway-service.png)

## <a name="install-an-agent-on-devices"></a>Instalar um agente em dispositivos
Se necessário, confira [Conectar computadores Windows ao Log Analytics](log-analytics-windows-agents.md) para obter informações sobre como instalar diretamente agentes conectados. O artigo descreve como é possível instalar o agente usando um assistente de Instalação ou usando a linha de comando.

## <a name="configure-oms-agents"></a>Configurar agentes OMS
Confira [Definir as configurações de proxy e firewall com o Microsoft Monitoring Agent](log-analytics-proxy-firewall.md) para obter informações sobre como configurar um agente para um servidor proxy, que nesse caso é o gateway.

Os agentes do Operations Manager enviam alguns dados, como alertas, avaliação de configuração, espaço de instância e dados de capacidade do Operations Manager, por meio do Servidor de Gerenciamento. Outros dados de alto volume, como logs do IIS, desempenho e segurança, são enviados diretamente ao Gateway do OMS. Confira [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md) para obter uma lista completa de dados que são enviados por meio de cada canal.

> [!NOTE]
> Se você planeja usar o Gateway com o balanceamento de carga de rede, confira [Configurar opcionalmente o balanceamento de carga de rede](#optionally-configure-network-load-balancing).
>
>

## <a name="configure-a-scom-proxy-server"></a>Configurar um servidor proxy do SCOM
Configure o Operations Manager para adicionar o gateway para atuar como um servidor proxy. Quando você atualiza a configuração do proxy, esta é aplicada automaticamente a todos os agentes que se reportam ao Operations Manager.

Para usar o Gateway para dar suporte ao Operations Manager, você precisa ter:

* Microsoft Monitoring Agent (versão do agente – **8.0.10900.0** e posterior) instalado no servidor do Gateway e configurado para os espaços de trabalho do OMS com os quais quer se comunicar.
* O gateway deve ter conectividade com a Internet ou estar conectado a um servidor proxy que tenha.

### <a name="to-configure-scom-for-the-gateway"></a>Para configurar o SCOM para o gateway
1. Abra o console do Operations Manager e, em **Operations Management Suite**, clique em **Conexão** e em **Configurar o Servidor Proxy**:  
    ![Operations Manager – Configurar o Servidor Proxy](./media/log-analytics-oms-gateway/scom01.png)
2. Selecione **Use um servidor proxy para acessar o serviço do Operations Management Suite** e digite o endereço IP do servidor do Gateway do OMS. Certifique-se de iniciar com o prefixo `http://`:  
    ![Operations Manager – endereço do servidor proxy](./media/log-analytics-oms-gateway/scom02.png)
3. Clique em **Concluir**. O servidor do Operations Manager está conectado ao seu espaço de trabalho do OMS.

## <a name="configure-network-load-balancing"></a>Configurar o balanceamento de carga de rede
Você pode configurar o gateway para alta disponibilidade usando o balanceamento de carga de rede criando um cluster. O cluster gerencia o tráfego de seus agentes redirecionando as conexões solicitadas dos Microsoft Monitoring Agents pelos seus nós. Se um servidor de Gateway falhar, o tráfego será redirecionado para outros nós.

1. Abra o Gerenciador de Balanceamento de Carga de Rede e criar um cluster.
2. Clique com botão direito no cluster antes de adicionar gateways e selecione **Propriedades do Cluster.** Configure o cluster para ter seu próprio endereço IP:  
    ![Gerenciador de Balanceamento de Carga de Rede – Endereços IP do Cluster](./media/log-analytics-oms-gateway/nlb01.png)
3. Para conectar um servidor do Gateway do OMS ao Microsoft Monitoring Agent instalado, clique com o botão direito no endereço IP do cluster e clique em **Adicionar Host ao Cluster**.  
    ![Gerenciador de Balanceamento de Carga de Rede – Adicionar Host ao Cluster](./media/log-analytics-oms-gateway/nlb02.png)
4. Digite o endereço IP do servidor do Gateway que deseja conectar:  
    ![Gerenciador de Balanceamento de Carga de Rede – Adicionar Host ao Cluster: Conectar](./media/log-analytics-oms-gateway/nlb03.png)
5. Em computadores que não têm conectividade com a Internet, certifique-se de usar o endereço IP do cluster quando você configura as **Propriedades do Microsoft Monitoring Agent**:  
    ![Propriedades do Microsoft Monitoring Agent – Configurações de Proxy](./media/log-analytics-oms-gateway/nlb04.png)

## <a name="configure-for-automation-hybrid-workers"></a>Configurar para hybrid workers de automação
Se você tiver hybrid workers de automação no ambiente, as etapas a seguir fornecem soluções alternativas temporárias manuais para configurar o Gateway que dê suporte a eles.

Nas etapas a seguir, você precisa saber em que região do Azure a conta de Automação reside. Para investigar o local:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Selecione o serviço de Automação do Azure.
3. Selecione a conta de Automação do Azure apropriada.
4. Exiba sua região em **Local**.  
    ![Portal do Azure – local da conta de Automação](./media/log-analytics-oms-gateway/location.png)

Use as seguintes tabelas para identificar a URL de cada local:

**URLs de serviço de dados de tempo de execução do trabalho**

| **local** | **URL** |
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

| **local** | **URL** |
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

Se o computador for registrado como um hybrid worker automaticamente para aplicação de patch usando a solução Gerenciamento de Atualizações, use estas etapas:

1. Adicione as URLs de serviço de dados de tempo de execução do trabalho à lista Hosts Permitidos no Gateway do OMS. Por exemplo:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Reinicie o serviço de Gateway do OMS usando o seguinte cmdlet do PowerShell: `Restart-Service OMSGatewayService`

Se o computador estiver integrado à Automação do Azure usando o cmdlet de registro do hybrid worker, use estas etapas:

1. Adicione a URL do registro de serviço do agente à lista Hosts Permitidos no Gateway do OMS. Por exemplo: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Adicione as URLs de serviço de dados de tempo de execução do trabalho à lista Hosts Permitidos no Gateway do OMS. Por exemplo:  `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Reinicie o serviço de Gateway do OMS.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets úteis do PowerShell
Os cmdlets podem ajudar na conclusão de tarefas que são necessárias para atualizar as definições de configuração do Gateway do OMS. Antes de você usá-los, não se esqueça de:

1. Instalar o Gateway do OMS (MSI).
2. Abrir a janela do PowerShell.
3. Para importar o módulo, digite este comando: `Import-Module OMSGateway`
4. Se nenhum erro tiver ocorrido na etapa anterior, o módulo foi importado com êxito, e os cmdlets poderão ser usados. Digite `Get-Module OMSGateway`
5. Depois de fazer as alterações usando os cmdlets, lembre-se de reiniciar o serviço de Gateway.

Se você receber um erro na etapa 3, o módulo não foi importado. O erro pode ocorrer quando o PowerShell não é capaz de encontrar o módulo. É possível encontrá-lo no caminho de instalação do Gateway: C:\Arquivos de Programas\Microsoft OMS Gateway\PowerShell.

| **Cmdlet** | **Parâmetros** | **Descrição** | **Exemplos** |
| --- | --- | --- | --- |
| `Set-OMSGatewayConfig` |Chave (obrigatória) <br> Valor |Altera a configuração do serviço |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |
| `Get-OMSGatewayConfig` |Chave |Obtém a configuração do serviço |`Get-OMSGatewayConfig` <br> <br> `Get-OMSGatewayConfig -Name ListenPort` |
| `Set-OMSGatewayRelayProxy` |Endereço <br> Nome de Usuário <br> Senha |Define o endereço (e as credenciais) do proxy de retransmissão (upstream) |1. Defina um proxy de resposta e a credencial: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080 -Username user1 -Password 123` <br> <br> 2. Defina um proxy de resposta que não precise de autenticação: `Set-OMSGatewayRelayProxy -Address http://www.myproxy.com:8080` <br> <br> 3. Desmarque a configuração do proxy de resposta, isto é, não há necessidade de um proxy de resposta: `Set-OMSGatewayRelayProxy -Address ""` |
| `Get-OMSGatewayRelayProxy` | |Obtém o endereço do proxy de retransmissão (upstream) |`Get-OMSGatewayRelayProxy` |
| `Add-OMSGatewayAllowedHost` |Host (obrigatório) |Adiciona o host à lista de permissões |`Add-OMSGatewayAllowedHost -Host www.test.com` |
| `Remove-OMSGatewayAllowedHost` |Host (obrigatório) |Remove o host da lista de permissões |`Remove-OMSGatewayAllowedHost -Host www.test.com` |
| `Get-OMSGatewayAllowedHost` | |Obtém o host atualmente permitido (somente o host permitido configurado localmente, não inclui hosts permitidos baixados automaticamente) |`Get-OMSGatewayAllowedHost` |
| `Add-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Adiciona o assunto do certificado do cliente à lista de permissões |`Add-OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Remove-OMSGatewayAllowedClientCertificate` |Assunto (obrigatório) |Remove o assunto do certificado do cliente da lista de permissões |`Remove- OMSGatewayAllowedClientCertificate -Subject mycert` |
| `Get-OMSGatewayAllowedClientCertificate` | |Obtém os assuntos do certificado do cliente atualmente permitidos (somente os assuntos permitidos configurados localmente, não incluem assuntos permitidos baixados automaticamente) |`Get-OMSGatewayAllowedClientCertificate` |

## <a name="troubleshoot"></a>Solucionar problemas
É necessário instalar o agente do OMS em computadores que têm o gateway instalado. Você poderá então usar o agente para coletar os eventos que são registrados em log pelo gateway.

![Visualizador de Eventos – Log do Gateway do OMS](./media/log-analytics-oms-gateway/event-viewer.png)

**IDs e descrições dos eventos de Gateway do OMS**

A tabela a seguir mostra as IDs e descrições de eventos do Log do Gateway do OMS.

| **ID** | **Descrição** |
| --- | --- |
| 400 |Qualquer erro de aplicativo que não tenha uma ID específica |
| 401 |Configuração incorreta. Por exemplo: listenPort = "texto" em vez de um número inteiro |
| 402 |Exceção ao analisar mensagens de handshake TLS |
| 403 |Rede de rede. Por exemplo: não é possível se conectar ao servidor de destino |
| 100 |Informações gerais |
| 101 |Serviço iniciado |
| 102 |Serviço interrompido |
| 103 |Comando HTTP CONNECT recebido do cliente |
| 104 |Não é um comando HTTP CONNECT |
| 105 |O servidor de destino não está na lista de permissões ou a porta de destino não é segura (443) <br> <br> Verifique se o agente MMA no servidor do Gateway e os agentes que se comunicam com o Gateway estão conectados ao mesmo espaço de trabalho do Log Analytics. |
| 105 |ERRO TcpConnection – Certificado de cliente inválido: CN=Gateway <br><br> Verifique se: <br>    <br> &#149; Você está usando um Gateway com o número de versão 1.0.395.0 ou superior. <br> &#149; O agente MMA no servidor do Gateway e os agentes que se comunicam com o Gateway estão conectados ao mesmo espaço de trabalho do Log Analytics. |
| 106 |Qualquer motivo pelo qual a sessão TLS é suspeita e rejeitada |
| 107 |A sessão TLS foi verificada |

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
Para solicitar assistência, clique no ponto e interrogação no canto superior direito do portal e clique em **Nova solicitação de suporte**. Em seguida, conclua o novo formulário de solicitação de suporte.

![Nova solicitação de suporte](./media/log-analytics-oms-gateway/support.png)

Você também pode deixar comentários sobre o OMS ou o Log Analytics no [Fórum de comentários do Microsoft Azure](https://feedback.azure.com/forums/267889).

## <a name="next-steps"></a>Próximas etapas
* [Adicionar fontes de dados](log-analytics-data-sources.md) para coletar dados das Fontes Conectadas em seu espaço de trabalho do OMS e armazená-los no repositório do OMS.

