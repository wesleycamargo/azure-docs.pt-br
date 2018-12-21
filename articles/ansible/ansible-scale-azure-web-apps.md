---
title: Dimensionar aplicativos Web do Serviço de Aplicativo do Azure usando o Ansible
description: Saiba como usar o Ansible para criar um aplicativo Web com tempo de execução de contêiner Java 8 e Tomcat no Serviço de Aplicativo no Linux
ms.service: ansible
keywords: ansible, azure, devops, bash, guia estratégico, Serviço de Aplicativo do Azure, aplicativo Web, escala, Java
author: tomarcher
manager: jeconnoc
ms.author: kyliel
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 4ec5a0691ce73a2ffebe07b316ce5b1dde8c2b49
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249372"
---
# <a name="scale-azure-app-service-web-apps-by-using-ansible"></a>Dimensionar aplicativos Web do Serviço de Aplicativo do Azure usando o Ansible
[Aplicativos Web do Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/app-service-web-overview) (ou apenas Aplicativos Web) hospedam aplicativos Web, APIs REST e back-end móvel. Você pode desenvolver usando sua linguagem favorita&mdash;.NET, .NET Core, Java, Ruby, Node.js, PHP ou Python.

O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. Este artigo mostra como usar o Ansible para dimensionar seu aplicativo no Serviço de Aplicativo do Azure.

## <a name="prerequisites"></a>Pré-requisitos
- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Aplicativos Web do Serviço de Aplicativo do Azure** – se ainda não tiver um aplicativo Web do Serviço de Aplicativo do Azure, você poderá [criar aplicativos Web do Azure usando o Ansible](ansible-create-configure-azure-web-apps.md).

## <a name="scale-up-an-app-in-app-service"></a>Expandir um aplicativo no Serviço de Aplicativo
Você pode expandir alterando o tipo de preço do plano do Serviço de Aplicativo ao qual seu aplicativo pertence. Esta seção apresenta um guia estratégico de exemplo do Ansible que define a seguinte operação:
- Obter fatos de um plano do Serviço de Aplicativo existente
- Atualizar o plano do Serviço de Aplicativo para o S2 com três funções de trabalho

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    plan_name: myAppServicePlan
    location: eastus

  tasks:
  - name: Get facts of existing App serivce plan
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku

  - name: Scale up the App service plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      is_linux: true
      sku: S2
      number_of_workers: 3
      
  - name: Get facts
    azure_rm_appserviceplan_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
    register: facts

  - debug: 
      var: facts.appserviceplans[0].sku
```

Salve este guia estratégico como *webapp_scaleup.yml*.

Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:
```bash
ansible-playbook webapp_scaleup.yml
```

Depois de executar o guia estratégico, uma saída semelhante ao exemplo a seguir mostra que o plano do Serviço de Aplicativo foi atualizado com êxito para S2 com três funções de trabalho:
```Output
PLAY [localhost] **************************************************************

TASK [Gathering Facts] ********************************************************
ok: [localhost]

TASK [Get facts of existing App serivce plan] **********************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

ok: [localhost]

TASK [debug] ******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 1,
        "family": "S",
        "name": "S1",
        "size": "S1",
        "tier": "Standard"
    }
}

TASK [Scale up the App service plan] *******************************************
changed: [localhost]

TASK [Get facts] ***************************************************************
ok: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => {
    "facts.appserviceplans[0].sku": {
        "capacity": 3,
        "family": "S",
        "name": "S2",
        "size": "S2",
        "tier": "Standard"
    }
}

PLAY RECAP **********************************************************************
localhost                  : ok=6    changed=1    unreachable=0    failed=0 
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Ansible no Azure](https://docs.microsoft.com/azure/ansible/)