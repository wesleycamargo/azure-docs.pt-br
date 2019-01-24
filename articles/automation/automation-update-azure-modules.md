---
title: Atualizar módulos do Azure na Automação do Azure
description: Este artigo descreve como você pode atualizar módulos comuns do Azure PowerShell fornecidos por padrão na Automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c0636a3e1fa50f90c68393aea910f36d38d8eaf5
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437260"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar módulos do Azure PowerShell na Automação do Azure

Os módulos mais comuns do Azure PowerShell são fornecidos por padrão em cada conta de Automação. A equipe do Azure atualiza os módulos do Azure regularmente. Na sua conta de Automação, você terá uma maneira de atualizar os módulos na conta quando novas versões estiverem disponíveis no portal.

> [!NOTE]
> O novo [módulo Az do Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azurermps-6.13.0) não tem suporte na Automação do Azure.

Como os módulos são atualizados regularmente pelo grupo de produtos, as alterações podem ocorrer com os cmdlets incluídos. Essa ação pode afetar negativamente seus runbooks dependendo do tipo de alteração, como renomear um parâmetro ou descontinuar um cmdlet inteiramente. Para evitar o impacto nos runbooks e nos processos que eles automatizam, teste e valide antes de continuar. Se você não tiver uma conta de Automação dedicada destinada para essa finalidade, considere criar uma para que você possa testar vários cenários e permutações diferentes durante o desenvolvimento de seus runbooks. Esse teste deve incluir alterações iterativas, como atualizar os módulos do PowerShell. Se você desenvolve seus scripts localmente, é recomendável ter as mesmas versões do módulo localmente que você tem em sua Conta de Automação quando os testes para garantir que você receberá os mesmos resultados. Depois que os resultados forem validados e você tiver aplicado as alterações necessárias, você pode mover as alterações para a produção.

> [!NOTE]
> Uma nova conta de Automação pode não conter os últimos módulos.

## <a name="updating-azure-modules"></a>Atualizando os módulos do Azure

1. Na página Módulos da sua conta de Automação, há uma opção chamada **Atualizar Módulos do Azure**. Ela está sempre habilitada.<br><br> ![Opção Atualizar Módulos do Azure na página Módulos](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Antes de atualizar os módulos do Azure, é recomendável que você os atualize em uma Conta de Automação de teste para garantir que seus scripts existentes funcionem conforme o esperado antes de atualizar os módulos do Azure.
  >
  > O botão **Atualizar módulos do Azure** só está disponível na nuvem pública. Não está disponível nas [regiões soberanas](https://azure.microsoft.com/global-infrastructure/). Consulte a seção [maneiras alternativas de atualizar seus módulos](#alternative-ways-to-update-your-modules) para saber mais.

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

    Não há suporte para os módulos AzureRm do .NET Core (AzureRm.*.Core) na Automação do Azure e eles não podem ser importados.

> [!NOTE]
> A Automação do Azure usa os módulos mais recentes de sua conta de Automação quando um novo trabalho agendado é executado.  

Se você utilizar cmdlets desses módulos do Azure PowerShell nos runbooks, esse processo de atualização será executado a cada mês ou mais para garantir que tenha os módulos mais recentes. A Automação do Azure usa a `AzureRunAsConnection` conexão para se autenticar ao atualizar os módulos. Se a entidade de serviço expirou ou não existe mais no nível da assinatura, a atualização do módulo falhará.

## <a name="alternative-ways-to-update-your-modules"></a>Maneiras alternativas de atualizar seus módulos

Conforme mencionado, o botão **Atualizar módulos do Azure** não está disponível nas nuvens soberanas; ele só está disponível na nuvem global do Azure. Isso ocorre devido ao fato de que a versão mais recente dos módulos do Azure PowerShell da Galeria do PowerShell não pode funcionar com os recursos do Gerenciador de Recursos implantados nessas nuvens no momento.

Você pode importar e executar o runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) para tentar atualizar os módulos do Azure na sua Conta de automação. Geralmente é uma boa ideia atualizar todos os módulos do Azure ao mesmo tempo. Porém, esse processo poderá falhar se as versões que você está tentando importar da galeria não são forem compatíveis com os serviços do Azure atualmente implantados no Ambiente do Azure de destino. Isso pode exigir que você verifique se as versões compatíveis dos módulos são especificadas nos parâmetros de runbook.

Use o parâmetro `AzureRmEnvironment` para passar o ambiente correto para o runbook.  Os valores aceitáveis são **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** e **AzureUSGovernment**. Esses valores podem ser recuperados usando `Get-AzureRmEnvironment | select Name`. Se você não passar um valor para esse parâmetro, o padrão do runbook será a nuvem pública do Azure **AzureCloud**

Se você desejar usar uma versão específica do módulo Azure PowerShell, em vez da versão mais recente disponível na Galeria do PowerShell, passe essas versões para o parâmetro `ModuleVersionOverrides` opcional do runbook **Update-AzureModule**. Para obter exemplos, confira o runbook [Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1). Os módulos do Azure PowerShell não mencionados no parâmetro `ModuleVersionOverrides` são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Se você não passar nada para o `ModuleVersionOverrides` parâmetro, todos os módulos são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Esse comportamento é igual ao botão **Atualizar Módulos do Azure**.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre Módulos de Integração e como criar módulos personalizados para integrar a Automação a outros sistemas, serviços ou soluções, confira [Módulos de integração](automation-integration-modules.md).


