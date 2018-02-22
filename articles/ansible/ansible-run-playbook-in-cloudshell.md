---
title: Executar Ansible com Back no Azure Cloud Shell
description: "Saiba como executar várias tarefas do Ansible com Bash no Azure Cloud Shell"
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 02/01/2018
ms.topic: article
ms.openlocfilehash: 0cd0390a381e85c8f047960ce06c581a433d4a2c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Executar Ansible com Back no Azure Cloud Shell

Neste tutorial, você aprenderá a realizar várias tarefas do Ansible com Bash no Cloud Shell. Essas tarefas incluem conectar-se a uma máquina virtual e criar manuais do Ansible para criar e excluir um grupo de recursos do Azure.

## <a name="prerequisites"></a>pré-requisitos

- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Credenciais do Azure** - [Crie credenciais do Azure e configure o Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

- **Configurar o Azure Cloud Shell** - Se você o Azure Cloud Shell for uma novidade para você, o artigo [Início rápido para Bash no Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/quickstart) ilustra como começar a usar e configurar o Cloud Shell. Inicie um site dedicado para o Cloud Shell aqui:

[![Iniciar o Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Cloud Shell")](https://shell.azure.com)

## <a name="use-ansible-to-connect-to-a-vm"></a>Use o Ansible para conectar-se a uma máquina virtual
O Ansible criou um script Python chamado [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) que gera um inventário dinâmico de recursos do Azure fazendo solicitações de API para o Azure Resource Manager. As etapas a seguir fornecem instruções sobre como usar o script `azure_rm.py` para conectar-se a uma máquina virtual do Azure:

1. Abra o Bash no Cloud Shell. O tipo do Shell é indicado no lado esquerdo da janela do Cloud Shell.

1. Se você não tiver uma máquina virtual para usar, insira os seguintes comandos no Cloud Shell para criar uma máquina virtual para teste:

  ```azurecli-interactive
  az group create --resource-group ansible-test-rg --location eastus
  ```

  ```azurecli-interactive
  az vm create --resource-group ansible-test-rg --name ansible-test-vm --image UbuntuLTS --generate-ssh-keys
  ```

1. Use o comando `wget` do GNU para recuperar o script `azure_rm.py`:

  ```azurecli-interactive
  wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
  ```

1. Use o comando `chmod` para alterar as permissões de acesso para o script `azure_rm.py`. O comando a seguir usa o parâmetro `+x` para permitir a execução do arquivo especificado (`azure_rm.py`):

  ```azurecli-interactive
  chmod +x azure_rm.py
  ```

1. Use o [comando ansible](https://docs.ansible.com/ansible/2.4/ansible.html) para se conectar à sua máquina virtual: 

  ```azurecli-interactive
  ansible -i azure_rm.py ansible-test-vm -m ping
  ```

  Uma vez conectado, você verá resultados semelhantes à saída abaixo:

  ```Output
  The authenticity of host 'nn.nnn.nn.nn (nn.nnn.nn.nn)' can't be established.
  ECDSA key fingerprint is SHA256:&lt;some value>.
  Are you sure you want to continue connecting (yes/no)? yes
  test-ansible-vm | SUCCESS => {
      "changed": false,
      "failed": false,
      "ping": "pong"
  }
  ```

1. Se você tiver criado um grupo de recursos e a máquina virtual nesta seção

  ```azurecli-interactive
  az group delete -n <resourceGroup>
  ```

## <a name="run-a-playbook-in-cloud-shell"></a>Executar um manual no Cloud Shell
O comando [ansible-playbook](https://docs.ansible.com/ansible/2.4/ansible-playbook.html) executa manuais do Ansible, executando as tarefas no(s) host(s) de destino. Esta seção orienta você a usar o Cloud Shell para criar e executar dois manuais - um para criar um grupo de recursos e um segundo para excluir o grupo de recursos. 

1. Crie um arquivo chamado `rg.yml` da seguinte maneira:

  ```azurecli-interactive
  vi rg.yml
  ```

1. Copie o seguinte conteúdo para a janela do Cloud Shell (que agora hospeda uma instância do editor VI):

  ```yml
  - name: My first Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
          name: demoresourcegroup
          location: eastus
  ```

1. Salve o arquivo e saia do editor de VI inserindo `:wq` e pressionando &lt;Enter>.

1. Use o comando `ansible-playbook` para executar o manual `rg.yml`:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Você verá resultados semelhantes à seguinte saída:

  ```Output
  PLAY [My first Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Create a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

1. Verifique a implantação:

  ```azurecli-interactive
  az group show -n demoresourcegroup
  ```

1. Agora que você criou o grupo de recursos, crie uma segundo manual do Ansible para excluir o grupo de recursos:

  ```azurecli-interactive
  vi rg2.yml
  ```

1. Copie o seguinte conteúdo para a janela do Cloud Shell (que agora hospeda uma instância do editor VI):

  ```yml
  - name: My second Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
          name: demoresourcegroup
          state: absent
  ```

1. Salve o arquivo e saia do editor de VI inserindo `:wq` e pressionando &lt;Enter>.

1. Use o comando `ansible-playbook` para executar o manual `rg2.yml`:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Você verá resultados semelhantes à seguinte saída:

  ```Output
  The output is as following. 
  PLAY [My second Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Delete a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Criar uma máquina virtual básica no Azure com Ansible](/azure/virtual-machines/linux/ansible-create-vm)
