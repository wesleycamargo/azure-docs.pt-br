---
title: Implantar aplicativos nos conjuntos de dimensionamento de máquinas virtuais no Azure usando o Ansible
description: Saiba como usar o Ansible para configurar um conjunto de dimensionamento de máquinas virtuais e implantar aplicativos nesse conjunto, no Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, guia estratégico, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/11/2018
ms.openlocfilehash: 3512cb7eda9f9e5a6e18dc83f6523029b17a9de2
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391463"
---
# <a name="deploy-applications-to-virtual-machine-scale-sets-in-azure-using-ansible"></a>Implantar aplicativos nos conjuntos de dimensionamento de máquinas virtuais no Azure usando o Ansible
O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. Você pode usar o Ansible para implantar aplicativos no Azure. Este artigo mostra como implantar um aplicativo Java em um VMSS (conjunto de dimensionamento de máquinas virtuais) do Azure.  

## <a name="prerequisites"></a>Pré-requisitos
- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- **Conjunto de dimensionamento de máquinas virtuais** – se ainda não tiver um conjunto de dimensionamento de máquinas virtuais, você poderá [criar um com o Ansible](ansible-create-configure-vmss.md). 
- **git** - O [git](https://git-scm.com) é usado para baixar um exemplo de Java usado neste tutorial.
- **JDK (Java SE Development Kit)** – o JDK é usado para compilar o projeto Java de exemplo.
- **Ferramentas de build do Apache Maven** – as [ferramentas de build do Apache Maven](https://maven.apache.org/download.cgi) são usadas para compilar o projeto Java de exemplo.

> [!Note]
> O Ansible 2.6 é necessário para executar, neste tutorial, os guias estratégicos de exemplo a seguir. 

## <a name="get-host-information"></a>Obter informações do host

Esta seção ilustra como usar o Ansible para recuperar informações do host para um grupo de máquinas virtuais do Azure. Abaixo está um guia estratégico de exemplo do Ansible. O código obtém os endereços IP públicos e o balanceador de carga dentro do grupo de recursos especificado e cria um grupo de hosts chamado **scalesethosts** no inventário. 

Salve o guia estratégico de exemplo a seguir como `get-hosts-tasks.yml`: 

  ```yaml
  - name: Get facts for all Public IPs within a resource groups
    azure_rm_publicipaddress_facts:
      resource_group: "{{ resource_group }}"
    register: output_ip_address

  - name: Get loadbalancer info
    azure_rm_loadbalancer_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ loadbalancer_name }}"
    register: output

  - name: Add all hosts
    add_host:
      groups: scalesethosts
      hostname: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}_{{ item.properties.frontendPort }}"
      ansible_host: "{{ output_ip_address.ansible_facts.azure_publicipaddresses[0].properties.ipAddress }}"
      ansible_port: "{{ item.properties.frontendPort }}"
      ansible_ssh_user: "{{ admin_username }}"
      ansible_ssh_pass: "{{ admin_password }}"
    with_items:
      - "{{ output.ansible_facts.azure_loadbalancers[0].properties.inboundNatRules }}"
  ```

## <a name="prepare-an-application-for-deployment"></a>Preparar um aplicativo para implantação  

Nesta seção, você usa o git para clonar um projeto de exemplo do Java do GitHub e compilar o projeto. Salve o guia estratégico a seguir como `app.yml`:

  ```yaml
  - hosts: localhost
    vars:
      repo_url: https://github.com/spring-guides/gs-spring-boot.git
      workspace: ~/src/helloworld

    tasks: 
    - name: Git Clone sample app
      git:
        repo: "{{ repo_url }}"
        dest: "{{ workspace }}"

    - name: Build sample app
      shell: mvn package chdir="{{ workspace }}/complete"
  ```

Execute o guia estratégico de exemplo do Ansible com o seguinte comando:

  ```bash
  ansible-playbook app.yml
  ```

A saída do comando de guia estratégico do Ansible é exibida com aspecto semelhante ao seguinte, em que você vê que ele compilou o aplicativo de exemplo clonado do GitHub:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Git Clone sample app] ***************************************************************************
  changed: [localhost]

  TASK [Build sample app] ***************************************************
  changed: [localhost]

  PLAY RECAP ***************************************************************************
  localhost                  : ok=3    changed=2    unreachable=0    failed=0

  ```

## <a name="deploy-the-application-to-vmss"></a>Implantar o aplicativo no VMSS

A seção a seguir em um guia estratégico do Ansible instala o JRE (Java Runtime Environment) em um grupo de hosts chamado **scalesethosts** e implanta o aplicativo Java em um grupo de hosts chamado **scalesethosts**: 

(Altere o `admin_password` para sua própria senha.)

  ```yaml
  - hosts: localhost
    vars:
      resource_group: myResourceGroup
      scaleset_name: myVMSS
      loadbalancer_name: myVMSSlb
      admin_username: azureuser
      admin_password: "your_password"
    tasks:   
    - include: get-hosts-tasks.yml

  - name: Install JRE on VMSS
    hosts: scalesethosts
    become: yes
    vars:
      workspace: ~/src/helloworld
      admin_username: azureuser

    tasks:
    - name: Install JRE
      apt:
        name: default-jre
        update_cache: yes

    - name: Copy app to Azure VM
      copy:
        src: "{{ workspace }}/complete/target/gs-spring-boot-0.1.0.jar"
        dest: "/home/{{ admin_username }}/helloworld.jar"
        force: yes
        mode: 0755

    - name: Start the application
      shell: java -jar "/home/{{ admin_username }}/helloworld.jar" >/dev/null 2>&1 &
      async: 5000
      poll: 0
  ```

Você pode salvar o guia estratégico de exemplo anterior do Ansible como `vmss-setup-deploy.yml`, ou [baixar o guia estratégico de exemplo inteiro](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss). 

Para usar o tipo de conexão ssh com senhas, você precisa instalar o programa sshpass. 
  - Para o Ubunto 16.04, execute o comando `apt-get install sshpass`.
  - Para o CentOS 7.4, execute o comando `yum install sshpass`.

Você pode ver um erro como **Não é possível usar uma senha SSH em vez de uma chave porque a verificação de Chave de Host está habilitada e a sshpass não é compatível com isso. Adicione a impressão digital desse host ao seu arquivo known_hosts para gerenciar esse host.** Se você vir esse erro, você poderá desabilitar a verificação de chave de host adicionando a um entre os arquivos `/etc/ansible/ansible.cfg` e `~/.ansible.cfg`:
  ```bash
  [defaults]
  host_key_checking = False
  ```

Execute o guia estratégico com o comando a seguir:

  ```bash
  ansible-playbook vmss-setup-deploy.yml
  ```

A saída resultante da execução do comando de guia estratégico do Ansible indica que o aplicativo Java de exemplo foi instalado no grupo de hosts do conjunto de dimensionamento de máquinas virtuais:

  ```bash
  PLAY [localhost] **********************************************************

  TASK [Gathering Facts] ****************************************************
  ok: [localhost]

  TASK [Get facts for all Public IPs within a resource groups] **********************************************
  ok: [localhost]

  TASK [Get loadbalancer info] ****************************************************************************
  ok: [localhost]

  TASK [Add all hosts] *****************************************************************************
  changed: [localhost] ...

  PLAY [Install JRE on VMSS] *****************************************************************************

  TASK [Gathering Facts] *****************************************************************************
  ok: [40.114.30.145_50000]
  ok: [40.114.30.145_50003]

  TASK [Copy app to Azure VM] *****************************************************************************
  changed: [40.114.30.145_50003]
  changed: [40.114.30.145_50000]

  TASK [Start the application] ********************************************************************
  changed: [40.114.30.145_50000]
  changed: [40.114.30.145_50003]

  PLAY RECAP ************************************************************************************************
  40.114.30.145_50000        : ok=4    changed=3    unreachable=0    failed=0
  40.114.30.145_50003        : ok=4    changed=3    unreachable=0    failed=0
  localhost                  : ok=4    changed=1    unreachable=0    failed=0
  ```

Parabéns! Seu aplicativo está agora em execução no Azure. Agora, você pode navegar para a URL do balanceador de carga para seu conjunto de dimensionamento de máquinas virtuais:

![Aplicativo Java em execução em um conjunto de dimensionamento de máquinas virtuais no Azure.](media/ansible-deploy-app-vmss/ansible-deploy-app-vmss.png)

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Guia estratégico de exemplo do Ansible para VMSS](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)