---
title: Integrar a Automação do Azure ao controle do código-fonte do Azure DevOps Services
description: O cenário orientará você durante a configuração da integração com uma conta de Automação do Azure e o controle do código-fonte do Azure DevOps Services.
services: automation
author: eamonoreilly
ms.author: eamono
keywords: azure powershell, Azure DevOps Services, controle do código-fonte, automação
ms.service: automation
ms.component: process-automation
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: b06e315cc12856976dce87791b423d10f002c6df
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801430"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-azure-devops"></a>Cenário da Automação do Azure – Integração de controle do código-fonte ao Azure DevOps

> [!NOTE]
> Há uma nova experiência para o controle do código-fonte. Para saber mais sobre a nova experiência, consulte [Controle do código-fonte (versão prévia)](source-control-integration.md).

Nesse cenário, você tem um projeto do Azure DevOps usado para gerenciar runbooks da Automação do Azure ou configurações do DSC sob o controle do código-fonte.

Este artigo descreve como integrar o Azure DevOps ao seu ambiente da Automação do Azure para que a integração contínua ocorra a cada check-in.

## <a name="getting-the-scenario"></a>Obtendo o cenário

Este cenário é composto por dois runbooks do PowerShell que você pode importar diretamente da [Galeria de Runbooks](automation-runbook-gallery.md) no Portal do Azure, ou baixar da [Galeria do PowerShell](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbooks

Runbook | DESCRIÇÃO|
--------|------------|
Sync-VSTS | Importar runbooks ou configurações do controle do código-fonte do Azure DevOps quando é realizado um check-in. Se executado manualmente, ele importa e publica todos os runbooks ou configurações para a conta de Automação.| 
Sync-VSTSGit | Importar runbooks ou configurações do controle do código-fonte do Azure DevOps no Git quando é realizado um check-in. Se executado manualmente, ele importa e publica todos os runbooks ou configurações para a conta de Automação.|

### <a name="variables"></a>variáveis

Variável | DESCRIÇÃO|
-----------|------------|
VSToken | Ativo de variável segura que você cria e que contém o token de acesso pessoal do Azure DevOps. Você pode aprender como criar um token de acesso pessoal do Azure DevOps na [página de autenticação do Azure DevOps](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

## <a name="installing-and-configuring-this-scenario"></a>Instalando e configurando esse cenário

Criar um [token de acesso pessoal](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) no Azure DevOps que é usado para sincronizar os runbooks ou as configurações na sua conta de automação.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Criar uma [variável segura](automation-variables.md) na sua conta de automação para armazenar o token de acesso pessoal para que o runbook possa autenticar o Azure DevOps e sincronizar os runbooks ou as configurações na conta de Automação. Você pode nomear esse VSToken.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

Importe o runbook que sincroniza seus runbooks ou configurações para a conta de automação. É possível utilizar o [runbook de exemplo do Azure DevOps](https://www.powershellgallery.com/packages/Sync-VSTS) ou o [runbook de exemplo do Azure DevOps com Git](https://www.powershellgallery.com/packages/Sync-VSTSGit) da [Galeria do PowerShell](https://www.powershellgallery.com), dependendo de se você usa o controle do código-fonte do Azure DevOps ou o Azure DevOps com Git e implanta na sua conta de automação.

![Galeria do PowerShell](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

Agora você pode [publicar](automation-creating-importing-runbook.md#publishing-a-runbook) esse runbook para que você possa criar um webhook.

![Runbook pulish](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

Criar um [webhook](automation-webhooks.md) para este runbook Sync-VSTS e preencha os parâmetros conforme mostrado abaixo. Verifique se você copiou a URL do webhook, pois ela será necessária para o gancho de serviço no Azure DevOps. O VSAccessTokenVariableName é o nome (VSToken) da variável segura que você criou anteriormente para conter o token de acesso pessoal.

A integração com o Azure DevOps (Sync-VSTS.ps1) utiliza os parâmetros a seguir:

### <a name="sync-vsts-parameters"></a>Parâmetros do Sync-VSTS

Parâmetro | DESCRIÇÃO|
--------|------------|
WebhookData | Isso contém as informações de check-in enviadas do gancho de serviço do Azure DevOps. Você deve deixar esse parâmetro em branco.| 
ResourceGroup | Esse é o nome do grupo de recursos em que a conta de automação está contida.|
AutomationAccountName | O nome da conta de automação que é sincronizada com o Azure DevOps.|
VSFolder | O nome da pasta no Azure DevOps em que os runbooks e as configurações existem.|
VSAccount | O nome da organização do Azure DevOps.|
VSAccessTokenVariableName | O nome da variável segura (VSToken) que contém o token de acesso pessoal do Azure DevOps.|

![webhook](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

Se você estiver usando o Azure DevOps com GIT (Sync-VSTSGit.ps1), ele utilizará os parâmetros a seguir.

Parâmetro | DESCRIÇÃO|
--------|------------|
WebhookData | Isso conterá as informações de check-in enviadas do gancho de serviço do Azure DevOps. Você deve deixar esse parâmetro em branco.|
ResourceGroup | Esse é o nome do grupo de recursos em que a conta de automação está contida.|
AutomationAccountName | O nome da conta de automação que é sincronizada com o Azure DevOps.|
VSAccount | O nome da organização do Azure DevOps.|
VSProject | O nome do projeto no Azure DevOps em que os runbooks e as configurações existem.|
GitRepo | O nome do repositório Git.|
GitBranch | O nome do branch no repositório Git do Azure DevOps.|
Pasta | O nome da pasta no branch Git do Azure DevOps.|
VSAccessTokenVariableName | O nome da variável segura (VSToken) que contém o token de acesso pessoal do Azure DevOps.|

![GIT Webhook](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

Crie um gancho de serviço no Azure DevOps para check-ins na pasta que dispara esse webhook no check-in de código. Selecione **Web Hooks** como o serviço com o qual se integrar ao criar uma nova assinatura. Você pode aprender mais sobre os ganchos de serviço na [documentação de Ganchos de serviço do Azure DevOps](https://www.visualstudio.com/docs/marketplace/integrate/service-hooks/get-started).
![Host de Serviço](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

Agora você deverá poder realizar todos os check-ins de runbooks e configurações no Azure DevOps e sincronizá-los automaticamente na sua conta de automação.

![Saída de sincronização de runbook](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Se você executar esse runbook manualmente sem ser disparado pelo Azure DevOps, você poderá deixar o parâmetro webhookdata vazio e ele fará uma sincronização completa da pasta do Azure DevOps especificada.

Se você deseja desinstalar o cenário, remova o gancho de serviço do Azure DevOps, exclua o runbook e a variável VSToken.
