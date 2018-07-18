---
title: Instalar e configurar Ansible para uso com máquinas virtuais do Azure | Microsoft Docs
description: Saber como instalar e configurar o Ansible para gerenciar recursos do Azure no Ubuntu, no CentOS e no SLES
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/04/2018
ms.author: iainfou
ms.openlocfilehash: e6fad548eda35d1832cb4ecc2fd9bdabf825f361
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896122"
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Instalar e configurar o Ansible para gerenciar máquinas virtuais no Azure

O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. Você pode usar o Ansible para gerenciar suas máquinas virtuais (VMs) no Azure, da mesma forma que faria com qualquer outro recurso. Este artigo fornece detalhes sobre como instalar o Ansible e os módulos necessários do SDK do Python no Azure para algumas das distribuições de Linux mais comuns. Você pode instalar o Ansible em outras distribuições ajustando os pacotes instalados para se adaptar a sua plataforma específica. Para criar recursos do Azure de maneira segura, você também aprenderá a criar e definir credenciais para o Ansible usar.

Para obter mais opções de instalação e etapas para outras plataformas, veja o [Guia de instalação do Ansible](https://docs.ansible.com/ansible/intro_installation.html).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que esteja em execução a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli).

## <a name="install-ansible"></a>Instalar o Ansible

Uma das maneiras mais fáceis de usar o Ansible com Azure é com o Azure Cloud Shell, uma experiência de shell baseada em navegador para gerenciar e desenvolver recursos do Azure. O Ansible é pré-instalado no Cloud Shell, portanto, você pode ignorar as instruções sobre como instalar o Ansible e acessar [Criar credenciais do Azure](#create-azure-credentials). Para obter uma lista de ferramentas adicionais também disponíveis no Cloud Shell, consulte [Recursos e ferramentas para Bash no Azure Cloud Shell](../../cloud-shell/features.md#tools).

As instruções a seguir mostram como criar uma VM do Linux para várias distribuições e, em seguida, instalar o Ansible. Se não for necessário criar uma VM Linux, ignore essa primeira etapa para criar um grupo de recursos do Azure. Se for necessário criar uma VM, primeiro crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Agora, selecione uma das distribuições a seguir para obter instruções sobre como criar uma VM, se necessário, e instale o Ansible:

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Se necessário, crie uma VM com [az vm create](/cli/azure/vm#az_vm_create). O exemplo a seguir cria uma VM chamada *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image OpenLogic:CentOS:7.4:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a sua VM usando o `publicIpAddress` observado na saída da operação de criação da VM:

```bash
ssh azureuser@<publicIpAddress>
```

Na sua VM, instale os pacotes necessários para os módulos de SDK de Python no Azure e no Ansible da seguinte maneira:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Agora vá para [Criar credenciais do Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Se necessário, crie uma VM com [az vm create](/cli/azure/vm#az_vm_create). O exemplo a seguir cria uma VM chamada *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a sua VM usando o `publicIpAddress` observado na saída da operação de criação da VM:

```bash
ssh azureuser@<publicIpAddress>
```

Na sua VM, instale os pacotes necessários para os módulos de SDK de Python no Azure e no Ansible da seguinte maneira:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

Agora vá para [Criar credenciais do Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Se necessário, crie uma VM usando [az vm create](/cli/azure/vm#az_vm_create). O exemplo a seguir cria uma VM chamada *myVMAnsible*:

```azurecli
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroup \
    --image SUSE:SLES:12-SP2:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a sua VM usando o `publicIpAddress` observado na saída da operação de criação da VM:

```bash
ssh azureuser@<publicIpAddress>
```

Na sua VM, instale os pacotes necessários para os módulos de SDK de Python no Azure e no Ansible da seguinte maneira:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Agora vá para [Criar credenciais do Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Criar as credenciais do Azure

O Ansible comunica-se com o Azure usando um nome de usuário e uma senha ou uma entidade de serviço. Uma entidade de serviço do Azure é uma identidade de segurança que você pode usar com aplicativos, serviços e ferramentas de automação como o Ansible. Você controla e define as permissões referentes a quais operações a entidade de serviço pode executar no Azure. Para aprimorar a segurança, em vez de apenas fornecer um nome de usuário e uma senha, este exemplo cria uma entidade de serviço básica.

No computador host ou no Azure Cloud Shell, crie uma entidade de serviço usando [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac). As credenciais que o Ansible precisa são exibidas na tela:

```azurecli-interactive
az ad sp create-for-rbac --query '{"client_id": appId, "secret": password, "tenant": tenant}'
```

Um exemplo da saída dos comandos anteriores é o seguinte:

```json
{
  "client_id": "eec5624a-90f8-4386-8a87-02730b5410d5",
  "secret": "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Para autenticar no Azure, também é necessário obter a ID da assinatura do Azure usando [az account show](/cli/azure/account#az_account_show):

```azurecli-interactive
az account show --query "{ subscription_id: id }"
```

Você pode usar a saída desses dois comandos na próxima etapa.

## <a name="create-ansible-credentials-file"></a>Criar arquivo de credenciais do Ansible

Para fornecer credenciais para o Ansible, você poderá definir variáveis de ambiente ou criar um arquivo de credenciais locais. Para saber mais sobre como definir credenciais do Ansible, veja [Fornecendo credenciais para os módulos do Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

Para um ambiente de desenvolvimento, crie um arquivo de *credenciais* para Ansible na VM Host. Crie um arquivo de credenciais na VM onde você instalou o Ansible em uma etapa anterior:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

O próprio arquivo de *credenciais* combina a ID da assinatura com a saída da criação de uma entidade de serviço. A saída do comando anterior [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) é a mesma conforme o necessário para *client_id*, *secret* e *tenant*. O arquivo de *credenciais* de exemplo a seguir mostra esses valores correspondentes à saída anterior. Insira seus próprios valores da seguinte maneira:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```

Salve e feche o arquivo.

## <a name="use-ansible-environment-variables"></a>Usar variáveis de ambiente Ansible

Se ferramentas como Ansible Tower ou Jenkins forem utilizadas, será necessário definir as variáveis de ambiente. Essa etapa poderá ser ignorada se você usar o cliente Ansible e o arquivo de credenciais do Azure criado na etapa anterior. As variáveis de ambiente definem as mesmas informações que o arquivo de credenciais do Azure:

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Próximas etapas

Agora você tem o Ansible e os módulos necessários do SDK do Python do Azure instalados e as credenciais definidas para o Ansible usar. Saiba como [criar uma VM com o Ansible](ansible-create-vm.md). Você também pode aprender a [criar uma VM do Azure completa e os recursos de apoio com o Ansible](ansible-create-complete-vm.md).
