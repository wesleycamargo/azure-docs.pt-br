<properties
	pageTitle="Conectar computadores diretamente a Insights Operacionais"
	description="Você pode conectar computadores diretamente a Insights Operacionais instalando o agente Insights Operacionais para cada computador que você desejar integrar."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/02/2015" 
	ms.author="banders"/>
# Conectar computadores diretamente a Insights Operacionais

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Você pode conectar computadores diretamente a Insights Operacionais instalando o agente Insights Operacionais em cada computador que você desejar integrar.

> [AZURE.TIP]Para máquinas virtuais em execução no Azure, instale o agente, seguindo as etapas em [Analisar dados de servidores no Microsoft Azure](operational-insights-analyze-data-azure.md)

## Baixe e instale o agente
Use os procedimentos a seguir para baixar e instalar o agente de Insights Operacionais.

### Para baixar o arquivo de instalação do agente
1. No portal de Insights Operacionais, na página **Visão geral**, clique no bloco **Configurações**. Clique na guia **Fontes Conectadas** na parte superior. ![página de configurações](./media/operational-insights-direct-agent/direct-agent01.png)
2. Em **Anexar Servidores Diretamente (64 bits)**, clique no botão Baixar o Agente para baixar o arquivo de configuração.
3. À direita da **ID do Espaço de Trabalho**, clique no ícone para copiar e colar a ID no Bloco de Notas.
4. À direita da **Chave Primária**, clique no ícone para copiar e colar a ID no Bloco de Notas. ![página de configurações](./media/operational-insights-direct-agent/direct-agent02.png)

### Para instalar o agente usando o programa de instalação
1. Execute Instalação para instalar o agente em um computador que você deseje gerenciar.
2. Selecione **Conectar o agente a Insights Operacionais do Microsoft Azure** e clique em **Avançar**.
3. Quando solicitado, insira a **ID do Espaço de Trabalho** e a **Chave Primária** que você copiou no Bloco de Notas no procedimento anterior.

4. Clique em **Próximo**. O agente verifica se pode se conectar ao Insights Operacionais.
5. Ao concluir, o **Agente de Gerenciamento da Microsoft** aparece no **Painel de Controle**.

### Para instalar o agente usando a linha de comando
- Modifique e, em seguida, use o exemplo a seguir para instalar o agente usando a linha de comando. ```MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"```

## Configurar o Microsoft Monitoring Agent (opcional)
Use as informações a seguir para habilitar um agente para se comunicar diretamente com o serviço de Insights Operacionais do Microsoft Azure. Depois que você tiver configurado o agente, ele será registrado com o serviço do agente e obterá as informações de configuração necessárias e os pacotes de gerenciamento contendo informações da solução.

Depois que dados forem coletados de computadores monitorados por agente, o número de computadores monitorados aparecerá no portal de Insights Operacionais na guia **Fontes Conectadas** em **Configurações** sob **Anexar Servidores Diretamente (64 bits)**. Para qualquer computador que envie dados, você pode exibir suas informações de avaliação e dados no portal de Insights Operacionais.

Você também pode desabilitar o agente, se necessário, ou habilitá-lo usando a linha de comando ou script.

### Para configurar o agente
1. Depois de instalar o **Microsoft Monitoring Agent**, abra o **Painel de Controle**.
2. Abra o Microsoft Monitoring Agent e, em seguida, clique na guia Insights Operacionais do Azure.
3. Selecione **Conectar a Insights Operacionais do Azure**.
4. Na caixa **ID do Espaço de Trabalho**, digite a ID do espaço de trabalho do portal de Insights Operacionais.
5. Na caixa **Chave da Conta**, cole a **Chave Primária** do portal de Insights Operacionais e clique em **OK**.

### Para desabilitar um agente
1. Depois de instalar o agente, abra o **Painel de Controle**.
2. Abra o Microsoft Monitoring Agent e, em seguida, clique na guia **Insights Operacionais do Azure**.
3. Desmarque **Conectar-se a Insights Operacionais do Azure**.

### Para habilitar o agente usando a linha de comando ou script
- Você pode usar o Windows PowerShell ou um script VB com o exemplo a seguir.

```powershell
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
$healthServiceSettings.ReloadConfiguration()
```

## Definir as configurações de proxy e firewall (opcional)
Se você tiver servidores proxy ou firewalls em seu ambiente que restrinjam o acesso à Internet, pode ser necessário realizar os procedimentos a seguir para habilitar o Operations Manager ou os agentes para se comunicarem com o serviço de Insights Operacionais

- [Definir as configurações de proxy e firewall (opcional)](operational-insights-proxy-firewall.md)

<!---HONumber=August15_HO6-->