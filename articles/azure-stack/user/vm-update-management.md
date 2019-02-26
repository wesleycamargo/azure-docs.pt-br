---
title: VM atualização e gerenciamento com o Azure Stack | Microsoft Docs
description: Saiba como usar as soluções de gerenciamento de atualizações, controle de alterações e inventário na automação do Azure para gerenciar o Windows e VMs do Linux que são implantados no Azure Stack.
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
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 4683b6f63af9fe0081911db9914f04b1c90f9d23
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819438"
---
# <a name="azure-stack-vm-update-and-management"></a>Atualização de VM de pilha e gerenciamento do Azure
Você pode usar os seguintes recursos de solução de automação do Azure para gerenciar o Windows e VMs do Linux que são implantados usando o Azure Stack:

- **[Gerenciamento de atualizações](https://docs.microsoft.com/azure/automation/automation-update-management)**. Com a solução de gerenciamento de atualizações, você pode rapidamente avaliar o status de atualizações disponíveis em todos os computadores de agente e gerenciar o processo de instalação de atualizações necessárias para essas VMs do Linux e Windows.

- **[O controle de alterações](https://docs.microsoft.com/azure/automation/automation-change-tracking)**. As alterações ao software instalado, serviços do Windows, do registro do Windows e arquivos e daemons do Linux nos servidores monitorados são enviadas para o serviço do Azure Monitor na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados. Usando as informações no painel Controle de Alterações, você pode ver facilmente as alterações feitas à sua infraestrutura de servidor.

- **[Estoque](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**. O acompanhamento de inventário para uma máquina de virtual do Azure Stack fornece uma interface de usuário baseada em navegador para instalar e configurar a coleta de inventário. 

> [!IMPORTANT]
> Essas soluções são as mesmas que aquelas usadas para gerenciar VMs do Azure. Azure e máquinas virtuais do Azure Stack são gerenciados da mesma forma, usando a mesma interface, usando as mesmas ferramentas. As VMs do Azure Stack são também tem o mesmo preço VMs do Azure ao usar o gerenciamento de atualizações, controle de alterações e soluções de estoque com o Azure Stack.

## <a name="prerequisites"></a>Pré-requisitos
Vários pré-requisitos devem ser atendidos antes de usar esses recursos para atualizar e gerenciar VMs do Azure Stack. Isso inclui as etapas que devem ser executadas no portal do Azure, bem como o portal de administração do Azure Stack.

### <a name="in-the-azure-portal"></a>No portal do Azure
Para usar o inventário, o controle de alterações e os recursos de automação do Azure de gerenciamento de atualização para VMs do Azure Stack, primeiro você precisa habilitar essas soluções no Azure.

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

### <a name="in-the-azure-stack-administration-portal"></a>No Portal de administração do Azure Stack
Depois de habilitar as soluções de automação do Azure no portal do Azure, em seguida você precisa entrar no portal de administração do Azure Stack como um administrador de nuvem e baixe o **atualização do Azure e o gerenciamento de configuração** e o  **Atualização e gerenciamento de configuração para Linux do Azure** itens do marketplace extensão do Azure Stack. 

   ![Azure atualização e configuração de gerenciamento extensão item do marketplace](media/vm-update-management/2.PNG) 

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

## <a name="enable-update-management-using-a-resource-manager-template"></a>Habilitar o gerenciamento de atualização usando um modelo do Resource Manager
Se você tiver um grande número de VMs do Azure Stack, você pode usar [este modelo do Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) para implantar mais facilmente a solução em máquinas virtuais. O modelo implanta a extensão Microsoft Monitoring Agent em uma VM de pilha do Azure existente e adiciona-o para um espaço de trabalho existente do Azure LogAnalytics.
 
## <a name="next-steps"></a>Próximas etapas
[Otimizar o desempenho de VM do SQL Server](azure-stack-sql-server-vm-considerations.md)
