---
title: "Use o Ansible para gerenciar seus inventários dinâmicos do Azure"
description: "Saiba como usar o Ansible para gerenciar seus inventários dinâmicos do Azure"
ms.service: ansible
keywords: "ansible, azure, devops, bash, cloudshell, inventário dinâmico"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 8753d039582abdf22f105bf7f139a35c224e7c59
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Use o Ansible para gerenciar seus inventários dinâmicos do Azure
O Ansible pode ser usado para extrair informações de inventário de várias fontes (incluindo as origens de nuvem, como o Azure) em um *inventário dinâmico*. Neste artigo, você usa o [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md) para configurar um Inventário Dinâmico do Azure Ansible e assim cria duas máquinas virtuais, marca uma dessas máquinas virtuais e instala o Nginx nela.

## <a name="prerequisites"></a>pré-requisitos

- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Credenciais do Azure** - [Criar credenciais do Azure e configurar o Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>Criar as máquinas virtuais de teste

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).

1. Crie um grupo de recursos do Azure para manter as máquinas virtuais para este tutorial.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Crie duas máquinas virtuais do Linux no Azure usando uma das seguintes técnicas:

    - **Guia estratégico do Ansible**: o artigo [Criar uma máquina virtual básica no Azure com o Ansible](/azure/virtual-machines/linux/ansible-create-vm) ilustra como criar uma máquina virtual a partir de um guia estratégico do Ansible. Se você usar um guia estratégico para definir uma ou ambas as máquinas virtuais, verifique se a conexão SSH é usada em vez de uma senha.

    - **CLI do Azure** – emita cada um dos seguintes comandos no Cloud Shell para criar as duas máquinas virtuais:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-virtual-machine"></a>Marque uma máquina virtual
Você pode [usar marcas para organizar os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) por categorias definidas pelo usuário. 

Insira o seguinte comando [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az_resource_tag) para marcar a máquina virtual `ansible-inventory-test-vm1` com a chave `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/&lt;YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Gerar um inventário dinâmico
Assim que as máquinas virtuais estiverem definidas (e marcadas), é hora de gerar o inventário dinâmico. O Ansible fornece um script Python chamado [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) que gera um inventário dinâmico de recursos do Azure fazendo solicitações de API para o Azure Resource Manager. As etapas a seguir o orientam a usar o script `azure_rm.py` para conectar-se à sua máquina virtual do Azure de dois testes:

1. Use o comando `wget` do GNU para recuperar o script `azure_rm.py`:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Use o comando `chmod` para alterar as permissões de acesso para o script `azure_rm.py`. O comando a seguir usa o parâmetro `+x` para permitir a execução (executando) do arquivo especificado (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Use o [comando ansible](https://docs.ansible.com/ansible/2.4/ansible.html) para se conectar ao grupo de recursos: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Uma vez conectado, você verá resultados semelhantes à seguinte saída:

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

## <a name="enable-the-virtual-machine-tag"></a>Habilitar a marca da máquina virtual
Assim que tiver configurado a marca desejada, você precisa “habilitar” a marca. Uma maneira de habilitar uma marca é exportando a marca para uma variável de ambiente chamada `AZURE_TAGS` por meio do comando **exportar**:

```azurecli-interactive
export AZURE_TAGS=nginx
```

Assim que a marca tiver sido exportada, você pode tentar o comando `ansible` novamente:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Agora você vê apenas uma máquina virtual (aquele cuja marca corresponde ao valor exportado para a variável de ambiente **AZURE_TAGS**):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Configurar Nginx na VM marcada
A finalidade das marcas é habilitar a capacidade de trabalhar de forma rápida e fácil com subgrupos das máquinas virtuais. Por exemplo, digamos que você deseja instalar o Nginx apenas em máquinas virtuais para as quais você atribuiu uma marca de `nginx`. As etapas a seguir ilustram como é fácil:

1. Crie um arquivo (para conter seu guia estratégico) chamado `nginx.yml` da seguinte maneira:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Insira o código a seguir no arquivo `nginx.yml` recém-criado:

    ```yml
    - name: Install and start Nginx on an Azure virtual machine
    hosts: azure
    become: yes
    tasks:
    - name: install nginx
        apt: pkg=nginx state=installed
        notify:
        - start nginx

    handlers:
    - name: start nginx
        service: name=nginx state=started
    ```

1. Execute o guia estratégico `nginx.yml`:

  ```azurecli-interactive
  ansible-playbook -i azure_rm.py nginx.yml
  ```

1. Depois de executar o guia estratégico, você verá resultados semelhantes à seguinte saída:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] **********

    TASK [Gathering Facts] **********
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] **********
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] **********
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP **********
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Testar a instalação do Nginx
Esta seção ilustra uma técnica para testar se o Nginx está instalado em sua máquina virtual.

1. Use o comando [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az_vm_list_ip_addresses) para recuperar o endereço IP da máquina virtual `ansible-inventory-test-vm1`. O valor retornado (endereço IP da máquina virtual) é usado como o parâmetro para o comando SSH para se conectar à máquina virtual.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. O comando [nginx-v](https://nginx.org/en/docs/switches.html) geralmente é usado para imprimir a versão do Nginx. No entanto, ele também pode ser usado para determinar se o Nginx está instalado. Insira-o enquanto estiver conectado à máquina virtual `ansible-inventory-test-vm1`.

    ```azurecli-interactive
    nginx -v
    ```

1. Assim que executa o comando `nginx -v`, você vê a versão do Nginx (segunda linha) que indica se o Nginx está instalado.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Pressione a combinação **&lt;Ctrl > D** no teclado para desconectar a sessão SSH.

1. Realizar as etapas anteriores para a máquina virtual `ansible-inventory-test-vm2` gera uma mensagem informativa que indica onde você pode obter o Nginx (o que implica que você não o tem instalado neste momento):

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Criar uma máquina virtual básica no Azure com o Ansible](/azure/virtual-machines/linux/ansible-create-vm)