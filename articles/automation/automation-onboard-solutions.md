---
title: "Integrar soluções de atualização e controle de alterações à Automação do Azure | Microsoft Docs"
description: "Saiba como integrar soluções de atualização e controle de alterações à Automação do Azure."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: edae1156-2dc7-4dab-9e5c-bf253d3971d0
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: eamono
ms.openlocfilehash: 9ae5e9ebcbcf3af61318eac98defc6b249417f41
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2017
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Integrar soluções de atualização e controle de alterações à Automação do Azure

Neste tutorial, você aprenderá a integrar automaticamente soluções de Atualização, Controle de Alterações e Inventário para VMs à Automação do Azure:

> [!div class="checklist"]
> * Integrar uma máquina virtual do Azure manualmente.
> * Instalar e atualizar módulos do Azure necessários.
> * Importar um runbook que integra VMs do Azure.
> * Inicia o runbook que integra VMs do Azure automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, os itens a seguir são necessários.
+ Assinatura do Azure. Se você ainda não tiver uma, poderá [ativar os benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ [Conta de automação](automation-offering-get-started.md) para gerenciar máquinas.

## <a name="onboard-an-azure-virtual-machine-manually"></a>Integrar uma máquina virtual do Azure manualmente

1.  Abra a conta da Automação.
2.  Clique na página Inventário.
![Integrar a solução de inventário](media/automation-onboard-solutions/inventory-onboard.png)
3.  Selecione um espaço de trabalho existente do Log Analytics ou crie um novo. Clique no botão Habilitar.
4.  Quando a notificação da integração da solução de inventário e controle de alterações for concluída, clique na página Gerenciamento de Atualizações.
![Integrar solução de atualização](media/automation-onboard-solutions/update-onboard.png)
4.  Clique no botão Habilitar que integra a solução de atualização.
5.  Quando a notificação da integração da solução de atualização for concluída, clique na página Controle de Alterações.
![Integrar o controle de alterações](media/automation-onboard-solutions/change-tracking-onboard-vm.png)
6.  Clique no botão Adicionar VM do Azure.
![Selecione a VM para controle de alterações](media/automation-onboard-solutions/enable-change-tracking.png)
7.  Selecione uma VM do Azure e clique no botão Habilitar para integrar a solução de inventário e controle de alterações.
8.  Quando a notificação de integração da VM for concluída, clique na página Gerenciamento de Atualizações.
![Integrar VM para gerenciamento de atualizações](media/automation-onboard-solutions/update-onboard-vm.png)
9.  Clique no botão Adicionar VM do Azure.
![Selecione a VM para gerenciamento de atualizações](media/automation-onboard-solutions/enable-update.png)
10.  Selecione uma VM do Azure e clique no botão Habilitar para integrar a solução de gerenciamento de atualizações.

## <a name="install-and-update-required-azure-modules"></a>Instalar e atualizar módulos do Azure necessários

É necessário atualizar para os módulos do Azure mais recentes e importar AzureRM.OperationalInsights a fim de automatizar a integração da solução com êxito.
1.  Clique na página Módulos.
![Atualizar módulos](media/automation-onboard-solutions/update-modules.png)
2.  Clique no botão Atualizar Módulos do Azure para atualizá-los na versão mais recente. Aguarde a conclusão das atualizações.
3.  Clique no botão Procurar na galeria e abra a galeria do módulo.
![Importar o módulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)
4.  Procure AzureRM.OperationalInsights e importe este módulo para a conta da Automação.

## <a name="import-a-runbook-that-onboards-solutions-to-azure-vms"></a>Importar um runbook que integre soluções a VMs do Azure

1.  Clique na página Runbooks sob a categoria "AUTOMAÇÃO DE PROCESSO".
2.  Clique no botão "Procurar na galeria".
3.  Procure "Atualização e controle de alterações" e importe o runbook para a conta da Automação.
![Importar runbook de integração](media/automation-onboard-solutions/import-from-gallery.png)
4.  Clique em "Editar" para exibir a origem do Runbook e clique no botão "Publicar".
![Importar runbook de integração](media/automation-onboard-solutions/publish-runbook.png)

## <a name="start-the-runbook-that-onboards-azure-vms-automatically"></a>Inicia o runbook que integra VMs do Azure automaticamente

Você precisa ter integrado a solução de controle de alterações de atualização a uma VM do Azure para iniciar esse runbook. Ele precisa de uma máquina virtual e um grupo de recursos existente com a solução incorporada para ter parâmetros.
1.  Abra o runbook Enable-MultipleSolution.
![Runbook de várias soluções](media/automation-onboard-solutions/runbook-overview.png)
2.  Clique no botão Iniciar e insira os valores de parâmetros a seguir.
    *   VMNAME. O nome de uma VM existente em que a solução de atualização ou controle de alterações deve ser carregada. Deixe em branco e todas as VMs no grupo de recursos serão integradas.
    *   VMRESOURCEGROUP. O nome do grupo de recursos de que a VM é membro.
    *   SUBSCRIPTIONID. A ID da assinatura na qual a nova VM a ser integrada está localizada. Deixe em branco e a assinatura do espaço de trabalho será usada. Quando uma ID de assinatura diferente é fornecida, a conta Executar como da conta de automação deve ser adicionada como um colaborador a essa assinatura também.
    *   ALREADYONBOARDEDVM. O nome da VM que foi integrada à solução de Controle de Alterações ou Atualização.
    *   ALREADYONBOARDEDVMRESOURCEGROUP. O nome do grupo de recursos de que a VM é membro.
    *   SOLUTIONTYPE. Insira "Atualizações" ou "Controle de alterações"
    
    ![Parâmetros de runbook de várias soluções](media/automation-onboard-solutions/runbook-parameters.png)
3.  Clique em OK para iniciar o trabalho de runbook.
4.  Monitore o andamento e os erros na página de trabalho do runbook.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, confira [Agendando runbooks](automation-schedules.md).