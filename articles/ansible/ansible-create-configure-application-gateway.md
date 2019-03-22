---
title: Gerenciar o tráfego da Web com o Gateway de Aplicativo do Azure usando Ansible
description: Saiba como usar o Ansible para criar e configurar um Gateway de Aplicativo do Azure para gerenciar o tráfego da Web
ms.service: azure
keywords: ansible, azure, devops, bash, guia estratégico, gateway de aplicativo, balanceador de carga, tráfego da Web
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2018
ms.openlocfilehash: 83f21573af7ec523acc376c4b3364cdcfb47f96f
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792133"
---
# <a name="manage-web-traffic-with-azure-application-gateway-by-using-ansible"></a>Gerenciar o tráfego da Web com o Gateway de Aplicativo do Azure usando Ansible

O [Gateway de Aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/) é um balanceador de carga do tráfego da Web que permite que você gerencie o tráfego para seus aplicativos Web.

O Ansible ajuda a automatizar a implantação e a configuração de recursos em seu ambiente. Este artigo mostra como usar o Ansible para criar um gateway de aplicativo. Ele também ensina a usar o gateway para gerenciar o tráfego de dois servidores Web que são executados em instâncias de contêiner do Azure.

Este tutorial mostra como:

> [!div class="checklist"]
> * Configurar a rede
> * Criar duas instâncias de contêiner do Azure com imagem HTTPD
> * Criar um gateway de aplicativo que funciona com as instâncias de contêiner do Azure no pool de servidores

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

> [!Note]
> O Ansible 2.7 é necessário para executar os guias estratégicos de exemplo contidos neste tutorial. 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.  

O exemplo a seguir cria um grupo de recursos chamado **myResourceGroup** no local **eastus**.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
```

Salve este guia estratégico como *rg.yml*. Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook rg.yml
```

## <a name="create-network-resources"></a>Criar recursos da rede

Crie uma rede virtual para permitir que o gateway de aplicativo se comunique com outros recursos.

O exemplo a seguir cria uma rede virtual denominada **myVNet**, uma sub-rede denominada **myAGSubnet** e um endereço IP público denominado **myAGPublicIPAddress** com um domínio chamado **mydomain**.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    vnet_name: myVNet 
    subnet_name: myAGSubnet 
    publicip_name: myAGPublicIPAddress
    publicip_domain: mydomain
  tasks:
    - name: Create a virtual network
      azure_rm_virtualnetwork:
        name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefixes_cidr:
            - 10.1.0.0/16
            - 172.100.0.0/16
        dns_servers:
            - 127.0.0.1
            - 127.0.0.2

    - name: Create a subnet
      azure_rm_subnet:
        name: "{{ subnet_name }}"
        virtual_network_name: "{{ vnet_name }}"
        resource_group: "{{ resource_group }}"
        address_prefix_cidr: 10.1.0.0/24

    - name: Create a public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}" 
        allocation_method: Dynamic
        name: "{{ publicip_name }}"
        domain_name_label: "{{ publicip_domain }}"
```

Salve esse guia estratégico como *vnet_create.yml*. Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook vnet_create.yml
```

## <a name="create-servers"></a>Criar servidores

O exemplo a seguir mostra como criar duas instâncias de contêiner do Azure com imagens HTTPD para serem usadas como servidores de back-end do gateway de aplicativo.  

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    location: eastus 
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Create a container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_1_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80

    - name: Create another container with httpd image 
      azure_rm_containerinstance:
        resource_group: "{{ resource_group }}"
        name: "{{ aci_2_name }}"
        os_type: linux
        ip_address: public
        location: "{{ location }}"
        ports:
          - 80
        containers:
          - name: mycontainer
            image: httpd
            memory: 1.5
            ports:
              - 80
```

Salve esse guia estratégico como *aci_create.yml*. Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook aci_create.yml
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

O exemplo a seguir cria um gateway de aplicativo chamado **myAppGateway** com configurações de HTTP, front-end e back-end.  

* **appGatewayIP** é definido no bloco **gateway_ip_configurations**. Uma referência de sub-rede é necessária para a configuração de IP do gateway.
* **appGatewayBackendPool** é definido no bloco **backend_address_pools**. Um gateway de aplicativo deve ter pelo menos um pool de endereços de back-end.
* **appGatewayBackendHttpSettings** é definido no bloco **backend_http_settings_collection**. Ele especifica que a porta 80 e um protocolo HTTP são usados para comunicação.
* **appGatewayHttpListener** é definido no bloco **backend_http_settings_collection**. É o ouvinte padrão associado a appGatewayBackendPool.
* **appGatewayFrontendIP** é definido no bloco **frontend_ip_configurations**. Ele atribui myAGPublicIPAddress a appGatewayHttpListener.
* **Rule1** é definido no bloco **request_routing_rules**. É a regra padrão de roteamento associada a appGatewayHttpListener.

```yml
- hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    vnet_name: myVNet
    subnet_name: myAGSubnet
    location: eastus
    publicip_name: myAGPublicIPAddress
    appgw_name: myAppGateway
    aci_1_name: myACI1
    aci_2_name: myACI2
  tasks:
    - name: Get info of Subnet
      azure_rm_resource_facts:
        api_version: '2018-08-01'
        resource_group: "{{ resource_group }}"
        provider: network
        resource_type: virtualnetworks
        resource_name: "{{ vnet_name }}"
        subresource:
          - type: subnets
            name: "{{ subnet_name }}"
      register: subnet

    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_1_name }}"
      register: aci_1_output
    - name: Get info of backend server 2
      azure_rm_resource_facts:
        api_version: '2018-04-01'
        resource_group: "{{ resource_group }}"
        provider: containerinstance
        resource_type: containergroups
        resource_name: "{{ aci_2_name }}"
      register: aci_2_output

    - name: Create instance of Application Gateway
      azure_rm_appgateway:
        resource_group: "{{ resource_group }}"
        name: "{{ appgw_name }}"
        sku:
          name: standard_small
          tier: standard
          capacity: 2
        gateway_ip_configurations:
          - subnet:
              id: "{{ subnet.response[0].id }}"
            name: appGatewayIP
        frontend_ip_configurations:
          - public_ip_address: "{{ publicip_name }}"
            name: appGatewayFrontendIP
        frontend_ports:
          - port: 80
            name: appGatewayFrontendPort
        backend_address_pools:
          - backend_addresses:
              - ip_address: "{{ aci_1_output.response[0].properties.ipAddress.ip }}"
              - ip_address: "{{ aci_2_output.response[0].properties.ipAddress.ip }}"
            name: appGatewayBackendPool
        backend_http_settings_collection:
          - port: 80
            protocol: http
            cookie_based_affinity: enabled
            name: appGatewayBackendHttpSettings
        http_listeners:
          - frontend_ip_configuration: appGatewayFrontendIP
            frontend_port: appGatewayFrontendPort
            name: appGatewayHttpListener
        request_routing_rules:
          - rule_type: Basic
            backend_address_pool: appGatewayBackendPool
            backend_http_settings: appGatewayBackendHttpSettings
            http_listener: appGatewayHttpListener
            name: rule1
```

Salve esse guia estratégico como *appgw_create.yml*. Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook appgw_create.yml
```

O gateway de aplicativo pode demorar vários minutos para ser criado.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

No guia estratégico de exemplo para recursos de rede, você criou o domínio **mydomain** em **eastus**. Vá para `http://mydomain.eastus.cloudapp.azure.com` em seu navegador. Se você vir a página a seguir, o gateway de aplicativo estará funcionando conforme o esperado.

![Teste bem-sucedido de um gateway de aplicativo em funcionamento](media/ansible-create-configure-application-gateway/applicationgateway.PNG)

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisar desses recursos, poderá excluí-los executando o código a seguir. Ele exclui um grupo de recursos chamado **myResourceGroup**.

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        state: absent
```

Salve esse guia estratégico como *rg_delete*.yml. Para executar o guia estratégico, use o comando **ansible-playbook** da seguinte maneira:

```bash
ansible-playbook rg_delete.yml
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ansible no Azure](https://docs.microsoft.com/azure/ansible/)
