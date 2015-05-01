<properties 
	pageTitle="connect-scom" 
	description="Conectar computadores diretamente a Insights Operacionais" 
	services="operational-insights" 
	documentationCenter="" 
	authors="lauracr" 
	manager="jwhit" 
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na" 
	ms.date="02/20/2015"
	ms.author="lauracr"/>

# Conectar computadores diretamente a Insights Operacionais 

Você pode conectar computadores diretamente a Insights Operacionais instalando o agente Insights Operacionais em cada computador que você desejar integrar. 


## Baixar, instalar e configurar o agente
### Para baixar o arquivo de instalação do agente
1. No **Portal de Insights Operacionais**, em **Visão Geral**, clique em **Servidores e Uso**.
1. Em **Servidores conectados diretamente**, clique em **configurar**.
1. Ao lado de **Adicionar Agentes**, clique no link Agente para baixar o arquivo de instalação.
1. Na caixa **Chave Primária do Espaço de Trabalho**, selecione a chave e copie-a (CTRL+C).


### Para instalar o agente usando o programa de instalação
1. Execute Instalação para instalar o agente em um computador que você deseje gerenciar.
1. Selecione **Conectar o agente a Insights Operacionais do Microsoft Azure** e clique em **Avançar**.
1. Quando solicitado, insira as informações copiadas na etapa 4.
1. Ao concluir, o **Agente de Gerenciamento da Microsoft** aparece no **Painel de Controle**.

### Para instalar o agente usando a linha de comando
Modifique e, em seguida, use o exemplo a seguir para instalar o agente usando a linha de comando.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

## Reconfigurar o Microsoft Monitoring Agent (opcional)
Use as informações a seguir para habilitar um agente para se comunicar diretamente com o serviço de Insights Operacionais do Microsoft Azure. Depois que você tiver configurado o agente, ele será registrado com o serviço do agente e obterá as informações de configuração necessárias e os pacotes de gerenciamento contendo informações do pacote de inteligência.

Depois que dados são coletados de computadores monitorados por agente, o número de computadores monitorados aparecerá no portal de Insights Operacionais na página Uso sob **Agentes Conectados Diretamente**. Para qualquer computador que envie dados, você pode exibir suas informações de avaliação e dados no portal de Insights Operacionais.

Você também pode desabilitar o agente, se necessário, ou habilitá-lo usando a linha de comando ou script.

### Para configurar o agente
- Depois de instalar o **Microsoft Monitoring Agent**, abra o **painel de controle**.
- Abra o Microsoft Monitoring Agent e, em seguida, clique na guia Insights Operacionais do Azure.
- Selecione **Conectar a Insights Operacionais do Azure**.
- Na caixa **ID do espaço de trabalho**, digite a ID do espaço de trabalho fornecida no portal de Insights Operacionais.
- Na caixa Chave de Conta, cole a **Chave Primária do Espaço de Trabalho** copiada quando você instalou o agente e clique em **OK**.

### Para desabilitar um agente
- Depois de instalar o agente, abra o **Painel de Controle**.
- Abra o Microsoft Monitoring Agent e, em seguida, clique na guia **Insights Operacionais do Azure**.
- Desmarque Conectar-se a **Insights Operacionais do Azure**.

### Para habilitar o agente usando a linha de comando ou script
Você pode usar o Windows PowerShell ou um script VB com o exemplo a seguir.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### Definir as configurações de proxy e Firewall (opcional)
Se você tiver servidores proxy ou firewalls em seu ambiente que restrinjam o acesso à Internet, pode ser necessário realizar os procedimentos a seguir para habilitar o Operations Manager e/ou os agentes para se comunicarem com o serviço de Insights Operacionais 



- [Definir as configurações de proxy e firewall (opcional)](https://msdn.microsoft.com/library/azure/dn884643.aspx) 


## Conteúdo relacionado

- [Postagem no blog: Conectar servidores diretamente aos Insights Operacionais](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [Postagem no blog: Habilitar Insights Operacionais para máquinas virtuais do Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)



<!--HONumber=52-->