---
title: "Atualizar módulos do Azure na Automação do Azure | Microsoft Docs"
description: "Este artigo descreve como você pode atualizar módulos comuns do Azure PowerShell fornecidos por padrão na Automação do Azure."
services: automation
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 7a2999b3b1a54668f6ef45433efabd5a495418fe
ms.openlocfilehash: ec84df70d4a77e3b81a88aa286fc492d92e3e753


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar módulos do Azure PowerShell na Automação do Azure

Os módulos mais comuns do Azure PowerShell são fornecidos por padrão em cada conta de Automação.  A equipe do Azure atualiza os módulos do Azure regularmente, assim, na conta de Automação fornecemos uma maneira de atualizar os módulos na conta quando novas versões são disponibilizadas.

## <a name="updating-azure-modules"></a>Atualizando os módulos do Azure

1. Na folha Módulos da sua conta de Automação, há uma opção chamada **Atualizar Módulos do Azure**.  Ela está sempre habilitada.<br><br> ![Opção Atualizar Módulos do Azure na folha Módulos](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Clique em **Atualizar Módulos do Azure** e você receberá uma notificação de confirmação que pergunta se deseja continuar.<br><br> ![Notificação de Atualizar Módulos do Azure](media/automation-update-azure-modules/automation-update-azure-modules-notification.png)

3. Clique em **Sim** e o processo de atualização do módulo será iniciado.  O processo de atualização leva aproximadamente de 15 a 20 minutos para atualizar os seguintes módulos:

  * As tabelas
  *    Azure.Storage
  *    AzureRm.Automation
  *    AzureRm.Compute
  *    AzureRm.Profile
  *    AzureRm.Resources
  *    AzureRm.Sql
  * AzureRm.Storage

    Se os módulos já estiverem atualizados, o processo será concluído em alguns segundos.  Quando o processo de atualização for concluído, você será notificado.<br><br> ![Status da atualização de Atualizar Módulos do Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

Como parte do processo de atualização, as agendas de todos os runbooks agendados serão atualizadas para usar a versão mais recente do módulo.

Caso use cmdlets desses módulos do Azure PowerShell em seus runbooks para gerenciar recursos do Azure, convém executar esse processo de atualização todos os meses, ou quase, para garantir que você tenha os módulos mais recentes.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Módulos de Integração e como criar módulos personalizados para integrar a Automação a outros sistemas, serviços ou soluções, confira [Módulos de integração](automation-integration-modules.md).


<!--HONumber=Feb17_HO2-->


