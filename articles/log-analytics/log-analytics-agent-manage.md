---
title: Gerenciando o agente de Log Analytics do Azure | Microsoft Docs
description: "Este artigo descreve as tarefas de gerenciamento diferentes que você executa normalmente durante o ciclo de vida do Microsoft Monitoring Agent (MMA) implantado em uma máquina."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: magoedte
ms.openlocfilehash: a17418142fb5f52a93d7a56cb2e6e6e97a250002
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Gerenciar e manter o agente de Log Analytics para o Windows e Linux

Após a implantação inicial do agente para Log Analytics do Windows ou Linux, talvez seja necessário reconfigurar o agente dependendo da situação ou removê-lo do computador, se atingir o estágio de desativação do ciclo de vida.  Você pode gerenciar facilmente essas tarefas de manutenção de rotina, manualmente ou por meio de automação, o que reduz o erro operacional e as despesas.

## <a name="adding-or-removing-a-workspace"></a>Adicionando ou removendo espaço de trabalho 

### <a name="windows-agent"></a>Agente do Windows

#### <a name="update-settings-from-control-panel"></a>Atualizar as configurações do painel de controle

1. Faça logon no computador com uma conta que tenha direitos administrativos.
2. Abra o **Painel de controle**.
3. Abra o **Microsoft Monitoring Agent** e clique na guia **Azure Log Analytics (OMS)**.
4. Se remover um espaço de trabalho, selecione-o e, em seguida, clique em **remover**. Repita essa etapa para qualquer outro espaço de trabalho que você deseja que o agente interrompa a emissão de relatórios.
5. Se estiver adicionando um espaço de trabalho, clique em **Adicionar** e na caixa de diálogo **Adicionar um espaço de trabalho de Log Analytics**, cole a ID do espaço de trabalho e chave do espaço de trabalho (chave primária). Caso o computador deva se reportar a um espaço de trabalho do Log Analytics na nuvem do Azure Governamental, selecione Azure US Government na lista suspensa do Azure Cloud. 
6. Clique em **OK** para salvar as alterações.

#### <a name="remove-a-workspace-using-powershell"></a>Remova um espaço de trabalho usando o PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Adicione um espaço de trabalho no Azure commercial usando o PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Adicione um espaço de trabalho no Azure for US Government usando o PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Se você já usou a linha de comando ou o script anteriormente para instalar ou configurar o agente, `EnableAzureOperationalInsights` foi substituído por `AddCloudWorkspace` e `RemoveCloudWorkspace`.
>

## <a name="update-proxy-settings"></a>Atualize as configurações de proxy 
Para configurar o agente para se comunicar com o serviço por meio de um servidor proxy ou [OMS Gateway](log-analytics-oms-gateway.md) após a implantação, use um dos métodos a seguir para concluir esta tarefa.

### <a name="windows-agent"></a>Agente do Windows

#### <a name="update-settings-using-control-panel"></a>Configurações de atualização usando o painel de controle

1. Faça logon no computador com uma conta que tenha direitos administrativos.
2. Abra o **Painel de controle**.
3. Abra o **Microsoft Monitoring Agent** e clique na guia **Configurações de proxy**.
4. Clique em **Usar um servidor proxy** e forneça a URL e o número de porta do servidor proxy ou gateway. Caso seu servidor proxy ou OMS Gateway exija autenticação, digite o nome de usuário e a senha para se autenticar e clique em **OK**. 

#### <a name="update-settings-using-powershell"></a>Configurações de atualização usando o PowerShell 

Copie o seguinte código de exemplo do PowerShell, atualize-o com informações específicas para seu ambiente e salve-o com uma extensão de nome de arquivo PS1.  Execute o script em cada computador que se conecta diretamente ao serviço de Log Analytics.

```PowerShell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

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
```  

### <a name="linux-agent"></a>Agente do Linux
Execute as etapas a seguir se os computadores Linux precisarem se comunicar por meio de um servidor proxy ou OMS Gateway com o Log Analytics.  O valor de configuração de proxy tem a seguinte sintaxe `[protocol://][user:password@]proxyhost[:port]`.  A propriedade *proxyhost* aceita um nome de domínio totalmente qualificado ou o endereço IP do servidor proxy.

1. Edite o arquivo `/etc/opt/microsoft/omsagent/proxy.conf` executando os comandos a seguir e altere os valores para as configurações específicas.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. Reinicie o agente executando o seguinte comando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="uninstall-agent"></a>Desinstalar o agente
Use um dos procedimentos a seguir para desinstalar o agente do Windows ou Linux usando o assistente de instalação ou de linha de comando.

### <a name="windows-agent"></a>Agente do Windows

#### <a name="uninstall-from-control-panel"></a>Desinstalar usando o painel de controle
1. Faça logon no computador com uma conta que tenha direitos administrativos.  
2. Abra o **Painel de Controle** e clique em **Programas e Recursos**.
3. Em **Programas e Recursos**, clique em **Microsoft Monitoring Agent** e clique em **Desinstalar** e, em seguida clique em **Sim**.

>[!NOTE]
>O assistente de instalação do agente também pode ser executado clicando duas vezes em **MMASetup-<platform>.exe**, que está disponível para download no espaço de trabalho no portal do Azure.

#### <a name="uninstall-from-the-command-line"></a>Desinstalar usando a linha de comando
O arquivo baixado para o agente é um pacote de instalação independente criado com IExpress.  O programa de instalação para o agente e os arquivos de suporte estão contidos no pacote e precisa ser extraído para desinstalar adequadamente usando a linha de comando mostrada no exemplo a seguir. 

1. Faça logon no computador com uma conta que tenha direitos administrativos.  
2. Para extrair os arquivos de instalação do agente de um prompt de comando com privilégios elevados, execute `extract MMASetup-<platform>.exe` e será solicitado que você especifique o caminho para extrair os arquivos.  Como alternativa, você poderá especificar o caminho ao passar os argumentos `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Para mais informações sobre as opções de linha de comando suportados por IExpress, consulte [Opções de linha de comando do IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) e, em seguida, atualize o exemplo para atender às suas necessidades.
3. No prompt, digite `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.  

### <a name="linux-agent"></a>Agente do Linux
Para remover o agente, execute as etapas a seguir.

1. Baixe o [script universal](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) do agente do Linux para o computador.
2. Execute o arquivo .sh do pacote com o argumento *--purge* no computador, que remove completamente o agente e sua configuração.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Configure o agente para relatar a um grupo de gerenciamento do Operations Manager

### <a name="windows-agent"></a>Agente do Windows
Execute as seguintes etapas para configurar o Agente do OMS para Windows para reportar para um grupo de gerenciamento do System Center Operations Manager. 

1. Faça logon no computador com uma conta que tenha direitos administrativos.
2. Abra o **Painel de controle**. 
3. Abra o **Microsoft Monitoring Agent** e clique na guia **Operations Manager**.
4. Se seus servidores do Operations Manager tiverem integração com o Active Directory, clique em **Atualizar automaticamente as atribuições de grupo de gerenciamento do AD DS**.
5. Clique em **Adicionar** para abrir a caixa de diálogo **Adicionar um Grupo de Gerenciamento**.
6. No campo **Nome do grupo de gerenciamento**, digite o nome do grupo de gerenciamento.
7. No campo **Servidor de gerenciamento primário**, digite o nome do computador do servidor de gerenciamento primário.
8. No campo **Porta do servidor de gerenciamento**, digite o número da porta TCP.
9. Na página **Conta de Ação de Agente**, escolha a conta Sistema Local ou uma conta de domínio local.
10. Clique em **OK** para fechar a caixa de diálogo **Adicionar um Grupo de Gerenciamento** e clique em **OK** para fechar a caixa de diálogo **Propriedades do Microsoft Monitoring Agent**.

### <a name="linux-agent"></a>Agente do Linux
Execute as seguintes etapas para configurar o Agente do OMS para Linux para reportar para um grupo de gerenciamento do System Center Operations Manager. 

1. Edite o arquivo `/etc/opt/omi/conf/omiserver.conf`
2. Verifique se a linha que começa com `httpsport=` define a porta 1270. Como: `httpsport=1270`
3. Reinicie o servidor OMI: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Próximas etapas

Consulte [Solução de problemas do agente do Linux](log-analytics-agent-linux-support.md) se você encontrar problemas ao instalar ou gerenciar o agente.  