---
title: Criar aplicativos Web do Azure usando o Ansible
description: Saiba como usar o Ansible para criar um aplicativo Web com tempo de execução de contêiner Java 8 e Tomcat no Serviço de Aplicativo no Linux
ms.service: ansible
keywords: ansible, azure, devops, bash, guia estratégico, Serviço de Aplicativo do Azure, aplicativo Web, Java
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/08/2018
ms.openlocfilehash: 4a772977130f5679da2d879cc12738b89be09f1d
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726724"
---
# <a name="create-azure-app-service-web-apps-by-using-ansible"></a>Criar aplicativos Web do Serviço de Aplicativo do Azure usando o Ansible
Os [Aplicativos Web do Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/overview) (ou apenas Aplicativos Web) hospedam aplicativos Web, APIs REST e back-ends móveis. Você pode desenvolver usando sua linguagem favorita&mdash;.NET, .NET Core, Java, Ruby, Node.js, PHP ou Python.

O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. Este artigo mostra como usar o Ansible para criar um aplicativo Web usando o tempo de execução Java. 

## <a name="prerequisites"></a>Pré-requisitos
- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> O Ansible 2.7 é necessário para executar os guias estratégicos de exemplo contidos neste tutorial.

## <a name="create-a-simple-app-service"></a>Criar um serviço de aplicativo simples
Esta seção apresenta um guia estratégico de exemplo do Ansible que define os seguintes recursos:
- Grupo de recursos no qual seu Plano do Serviço de Aplicativo e aplicativo Web serão implantados
- Aplicativo Web com tempo de execução de contêiner Tomcat e Java 8 no Serviço de Aplicativo no Linux

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    webapp_name: myfirstWebApp
    plan_name: myAppServicePlan
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create App Service on Linux with Java Runtime
      azure_rm_webapp:
        resource_group: "{{ resource_group }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        frameworks:
          - name: "java"
            version: "8"
            settings:
              java_container: tomcat
              java_container_version: 8.5
```
Salve o guia estratégico anterior como **firstwebapp.yml**.

Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:
```bash
ansible-playbook firstwebapp.yml
```

A saída da execução do guia estratégico do Ansible mostra que o aplicativo Web foi criado com êxito:

```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create a resource group] *************************************************
changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] *************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

PLAY RECAP *************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## <a name="create-an-app-service-by-using-traffic-manager"></a>Criar um serviço de aplicativo usando o Gerenciador de Tráfego
Você pode usar o [Gerenciador de Tráfego do Azure](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager) para controlar como as solicitações de clientes Web são distribuídas aos aplicativos no Serviço de Aplicativo do Azure. Quando os pontos de extremidade do Serviço de Aplicativo são adicionados a um perfil do Gerenciador de Tráfego do Azure, o Gerenciador de Tráfego rastreia o status dos aplicativos do Serviço de Aplicativo. Os status incluem em execução, parado e excluído. O Gerenciador de Tráfego, em seguida, pode decidir quais desses pontos de extremidade devem receber tráfego.

No Serviço de Aplicativo, um aplicativo é executado em um [Plano de Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/overview-hosting-plans
). Um plano de serviço de aplicativo define um conjunto de recursos de computação para um aplicativo Web ser executado. Você pode gerenciar o Plano do Serviço de Aplicativo e o aplicativo Web em grupos diferentes.

Esta seção apresenta um guia estratégico de exemplo do Ansible que define os seguintes recursos:
- Grupo de recursos no qual seu plano do serviço de aplicativo será implantado
- Plano do Serviço de Aplicativo
- Grupo de recursos secundário no qual seu aplicativo Web será implantado
- Aplicativo Web com tempo de execução de contêiner Tomcat e Java 8 no Serviço de Aplicativo no Linux
- Perfil do Gerenciador de Tráfego
- Ponto de extremidade do Gerenciador de Tráfego usando o site criado

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group_webapp: myResourceGroupWebapp
    resource_group: myResourceGroup
    webapp_name: myLinuxWebApp
    plan_name: myAppServicePlan
    location: eastus
    traffic_manager_profile_name: myTrafficManagerProfile
    traffic_manager_endpoint_name: myTrafficManagerEndpoint

  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group_webapp }}"
        location: "{{ location }}"

  - name: Create secondary resource group
    azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

  - name: Create App Service Plan
    azure_rm_appserviceplan:
      resource_group: "{{ resource_group }}"
      name: "{{ plan_name }}"
      location: "{{ location }}"
      is_linux: true
      sku: S1
      number_of_workers: 1

  - name: Create App Service on Linux with Java Runtime
    azure_rm_webapp:
        resource_group: "{{ resource_group_webapp }}"
        name: "{{ webapp_name }}"
        plan:
          resource_group: "{{ resource_group }}"
          name: "{{ plan_name }}"
          is_linux: true
          sku: S1
          number_of_workers: 1
        app_settings:
          testkey: "testvalue"
        frameworks:
          - name: java
            version: 8
            settings:
              java_container: "Tomcat"
              java_container_version: "8.5"

  - name: Get web app facts
    azure_rm_webapp_facts:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ webapp_name }}"
    register: webapp
    
  - name: Create Traffic Manager Profile
    azure_rm_trafficmanagerprofile:
      resource_group: "{{ resource_group_webapp }}"
      name: "{{ traffic_manager_profile_name }}"
      location: global
      routing_method: performance
      dns_config:
        relative_name: "{{ traffic_manager_profile_name }}"
        ttl:  60
      monitor_config:
        protocol: HTTPS
        port: 80
        path: '/'

  - name: Add endpoint to traffic manager profile, using created web site
    azure_rm_trafficmanagerendpoint:
      resource_group: "{{ resource_group_webapp }}"
      profile_name: "{{ traffic_manager_profile_name }}"
      name: "{{ traffic_manager_endpoint_name }}"
      type: azure_endpoints
      location: "{{ location }}"
      target_resource_id: "{{ webapp.webapps[0].id }}"
```
Salve o guia estratégico acima como **webapp.yml** ou [baixe o guia estratégico](https://github.com/Azure-Samples/ansible-playbooks/blob/master/webapp.yml).

Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:
```bash
ansible-playbook webapp.yml
```

A saída da execução do guia estratégico do Ansible mostra que o Plano do Serviço de Aplicativo, o aplicativo Web, o perfil do Gerenciador de Tráfego e o ponto de extremidade foram criados com êxito:
```Output
PLAY [localhost] *************************************************

TASK [Gathering Facts] *************************************************
ok: [localhost]

TASK [Create resource group] ****************************************************************************
changed: [localhost]

TASK [Create resource group for app service plan] ****************************************************************************
changed: [localhost]

TASK [Create App Service Plan] ****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for WebSiteManagementClient

changed: [localhost]

TASK [Create App Service on Linux with Java Runtime] ****************************************************************************
changed: [localhost]

TASK [Get web app facts] *****************************************************************************
ok: [localhost]

TASK [Create Traffic Manager Profile] *****************************************************************************
 [WARNING]: Azure API profile latest does not define an entry for TrafficManagerManagementClient

changed: [localhost]

TASK [Add endpoint to traffic manager profile, using the web site created above] *****************************************************************************
changed: [localhost]

TASK [Get Traffic Manager Profile facts] ******************************************************************************
ok: [localhost]

PLAY RECAP ******************************************************************************
localhost                  : ok=9    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Dimensionar aplicativos Web do Serviço de Aplicativo do Azure usando o Ansible](https://docs.microsoft.com/azure/ansible/ansible-scale-azure-web-apps)