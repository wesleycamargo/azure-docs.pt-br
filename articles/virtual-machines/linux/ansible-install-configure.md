---
title: Instalar o Ansible em máquinas virtuais do Azure
description: Saber como instalar e configurar o Ansible para gerenciar recursos do Azure no Ubuntu, no CentOS e no SLES
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: quickstart
ms.date: 08/21/2018
ms.openlocfilehash: fcb2da93a39bd4e1a81f7767dc40b3a24fd7d213
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250625"
---
# <a name="install-ansible-on-azure-virtual-machines"></a>Instalar o Ansible em máquinas virtuais do Azure

O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. Você pode usar o Ansible para gerenciar suas máquinas virtuais (VMs) no Azure, da mesma forma que faria com qualquer outro recurso. Este artigo fornece detalhes sobre como instalar o Ansible e os módulos necessários do SDK do Python no Azure para algumas das distribuições de Linux mais comuns. Você pode instalar o Ansible em outras distribuições ajustando os pacotes instalados para se adaptar a sua plataforma específica. Para criar recursos do Azure de maneira segura, você também aprenderá a criar e definir credenciais para o Ansible usar. Para obter uma lista de ferramentas adicionais disponíveis no Cloud Shell, consulte [Recursos e ferramentas para Bash no Azure Cloud Shell](../../cloud-shell/features.md#tools).

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure** - Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Acesso ao Linux ou a uma máquina virtual do Linux** - Se você não tiver acesso a um computador do Linux, crie uma [máquina virtual do Linux](/virtual-machines/linux/quick-create-cli.md).

- **Principal do serviço do Azure**: siga as instruções na seção **Criar o principal do serviço** no artigo, [ Crie um objeto de serviço do Azure com o Azure CLI 2.0 ](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Anote os valores para o **appId**, **displayName**, **senha** e **locatário**.

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Instalar o Ansible em uma máquina virtual do Linux do Azure

Entre no seu computador do Linux e selecione uma das distribuições a seguir para obter instruções sobre como instalar o Ansible:

- [CentOS 7.4](#centos-74)
- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Instale os pacotes necessários para os módulos de SDK de Python do Azure e o Ansible, inserindo os comandos a seguir em uma janela de Bash ou terminal:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Siga as instruções descritas na seção [Criar credenciais do Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Instale os pacotes necessários para os módulos de SDK de Python do Azure e o Ansible, inserindo os comandos a seguir em uma janela de Bash ou terminal:


```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Siga as instruções descritas na seção [Criar credenciais do Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Instale os pacotes necessários para os módulos de SDK de Python do Azure e o Ansible, inserindo os comandos a seguir em uma janela de Bash ou terminal:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
    python-devel libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]

# Remove conflicting Python cryptography package
sudo pip uninstall -y cryptography
```

Siga as instruções descritas na seção [Criar credenciais do Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Criar as credenciais do Azure

A combinação de ID da assinatura com as informações retornadas ao criar a entidade de serviço é usada para configurar as credenciais do Ansible de uma das duas maneiras a seguir:

- [Criar um arquivo de credenciais do Ansible](#file-credentials)
- [Usar variáveis de ambiente Ansible](#env-credentials)

Se você pretende usar ferramentas como o Ansible Tower ou Jenkins, você precisará usar a opção de declarar os valores da entidade de serviço como variáveis de ambiente.

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Criar arquivo de credenciais do Ansible

Esta seção explica como criar um arquivo de credenciais locais para fornecer credenciais para o Ansible. Para saber mais sobre como definir credenciais do Ansible, veja [Fornecendo credenciais para os módulos do Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

Para um ambiente de desenvolvimento, crie um arquivo de *credenciais* para o Ansible em sua máquina virtual host da seguinte maneira:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

Insira as seguintes linhas no arquivo de *credenciais* - substituindo os espaços reservados pelas informações da criação de entidade de serviço.

```bash
[default]
subscription_id=<your-subscription_id>
client_id=<security-principal-appid>
secret=<security-principal-password>
tenant=<security-principal-tenant>
```

Salve e feche o arquivo.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Usar variáveis de ambiente Ansible

Esta seção explica como configurar suas credenciais do Ansible exportando-as como variáveis de ambiente.

Em uma janela de terminal ou Bash, execute os comandos a seguir:

```bash
export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
export AZURE_CLIENT_ID=<security-principal-appid>
export AZURE_SECRET=<security-principal-password>
export AZURE_TENANT=<security-principal-tenant>
```

## <a name="verify-the-configuration"></a>Verificar a configuração
Para verificar se a configuração foi bem-sucedida, agora você pode usar o Ansible para criar um grupo de recursos.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Usar o Ansible para criar uma máquina virtual do Linux no Azure](./ansible-create-vm.md)