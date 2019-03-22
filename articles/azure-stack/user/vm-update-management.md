---
title: VM atualização e gerenciamento com o Azure Stack | Microsoft Docs
description: Saiba como usar o Azure Monitor para máquinas virtuais, gerenciamento de atualizações, controle de alterações e soluções de inventário na automação do Azure para gerenciar o Windows e VMs do Linux que são implantados no Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: cb8258c0f837d0e70ba87a26246f055b0efe5c00
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316136"
---
# <a name="azure-stack-vm-update-and-management"></a>Atualização de VM de pilha e gerenciamento do Azure
Você pode usar os seguintes recursos de solução de automação do Azure para gerenciar o Windows e VMs do Linux que são implantados usando o Azure Stack:

- **[Gerenciamento de atualizações](https://docs.microsoft.com/azure/automation/automation-update-management)**. Com a solução de gerenciamento de atualizações, você pode rapidamente avaliar o status de atualizações disponíveis em todos os computadores de agente e gerenciar o processo de instalação de atualizações necessárias para essas VMs do Linux e Windows.

- **[O controle de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking)**. As alterações ao software instalado, serviços do Windows, do registro do Windows e arquivos e daemons do Linux nos servidores monitorados são enviadas para o serviço do Azure Monitor na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados. Usando as informações no painel Controle de Alterações, você pode ver facilmente as alterações feitas à sua infraestrutura de servidor.

- **[Estoque](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**. O acompanhamento de inventário para uma máquina de virtual do Azure Stack fornece uma interface de usuário baseada em navegador para instalar e configurar a coleta de inventário.

- **[O Azure Monitor para VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)**. O Azure Monitor para VMs monitora suas máquinas virtuais do Azure e o Azure Stack (VM) e conjuntos de dimensionamento de máquina virtual em grande escala. Ele analisa o desempenho e a integridade de suas VMs do Linux e Windows e monitora seus processos e dependências de outros recursos e processos externos. 

> [!IMPORTANT]
> Essas soluções são as mesmas que aquelas usadas para gerenciar VMs do Azure. Azure e máquinas virtuais do Azure Stack são gerenciados da mesma forma, usando a mesma interface, usando as mesmas ferramentas. As VMs do Azure Stack são também tem o mesmo preço VMs do Azure ao usar as soluções de gerenciamento de atualizações, controle de alterações, inventário e máquinas virtuais do Azure Monitor com o Azure Stack.

## <a name="prerequisites"></a>Pré-requisitos
Vários pré-requisitos devem ser atendidos antes de usar esses recursos para atualizar e gerenciar VMs do Azure Stack. Isso inclui as etapas que devem ser executadas no portal do Azure, bem como o portal de administração do Azure Stack.

### <a name="in-the-azure-portal"></a>No portal do Azure
Para usar o Azure Monitor para máquinas virtuais, inventário, controle de alterações e recursos de automação do Azure de gerenciamento de atualização para VMs do Azure Stack, primeiro você precisa habilitar essas soluções no Azure.

> [!TIP]
> Se você já tiver esses recursos habilitados para VMs do Azure, você pode usar suas credenciais do espaço de trabalho LogAnalytics já existentes. Se você já tiver um LogAnalytics WorkspaceID e a chave primária que você deseja usar, pule para [a próxima seção](./vm-update-management.md#in-the-azure-stack-administration-portal). Caso contrário, prossiga nesta seção para criar uma nova conta de automação e espaço de trabalho LogAnalytics.

A primeira etapa na habilitação dessas soluções é [criar um espaço de trabalho LogAnalytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) em sua assinatura do Azure. Um espaço de trabalho do Log Analytics é um ambiente de logs do Azure Monitor exclusivo com seu próprio repositório de dados, fontes de dados e soluções. Depois de criar um espaço de trabalho, observe a WorkspaceID e a chave. Para exibir essas informações, vá até a folha do espaço de trabalho, clique em **configurações avançadas**e examine o **ID do espaço de trabalho** e **chave primária** valores. 

Em seguida, você deve [criar uma conta de automação](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Uma conta de automação é um contêiner para os seus recursos de automação do Azure. Ele fornece uma maneira de separar seus ambientes ou organizar ainda mais seus fluxos de trabalho de automação e recursos. Depois de criar a conta de automação, você precisa para habilitar o inventário, o controle de alterações e atualizar recursos de gerenciamento. Para fazer isso, siga estas etapas para habilitar cada recurso:

1. No portal do Azure, vá para a conta de automação que você deseja usar.

2. Selecione a solução para habilitar (qualquer um dos **estoque**, **o controle de alterações**, ou **gerenciamento de atualizações**).

3. Use o **selecionar espaço de trabalho...**  lista suspensa para selecionar o espaço de trabalho do Log Analytics para usar.

4. Verifique se todas as informações restantes estão corretas e, em seguida, clique em **habilitar** para habilitar a solução.

5. Repita as etapas 2 a 4 para habilitar todas as três soluções. 

   [![](media/vm-update-management/1-sm.PNG "Habilitar recursos de conta de automação")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>Habilitar o Azure Monitor para VMs

O Azure Monitor para VMs monitora as VMs (máquinas virtuais) e os conjuntos de dimensionamento de máquinas virtuais do Azure em escala. Ele analisa o desempenho e a integridade de suas VMs do Linux e Windows e monitora seus processos e dependências de outros recursos e processos externos.

Como solução, o Azure Monitor para VMs inclui suporte para monitorar o desempenho e as dependências de aplicativos das VMs hospedadas localmente ou em outro provedor de nuvem. Três principais recursos fornecem insights detalhados:

1. Componentes lógicos de VMs do Azure que executam o Windows e Linux: São medidos em relação aos critérios de integridade pré-configurados e alertam quando a condição avaliada é atendida. 

2. Gráficos de desempenho mais populares predefinidos: Exibem métricas básicas de desempenho no sistema operacional da VM convidada.

3. Mapa de dependências: Exibe os componentes interconectados com a VM de vários grupos de recursos e assinaturas.

Depois que o espaço de trabalho do Log Analytics é criado, você precisará habilitar os contadores de desempenho no espaço de trabalho para a coleção em VMs do Linux e Windows, bem como instalar e habilitar a solução do ServiceMap e InfrastructureInsights no espaço de trabalho. O processo é descrito na [implantar o Azure Monitor para VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#deploy-azure-monitor-for-vms) guia.

### <a name="in-the-azure-stack-administration-portal"></a>No Portal de administração do Azure Stack
Depois de habilitar as soluções de automação do Azure no portal do Azure, em seguida você precisa entrar no portal de administração do Azure Stack como um administrador de nuvem e baixe o **Azure Monitor, atualização e gerenciamento de configuração** e o **Azure Monitor, atualização e gerenciamento de configuração para o Linux** itens do marketplace extensão do Azure Stack. 

   ![Azure Monitor, atualização e configuração de gerenciamento extensão item do marketplace](media/vm-update-management/2.PNG) 

Para habilitar o Azure Monitor para a solução do mapa de VMs e obtenha percepções sobre as dependências de rede, você precisará também baixar o **agente de dependência do Azure Monitor**:

   ![Azure Monitor Dependency Agent](media/vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Habilitar o gerenciamento de atualizações para máquinas virtuais do Azure Stack
Siga estas etapas para habilitar o gerenciamento de atualizações para VMs do Azure Stack.

1. Faça logon no portal do usuário do Azure Stack.

2. No Azure Stack-portal do usuário, vá até a folha de extensões das máquinas virtuais para o qual você deseja habilitar essas soluções, clique em **+ adicionar**, selecione o **atualização do Azure e o gerenciamento de configuração** extensão e clique em **criar**:

   [![](media/vm-update-management/3-sm.PNG "Folha de extensão VM")](media/vm-update-management/3-lg.PNG#lightbox)

3. Forneça o WorkspaceID e a chave primária criada anteriormente para vincular o agente com o espaço de trabalho LogAnalytics e clique em **Okey** para implantar a extensão.

   [![](media/vm-update-management/4-sm.PNG "Fornecendo a WorkspaceID e a chave")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Conforme descrito na [documentação do gerenciamento de atualização de automação](https://docs.microsoft.com/azure/automation/automation-update-management), você precisa habilitar a solução de gerenciamento de atualizações para cada VM que você deseja gerenciar. Para habilitar a solução para todas as VMs de emissão de relatórios no espaço de trabalho, selecione **gerenciamento de atualizações**, clique em **gerenciar máquinas**e, em seguida, selecione o **habilitar em todos os computadores disponíveis e futuros** opção.

   [![](media/vm-update-management/5-sm.PNG "Fornecendo a WorkspaceID e a chave")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Repita essa etapa para habilitar cada solução para as VMs de pilha do Azure que se reportam a espaço de trabalho. 
  
Depois que a extensão de atualização do Azure e o gerenciamento de configuração está habilitada, uma verificação é executada duas vezes por dia para cada VM gerenciada. A API é chamada para consultar a hora da última atualização determinar se o status foi alterado a cada 15 minutos. Se o status foi alterado, uma verificação de conformidade é iniciada.

Depois que as VMs são verificadas, eles aparecerão na conta de automação do Azure na solução de gerenciamento de atualização: 

   [![](media/vm-update-management/6-sm.PNG "Fornecendo a WorkspaceID e a chave")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> Pode demorar entre 30 minutos e 6 horas para o painel exibir os dados atualizados dos computadores gerenciados.

As VMs do Azure Stack agora podem ser incluídas em implantações de atualização agendada junto com VMs do Azure.

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack"></a>Habilitar o Azure Monitor para VMs em execução no Azure Stack
Depois que a VM tem o **Azure Monitor, atualização e gerenciamento de configuração** e o **agente de dependência do Azure Monitor** extensões instaladas, ele começará a reportar dados no [Azure Monitor para VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) solução. 

> [!TIP]
> O **agente de dependência do Azure Monitor** extensão não requer nenhum parâmetro. O Dependency Agent de Mapa do Azure Monitor para VMs não transmite dados por conta própria e não exige alterações em firewalls ou portas. Os dados do Mapa sempre são transmitidos pelo agente do Log Analytics para o serviço Azure Monitor, diretamente ou por meio do [Gateway do OMS](https://docs.microsoft.com/azure/azure-monitor/platform/gateway), caso as políticas de segurança de TI não permitam que os computadores na rede se conectem à Internet.

O Monitor do Azure para VMs inclui um conjunto de gráficos de desempenho que segmentam vários KPIs (principais indicadores de desempenho) para ajudá-lo a determinar o desempenho de uma máquina virtual. Os gráficos mostram a utilização de recursos durante um período de tempo para que você possa identificar afunilamentos, anomalias ou alternar para uma perspectiva listando cada máquina para exibir a utilização de recursos com base na métrica selecionada. Embora haja vários elementos a serem considerados ao lidar com o desempenho, Monitor do Azure para VMs indicadores de desempenho de chave do sistema operacional de monitores relacionados ao processador, memória, adaptador de rede e utilização de disco. O desempenho complementa o recurso de monitoramento de integridade e ajuda a expor problemas que indicam uma possível falha do componente do sistema, suporte ao ajuste e otimização para obter eficiência ou suportar o planejamento da capacidade.

   ![Guia do Monitor de desempenho do Azure](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

Exibir os componentes de aplicativos descobertos em máquinas virtuais Windows e Linux, em execução no Azure Stack pode ser observado de duas maneiras com o Azure Monitor para VMs, de uma máquina virtual diretamente ou entre os grupos de VMs do Azure Monitor.
O [usando o Azure Monitor para máquinas virtuais (versão prévia) são mapeados para entender os componentes do aplicativo](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps) artigo o ajudará a entender a experiência entre duas perspectivas e como usar o recurso de mapa.

   ![Guia do Monitor de desempenho do Azure](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)


## <a name="enable-update-management-using-a-resource-manager-template"></a>Habilitar o gerenciamento de atualização usando um modelo do Resource Manager
Se você tiver um grande número de VMs do Azure Stack, você pode usar [este modelo do Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) para implantar mais facilmente a solução em máquinas virtuais. O modelo implanta a extensão Microsoft Monitoring Agent em uma VM de pilha do Azure existente e adiciona-o para um espaço de trabalho existente do Azure LogAnalytics.
 
## <a name="next-steps"></a>Próximas etapas
[Otimizar o desempenho de VM do SQL Server](azure-stack-sql-server-vm-considerations.md)




