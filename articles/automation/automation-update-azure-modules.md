---
title: Atualizar módulos do Azure na Automação do Azure
description: Este artigo descreve como você pode atualizar módulos comuns do Azure PowerShell fornecidos por padrão na Automação do Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fbb57753117f3c60010fe910616b8d0af5178360
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434816"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar módulos do Azure PowerShell na Automação do Azure

Os módulos mais comuns do Azure PowerShell são fornecidos por padrão em cada conta de Automação. A equipe do Azure atualiza os módulos do Azure regularmente, portanto, na conta de Automação você terá uma maneira de atualizar os módulos na conta quando novas versões estiverem disponíveis no portal.

Uma vez que os módulos são atualizados regularmente pelo grupo de produtos, as alterações podem ocorrer com os cmdlets incluídos, o que pode afetar negativamente seus runbooks dependendo do tipo de alteração, como renomear um parâmetro ou descontinuar um cmdlet inteiramente. Para evitar o impacto nos runbooks e nos processos que eles automatizam, é recomendável testar e validar antes de continuar. Se você não tiver uma conta de Automação dedicada destinada para essa finalidade, considere criar uma para que você possa testar vários cenários e permutações diferentes durante o desenvolvimento de seus runbooks, além das alterações iterativas, como atualizar os módulos do PowerShell. Depois que os resultados forem validados e você tiver aplicado as alterações necessárias, continue coordenando a migração de quaisquer runbooks que precisaram de modificação e execute a atualização a seguir, conforme descrito na produção.

> [!NOTE]
> Uma nova conta de Automação pode não conter os últimos módulos.

## <a name="updating-azure-modules"></a>Atualizando os módulos do Azure

1. Na página Módulos da sua conta de Automação, há uma opção chamada **Atualizar Módulos do Azure**. Ela está sempre habilitada.<br><br> ![Opção Atualizar Módulos do Azure na página Módulos](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Antes de atualizar os módulos do Azure, é recomendável que você os atualize em uma Conta de Automação de teste para garantir que seus scripts existentes funcionem conforme o esperado antes de atualizar os módulos do Azure.
  >
  > O botão **Atualizar módulos do Azure** só está disponível na nuvem pública. Ele não está disponível nas [regiões soberanas](https://azure.microsoft.com/global-infrastructure/). Consulte a seção [maneiras alternativas de atualizar seus módulos](#alternative-ways-to-update-your-modules) para saber mais.


2. Clique em **Atualizar Módulos do Azure** e uma notificação de confirmação será exibida perguntando se você deseja continuar.<br><br> ![Notificação de Atualizar Módulos do Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Clique em **Sim** e o processo de atualização do módulo será iniciado. O processo de atualização leva aproximadamente de 15 a 20 minutos para atualizar os seguintes módulos:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Se os módulos já estiverem atualizados, o processo será concluído em alguns segundos. Quando o processo de atualização for concluído, você será notificado.<br><br> ![Status da atualização de Atualizar Módulos do Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

    Não há suporte para os módulos AzureRm do .NET Core (AzureRm.*.Core) na automação do Azure e eles não podem ser importados.

> [!NOTE]
> A Automação do Azure usa os módulos mais recentes de sua conta de Automação quando um novo trabalho agendado é executado.  

Se você utilizar cmdlets desses módulos do Azure PowerShell nos runbooks, esse processo de atualização será executado a cada mês ou mais para garantir que tenha os módulos mais recentes. A Automação do Azure usa a conexão AzureRunAsConnection para autenticação ao atualizar os módulos; se a entidade de serviço expirar ou não existir mais no nível da assinatura, a atualização do módulo falhará.

## <a name="alternative-ways-to-update-your-modules"></a>Maneiras alternativas de atualizar seus módulos

Conforme mencionado, o botão **Atualizar módulos do Azure** não está disponível nas nuvens soberanas; ele só está disponível na nuvem global do Azure. Isso ocorre devido ao fato de que a versão mais recente dos módulos do Azure PowerShell da Galeria do PowerShell não pode funcionar com os serviços do Resource Manager implantados nessas nuvens no momento.

Atualizar os módulos ainda pode ser feito com a importação do runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) em sua Conta de Automação e sua execução.

Use o parâmetro `AzureRmEnvironment` para passar o ambiente correto para o runbook.  Os valores aceitáveis são **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** e **AzureUSGovernmentCloud**. Se você não passar um valor para esse parâmetro, o padrão do runbook será a nuvem pública do Azure **AzureCloud**.

Se você desejar usar uma versão específica do módulo Azure PowerShell, em vez da versão mais recente disponível na Galeria do PowerShell, passe essas versões para o parâmetro `ModuleVersionOverrides` opcional do runbook **Update-AzureModule**. Para obter exemplos, confira o runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1). Os módulos do Azure PowerShell não mencionados no parâmetro `ModuleVersionOverrides` são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Se nada for passado para o parâmetro `ModuleVersionOverrides`, todos os módulos serão atualizados com as versões mais recentes do módulo na Galeria do PowerShell, que é o comportamento do botão **Atualizar módulos do Azure**.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre Módulos de Integração e como criar módulos personalizados para integrar a Automação a outros sistemas, serviços ou soluções, confira [Módulos de integração](automation-integration-modules.md).

* Considere a integração de controle do código-fonte usando [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) ou [Azure DevOps](automation-scenario-source-control-integration-with-vsts.md) para gerenciar e controlar centralmente versões do seu portfólio de runbook e de configuração de Automação.  
