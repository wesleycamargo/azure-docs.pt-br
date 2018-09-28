---
title: Integrar o Azure Monitor para VMs | Microsoft Docs
description: Este artigo descreve como você integra e configura o Azure Monitor para VMs, de modo que você possa começar a entender mais sobre o desempenho do aplicativo distribuído e quais problemas de integridade foram identificados.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: e5421ca791ae9d0059639000f33b77be57f4d891
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968026"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms"></a>Como integrar o Azure Monitor para VMs 
Este artigo descreve como configurar o Azure Monitor para VMs para monitorar a integridade do sistema operacional das máquinas virtuais do Azure e descobrir e mapear as dependências de aplicativo que possam estar hospedadas nelas.  

A habilitação do Azure Monitor para VMs é feita usando um dos métodos a seguir, e os detalhes sobre como usar cada método são fornecidos mais adiante neste artigo.  

* Uma máquina virtual individual do Azure, selecionando **Insights (versão prévia)** diretamente na VM.
* Várias VMs do Azure, usando o Azure Policy para garantir que as VMs novas e existentes avaliadas tenham as dependências necessárias instaladas e estejam configuradas corretamente.  As VMs fora de conformidade são relatadas, de modo que você possa decidir, de acordo com o que não está em conformidade, como deseja corrigir isso.  
* Várias VMs ou vários conjuntos de dimensionamento de máquinas virtuais do Azure em uma assinatura ou um grupo de recursos especificado usando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, verifique se você tem os itens a seguir, conforme descrito nas subseções abaixo.

### <a name="log-analytics"></a>Log Analytics 

Atualmente, há suporte para um workspace do Log Analytics nas seguintes regiões:

  - Centro-Oeste dos EUA  
  - Leste dos EUA  
  - Europa Ocidental  
  - Sudeste Asiático<sup>1</sup>  

<sup>1</sup> atualmente, essa região não dá suporte ao recurso Integridade do Azure Monitor para VMs   

Caso não tenha um workspace, crie-o por meio do [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), do [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) ou no [portal do Azure](../log-analytics/log-analytics-quick-create-workspace.md).  

Para habilitar a solução, você precisa ser um membro da função Colaborador do Log Analytics. Para obter mais informações sobre como controlar o acesso a um espaço de trabalho do Log Analytics, veja [Gerenciar espaços de trabalho](../log-analytics/log-analytics-manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

### <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

As seguintes versões dos sistemas operacionais Windows e Linux são oficialmente compatíveis com o Azure Monitor para VMs:

|Versão do SO |Desempenho |Mapas |Integridade |  
|-----------|------------|-----|-------|  
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|RHEL 7, 6| X | X| X |  
|Ubuntu 18.04, 16.04, 14.04 | X | X| X |  
|Cent OS Linux 7, 6 | X | X| X |  
|SLES 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4, 8 | X<sup>1</sup> | | X | 

<sup>1</sup> O recurso Desempenho do Azure Monitor para VMs só está disponível no Azure Monitor; ele não está disponível quando você o acessa no painel esquerdo da VM do Azure diretamente.  

### <a name="hybrid-environment-connected-sources"></a>Fontes conectadas do ambiente híbrido
O Mapa do Azure Monitor para VMs obtém seus dados do Microsoft Dependency Agent. O Dependency Agent depende do agente do Log Analytics para suas conexões com o Log Analytics. Isso significa que um sistema precisa ter o agente do Log Analytics instalado e configurado com o Dependency Agent.  A tabela a seguir descreve as fontes conectadas para as quais o recurso Mapa dá suporte em um ambiente híbrido.

| Fonte conectada | Suportado | Descrição |
|:--|:--|:--|
| Agentes do Windows | SIM | Além do [Agente do Log Analytics](../log-analytics/log-analytics-concept-hybrid.md), os agentes do Windows exigem o Microsoft Dependency Agent. Veja os [sistemas operacionais com suporte](#supported-operating-systems) para obter uma lista completa das versões de sistema operacional. |
| Agentes do Linux | SIM | Além do [Agente do Log Analytics](../log-analytics/log-analytics-concept-hybrid.md), os agentes do Linux exigem o Microsoft Dependency Agent. Veja os [sistemas operacionais com suporte](#supported-operating-systems) para obter uma lista completa das versões de sistema operacional. |
| Grupo de gerenciamento do System Center Operations Manager | Não  | |  

No Windows, o MMA (Microsoft Monitoring Agent) é usado pelo System Center Operations Manager e pelo Log Analytics para coletar e enviar dados de monitoramento. O System Center Operations Manager e o Log Analytics fornecem diferentes versões prontas para uso do agente. Essas versões podem relatar para o System Center Operations Manager, para o Log Analytics ou para ambos.  

No Linux, o Agente do Log Analytics para Linux coleta e envia os dados de monitoramento para o Log Analytics.   

Se seus computadores Windows ou Linux não puderem se conectar diretamente ao serviço, você precisará configurar o agente do Log Analytics para se conectar ao Log Analytics usando o Gateway do OMS. Para obter mais informações sobre como implantar e configurar o Gateway de OMS, consulte [Conectar computadores sem acesso à Internet usando o Gateway de OMS](../log-analytics/log-analytics-oms-gateway.md).  

O Dependency Agent pode ser baixado no local a seguir.

| Arquivo | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.5.0 | 8B8FE0F6B0A9F589C4B7B52945C2C25DF008058EB4D4866DC45EE2485062C9D7 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.5.1 | 09D56EF43703A350FF586B774900E1F48E72FE3671144B5C99BB1A494C201E9E |

## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico
A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço Azure Monitor. A Microsoft utiliza esses dados para fornecer e aprimorar a qualidade, a segurança e a integridade do serviço. Para fornecer funcionalidades precisas e eficientes de solução de problemas, os dados do recurso Mapa incluem informações sobre a configuração de seu software, como sistema operacional e versão, endereço IP, nome DNS e nome da estação de trabalho. A Microsoft não coleta nomes, endereços ou outras informações de contato.

Para obter mais informações sobre a coleta e uso de dados, consulte a [Política de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="performance-counters-enabled"></a>Contadores de desempenho habilitados
O Azure Monitor para VMs configura um Workspace do Log Analytics para coletar os contadores de desempenho usados pela solução.  A tabela a seguir lista os objetos e os contadores configurados pela solução que são coletados a cada 60 segundos.

### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

|Nome do objeto |Nome do contador |  
|------------|-------------|  
|LogicalDisk |% de Espaço Livre |  
|LogicalDisk |Média de segundos/Leitura do Disco |  
|LogicalDisk |Média de segundos/Transferência do Disco |  
|LogicalDisk |Média de segundos/Gravação do Disco |  
|LogicalDisk |Bytes de Disco/s |  
|LogicalDisk |Bytes Lidos no Disco/s  |  
|LogicalDisk |Leituras de Disco/s  |  
|LogicalDisk |Transferências de Disco/s |  
|LogicalDisk |Bytes Gravados no Disco/s |  
|LogicalDisk |Gravações de Disco/s |  
|LogicalDisk |Megabytes Livres |  
|Memória |MBytes Disponíveis |  
|Adaptador de rede |Bytes Recebidos/s |  
|Adaptador de rede |Bytes Enviados/s |  
|Processador |% Tempo do Processador |  

### <a name="linux-performance-counters"></a>Contadores de desempenho do Linux

|Nome do objeto |Nome do contador |  
|------------|-------------|  
|Disco Lógico |% de Espaço Usado |  
|Disco Lógico |Bytes Lidos no Disco/s  |  
|Disco Lógico |Leituras de Disco/s  |  
|Disco Lógico |Transferências de Disco/s |  
|Disco Lógico |Bytes Gravados no Disco/s |  
|Disco Lógico |Gravações de Disco/s |  
|Disco Lógico |Megabytes Livres |  
|Disco Lógico |Bytes de Disco Lógico/s |  
|Memória |MBytes de Memória Disponíveis |  
|Rede |Total de Bytes Recebidos |  
|Rede |Total de Bytes Transmitidos |  
|Processador |% Tempo do Processador |  

## <a name="enable-from-the-azure-portal"></a>Habilitar por meio do portal do Azure
Para habilitar o monitoramento da VM do Azure no portal do Azure, faça o seguinte:

1. No Portal do Azure, selecione **Máquinas Virtuais**. 
2. Na lista, selecione uma VM. 
3. Na página da VM, na seção **Monitoramento**, selecione **Insights (versão prévia)**.
4. Na página **Insights (versão prévia)**, selecione **Experimentar agora**.

    ![Habilitar o Azure Monitor para VMs em uma VM](./media/monitoring-vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. Na página **Integração de Insights do Azure Monitor**, caso você tenha um workspace existente do Log Analytics na mesma assinatura, selecione-o na lista suspensa.  A lista pré-seleciona o workspace e o local padrão nos quais a máquina virtual é implantada na assinatura. 

    >[!NOTE]
    >Caso deseje criar um workspace do Log Analytics para armazenar os dados de monitoramento da VM, siga as instruções em [Criar um workspace do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Crie o workspace na mesma assinatura na qual a VM é implantada. 

Depois de habilitar o monitoramento, poderão ser necessários cerca de 10 minutos antes da exibição das métricas de integridade para a máquina virtual. 

![Habilitar o Azure Monitor para o processamento da implantação do monitoramento das VMs](./media/monitoring-vminsights-onboard/onboard-vminsights-vm-portal-status.png)

## <a name="enable-using-azure-policy"></a>Habilitar usando o Azure Policy
Para habilitar a solução para várias VMs do Azure que garantem a conformidade consistente e a habilitação automática para novas VMs provisionadas, é recomendado usar o [Azure Policy](../azure-policy/azure-policy-introduction.md).  O uso do Azure Policy com as políticas fornecidas oferece os seguintes benefícios para novas VMs:

* Habilitando o Azure Monitor para VMs para cada VM no escopo definido
* Implantar o Agente do Log Analytics 
* Implantar o Dependency Agent para descobrir dependências de aplicativo e mostrá-las no Mapa
* Auditar se a imagem do sistema operacional da VM do Azure está em uma lista predefinida na definição de política  
* Auditar se a VM do Azure está fazendo logon em um workspace diferente daquele especificado
* Relatar os resultados de conformidade 
* Dar suporte à correção para VMs fora de conformidade

Para ativá-lo para seu locatário, esse processo exige:

- Configurar um workspace do Log Analytics usando as etapas listadas aqui
- Importar a definição de iniciativa para seu locatário (no nível do Grupo de Gerenciamento ou da Assinatura)
- Atribuir a política ao escopo desejado
- Examinar os resultados de conformidade

### <a name="add-the-policies-and-initiative-to-your-subscription"></a>Adicionar as políticas e a iniciativa à sua assinatura
Para usar as políticas, use um script do PowerShell fornecido – [Add-VMInsightsPolicy.ps1](https://www.powershellgallery.com/packages/Add-VMInsightsPolicy/1.2) disponível por meio da Galeria do Azure PowerShell para concluir esta tarefa. O script adiciona as políticas e uma iniciativa à sua assinatura.  Execute as etapas a seguir para configurar o Azure Policy em sua assinatura. 

1. Baixe o script do PowerShell no sistema de arquivos local.

2. Use o comando do PowerShell a seguir na pasta para adicionar políticas. O script dá suporte aos seguintes parâmetros opcionais: 

    ```powershell
    -UseLocalPolicies [<SwitchParameter>]
      <Optional> Load the policies from a local folder instead of https://raw.githubusercontent.com/dougbrad/OnBoardVMInsights/Policy/Policy/

    -SubscriptionId <String>
      <Optional> SubscriptionId to add the Policies/Initiatives to
    -ManagementGroupId <String>
      <Optional> Management Group Id to add the Policies/Initiatives to

    -Approve [<SwitchParameter>]
      <Optional> Gives the approval to add the Policies/Initiatives without any prompt
    ```  

    >[!NOTE]
    >Observação: se você pretende atribuir a iniciativa/política a várias assinaturas, o local precisa ser armazenado no grupo de gerenciamento que contém as assinaturas às quais você atribuirá a política. Portanto, é necessário usar o parâmetro -ManagementGroupID.
    >
   
    Exemplo sem parâmetros: `.\Add-VMInsightsPolicy.ps1`

### <a name="create-a-policy-assignment"></a>Criar uma atribuição de política
Depois de executar o script `Add-VMInsightsPolicy.ps1` do PowerShell, as seguintes políticas e iniciativa são adicionadas:

* **Implantar o Agente do Log Analytics para VMs do Windows – versão prévia**
* **Implantar o Agente do Log Analytics para VMs do Linux – versão prévia**
* **Implantar o Dependency Agent para VMs do Windows – versão prévia**
* **Implantar o Dependency Agent para VMs do Linux – versão prévia**
* **Auditar a implantação do Agente do Log Analytics – imagem de VM (sistema operacional) não listada – versão prévia**
* **Auditar a implantação do Dependency Agent – imagem de VM (sistema operacional) não listada – versão prévia**

O seguinte parâmetro de iniciativa é adicionado:

- **Workspace do Log Analytics** (você precisará fornecer a ResourceID do workspace se estiver aplicando uma atribuição usando o PowerShell ou a CLI)

    Para as VMs consideradas fora de conformidade nas políticas de auditoria **As VMs não estão no escopo do sistema operacional...**, os critérios da política de implantação incluem apenas as VMs implantadas por meio de imagens de VM do Azure conhecidas. Verifique a documentação caso o sistema operacional da VM seja compatível ou não.  Se ele não for, você precisará duplicar a política de implantação e atualizá-la/modificá-la para tornar a imagem no escopo.

A seguinte política opcional autônoma é adicionada:

- **A VM está configurada para um Workspace incompatível do Log Analytics – versão prévia**

    Isso pode ser usado para identificar as VMs já configuradas com a [Extensão de VM do Log Analytics](../virtual-machines/extensions/oms-windows.md), mas que estão configuradas com um workspace diferente do pretendido (conforme indicado pela atribuição de política). Isso usa um parâmetro para a WorkspaceID.

Com essa versão inicial, você só pode criar a atribuição de política no portal do Azure. Para entender como concluir essas etapas, confira [Criar uma atribuição de política no portal do Azure](../azure-policy/assign-policy-definition.md).

## <a name="enable-with-powershell"></a>Habilitar com o PowerShell
Para habilitar o Azure Monitor para VMs para várias VMs ou para conjuntos de dimensionamento de VMs, use um script do PowerShell fornecido – [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), disponível por meio da Galeria do Azure PowerShell para concluir esta tarefa.  Esse script iterará por cada máquina virtual e conjunto de dimensionamento de VMs em sua assinatura, no grupo de recursos no escopo especificado pelo *ResourceGroup* ou em uma VM ou um conjunto de dimensionamento individual especificado pelo *Name*.  Para cada VM ou conjunto de dimensionamento de VMs, o script verifica se a extensão de VM já está instalada e, se ela não estiver, tentará reinstalá-la.  Caso contrário, ele continua para instalar as extensões de VM do Dependency Agent e do Log Analytics.   

Este script exige o módulo do Azure PowerShell, versão 5.7.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.

Para obter ajuda sobre o script, execute `Get-Help` para obter uma lista de detalhes de argumento e o uso de exemplo.   

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and Dependency Agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VM's and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VM's/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VM's are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong to

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VM's to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VM's/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Suported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VM's in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to ReInstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to ReInstall (move to a new workspace)
```

O exemplo a seguir demonstra como usar os comandos do PowerShell na pasta para habilitar o Azure Monitor para VMs e entender o resultado esperado:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VM's or VM ScaleSets matching criteria specified

VM's or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency Agent extensions on above 2 VM's or VM Scale Sets.
VM's in a non-running state will be skipped.
Extension will not be re-installed if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-hybrid-environment"></a>Habilitar para o ambiente Híbrido
Esta seção explica como integrar máquinas virtuais ou computadores físicos hospedados em seu datacenter ou em outro ambiente de nuvem para o monitoramento pelo Azure Monitor para VMs.  

O Dependency Agent do Mapa do Azure Monitor para VMs não transmite dados por conta própria e não exige alterações em firewalls ou portas. Os dados no Mapa sempre são transmitidos pelo agente do Log Analytics para o serviço Azure Monitor, diretamente ou por meio do [Gateway do OMS](../log-analytics/log-analytics-oms-gateway.md), caso as políticas de segurança não permitam que os computadores na rede se conectem à Internet.

Examine os requisitos e os métodos de implantação do [agente do Linux e do Windows do Log Analytics](../log-analytics/log-analytics-concept-hybrid.md).

Etapas resumidas:

1. Instalar o Agente do Log Analytics para Windows ou Linux
2. Instalar o Dependency Agent do Mapa do Azure Monitor para VMs
3. Habilitar a coleta de contadores de desempenho
4. Integrar o Azure Monitor para VMs

### <a name="install-the-dependency-agent-on-windows"></a>Instalar o Dependency Agent no Windows 
O agente de dependência pode ser instalado manualmente em computadores Windows executando `InstallDependencyAgent-Windows.exe`. Se você executar o arquivo executável sem opções, ele iniciará um assistente que você poderá seguir para executar a instalação interativamente.  

>[!NOTE]
>São necessários privilégios de administrador para instalar ou desinstalar o agente.

A tabela a seguir destaca os parâmetros específicos compatíveis com a instalação do agente por meio da linha de comando.  

| Parâmetro | DESCRIÇÃO |
|:--|:--|
| /? | Retorna uma lista das opções de linha de comando. |
| /S | Realiza uma instalação silenciosa sem a interação do usuário. |

Por exemplo, para executar o programa de instalação com o parâmetro `/?`, digite `InstallDependencyAgent-Windows.exe /?`

Os arquivos do Dependency Agent do Windows são instalados no `C:\Program Files\Microsoft Dependency Agent` por padrão.  Se o Dependency Agent não for iniciado após a conclusão da instalação, verifique os logs para obter informações de erro detalhadas. O diretório de log é `%Programfiles%\Microsoft Dependency Agent\logs`. 

### <a name="install-the-dependency-agent-on-linux"></a>Instale o Agente de Dependência no Linux
O Dependency Agent é instalado em servidores Linux por meio do `InstallDependencyAgent-Linux64.bin`, um script de shell com um binário de extração automática. Você pode executar o arquivo usando `sh` ou adicionar permissões de execução ao próprio arquivo.

>[!NOTE]
> O acesso root é necessário para instalar ou configurar o agente.
> 

| Parâmetro | DESCRIÇÃO |
|:--|:--|
| -help | Obtenha uma lista das opções de linha de comando. |
| -s | Realize uma instalação silenciosa sem solicitações ao usuário. |
| --check | Verifica as permissões e o sistema operacional, mas não instala o agente. |

Por exemplo, para executar o programa de instalação com o parâmetro `-help`, digite `InstallDependencyAgent-Linux64.bin -help`.

Instale o Dependency Agent do Linux como raiz usando o comando `sh InstallDependencyAgent-Linux64.bin`
    
Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Em agentes do Linux, o diretório de log é `/var/opt/microsoft/dependency-agent/log`.

Os arquivos do Agente de Dependência são colocados nos diretórios a seguir:

| Arquivos | Local padrão |
|:--|:--|
| Arquivos de núcleo | /opt/microsoft/dependency-agent |
| Arquivos de log | /var/opt/microsoft/dependency-agent/log |
| Arquivos de configuração | /etc/opt/microsoft/dependency-agent/config |
| Arquivos executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Arquivos de armazenamento binário | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Habilitar contadores de desempenho
Se o workspace do Log Analytics referenciado pela solução não estiver configurado para coletar imediatamente os contadores de desempenho necessários para a solução, eles precisarão ser habilitados. Isso pode ser feito manualmente, conforme descrito [aqui](../log-analytics/log-analytics-data-sources-performance-counters.md), ou baixando e executando um script do PowerShell disponível por meio da [Galeria do Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
### <a name="onboard-azure-monitor-for-vms"></a>Integrar o Azure Monitor para VMs
Esse método inclui um modelo JSON que especifica a configuração para habilitar os componentes da solução no workspace do Log Analytics.  

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a CLI do Azure versão 2.0.27 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Criar e executar um modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. Salve esse arquivo como **installsolutionsforvminsights.json** em uma pasta local.
3. Edite os valores de **WorkspaceName**, **ResourceGroupName** e **WorkspaceLocation**.  O valor de **WorkspaceName** é a ID do recurso completa do workspace do Log Analytics, que inclui o nome do workspace, e o valor de **WorkspaceLocation** é a região na qual o workspace está definido.
4. Você está pronto para implantar esse modelo usando o seguinte comando do PowerShell:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

    ```powershell
    provisioningState       : Succeeded
    ```
Depois de habilitar o monitoramento, podem ser necessários cerca de 10 minutos antes da exibição do estado de integridade e das métricas para o computador híbrido. 

## <a name="next-steps"></a>Próximas etapas

Com o monitoramento habilitado para a máquina virtual, essas informações estarão disponíveis para análise com o Azure Monitor para VMs.  Para saber como usar o recurso Integridade, confira [Exibir a Integridade do Azure Monitor para VMs](monitoring-vminsights-health.md), ou para exibir dependências de aplicativos descobertos, confira [Exibir o Mapa do Azure Monitor para VMs](monitoring-vminsights-maps.md).  