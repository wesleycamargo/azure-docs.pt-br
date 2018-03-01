---
title: Conectar computadores Windows ao Azure Log Analytics | Microsoft Docs
description: Este artigo descreve como conectar computadores Windows hospedados em outras nuvens ou locais no Log Analytics com o MMA (Microsoft Monitoring Agent).
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
ms.date: 12/14/2017
ms.author: magoedte
ms.openlocfilehash: 87513ef82b5f754669a3a21dd736ecab6fb26fba
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Conectar computadores Windows ao serviço do Log Analytics no Azure

Para monitorar e gerenciar máquinas virtuais ou computadores físicos em seu datacenter local ou outro ambiente de nuvem com o Log Analytics, é necessário implantar o MMA (Microsoft Monitoring Agent) e configurá-lo para relatar a um ou mais espaços de trabalho do Log Analytics.  Adicionalmente, o agente fornece suporte à função do Hybrid Runbook Worker para a Automação do Azure.  

Em um computador Windows monitorado, o agente está listado como o serviço do Microsoft Monitoring Agent. O serviço do Microsoft Monitoring Agent coleta eventos de arquivos de log e log de eventos do Windows, dados de desempenho e outra telemetria. Mesmo quando o agente está impossibilitado de se comunicar com o serviço do Log Analytics ao qual ele relata, o agente continua em execução e coloca em fila os dados coletados no disco do computador monitorado. Quando a conexão é restaurada, o serviço do Microsoft Monitoring Agent envia os dados coletados para o serviço.

O agente pode ser instalado usando um dos seguintes métodos. A maioria das instalações usa uma combinação desses métodos para instalar diferentes conjuntos de computadores, conforme apropriado.

* Instalação manual. O programa de configuração é executado manualmente no computador usando o assistente de configuração, a partir da linha de comando ou implantado, usando uma ferramenta de distribuição de software existente.
* DSC (Desired State Configuration) na Automação do Azure. Usar o DSC na Automação do Azure com um script para computadores Windows já implantado em seu ambiente.  
* Script do PowerShell.
* Modelo do Resource Manager para máquinas virtuais executando o Windows local no Azure Stack.  

Para entender os requisitos de sistema e de rede para implantar o Agente do Windows, examine [Coletar dados de seu ambiente com o Azure Log Analytics](log-analytics-concept-hybrid.md#prerequisites).

## <a name="obtain-workspace-id-and-key"></a>Obter a ID e a chave do espaço de trabalho
Antes de instalar o Microsoft Monitoring Agent para Windows, você precisa da ID e da chave do seu espaço de trabalho do Log Analytics.  Essas informações serão necessárias durante a configuração de cada método de instalação para configurar corretamente o agente e garantir que ele possa se comunicar com êxito com o Log Analytics.  

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
2. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho que pretende configurar o agente a qual relatar.
3. Selecione **Configurações avançadas**.<br><br> ![Configurações avançadas do Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Selecione **Fontes Conectadas** e depois **Servidores Windows**.   
5. O valor à direita da **ID do Espaço de Trabalho** e **Chave Primária**. Copie e cole os dois em seu editor favorito.   
   
## <a name="install-the-agent-using-setup"></a>Instalar o agente usando a instalação
As etapas a seguir instalam e configuram o agente para o Log Analytics do Azure e a nuvem do Azure Government usando a instalação para o Microsoft Monitoring Agent em seu computador.  O programa de instalação para o agente está contido no arquivo baixado e deve ser extraído para 

1. Na página **Servidores Windows**, selecione a versão apropriada de **Baixar Agente do Windows** a ser baixada com base na arquitetura do processador do sistema operacional Windows.
2. Execute a Instalação para instalar o agente no seu computador.
2. Na página de **Boas-vindas**, clique em **Avançar**.
3. Na página **Termos de Licença**, leia a licença e clique em **Aceito**.
4. Na página **Pasta de Destino**, altere ou mantenha a pasta de instalação padrão e clique em **Avançar**.
5. Na página **Opções de Instalação do Agente**, escolha a opção de conectar o agente ao Azure Log Analytics (OMS) e clique em **Avançar**.   
6. Na página **Log Analytics do Azure**, faça o seguinte:
   1. Cole a **ID do Espaço de Trabalho** e a **Chave do Espaço de Trabalho (Chave Primária)** que você copiou anteriormente.  Caso o computador deva se reportar a um espaço de trabalho do Log Analytics na nuvem do Azure Governamental, selecione **Governo dos EUA do Azure** na lista suspensa do **Azure Cloud**.  
   2. Caso o computador precise se comunicar por meio de um servidor proxy ao serviço Log Analytics, clique em **Avançado** e forneça a URL e o número da porta do servidor proxy.  Caso seu servidor proxy exija autenticação, digite o nome de usuário e a senha para se autenticar com o servidor proxy e clique em **Avançar**.  
7. Clique em **Avançar** depois de ter terminado de fornecer as configurações necessárias.<br><br> ![colar ID de Espaço de Trabalho e a Chave Primária](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Na página **Pronto para Instalar**, revise suas escolhas e clique em **Instalar**.
9. Na página **Configuração concluída com êxito**, clique em **Concluir**.

Após concluir, o **Microsoft Monitoring Agent** aparecerá no **Painel de Controle**. Para confirmar que está relatando ao Log Analytics, revise [Verificar a conectividade do agente com Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Instalar o agente usando a linha de comando
O arquivo baixado para o agente é um pacote de instalação autossuficiente criado com o IExpress.  O programa de instalação para o agente e os arquivos de suporte estão contidos no pacote e precisam ser extraídos para instalar corretamente usando a linha de comando mostrada nos exemplos a seguir.  Este método é compatível com a configuração do agente para relatar à nuvem do Governo dos EUA e comercial do Azure.  

>[!NOTE]
>Se você quiser atualizar um agente, precisará usar a API de script do Log Analytics. Consulte o tópico [Gerenciar e manter o agente de Log Analytics para o Windows e Linux](log-analytics-agent-manage.md), para obter mais informações.

A tabela a seguir destaca os parâmetros específicos do Log Analytics com suporte pela configuração do agente, inclusive quando implantados usando o DSC de Automação.

|Opções específicas do MMA                   |Observações         |
|---------------------------------------|--------------|
|ADD_OPINSIGHTS_WORKSPACE               | 1 = configurar o agente para reportar a um espaço de trabalho                |
|OPINSIGHTS_WORKSPACE_ID                | ID do espaço de trabalho (guid) para o espaço de trabalho a ser adicionado                    |
|OPINSIGHTS_WORKSPACE_KEY               | Chave do espaço de trabalho usada para autenticar inicialmente com o espaço de trabalho |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Especifique o ambiente de nuvem no qual o espaço de trabalho está <br> 0 = nuvem comercial do Azure (padrão) <br> 1 = Azure Governamental |
|OPINSIGHTS_PROXY_URL               | URI do proxy a ser usado |
|OPINSIGHTS_PROXY_USERNAME               | Nome de usuário para acessar um proxy autenticado |
|OPINSIGHTS_PROXY_PASSWORD               | Senha para acessar um proxy autenticado |

1. Para extrair os arquivos de instalação do agente de um prompt de comando com privilégios elevados, execute `extract MMASetup-<platform>.exe` e será solicitado que você especifique o caminho para extrair os arquivos.  Como alternativa, você poderá especificar o caminho ao passar os argumentos `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Para mais informações sobre as opções de linha de comando suportados por IExpress, consulte [Opções de linha de comando do IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) e, em seguida, atualize o exemplo para atender às suas necessidades.
2. Para instalar silenciosamente o agente e configurá-lo para relatar a um espaço de trabalho na nuvem comercial do Azure, da pasta que extraiu os arquivos de configuração para inserir: 
   
     ```dos
    setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

   ou para configurar o agente para relatar a nuvem do Governo dos EUA do Azure, inserir: 

     ```dos
    setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Instalar o agente usando o DSC na Automação do Azure

Você pode usar o seguinte exemplo de script para instalar o agente usando o DSC de Automação do Azure.   Se você não possuir uma conta de Automação, consulte [Comece a usar a automação do Azure](../automation/automation-offering-get-started.md) para entender os requisitos e as etapas para criar uma conta de Automação necessária antes de usar o DSC de Automação.  Se você não estiver familiarizado com o DSC de Automação, revise [Introdução ao DSC de Automação](../automation/automation-dsc-getting-started.md).

O exemplo a seguir instala o agente de 64 bits, identificado pelo valor `URI`. Também é possível usar a versão de 32 bits, substituindo o valor do URI. Os URIs para ambas as versões são:

- Agente do Windows de 64 bits – https://go.microsoft.com/fwlink/?LinkId=828603
- Agente do Windows de 32 bits – https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Esse procedimento e exemplo de script não dá suporte para a atualização do agente já implantado em um computador Windows.

As versões de 32 bits e 64 bits do pacote do agente têm códigos de produtos diferentes e as novas versões liberadas também possuem um valor exclusivo.  O código do produto é um GUID que é a principal identificação de um aplicativo ou produto e é representado pela propriedade **ProductCode** do Windows Installer.  O `ProductId value` no script **MMAgent.ps1** deve corresponder com o código do produto do pacote do instalador do agente de 32 bits ou 64 bits.

Para recuperar o código do produto do pacote do instalador do agente diretamente, você pode usar Orca.exe dos [Componentes SDK do Windows para desenvolvedores do Windows Installer](https://msdn.microsoft.com/library/windows/desktop/aa370834%27v=vs.85%28.aspx) que é um Software Development Kit do Windows ou usando o PowerShell após um [script de exemplo](http://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) gravado por um MVP (Microsoft Valuable Professional).

1. Importe o Módulo xPSDesiredStateConfiguration do DSC de [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) para a Automação do Azure.  
2.  Crie ativos de variável da Automação do Azure para *OPSINSIGHTS_WS_ID* e *OPSINSIGHTS_WS_KEY*. Defina *OPSINSIGHTS_WS_ID* para sua ID do espaço de trabalho do Log Analytics e defina *OPSINSIGHTS_WS_KEY* para a chave primária do seu espaço de trabalho.
3.  Copie o script e salve-o como MMAgent.ps1

    ```PowerShell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
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
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:Deploy"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. [Importe o script de configuração MMAgent.ps1](../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) na sua conta de Automação. 
5. [Atribua um computador Windows ou um nó](../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-dsc) à configuração. Dentro de 15 minutos, o nó verificará a configuração e o agente será enviado por push para o nó.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Verificar a conectividade do agente com Log Analytics

Quando a instalação do agente for concluída, verifique se está conectado com êxito e se o relatório pode ser realizado de duas maneiras.  

No computador, no **Painel de Controle**, localize o item **Microsoft Monitoring Agent**.  Selecione-o e na guia **Azure Log Analytics (OMS)**, o agente deve exibir uma mensagem indicando: **o Microsoft Monitoring Agent foi conectado com êxito ao serviço do Microsoft Operations Management Suite.**<br><br> ![Status de conexão do MMA ao Log Analytics](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

Você também pode realizar uma pesquisa de registro simples no Portal do Azure.  

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.  
2. Na página de espaço de trabalho do Log Analytics, selecione o espaço de trabalho de destino e, em seguida, selecione o bloco **Pesquisa de Logs**. 
2. No painel Pesquisa de Logs, no tipo de campo de consulta:  

    ```
    search * 
    | where Type == "Heartbeat" 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Nos resultados de pesquisa retornados, você deverá ver os registros de pulsação para o computador, indicando que está conectado e relatando para o serviço.   

## <a name="next-steps"></a>Próximas etapas

Revisar[Gerenciar e manter o agente de Log Analytics para o Windows e Linux](log-analytics-agent-manage.md) para aprender sobre como gerenciar o agente durante o ciclo de vida da implantação em suas máquinas.  