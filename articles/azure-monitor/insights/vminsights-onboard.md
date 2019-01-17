---
title: Implantar o Azure Monitor para VMs (versão prévia) | Microsoft Docs
description: Este artigo descreve como implantar e configurar o Azure Monitor para VMs, de modo que você possa começar a entender mais sobre o desempenho do aplicativo distribuído e quais problemas de integridade foram identificados.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/07/2018
ms.author: magoedte
ms.openlocfilehash: 3c1caa2485437768781ada2c7271445ccd3c19e1
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190316"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>Implantar o Azure Monitor para VMs (versão prévia)
Este artigo descreve como configurar o Azure Monitor para VMs. O serviço monitora a integridade do sistema operacional de VMs (máquinas virtuais) do Azure e de conjuntos de dimensionamento de máquinas virtuais, bem como as máquinas virtuais no ambiente. Esse monitoramento inclui a descoberta e o mapeamento de dependências de aplicativo que podem ser hospedadas nelas. 

Habilite o Azure Monitor para VMs usando um dos seguintes métodos:

* Habilite uma máquina virtual individual do Azure selecionando **Insights (versão prévia)** diretamente na VM.
* Habilite duas ou mais VMs do Azure usando o Azure Policy. Por meio desse método, as dependências necessárias das VMs novas e existentes são instaladas e configuradas corretamente. As VMs fora de conformidade são relatadas, para que você possa decidir se deseja habilitá-las e como deseja corrigi-las.
* Habilite duas ou mais VMs do Azure ou conjuntos de dimensionamento de máquinas virtuais em uma assinatura ou um grupo de recursos especificado usando o PowerShell.

Mais informações sobre cada método são fornecidas mais adiante neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, verifique se você compreende as informações descritas nas seções a seguir.

### <a name="log-analytics"></a>Log Analytics

Atualmente, há suporte para um workspace do Log Analytics nas seguintes regiões:

- Centro-Oeste dos EUA
- Leste dos EUA
- Europa Ocidental
- Sudeste Asiático<sup>1</sup>

<sup>1</sup> Atualmente, essa região não dá suporte ao recurso Integridade do Azure Monitor para VMs.

>[!NOTE]
>As máquinas virtuais do Azure podem ser implantadas de qualquer região e não são limitadas para as regiões com suporte para o workspace do Log Analytics.
>

Caso não tenha um workspace, crie um com um dos seguintes métodos:
* [A CLI do Azure](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [O portal do Azure](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Se estiver habilitando o monitoramento para uma VM individual do Azure no portal do Azure, você poderá criar um workspace durante esse processo.

Para habilitar a solução para o cenário em escala, primeiro configure o seguinte no workspace do Log Analytics:

* Instale as soluções ServiceMap e InfrastructureInsights. Conclua essa instalação usando um modelo do Azure Resource Manager que é fornecido neste artigo.
* Configure o espaço de trabalho do Log Analytics para coletar contadores de desempenho.

Para configurar o workspace no cenário em escala, confira [Configurar o workspace do Log Analytics para uma implantação em escala](#setup-log-analytics-workspace).

### <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

A tabela a seguir lista os sistemas operacionais Windows e Linux compatíveis com o Azure Monitor para VMs. Uma lista completa que fornece detalhes das versões principais e secundárias do kernel com suporte e de lançamento do sistema operacional Linux é fornecida mais adiante nesta seção.

|Versão do SO |Desempenho |Mapas |Integridade |
|-----------|------------|-----|-------|
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 7, 6| X | X| X |
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |
|CentOS Linux 7, 6 | X | X | X |
|SLES (SUSE Linux Enterprise Server) 12 | X | X | X |
|Oracle Linux 7 | X<sup>1</sup> | | X |
|Oracle Linux 6 | X | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | | X |

<sup>1</sup> O recurso Desempenho do Azure Monitor para VMs está disponível somente no Azure Monitor. Ele não está disponível quando você o acessa diretamente no painel esquerdo da VM do Azure.

>[!NOTE]
>As informações a seguir aplicam-se ao suporte do sistema operacional Linux:
> - Somente as versões de kernel padrão e Linux SMP têm suporte.
> - Não há suporte para nenhuma distribuição do Linux em versões de kernel não padrão, como PAE (Extensão do Endereço Físico) e Xen. Por exemplo, não há suporte para um sistema com a cadeia de caracteres de versão *2.6.16.21-0.8-xen*.
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

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent
O recurso Mapa do Azure Monitor para VMs obtém seus dados do Microsoft Dependency Agent. O Dependency Agent depende do agente do Log Analytics para suas conexões com o Log Analytics. Portanto, o sistema precisa ter o agente do Log Analytics instalado e configurado com o Dependency Agent.

Se você habilitar o Azure Monitor para VMs para uma VM individual do Azure ou usar o método de implantação em escala, você precisará usar a extensão Dependency Agent da VM do Azure para instalar o agente como parte da experiência.

Em um ambiente híbrido, baixe e instale o Dependency Agent de uma dessas duas maneiras: Manualmente ou usando um método de implantação automatizado para máquinas virtuais hospedadas fora do Azure.

A tabela a seguir descreve as fontes conectadas para as quais o recurso Mapa dá suporte em um ambiente híbrido.

| Fonte conectada | Suportado | Descrição |
|:--|:--|:--|
| Agentes do Windows | SIM | Além do [Agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md), os agentes do Windows exigem o Microsoft Dependency Agent. Para obter uma lista completa das versões de sistema operacional, confira os [sistemas operacionais compatíveis](#supported-operating-systems). |
| Agentes do Linux | SIM | Além do [Agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md), os agentes do Linux exigem o Microsoft Dependency Agent. Para obter uma lista completa das versões de sistema operacional, confira os [sistemas operacionais compatíveis](#supported-operating-systems). |
| Grupo de gerenciamento do System Center Operations Manager | Não  | |

O Dependency Agent pode ser baixado nos seguintes locais:

| Arquivo | SO | Versão | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) |  Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="role-based-access-control"></a>Controle de acesso baseado em função
Para habilitar e acessar os recursos do Azure Monitor para VMs, você precisará receber as seguintes funções de acesso:

- Para habilitar a solução, você precisará ter a função *colaborador do Log Analytics*.

- Para exibir os dados de desempenho, integridade e mapa, você precisará ter a função *Leitor de Monitoramento* na VM do Azure. O workspace do Log Analytics precisa ser configurado para o Azure Monitor para VMs.

Para obter mais informações sobre como controlar o acesso a um workspace do Log Analytics, veja [Gerenciar workspaces](../../azure-monitor/platform/manage-access.md).

## <a name="enable-monitoring-in-the-azure-portal"></a>Habilitar o monitoramento no portal do Azure
Para habilitar o monitoramento da VM do Azure no portal do Azure, faça o seguinte:

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Selecione **Máquinas Virtuais do Microsoft Azure**.

1. Na lista, selecione uma VM.

1. Na página da VM, na seção **Monitoramento**, selecione **Insights (versão prévia)**.

1. Na página **Insights (versão prévia)**, selecione **Experimentar agora**.

    ![Habilitar o Azure Monitor para VMs em uma VM](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)
1. Na página **Integração de Insights do Azure Monitor**, caso você tenha um workspace existente do Log Analytics na mesma assinatura, selecione-o na lista suspensa.  
    A lista pré-seleciona o workspace e o local padrão nos quais a máquina virtual é implantada na assinatura. 

    >[!NOTE]
    >Caso deseje criar um workspace do Log Analytics para armazenar os dados de monitoramento da VM, siga as instruções em [Criar um workspace do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) em uma das regiões com suporte já listadas.

Depois de habilitar o monitoramento, poderão ser necessários cerca de 10 minutos antes da exibição das métricas de integridade para a máquina virtual.

![Habilitar o Azure Monitor para o processamento da implantação do monitoramento das VMs](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="deploy-at-scale"></a>Implantar em escala
Nesta seção, você implanta o Azure Monitor para VMs em escala usando o Azure Policy ou o Azure PowerShell.

Antes de implantar as VMs, pré-configure o workspace do Log Analytics fazendo o seguinte:

1. Caso ainda não tenha um workspace, crie um que possa dar suporte ao Azure Monitor para VMs.  
    Antes de continuar, confira [Gerenciar workspaces](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json) para entender as considerações sobre custo, gerenciamento e conformidade.

1. Crie um novo espaço de trabalho, se ainda não houver um que possa ser usado para dar suporte ao Azure Monitor para VMs. Antes de continuar, revise previamente [Gerenciar espaços de trabalho](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json) antes de criar um novo espaço de trabalho para reconhecer as considerações de custo, gerenciamento e conformidade.

1. Habilite os contadores de desempenho no espaço de trabalho para coleta em VMs do Windows e Linux.

1. Instale e habilite as soluções ServiceMap e InfrastructureInsights no workspace.

### <a name="set-up-a-log-analytics-workspace"></a>Configurar um workspace do Log Analytics
Caso não tenha um workspace do Log Analytics, crie um examinando os métodos sugeridos na seção ["Pré-requisitos"](#log-analytics).

#### <a name="enable-performance-counters"></a>Habilitar contadores de desempenho
Se o workspace do Log Analytics referenciado pela solução ainda não estiver configurado para coletar os contadores de desempenho necessários para a solução, você precisará habilitá-los. Faça isso de umas dessas duas maneiras:
* Manualmente, conforme descrito em [Fontes de dados de desempenho do Windows e do Linux no Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Baixando e executando um script do PowerShell disponível na [Galeria do Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Instale as soluções ServiceMap e InfrastructureInsights
Esse método inclui um modelo JSON que especifica a configuração para habilitar os componentes da solução no workspace do Log Analytics.

Caso não esteja familiarizado com a implantação de recursos usando um modelo, confira:
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

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
    }
    ```

1. Salve esse arquivo como *installsolutionsforvminsights.json* em uma pasta local.

1. Edite os valores de *WorkspaceName*, *ResourceGroupName* e *WorkspaceLocation*. O valor de *WorkspaceName* é a ID completa do recurso do workspace do Log Analytics, que inclui o nome do workspace. O valor de *WorkspaceLocation* é a região na qual o workspace foi definido.

1. Você está pronto para implantar esse modelo usando o seguinte comando do PowerShell:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

    ```powershell
    provisioningState       : Succeeded
    ```

### <a name="enable-by-using-azure-policy"></a>Habilitar usando o Azure Policy
Para habilitar o Azure Monitor para VMs em escala de forma que ajude a garantir a conformidade consistente e a habilitação automática de VMs recém-provisionados, recomendamos [Azure Policy](../../azure-policy/azure-policy-introduction.md). Essas políticas:

* Implantam o agente do Log Analytics e o Dependency Agent.
* Relatam os resultados da conformidade.
* Corrigem VMs fora de conformidade.

Para habilitar o Azure Monitor para VMs usando o Azure Policy no locatário:

- Atribua a iniciativa a um escopo: grupo de gerenciamento, assinatura ou grupo de recursos
- Examinar e corrigir os resultados de conformidade

Para obter mais informações sobre como atribuir o Azure Policy, confira [Visão geral do Azure Policy](../../governance/policy/overview.md#policy-assignment) e examine a [Visão geral dos grupos de gerenciamento](../../governance/management-groups/index.md) antes de continuar.

As definições de política são listadas na seguinte tabela:

|NOME |DESCRIÇÃO |Tipo |
|-----|------------|-----|
|[Visualização]: Habilitar o Azure Monitor para VMs |Habilite o Azure Monitor para as VMs (Máquinas Virtuais) no escopo especificado (grupo de gerenciamento, assinatura ou grupo de recursos). Usa o workspace do Log Analytics como parâmetro. |Iniciativa |
|[Visualização]: Auditar a Implantação do Dependency Agent – Imagem de VM (sistema operacional) não listada |Relata as VMs como fora de conformidade se a Imagem de VM (sistema operacional) não está definida na lista e o agente não está instalado. |Política |
|[Visualização]: Auditar a Implantação do Agente do Log Analytics – Imagem de VM (sistema operacional) não listada |Relata as VMs como fora de conformidade se a Imagem de VM (sistema operacional) não está definida na lista e o agente não está instalado. |Política |
|[Visualização]: Implantar o Dependency Agent para VMs do Linux |Implante o Dependency Agent para VMs do Linux se a Imagem de VM (sistema operacional) estiver definida na lista e o agente não estiver instalado. |Política |
|[Visualização]: Implantar o Dependency Agent para VMs do Windows |Implante o Dependency Agent para VMs do Windows se a Imagem de VM (sistema operacional) estiver definida na lista e o agente não estiver instalado. |Política |
|[Visualização]: Implantar o Agente do Log Analytics para VMs do Linux |Implante o Agente do Log Analytics para VMs do Linux se a Imagem de VM (sistema operacional) estiver definida na lista e o agente não estiver instalado. |Política |
|[Visualização]: Implantar o Agente do Log Analytics para VMs do Windows |Implante o Agente do Log Analytics para VMs do Windows se a Imagem de VM (sistema operacional) estiver definida na lista e o agente não estiver instalado. |Política |

A política independente (não incluída com a iniciativa) é descrita aqui:

|NOME |DESCRIÇÃO |Tipo |
|-----|------------|-----|
|[Visualização]: Auditar o workspace do Log Analytics para a VM – Relatar incompatibilidade |Relate as VMs como fora de conformidade se elas não estiverem fazendo logon no workspace do Log Analytics especificado na atribuição de política/iniciativa. |Política |

#### <a name="assign-the-azure-monitor-initiative"></a>Atribuir a iniciativa do Azure Monitor
Com essa versão inicial, você só pode criar a atribuição de política no portal do Azure. Para entender como concluir essas etapas, confira  [Criar uma atribuição de política no portal do Azure](../../governance/policy/assign-policy-portal.md).

1. Para iniciar o serviço Azure Policy no portal do Azure, selecione **Todos os serviços** e, em seguida, pesquise e selecione **Política**.

1. No painel esquerdo da página Azure Policy, selecione **Atribuições**.  
    Uma atribuição é uma política que foi atribuída para entrar em vigor em um escopo específico.
    
1. Na parte superior da página **Política – Atribuições**, selecione **Atribuir Iniciativa**.

1. Na página **Atribuir Iniciativa**, selecione o **Escopo** clicando nas reticências (...) e selecione uma assinatura ou um grupo de gerenciamento.  
    Em nosso exemplo, um escopo limita a atribuição de política a um agrupamento de máquinas virtuais para imposição.
    
1. Na parte inferior da página **Escopo**, salve as alterações selecionando **Selecionar**.

1. (Opcional) Para remover um ou mais recursos do escopo, selecione **Exclusões**.

1. Selecione as reticências (...) **Definição de iniciativa** para exibir a lista das definições disponíveis, selecione **[Visualização] Habilitar o Azure Monitor para VMs** e, em seguida, escolha **Selecionar**.  
    A caixa **Nome da atribuição** é populado automaticamente com o nome da iniciativa selecionada, mas você pode alterá-lo. Adicione também uma descrição opcional. A caixa **Atribuído por** é populada automaticamente com base em quem está conectado e esse valor é opcional.
    
1. Na lista suspensa **Workspace do Log Analytics** da região com suporte, selecione um workspace.

    >[!NOTE]
    >Se o workspace estiver fora do escopo da atribuição, conceda as permissões *Colaborador do Log Analytics* à ID de Entidade de Segurança da atribuição de política. Se você não fizer isso, poderá ver uma falha de implantação, como: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... ` Para conceder acesso, examine [Como configurar manualmente a identidade gerenciada](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
    >  
    A caixa de seleção **Identidade Gerenciada** está marcada, pois a iniciativa que está sendo atribuída inclui uma política com o efeito *deployIfNotExists*.
    
1. Na lista suspensa **Gerenciar localização de identidade**, selecione a região apropriada.

1. Selecione **Atribuir**.

#### <a name="review-and-remediate-the-compliance-results"></a>Revisar e corrigir os resultados de conformidade

Você pode aprender como analisar os resultados de conformidade, lendo [identificar resultados de não conformidade](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). No painel esquerdo, selecione **Conformidade** e, em seguida, localize a iniciativa **[Visualização] Habilitar o Azure Monitor para VMs** para as VMs que estão fora de conformidade, de acordo com a atribuição criada.

![Conformidade com a política para máquinas virtuais do Azure](./media/vminsights-onboard/policy-view-compliance-01.png)

Com base nos resultados das políticas incluídas com a iniciativa, as VMs são relatadas como fora de conformidade nos seguintes cenários:

* O Log Analytics ou o Dependency Agent não está implantado.  
    Esse cenário é típico para um escopo com as VMs existentes. Para atenuar isso, implante os agentes necessários [criando tarefas de correção](../../governance/policy/how-to/remediate-resources.md) em uma política de não conformidade.  
    - [Visualização]: Deploy Dependency Agent for Linux VMs
    - [Visualização]: Deploy Dependency Agent for Windows VMs
    - [Visualização]: Deploy Log Analytics Agent for Linux VMs
    - [Visualização]: Deploy Log Analytics Agent for Windows VMs

* A Imagem de VM (sistema operacional) não está identificada na definição de política.  
    Os critérios da política de implantação incluem apenas as VMs implantadas com base em imagens de VM conhecidas do Azure. Verifique a documentação para ver se o sistema operacional da VM é compatível. Caso ele não seja, duplique a política de implantação e atualize-a ou modifique-a para fazer com que a imagem fique em conformidade.  
    - [Visualização]: Auditar a Implantação do Dependency Agent – Imagem de VM (sistema operacional) não listada
    - [Visualização]: Auditar a Implantação do Agente do Log Analytics – Imagem de VM (sistema operacional) não listada

* As VMs não estão fazendo logon no workspace do Log Analytics especificado.  
    É possível que algumas VMs no escopo da iniciativa estejam fazendo logon em um workspace do Log Analytics diferente daquele especificado na atribuição de política. Essa política é uma ferramenta para identificar quais VMs estão relatando para um espaço de trabalho fora de conformidade.  
    - [Visualização]: Audit Log Analytics Workspace for VM - Report Mismatch

### <a name="enable-with-powershell"></a>Habilitar com o PowerShell
Para habilitar o Azure Monitor para VMs em várias VMs ou em conjuntos de dimensionamento de máquinas virtuais, use o script do PowerShell [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), disponível na Galeria do Azure PowerShell. Esse script itera por cada máquina virtual e conjunto de dimensionamento de máquinas virtuais na assinatura, no grupo de recursos com escopo especificado pelo *ResourceGroup*, ou em uma VM individual ou um conjunto de dimensionamento de máquinas virtuais especificado pelo *Name*. Para cada VM ou conjunto de dimensionamento de máquinas virtuais, o script verifica se a extensão de VM já está instalada. Se a extensão de VM não estiver instalada, o script tentará reinstalá-la. Se a extensão de VM estiver instalada, o script instalará as extensões de VM do Log Analytics e do Dependency Agent.

Este script exige o módulo do Azure PowerShell, versão 5.7.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.

Para obter uma lista de detalhes de argumento do script e o uso de exemplo, execute `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
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
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
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

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

O exemplo a seguir demonstra como usar os comandos do PowerShell na pasta para habilitar o Azure Monitor para VMs e entender o resultado esperado:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

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

## <a name="enable-for-a-hybrid-environment"></a>Habilitar para o ambiente híbrido
Esta seção explica como implantar máquinas virtuais ou computadores físicos hospedados no datacenter ou em outros ambientes de nuvem para o monitoramento pelo Azure Monitor para VMs.

O Dependency Agent de Mapa do Azure Monitor para VMs não transmite dados por conta própria e não exige alterações em firewalls ou portas. Os dados do Mapa sempre são transmitidos pelo agente do Log Analytics para o serviço Azure Monitor, diretamente ou por meio do [Gateway do OMS](../../azure-monitor/platform/gateway.md), caso as políticas de segurança de TI não permitam que os computadores na rede se conectem à Internet.

Examine os requisitos e os métodos de implantação do [agente do Linux e do Windows do Log Analytics](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

As etapas são resumidas da seguinte maneira:

1. Instalar o Agente do Log Analytics para Windows ou Linux.

1. Baixar e instalar o Dependency Agent de Mapa do Azure Monitor para VMs para [Windows](https://aka.ms/dependencyagentwindows) ou [Linux](https://aka.ms/dependencyagentlinux).

1. Habilitar a coleta de contadores de desempenho.

1. Implantar o Azure Monitor para VMs.

### <a name="install-the-dependency-agent-on-windows"></a>Instalar o Dependency Agent no Windows
Instale o Dependency Agent manualmente em computadores Windows executando `InstallDependencyAgent-Windows.exe`. Se você executar esse arquivo executável sem opções, ele iniciará um assistente de instalação que você poderá seguir para instalar o agente de forma interativa.

>[!NOTE]
>São necessários privilégios de *Administrador* para instalar ou desinstalar o agente.

A tabela a seguir destaca os parâmetros compatíveis com a instalação do agente por meio da linha de comando.

| Parâmetro | DESCRIÇÃO |
|:--|:--|
| /? | Retorna uma lista das opções de linha de comando. |
| /S | Realiza uma instalação silenciosa sem a interação do usuário. |

Por exemplo, para executar o programa de instalação com o parâmetro `/?`, digite **InstallDependencyAgent-Windows.exe /?**.

Os arquivos do Dependency Agent do Windows são instalados em *C:\Arquivos de Programas\Microsoft Dependency Agent* por padrão. Se o Dependency Agent não for iniciado após a conclusão da instalação, verifique os logs para obter informações de erro detalhadas. O diretório de log é *%Programfiles%\Microsoft Dependency Agent\logs*.

### <a name="install-the-dependency-agent-on-linux"></a>Instale o Agente de Dependência no Linux
O Dependency Agent é instalado em servidores Linux por meio de *InstallDependencyAgent-Linux64.bin*, um script de shell com um binário de extração automática. Você pode executar o arquivo usando `sh` ou adicionar permissões de execução ao próprio arquivo.

>[!NOTE]
> O acesso root é necessário para instalar ou configurar o agente.
>

| Parâmetro | DESCRIÇÃO |
|:--|:--|
| -help | Obtenha uma lista das opções de linha de comando. |
| -s | Realize uma instalação silenciosa sem solicitações ao usuário. |
| --check | Verifica as permissões e o sistema operacional, mas não instala o agente. |

Por exemplo, para executar o programa de instalação com o parâmetro `-help`, digite **InstallDependencyAgent-Linux64.bin -help**.

Instale o Dependency Agent do Linux como raiz executando o comando `sh InstallDependencyAgent-Linux64.bin`.

Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Em agentes do Linux, o diretório de log é */var/opt/microsoft/dependency-agent/log*.

Os arquivos do Agente de Dependência são colocados nos diretórios a seguir:

| Arquivos | Local padrão |
|:--|:--|
| Arquivos de núcleo | /opt/microsoft/dependency-agent |
| Arquivos de log | /var/opt/microsoft/dependency-agent/log |
| Arquivos de configuração | /etc/opt/microsoft/dependency-agent/config |
| Arquivos executáveis de serviço | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Arquivos de armazenamento binário | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Habilitar contadores de desempenho
Se o workspace do Log Analytics referenciado pela solução ainda não estiver configurado para coletar os contadores de desempenho necessários para a solução, você precisará habilitá-los. Faça isso de umas dessas duas maneiras:
* Manualmente, conforme descrito em [Fontes de dados de desempenho do Windows e do Linux no Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Baixando e executando um script do PowerShell disponível na [Galeria do Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="deploy-azure-monitor-for-vms"></a>Implantar o Azure Monitor para VMs
Esse método inclui um modelo JSON que especifica a configuração para habilitar os componentes da solução no workspace do Log Analytics.

Caso não esteja familiarizado com a implantação de recursos usando um modelo, confira:
* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

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
    }
    ```

1. Salve esse arquivo como *installsolutionsforvminsights.json* em uma pasta local.

1. Edite os valores de *WorkspaceName*, *ResourceGroupName* e *WorkspaceLocation*. O valor de *WorkspaceName* é a ID completa do recurso do workspace do Log Analytics, que inclui o nome do workspace. O valor de *WorkspaceLocation* é a região na qual o workspace foi definido.

1. Você está pronto para implantar esse modelo usando o seguinte comando do PowerShell:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A alteração da configuração pode levar alguns minutos para ser concluída. Quando ela for concluída, será exibida uma mensagem semelhante à seguinte e incluindo o resultado:

    ```powershell
    provisioningState       : Succeeded
    ```
Depois de habilitar o monitoramento, poderão ser necessários cerca de 10 minutos antes da exibição do estado de integridade e das métricas para o computador híbrido.

## <a name="performance-counters-enabled"></a>Contadores de desempenho habilitados
O Azure Monitor para VMs configura um workspace do Log Analytics para coletar os contadores de desempenho usados pela solução. A tabela a seguir lista os objetos e os contadores configurados pela solução que são coletados a cada 60 segundos.

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
|LogicalDisk | Bytes Gravados no Disco/s |
|LogicalDisk | Gravações de Disco/s |
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
|Disco Lógico | Bytes Gravados no Disco/s |
|Disco Lógico | Gravações de Disco/s |
|Disco Lógico |Megabytes Livres |
|Disco Lógico |Bytes de Disco Lógico/s |
|Memória |MBytes de Memória Disponíveis |
|Rede |Total de Bytes Recebidos |
|Rede |Total de Bytes Transmitidos |
|Processador |% Tempo do Processador |

## <a name="diagnostic-and-usage-data"></a>Dados de uso e de diagnóstico
A Microsoft coleta automaticamente dados de uso e de desempenho por meio do uso do serviço Azure Monitor. A Microsoft utiliza esses dados para fornecer e aprimorar a qualidade, a segurança e a integridade do serviço. Para fornecer funcionalidades precisas e eficientes de solução de problemas, os dados do recurso Mapa incluem informações sobre a configuração de seu software, como sistema operacional e versão, endereço IP, nome DNS e nome da estação de trabalho. A Microsoft não coleta nomes, endereços nem outras informações de contato.

Para obter mais informações sobre a coleta e uso de dados, consulte a [Política de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]
## <a name="next-steps"></a>Próximas etapas

Agora que o monitoramento está habilitado para a máquina virtual, essas informações ficarão disponíveis para análise com o Azure Monitor para VMs. Para saber como usar o recurso Integridade, confira [Exibir a Integridade do Azure Monitor para VMs](vminsights-health.md). Para exibir as dependências de aplicativos descobertas, confira [Exibir o Mapa do Azure Monitor para VMs](vminsights-maps.md).
