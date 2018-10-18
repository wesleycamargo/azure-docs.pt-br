---
title: Gerenciar pacotes do Python 2 no Automação do Azure
description: Este artigo descreve como gerenciar pacotes do Python 2 na Automação do Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5974a8e622ca0969b2a7b5ee9500766ac95398c9
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987217"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Gerenciar pacotes do Python 2 no Automação do Azure

A Automação do Azure permite executar runbooks do Python 2 no Azure e no Hybrid Runbook Worker do Linux. Para ajudar na simplificação de runbooks, é possível usar pacotes do Python para importar os módulos que forem necessários. Este artigo descreve como você gerencia e usa pacotes do Python na Automação do Azure.

## <a name="import-packages"></a>Importar pacotes

Na sua Conta de Automação, selecione **pacotes do Python 2** em **Recursos Compartilhar**. Clique em **+ Adicionar um pacote do Python 2**.

![Adicionar pacote do Python](media/python-packages/add-python-package.png)

Na página **Adicionar pacote do Python 2**, selecione um pacote local para carregar. O pacote pode ser um arquivo `.whl` ou arquivo `.tar.gz`. Quando selecionado, clique em **OK** para carregar o pacote.

![Adicionar pacote do Python](media/python-packages/upload-package.png)

Depois que um pacote é importado, ele é listado na página **pacotes do Python 2** na Conta de Automação. Se for necessário remover um pacote, selecione o pacote e escolha **Excluir**  na página do pacote.

![Lista de pacotes](media/python-packages/package-list.png)

## <a name="use-a-package-in-a-runbook"></a>Use um pacote em um runbook

Após importar um pacote, você poderá usá-lo em um runbook. O exemplo a seguir usa o [ pacote utilitário de Automação do Azure](https://github.com/azureautomation/azure_automation_utility). Esse pacote facilita o uso do Python com a Automação do Azure. Para usar o pacote, siga as instruções no repositório GitHub e adicione-o ao runbook, usando `from azure_automation_utility import get_automation_runas_credential` por exemplo, para importar a função para recuperar a Conta Executar como.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>Desenvolva e teste runbooks offline

Para desenvolver e testar os runbooks do Python 2 offline, você pode usar o módulo de [ativos emulados do python de Automação do Azure](https://github.com/azureautomation/python_emulated_assets) no GitHub. Esse módulo permite referenciar os recursos compartilhados como credenciais, variáveis, conexões e certificados.

## <a name="next-steps"></a>Próximas etapas

Para começar a usar os runbooks do Python 2, consulte [Meu primeiro runbook do Python 2](automation-first-runbook-textual-python2.md)