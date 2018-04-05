---
title: Atualizar módulos do Azure na Automação do Azure
description: Este artigo descreve como você pode atualizar módulos comuns do Azure PowerShell fornecidos por padrão na Automação do Azure.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: f1f7068de1781d1cc66a412752f6fd99d603a6be
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar módulos do Azure PowerShell na Automação do Azure

Os módulos mais comuns do Azure PowerShell são fornecidos por padrão em cada conta de Automação. A equipe do Azure atualiza os módulos do Azure regularmente, portanto, na conta de Automação você terá uma maneira de atualizar os módulos na conta quando novas versões estiverem disponíveis no portal.  

Uma vez que os módulos são atualizados regularmente pelo grupo de produtos, as alterações podem ocorrer com os cmdlets incluídos, o que pode afetar negativamente seus runbooks dependendo do tipo de alteração, como renomear um parâmetro ou descontinuar um cmdlet inteiramente. Para evitar o impacto nos runbooks e nos processos que eles automatizam, é recomendável testar e validar antes de continuar. Se você não tiver uma conta de Automação dedicada destinada para essa finalidade, considere criar uma para que você possa testar vários cenários e permutações diferentes durante o desenvolvimento de seus runbooks, além das alterações iterativas, como atualizar os módulos do PowerShell. Depois que os resultados forem validados e você tiver aplicado as alterações necessárias, continue coordenando a migração de quaisquer runbooks que precisaram de modificação e execute a atualização a seguir, conforme descrito na produção.

## <a name="updating-azure-modules"></a>Atualizando os módulos do Azure

1. Na página Módulos da sua conta de Automação, há uma opção chamada **Atualizar Módulos do Azure**. Ela está sempre habilitada.<br><br> ![Opção Atualizar Módulos do Azure na página Módulos](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Clique em **Atualizar Módulos do Azure** e uma notificação de confirmação será exibida perguntando se você deseja continuar.<br><br> ![Notificação de Atualizar Módulos do Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Clique em **Sim** e o processo de atualização do módulo será iniciado. O processo de atualização leva aproximadamente de 15 a 20 minutos para atualizar os seguintes módulos:

  * As tabelas
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Se os módulos já estiverem atualizados, o processo será concluído em alguns segundos. Quando o processo de atualização for concluído, você será notificado.<br><br> ![Status da atualização de Atualizar Módulos do Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> A Automação do Azure usa os módulos mais recentes de sua conta de Automação quando um novo trabalho agendado é executado.    

Se você utilizar cmdlets desses módulos do Azure PowerShell nos runbooks, esse processo de atualização será executado a cada mês ou mais para garantir que tenha os módulos mais recentes.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre Módulos de Integração e como criar módulos personalizados para integrar a Automação a outros sistemas, serviços ou soluções, confira [Módulos de integração](automation-integration-modules.md).

* Considere a integração de controle do código-fonte usando [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) ou [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) para gerenciar e controlar centralmente versões do seu portfólio de runbook e configuração de Automação.  