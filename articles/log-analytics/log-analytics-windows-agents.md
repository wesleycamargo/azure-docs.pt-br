---
title: Conectar computadores Windows ao Log Analytics | Microsoft Docs
description: Este artigo mostra as etapas para conectar os computadores Windows em sua infraestrutura local diretamente ao OMS usando uma versão personalizada do MMA (Microsoft Monitoring Agent).
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2016
ms.author: banders

---
# <a name="connect-windows-computers-to-log-analytics"></a>Conectar computadores Windows ao Log Analytics
Este artigo mostra as etapas para conectar os computadores Windows em sua infraestrutura local diretamente a espaços de trabalho do OMS usando uma versão personalizada do MMA (Microsoft Monitoring Agent). Você precisa instalar e conectar agentes para todos os computadores que deseja carregar no OMS para que eles enviem dados ao OMS e para exibir e interagir com esses dados no portal do OMS. Cada agente pode relatar a vários espaços de trabalho.

Você pode instalar agentes usando a Instalação, a linha de comando ou com o DSC (Configuração de Estado Desejado) na Automação do Azure.  

> [!NOTE]
> Para máquinas virtuais em execução no Azure, você poderá simplificar a instalação usando a [extensão da máquina virtual](log-analytics-azure-vm-extension.md).
> 
> 

Em computadores com conectividade com a Internet, o agente usará a conexão com a Internet para enviar dados ao OMS. Para computadores que não têm conectividade com a Internet, você pode usar um proxy ou o Encaminhador do Log Analytics do OMS.

É muito simples conectar seus computadores Windows ao OMS usando três etapas simples:

1. Baixar o arquivo de instalação do agente
2. Instalar o agente usando o método que você preferir
3. Configurar o agente ou adicionar outros espaços de trabalho, se necessário

O diagrama a seguir mostra a relação entre seus computadores Windows e o OMS depois de ter instalado e configurado os agentes.

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)

## <a name="system-requirements-and-required-configuration"></a>Requisitos do sistema e a configuração necessária
Antes de instalar ou implantar agentes, examine os detalhes a seguir para verificar se você atende aos requisitos necessários.

* Só é possível instalar o MMA do OMS em computadores que executam o Windows Server 2008 SP 1 ou posterior ou o Windows 7 SP1 ou posterior.
* Você precisará de uma assinatura do OMS.  Para obter informações adicionais, consulte [Introdução ao Log Analytics](log-analytics-get-started.md).
* Todo computador do Windows deve ser capaz de se conectar à Internet usando HTTPS. Essa conexão pode ser direta, por meio de um proxy ou pelo Encaminhador do Log Analytics do OMS.
* Você pode instalar o MMA do OMS em computadores autônomos, servidores e máquinas virtuais. Se você deseja se conectar a máquinas virtuais hospedadas no Azure ao OMS, consulte [Conectar as Máquinas Virtuais do Azure ao Log Analytics](log-analytics-azure-vm-extension.md).
* O agente deve usar a porta TCP 443 para vários recursos. Para obter mais informações, consulte [Definir configurações de proxy e firewall no Log Analytics](log-analytics-proxy-firewall.md).

## <a name="download-the-agent-setup-file-from-oms"></a>Baixar o arquivo de instalação do agente do OMS
1. No portal do OMS, na página **Visão Geral**, clique no bloco **Configurações**.  Clique na guia **Fontes Conectadas** na parte superior.  
    ![Guia Fontes Conectadas](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. Em **Conectar Computadores Diretamente**, clique em **Baixar o Agente do Windows** aplicáveis ao tipo de processador do computador para baixar o arquivo de instalação.
3. À direita da **ID do Espaço de Trabalho**, clique no ícone para copiar e cole-a no Bloco de Notas.
4. À direita da **Chave Primária**, clique no ícone para copiar e cole-a Bloco de Notas.     
    ![copiar ID de Espaço de Trabalho e a Chave Primária](./media/log-analytics-windows-agents/oms-direct-agent-primary-key.png)

## <a name="install-the-agent-using-setup"></a>Instalar o agente usando a instalação
1. Execute Instalação para instalar o agente em um computador que você deseje gerenciar.
2. Na página de Boas-vindas, clique em **Avançar**.
3. Na página Termos de Licença, leia a licença e clique em **Aceito**.
4. Na página Pasta de Destino, altere ou mantenha a pasta de instalação padrão e clique em **Avançar**.
5. Na página Opções de Instalação do Agente, você pode optar por conectar o agente ao OMS (Azure Log Analytics), ao Operations Manager ou você pode deixar as escolhas em branco se quiser configurar o agente mais tarde. Clique em **Próximo**.   
   
   * Se você optar por conectar-se ao OMS (Azure Log Analytics), cole a **ID do Espaço de Trabalho** e a **Chave do Espaço de Trabalho (Chave Primária)** que você copiou para o Bloco de Notas no procedimento anterior e clique em **Avançar**.  
       ![colar ID de Espaço de Trabalho e a Chave Primária](./media/log-analytics-windows-agents/connect-workspace.png)
   * Se você optar por conectar-se ao Operations Manager, digite o **Nome do Grupo de Gerenciamento**, nome do **Servidor de Gerenciamento** e **Porta do Servidor de Gerenciamento**, e clique em **Avançar**. Na página Conta de Ação de Agente, escolha a conta Sistema Local ou uma conta de domínio local e clique em **Avançar**.  
       ![configuração do grupo de gerenciamento ](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![conta de ação de agente](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)
6. Na página Pronto para Instalar, reveja suas escolhas e clique em **Instalar**.
7. Na página Configuração concluída com êxito, clique em **Concluir**.
8. Após concluir, o **Microsoft Monitoring Agent** aparecerá no **Painel de Controle**. Você pode examinar sua configuração e verificar se o agente está conectado ao OMS (Operational Insights). Quando conectado ao OMS, o agente exibe uma mensagem dizendo: **o Microsoft Monitoring Agent conectou-se com êxito ao serviço Microsoft Operations Management Suite.**

## <a name="install-the-agent-using-the-command-line"></a>Instalar o agente usando a linha de comando
* Modifique e, em seguida, use o exemplo a seguir para instalar o agente usando a linha de comando.
  
  > [!NOTE]
  > Se você quiser atualizar um agente, precisará usar a API de script do Log Analytics. Consulte a próxima seção para atualizar um agente.
  > 
  > 
  
    ```
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

## <a name="upgrade-the-agent-and-add-a-workspace-using-a-script"></a>Atualizar o agente e adicionar um espaço de trabalho usando um script
Você pode atualizar um agente e adicionar um espaço de trabalho usando a API de script do Log Analytics com o exemplo do PowerShell a seguir.

```
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

> [!NOTE]
> Se você já usou a linha de comando ou o script anteriormente para instalar ou configurar o agente, `EnableAzureOperationalInsights` foi substituído por `AddCloudWorkspace`.
> 
> 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalar o agente usando o DSC na Automação do Azure
> [!NOTE]
> Este exemplo de procedimento e de script não atualizará um agente existente.
> 
> 

1. Importe o Módulo xPSDesiredStateConfiguration do DSC de [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) para a Automação do Azure.  
2. Crie ativos de variável da Automação do Azure para *OPSINSIGHTS_WS_ID* e *OPSINSIGHTS_WS_KEY*. Defina *OPSINSIGHTS_WS_ID* para sua ID do espaço de trabalho do Log Analytics do OMS e defina *OPSINSIGHTS_WS_KEY* para a chave primária do seu espaço de trabalho.
3. Use o script abaixo e salve-o como MMAgent.ps1
4. Modifique e use o exemplo a seguir para instalar o agente usando o DSC na Automação do Azure. Importe o MMAgent.ps1 para Automação do Azure usando a interface ou o cmdlet da Automação do Azure.
5. Atribua um nó à configuração. Dentro de 15 minutos, o nó verificará sua configuração e o MMA será enviado para o nó.

```
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "http://download.microsoft.com/download/0/C/0/0C072D6E-F418-4AD4-BCB2-A362624F400A/MMASetup-AMD64.exe"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}  


```


## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>Configurar um agente manualmente ou adicionar outros espaços de trabalho
Se você tiver instalado os agentes, mas não os configurou, ou se quiser que o agente relate a vários espaços de trabalho, poderá usar as informações a seguir para habilitar um agente ou para reconfigurá-lo. Depois que você tiver configurado o agente, ele será registrado com o serviço do agente e obterá as informações de configuração necessárias e os pacotes de gerenciamento contendo informações da solução.

1. Depois de instalar o Microsoft Monitoring Agent, abra o **Painel de Controle**.
2. Abra o **Microsoft Monitoring Agent** e clique na guia **OMS (Azure Log Analytics)**.   
3. Clique em **Adicionar** para abrir a caixa **Adicionar um Espaço de Trabalho do Log Analytics**.
4. Cole a **ID do Espaço de Trabalho** e a **Chave do Espaço de Trabalho (Chave Primária)** que você copiou no procedimento anterior para o espaço de trabalho que você deseja adicionar e clique em **OK**.  
    ![configurar o Azure Operational Insights](./media/log-analytics-windows-agents/add-workspace.png)

Depois que dados forem coletados de computadores monitorados por agente, o número de computadores monitorados pelo OMS aparecerá no portal do OMS na guia **Fontes Conectadas** em **Configurações** como **Servidores Conectados**.

## <a name="to-disable-an-agent"></a>Para desabilitar um agente
1. Depois de instalar o agente, abra o **Painel de Controle**.
2. Abra o Microsoft Monitoring Agent e clique na guia **OMS (Azure Log Analytics)** .
3. Selecione um espaço de trabalho e clique em **Remover**. Repita essa etapa para todos os outros espaços de trabalho.

## <a name="optionally,-configure-agents-to-report-to-an-operations-manager-management-group"></a>Outra opção é configurar os agentes para relatar a um grupo de gerenciamento do Operations Manager
Se você usar o Operations Manager em sua infraestrutura de TI, também poderá usar o agente de MMA como um agente do Operations Manager.

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>Para configurar os agentes do MMA para relatar a um grupo de gerenciamento do Operations Manager
1. No computador no qual o agente está instalado, abra o **Painel de Controle**.
2. Abra o **Microsoft Monitoring Agent** e clique na guia **Operations Manager**.
   ![Guia Microsoft Monitoring Agent Operations Manager](./media/log-analytics-windows-agents/om-mg01.png)
3. Se seus servidores do Operations Manager tiverem integração com o Active Directory, clique em **Atualizar automaticamente as atribuições de grupo de gerenciamento do AD DS**.
4. Clique em **Adicionar** para abrir a caixa de diálogo **Adicionar um Grupo de Gerenciamento**.  
   ![Adicionar um Grupo de Gerenciamento do Microsoft Monitoring Agent](./media/log-analytics-windows-agents/oms-mma-om02.png)
5. Na caixa **Nome do grupo de gerenciamento** , digite o nome do grupo de gerenciamento.
6. Na caixa **Servidor de gerenciamento primário** , digite o nome do computador do servidor de gerenciamento primário.
7. Na caixa **Porta do servidor de gerenciamento** , digite o número da porta TCP.
8. Na página **Conta de Ação de Agente**, escolha a conta Sistema Local ou uma conta de domínio local.
9. Clique em **OK** para fechar a caixa de diálogo **Adicionar um Grupo de Gerenciamento** e clique em **OK** para fechar a caixa de diálogo **Propriedades do Microsoft Monitoring Agent**.

## <a name="optionally,-configure-agents-to-use-the-oms-log-analytics-forwarder"></a>Outra opção é configurar os agentes para usar o Encaminhador do Log Analytics do OMS
Se você tiver servidores ou clientes sem conexão com a Internet, eles ainda poderão enviar dados para o OMS usando o Encaminhador do Log Analytics do OMS.  Quando você usa o encaminhador, todos os dados dos agentes são enviados por meio de um único servidor com acesso à Internet. O Encaminhador transfere dados dos agentes para o OMS diretamente sem analisar nenhum dado transferido.

Consulte [Encaminhador do Log Analytics do OMS](https://blogs.technet.microsoft.com/msoms/2016/03/17/oms-log-analytics-forwarder) para saber mais sobre o encaminhador, incluindo sua instalação e configuração.

Para obter informações sobre como configurar seus agentes para usar um servidor proxy, que nesse caso é o Encaminhador de OMS, consulte [Definir a configurações de proxy e firewall no Log Analytics](log-analytics-proxy-firewall.md).

## <a name="optionally,-configure-proxy-and-firewall-settings"></a>Outra opção é definir as configurações de proxy e firewall
Se você tiver servidores proxy ou firewalls em seu ambiente que restringem o acesso à Internet, consulte [Definir configurações de proxy e firewall no Log Analytics](log-analytics-proxy-firewall.md) para permitir que os agentes se comuniquem com o serviço do OMS.

## <a name="next-steps"></a>Próximas etapas
* [Adicionar soluções do Log Analytics da Galeria de Soluções](log-analytics-add-solutions.md) para adicionar funcionalidade e obter dados.
* [Definir configurações de proxy e firewall no Log Analytics](log-analytics-proxy-firewall.md) se sua organização usar um servidor proxy ou firewall para que os agentes possam se comunicar com o serviço do Log Analytics.

<!--HONumber=Oct16_HO2-->


