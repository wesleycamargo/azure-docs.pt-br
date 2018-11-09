---
title: Monitor do Azure a bordo para VMs (visualização) | Microsoft Docs
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
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: f55f81d1e28a7626dfe00f6bea349bf74e3a1d24
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092767"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms-preview"></a>Como integrar o Monitor do Azure para VMs (visualização)
Este artigo descreve como configurar o Azure Monitor para VMs para monitorar a integridade do sistema operacional das máquinas virtuais do Azure e descobrir e mapear as dependências de aplicativo que possam estar hospedadas nelas.  

A habilitação do Azure Monitor para VMs é feita usando um dos métodos a seguir, e os detalhes sobre como usar cada método são fornecidos mais adiante neste artigo.  

* Uma máquina virtual individual do Azure, selecionando **Insights (versão prévia)** diretamente na VM.
* Várias VMs do Azure, usando o Azure Policy para garantir que as VMs novas e existentes avaliadas tenham as dependências necessárias instaladas e estejam configuradas corretamente.  As VMs fora de conformidade são relatadas, de modo que você possa decidir, de acordo com o que não está em conformidade, como deseja corrigir isso.  
* Várias VMs ou vários conjuntos de dimensionamento de máquinas virtuais do Azure em uma assinatura ou um grupo de recursos especificado usando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de entender o seguinte, conforme descrito nas subseções abaixo.

### <a name="log-analytics"></a>Log Analytics 

Atualmente, há suporte para um espaço de trabalho do Log Analytics nas seguintes regiões:

  - Centro-Oeste dos EUA  
  - Leste dos EUA  
  - Europa Ocidental  
  - Sudeste Asiático<sup>1</sup>  

<sup>1</sup> atualmente, essa região não dá suporte ao recurso Integridade do Azure Monitor para VMs   

>[!NOTE]
>As máquinas virtuais do Azure podem ser integradas de qualquer região e não são limitadas para as regiões com suporte para o espaço de trabalho do Log Analytics.
>

Se você não tiver um espaço de trabalho, você pode criá-lo por meio da [CLI do Azure](../log-analytics/log-analytics-quick-create-workspace-cli.md), por meio do [PowerShell](../log-analytics/log-analytics-quick-create-workspace-posh.md), no [portal do Azure](../log-analytics/log-analytics-quick-create-workspace.md), ou com [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md).  Se você estiver habilitando o monitoramento para uma única VM do Azure do portal do Azure, você tem a opção de criar um espaço de trabalho durante esse processo.  

Para habilitar a solução, você precisa ser um membro da função Colaborador do Log Analytics. Para obter mais informações sobre como controlar o acesso a um workspace do Log Analytics, veja [Gerenciar workspaces](../log-analytics/log-analytics-manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

Habilitar a solução para o cenário em escala primeiro requer configurar o seguinte no seu espaço de trabalho do Log Analytics:

* Instale as soluções **ServiceMap** e **InfrastructureInsights**
* Configure o espaço de trabalho do Log Analytics para coletar contadores de desempenho

Para configurar seu espaço de trabalho para este cenário, consulte [Espaço de trabalho do Log Analytics](#setup-log-analytics-workspace).

### <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

A tabela a seguir lista os sistemas operacionais Windows e Linux compatíveis com o Azure Monitor para VMs.  Mais adiante nesta seção, será fornecida uma lista completa, detalhando as versões principais e secundárias do sistema operacional Linux e do kernel com suporte.

|Versão do SO |Desempenho |Mapas |Integridade |  
|-----------|------------|-----|-------|  
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|RHEL 7, 6| X | X| X |  
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |  
|Cent OS Linux 7, 6 | X | X | X |  
|SLES 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4, 8 | X<sup>1</sup> | | X | 

<sup>1</sup> O recurso Desempenho do Azure Monitor para VMs só está disponível no Azure Monitor; ele não está disponível quando você o acessa no painel esquerdo da VM do Azure diretamente.  

>[!NOTE]
>As informações a seguir aplicam-se ao suporte do sistema operacional Linux:  
> - Somente as versões de kernel padrão e Linux SMP têm suporte.  
> - Nenhuma distribuição do Linux dá suporte às versões de kernel não padrão, como PAE e Xen. Por exemplo, não há suporte para um sistema com a cadeia de caracteres de versão "2.6.16.21-0.8-xen".  
> - Não há suporte para kernels personalizados, incluindo recompilações de kernels padrão.  
> - Não há suporte para o kernel CentOSPlus.  


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versão do SO | Versão do kernel |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7,2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versão do SO | Versão do kernel |
|:--|:--|
| 6,0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Versão do SO | Versão do kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 com Unbreakable Enterprise Kernel
| Versão do SO | Versão do kernel
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 com Unbreakable Enterprise Kernel

| Versão do SO | Versão do kernel
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versão do SO | Versão do kernel
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="hybrid-environment-connected-sources"></a>Fontes conectadas do ambiente híbrido
O Mapa do Azure Monitor para VMs obtém seus dados do Microsoft Dependency Agent. O agente de dependência depende do agente do Log Analytics para sua conexão ao Log Analytics e, portanto, um sistema deve ter o agente do Log Analytics instalado e configurado com o agente de Dependência. A tabela a seguir descreve as fontes conectadas para as quais o recurso Mapa dá suporte em um ambiente híbrido.

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
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.1 | 55030ABF553693D8B5112569FB2F97D7C54B66E9990014FC8CC43EFB70DE56C6 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.1 | 43C75EF0D34471A0CBCE5E396FFEEF4329C9B5517266108FA5D6131A353D29FE |

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

## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure
Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="enable-from-the-azure-portal"></a>Habilitar por meio do portal do Azure
Para habilitar o monitoramento da VM do Azure no portal do Azure, faça o seguinte:

1. No Portal do Azure, selecione **Máquinas Virtuais**. 
2. Na lista, selecione uma VM. 
3. Na página da VM, na seção **Monitoramento**, selecione **Insights (versão prévia)**.
4. Na página **Insights (versão prévia)**, selecione **Experimentar agora**.

    ![Habilitar o Azure Monitor para VMs em uma VM](./media/monitoring-vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. Na página **Integração de Insights do Azure Monitor**, caso você tenha um workspace existente do Log Analytics na mesma assinatura, selecione-o na lista suspensa.  A lista pré-seleciona o workspace e o local padrão nos quais a máquina virtual é implantada na assinatura. 

    >[!NOTE]
    >Caso deseje criar um workspace do Log Analytics para armazenar os dados de monitoramento da VM, siga as instruções em [Criar um workspace do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md) em uma das regiões com suporte já listadas.   

Depois de habilitar o monitoramento, poderão ser necessários cerca de 10 minutos antes da exibição das métricas de integridade para a máquina virtual. 

![Habilitar o Azure Monitor para o processamento da implantação do monitoramento das VMs](./media/monitoring-vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="on-boarding-at-scale"></a>Integração em escala
Esta seção, instrui a respeito de como executar na implantação de escala do Azure Monitor para máquinas virtuais usando o Azure Policy ou com o Microsoft Azure PowerShell.  A primeira etapa necessária é configurar seu espaço de trabalho do Log Analytics.  

### <a name="setup-log-analytics-workspace"></a>Configuração do espaço de trabalho do Log Analytics
Se você não tiver um espaço de trabalho do Log Analytics, examine os métodos disponíveis sugeridos na seção [Pré-requisitos](#log-analytics) seção para criar um.  

#### <a name="enable-performance-counters"></a>Habilitar contadores de desempenho
Se o workspace do Log Analytics referenciado pela solução não estiver configurado para coletar imediatamente os contadores de desempenho necessários para a solução, eles precisarão ser habilitados. Isso pode ser feito manualmente, conforme descrito [aqui](../log-analytics/log-analytics-data-sources-performance-counters.md), ou baixando e executando um script do PowerShell disponível por meio da [Galeria do Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Instale as soluções ServiceMap e InfrastructureInsights
Esse método inclui um modelo JSON que especifica a configuração para habilitar os componentes da solução no workspace do Log Analytics.  

Se você não estiver familiarizado com o conceito de implantação de recursos usando um modelo, veja:
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 

Se você optar por usar a CLI do Azure, primeiro precisará instalar e usar a CLI localmente. Você deve estar executando a CLI do Azure versão 2.0.27 ou posterior. Para identificar sua versão, execute `az --version`. Caso precise instalar ou atualizar a CLI do Azure, veja [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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

### <a name="enable-using-azure-policy"></a>Habilitar usando o Azure Policy
Para habilitar o Azure Monitor para máquinas virtuais em grande escala que garante a conformidade consistente e habilitação automática para novas máquinas virtuais provisionadas, recomenda-se a [Azure Policy](../azure-policy/azure-policy-introduction.md). Essas políticas:

* Implantar o agente do Log Analytics e o agente de dependência 
* Relatar os resultados de conformidade 
* Corrigir para máquinas virtuais sem conformidade

Habilitar o Azure Monitor para as máquinas virtuais por meio da política ao seu locatário requer: 

- Atribuir a iniciativa a um escopo – um grupo de gerenciamento, assinatura ou grupo de recursos 
- Revisão e correção dos resultados de conformidade  

Para obter mais informações sobre atribuição do Azure Policy, consulte [visão geral do Azure Policy](../governance/policy/overview.md#policy-assignment) e examine a [visão geral dos grupos de gerenciamento](../governance/management-groups/index.md) antes de continuar.  

A tabela a seguir lista as definições de política fornecidas.  

|NOME |DESCRIÇÃO |Tipo |  
|-----|------------|-----|  
|[Visualização]: habilitar o Azure Monitor para máquinas virtuais |Habilite o Azure Monitor para as Máquinas Virtuais (VMs) no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Usa o espaço de trabalho do Log Analytics como parâmetro. |Iniciativa |  
|[Visualização]: Implantação Microsoft Dependency Agent – Imagem da máquina virtual (SO) não listada |Máquinas virtuais de relatórios como não compatível se a imagem de máquina virtual (SO) não estiver na lista definida e o agente não estiver instalado. |Política |  
|[Visualização]: implantação do agente do Audit Log Analytics – imagem da máquina virtual (SO) não listada |Máquinas virtuais de relatórios como não compatível se a imagem de máquina virtual (SO) não estiver na lista definida e o agente não estiver instalado. |Política |  
|[Visualização]: implantar o Microsoft Dependency Agent para máquinas virtuais do Linux |Implante o Microsoft Dependency Agent para VMs do Linux, se a imagem da VM (SO) estiver na lista definida e o agente não estiver instalado. |Política |  
|[Visualização]: implantar o Microsoft Dependency Agent para VMs do Windows |Implante o Microsoft Dependency Agent para VMs do Windows se a imagem de VM (SO) estiver na lista definida e o agente não estiver instalado. |Política |  
|[Visualização]: implantar o agente do Log Analytics para VMs do Linux |Implante o agente do Log Analytics para VMs do Linux, se a imagem de VM (SO) estiver na lista definida e o agente não estiver instalado. |Política |  
|[Visualização]: implantar o agente do Log Analytics para VMs do Windows |Implante o Agente do Log Analytics para as máquinas virtuais do Windows se a imagem de máquina virtual (SO) estiver na lista definida e o agente não estiver instalado. |Política |  

Política autônoma (não incluída com a iniciativa) 

|NOME |DESCRIÇÃO |Tipo |  
|-----|------------|-----|  
|[Visualização]: auditar o espaço de trabalho do Log Analytics para a VM - Relatar incompatibilidade |Ralete as máquinas virtuais como não compatíveis se não fizerem logon no espaço de trabalho de LA especificada na atribuição de política/iniciativa. |Política |

#### <a name="assign-azure-monitor-initiative"></a>Atribuir iniciativa do Azure Monitor
Com essa versão inicial, você só pode criar a atribuição de política no portal do Azure. Para entender como concluir essas etapas, confira  [Criar uma atribuição de política no portal do Azure](../governance/policy/assign-policy-portal.md). 

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**. 
2. Selecione **Atribuições** no lado esquerdo da página de Política do Azure. Uma atribuição é uma política que foi atribuída para entrar em vigor em um escopo específico.
3. Selecione **Atribuir Iniciativa** na parte superior da página **Política - Atribuições**.
4. Na página **atribuir Iniciativa**, selecione o **Escopo** clicando no botão de reticências e selecione um grupo de gerenciamento ou assinatura e, opcionalmente, um grupo de recursos. Um escopo limita a atribuição de política, em nosso caso, a um agrupamento de máquinas virtuais para a imposição. Clique em **Selecionar** na parte inferior da página do **Escopo** para salvar suas alterações.
5. **Exclusões** permitem omitir um ou mais recursos do escopo, que é opcional. 
6. Selecione a **Definição de iniciativa** reticências para abrir a lista de definições disponíveis e selecione  **[Visualização]Ativar o Azure Monitor para VMs** na lista e, em seguida, clique em **Selecionar**.
7. O **Nome da atribuição** é automaticamente preenchido com o nome da iniciativa selecionada, mas você pode alterá-lo. Você também pode adicionar uma **Descrição**opcional. **Atribuído por** é preenchido automaticamente com base em quem está conectado, e esse campo é opcional.
8. Selecione um **espaço de trabalho do Log Analytics** na lista suspensa que está disponível na região com suporte.

    >[!NOTE]
    >Se o espaço de trabalho estiver fora do escopo da atribuição, você deve conceder ao **Colaborador do Log Analytics** permissões para a ID Principal de atribuição da política. Se você não fizer isso você poderá ver uma falha de implantação, como: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... ` Revisão [como configurar manualmente a identidade gerenciada](../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity) para conceder acesso.
    >

9. Observe se a opção **Identidade Gerenciada** está marcada. Isso é verificado quando a iniciativa que está sendo atribuída inclui uma política com o efeito deployIfNotExists. Na lista suspensa **Gerenciar Local de Identidade**, selecione a região apropriada.  
10. Clique em **Atribuir**.

#### <a name="review-and-remediate-the-compliance-results"></a>Revisar e corrigir os resultados de conformidade 

Você pode aprender como analisar os resultados de conformidade, lendo [identificar resultados de não conformidade](../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). Selecione **Conformidade** no lado esquerdo da página e localize a  **[Visualização] ativar o Azure Monitor para a iniciativa de máquinas virtuais** que não são compatíveis de acordo com a atribuição que você criou.

![Conformidade com a política para máquinas virtuais do Azure](./media/monitoring-vminsights-onboard/policy-view-compliance-01.png)

Com base nos resultados das políticas incluídas com a iniciativa, as máquinas virtuais são relatadas como não conformidade nos cenários a seguir:  
  
1. O Log Analytics ou o Microsoft Dependency Agent não está implantado.  
   Isso é típico para um escopo com máquinas virtuais existentes. Para resolvê-lo, [crie tarefas de correção](../governance/policy/how-to/remediate-resources.md) em uma política de não conformidade para implantar os agentes necessários.    
 
    - [Visualização]: Deploy Dependency Agent for Linux VMs   
    - [Visualização]: Deploy Dependency Agent for Windows VMs  
    - [Visualização]: Deploy Log Analytics Agent for Linux VMs  
    - [Visualização]: Deploy Log Analytics Agent for Windows VMs  

2. A imagem de VM (SO) não está na lista identificada na definição de política.  
   Os critérios da política de implantação incluem apenas as máquinas virtuais implantadas por meio de imagens de máquina virtual bem conhecidas do Azure. Verifique a documentação caso o sistema operacional da VM seja compatível ou não. Se não for, você precisa duplicar a política de implantação e atualização/modificá-lo para tornar a imagem em conformidade. 
  
    - [Visualização]: Implantação Microsoft Dependency Agent – Imagem da máquina virtual (SO) não listada  
    - [Visualização]: implantação do agente do Audit Log Analytics – imagem da máquina virtual (SO) não listada

3. VMs não efetuou logon no espaço de trabalho de LA especificado.  
É possível que algumas VMs no escopo de iniciativa estejam fazendo em um espaço de trabalho de LA diferente de uma vez especificada na atribuição de política. Essa política é uma ferramenta para identificar quais VMs estão relatando para um espaço de trabalho fora de conformidade.  
 
    - [Visualização]: Audit Log Analytics Workspace for VM - Report Mismatch  

### <a name="enable-with-powershell"></a>Habilitar com o PowerShell
Para habilitar o Azure Monitor para VMs para várias máquinas virtuais ou para conjuntos de dimensionamento de VMs, use um script do PowerShell fornecido – [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), disponível por meio da Galeria do PowerShell para concluir esta tarefa.  Esse script irá iterar através de cada máquina virtual e conjunto de dimensionamento de máquinas virtuais na sua assinatura, no grupo de recursos no escopo especificado pelo *ResourceGroup*, ou em uma máquina virtual ou em um conjunto de dimensionamento de máquinas virtuais por *Nome*.  Para cada máquina virtual ou conjunto de dimensionamento de VM, o script verifica se a extensão de VM já está instalada e, se ela não estiver, tentará reinstalá-la.  Caso contrário, ele continua para instalar as extensões de VM do Dependency Agent e do Log Analytics.   

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