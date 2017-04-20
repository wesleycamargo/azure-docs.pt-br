---
title: "Definir as configurações de proxy e firewall no Azure Log Analytics | Microsoft Docs"
description: "Defina as configurações de proxy e firewall quando seus agentes ou serviços do OMS precisarem usar portas específicas."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b55ebd80-efd4-4220-971b-c18aea1b1ab2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/12/2017
ms.author: banders;magoedte
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: cc3ff1ca5e638896df155b55145b300f70f7540a
ms.lasthandoff: 04/13/2017


---
# <a name="configure-proxy-and-firewall-settings-in-log-analytics"></a>Definir as configurações de proxy e firewall no Log Analytics
Ações necessárias para configurar o proxy e configurações de firewall para Log Analytics diferem para o tipo de agentes que você está usando. Examine as seções a seguir para o tipo de agente usado.

## <a name="settings-for-the-oms-gateway"></a>Configurações do gateway do OMS

Se seus agentes não tiver acesso à Internet, em vez disso, eles podem enviar seus dados usando seus próprios recursos de rede para o Gateway do OMS. O Gateway coleta seus dados e os envia para o serviço OMS em seu nome.

Configure os agentes que se comunicam com o Gateway de OMS usando seu nome de domínio totalmente qualificado e o número de porta personalizada.

O Gateway do OMS precisa de acesso à Internet. Use as configurações de firewall ou o mesmo servidor proxy para o Gateway de OMS que você faria para o tipo de agentes que você tem. Para saber mais sobre o Gateway OMS, veja [Conectar computadores e dispositivos OMS usando o Gateway OMS](log-analytics-oms-gateway.md).

## <a name="configure-settings-with-the-microsoft-monitoring-agent"></a>Definir as configurações com o Microsoft Monitoring Agent
Para o Microsoft Monitoring Agent se conectar e se registrar no serviço do OMS, ele deverá ter acesso ao número da porta de seus domínios e às URLs. Se você usar um servidor proxy para comunicação entre o agente e o serviço do OMS, será necessário garantir que os recursos apropriados estejam acessíveis. Se você usar um firewall para restringir o acesso à Internet, precisará configurar o firewall para permitir o acesso ao OMS. As tabelas a seguir listam as portas de que o serviço do OMS precisa.

| **Recurso de agente** | **Portas** | **Ignorar a inspeção de HTTPS** |
| --- | --- | --- |
| \*.ods.opinsights.azure.com |443 |Sim |
| \*.oms.opinsights.azure.com |443 |Sim |
| \*.blob.core.windows.net |443 |Sim |
| \*.azure-automation.net |443 |Sim |

Você pode usar o procedimento a seguir para definir configurações de proxy para o Microsoft Monitoring Agent usando o painel de controle. Você precisará usar o procedimento para cada servidor. Se você tiver vários servidores que precisa configurar, talvez seja mais fácil usar um script para automatizar esse processo. Nesse caso, consulte o próximo procedimento [Definir configurações de proxy para o Microsoft Monitoring Agent usando um script](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>Para definir configurações de proxy para o Microsoft Monitoring Agent usando o painel de controle
1. Abra o **Painel de controle**.
2. Abra o **Microsoft Monitoring Agent**.
3. Clique na guia **Configurações de Proxy** .<br>  
   ![guia Configurações de proxy](./media/log-analytics-proxy-firewall/proxy-direct-agent-proxy.png)
4. Selecione **Usar um servidor proxy** e digite a URL e o número da porta, se for necessário, semelhante ao exemplo mostrado. Se o servidor proxy requer autenticação, digite o nome de usuário e senha para acessar o servidor proxy.

Use o procedimento a seguir para criar um script do PowerShell que você possa executar para definir as configurações de proxy para cada agente que se conecte diretamente a servidores.

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>Definir configurações de proxy para o Microsoft Monitoring Agent usando um script
Copie o exemplo a seguir, atualize-o com informações específicas para seu ambiente, salve-o com uma extensão de nome de arquivo PS1 e então execute o script em cada computador que se conecte diretamente ao serviço do OMS.

    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)


## <a name="configure-settings-with-operations-manager"></a>Definir configurações com o Operations Manager
Para um grupo de gerenciamento do Operations Manager se conectar e se registrar no serviço do OMS, ele deverá ter acesso aos números de porta de seus domínios e URLs. Se você usar um servidor proxy para comunicação entre o servidor de gerenciamento do Operations Manager e o serviço do OMS, será necessário garantir que os recursos apropriados estejam acessíveis. Se você usar um firewall para restringir o acesso à Internet, precisará configurar o firewall para permitir o acesso ao OMS. Mesmo que um servidor de gerenciamento do Operations Manager não esteja protegido por um servidor proxy, seus agentes podem estar. Nesse caso, o servidor proxy deve ser configurado da mesma maneira como os agentes para habilitar e permitir que os dados da solução Gerenciamento de Logs e Segurança sejam enviados ao serviço Web do OMS.

Para que os agentes do Operations Manager se comuniquem com o serviço do OMS, sua infraestrutura do Operations Manager (incluindo agentes) deve ter as configurações e a versão de proxy corretas. A configuração do proxy para agentes é especificada no console do Operations Manager. Sua versão deve ser uma das seguintes:

* Operations Manager 2012 SP1 com Pacote Cumulativo de Atualizações 7 ou posterior
* Operations Manager 2012 R2 com Pacote Cumulativo de Atualizações 3 ou posterior

As tabelas a seguir listam as portas relacionadas a essas tarefas.

> [!NOTE]
> Alguns dos recursos a seguir mencionam o Advisor e o Operational Insights, ambos versões anteriores do OMS. No entanto, os recursos listados serão alterado no futuro.
>
>

Aqui está uma lista de portas e recursos de agente:<br>

| **Recurso de agente** | **Portas** |
| --- | --- |
| \*.ods.opinsights.azure.com |443 |
| \*.oms.opinsights.azure.com |443 |
| \*.blob.core.windows.net/\* |443 |

<br>
Aqui está uma lista de portas e recursos de servidor de gerenciamento:<br>

| **Recurso do servidor de gerenciamento** | **Portas** | **Ignorar a inspeção de HTTPS** |
| --- | --- | --- |
| service.systemcenteradvisor.com |443 | |
| \*.service.opinsights.azure.com |443 | |
| \*.blob.core.windows.net |443 |Sim |
| \*.ods.opinsights.azure.com |443 |Sim |
| \*.azure-automation.net |443 |Sim |

<br>
Aqui está uma lista de portas e recursos do OMS e do console do Operations Manager.<br>

| **Recurso do console do OMS e do Operations Manager** | **Portas** |
| --- | --- |
| service.systemcenteradvisor.com |443 |
| \*.service.opinsights.azure.com |443 |
| \*.live.com |Porta 80 e 443 |
| \*.microsoft.com |Porta 80 e 443 |
| \*.microsoftonline.com |Porta 80 e 443 |
| \*.mms.microsoft.com |Porta 80 e 443 |
| login.windows.net |Porta 80 e 443 |

<br>

Use os procedimentos a seguir para registrar o seu grupo de gerenciamento do Operations Manager com o serviço do OMS. Se você estiver tendo problemas de comunicação entre o grupo de gerenciamento e o serviço do OMS, use os procedimentos de validação para solucionar problemas de transmissão de dados para o serviço do OMS.

### <a name="to-request-exceptions-for-the-oms-service-endpoints"></a>Para solicitar exceções para os pontos de extremidade do serviço do OMS
1. Use as informações da primeira tabela apresentada anteriormente para garantir que os recursos necessários para o servidor de gerenciamento do Operations Manager estejam acessíveis por meio de firewalls que talvez você tenha.
2. Use as informações da segunda tabela apresentada anteriormente para garantir que os recursos necessários para o console de Operações no Operations Manager e no OMS estejam acessíveis através de firewalls que você possa ter.
3. Se você usar um servidor proxy com o Internet Explorer, certifique-se de que ele esteja configurado e funcionando corretamente. Para verificar, você pode abrir uma conexão segura da Web (https), por exemplo [https://bing.com](https://bing.com). Se a conexão segura da web não funcionar em um navegador, provavelmente ela não funcionará no console de gerenciamento do Operations Manager com serviços da Web na nuvem.

### <a name="to-configure-the-proxy-server-in-the-operations-manager-console"></a>Para configurar o servidor proxy no console do Operations Manager
1. Abra o console do Operations Manager e selecione o espaço de trabalho **Administração** .
2. Expanda **Insights Operacionais**, em seguida, selecione **Conexão dos Insights Operacionais**.<br>  
   ![Conexão do Operations Manager OMS](./media/log-analytics-proxy-firewall/proxy-om01.png)
3. Na exibição Conexão do OMS, clique em **Configurar Servidor Proxy**.<br>  
   ![Conexão do Operations Manager OMS Configurar Servidor Proxy](./media/log-analytics-proxy-firewall/proxy-om02.png)
4. No Assistente de Configurações dos Insights Operacionais: Servidor Proxy, selecione **Usar um servidor proxy para acessar o Serviço Web dos Insights Operacionais**, em seguida, digite a URL com o número da porta, como por exemplo, **http://myproxy:80**.<br>  
   ![Endereço de proxy do Operations Manager OMS](./media/log-analytics-proxy-firewall/proxy-om03.png)

### <a name="to-specify-credentials-if-the-proxy-server-requires-authentication"></a>Para especificar credenciais caso o servidor proxy exija autenticação
 As credenciais e configurações do servidor proxy precisam ser propagadas em computadores gerenciados que se reportarão aos OMS. Esses servidores devem estar no *Grupo de Servidores de Monitoramento do Microsoft System Center Advisor*. As credenciais são criptografadas no registro de cada servidor do grupo.

1. Abra o console do Operations Manager e selecione o espaço de trabalho **Administração** .
2. Em **Configuração RunAs**, selecione **Perfis**.
3. Abra o perfil **System Center Advisor executado como Proxy de perfil** .<br>  
   ![imagem do perfil do System Center Advisor Executado Como Proxy](./media/log-analytics-proxy-firewall/proxy-proxyacct1.png)
4. No Assistente Executar como Perfil, clique em **Adicionar** para usar uma conta Executar como. Você pode criar uma nova conta Executar como ou usar uma conta existente. Essa conta deve ter permissões suficientes para passar pelo servidor proxy.<br>   
   ![imagem do Assistente para Executar Como Perfil](./media/log-analytics-proxy-firewall/proxy-proxyacct2.png)
5. Para definir a conta a ser gerenciada, escolha **Uma classe, grupo ou objeto selecionado** para abrir a caixa de Pesquisa de Objetos.<br>  
   ![imagem do Assistente para Executar Como Perfil](./media/log-analytics-proxy-firewall/proxy-proxyacct2-1.png)
6. Procure e selecione o **Grupo de Servidores de Monitoramento do Microsoft System Center Advisor**.<br>  
   ![imagem da caixa Pesquisa de Objetos](./media/log-analytics-proxy-firewall/proxy-proxyacct3.png)
7. Clique em **OK** para fechar a caixa Adicionar uma conta Executar como.<br>  
   ![imagem do Assistente para Executar Como Perfil](./media/log-analytics-proxy-firewall/proxy-proxyacct4.png)
8. Conclua o assistente e salve as alterações.<br>  
   ![imagem do Assistente de Perfil Executar Como](./media/log-analytics-proxy-firewall/proxy-proxyacct5.png)

### <a name="to-validate-that-oms-management-packs-are-downloaded"></a>Para validar se os pacotes de gerenciamento do OMS foram baixados
Se você tiver adicionado soluções ao OMS, poderá exibi-las no console do Operations Manager como pacotes de gerenciamento em **Administração**. Pesquise *System Center Advisor* para encontrá-las rapidamente.<br>  
   ![download de pacotes de gerenciamento](./media/log-analytics-proxy-firewall/proxy-mpdownloaded.png)  <br>  
Ou então, você também pode verificar os pacotes de gerenciamento do OMS usando o seguinte comando do Windows PowerShell no servidor de gerenciamento do Operations Manager:

   ```  
    Get-ScomManagementPack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken
   ```  

### <a name="to-validate-that-operations-manager-is-sending-data-to-the-oms-service"></a>Para validar se o Operations Manager está enviando dados para o serviço do OMS
1. No servidor de gerenciamento do Operations Manager, abra o Monitor de Desempenho (perfmon.exe) e selecione **o Monitor de Desempenho**.
2. Clique em **Adicionar**, em seguida, selecione **Grupos de Gerenciamento do Serviço de Integridade**.
3. Adicione todos os contadores que começam com **HTTP**.<br>  
   ![adicionar contadores](./media/log-analytics-proxy-firewall/proxy-sendingdata1.png)
4. Se a sua configuração do Operations Manager for boa, você verá a atividade dos contadores do Gerenciamento do Serviço de Integridade para eventos e outros itens de dados, com base nos pacotes de gerenciamento adicionados no OMS e a política de coleta de logs configurada.<br>  
   ![Atividade de exibição de Monitor de desempenho](./media/log-analytics-proxy-firewall/proxy-sendingdata2.png)

## <a name="next-steps"></a>Próximas etapas
* [Adicionar soluções do Log Analytics da Galeria de Soluções](log-analytics-add-solutions.md) para adicionar funcionalidade e obter dados.
* Familiarize-se com as [pesquisas de log](log-analytics-log-searches.md) para exibir informações detalhadas reunidas por soluções.

