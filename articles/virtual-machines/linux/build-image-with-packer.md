---
title: Como criar imagens de VM Linux do Azure com o Packer | Microsoft Docs
description: "Saiba como usar o Packer para criar imagens de máquinas virtuais Linux no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: fa30f7b9aebf3b9a3fb1e037983e8460aa76442e
ms.contentlocale: pt-br
ms.lasthandoff: 06/15/2017

---

# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Como usar o Packer para criar imagens de máquina virtual Linux no Azure
Cada VM (máquina virtual) no Azure é criada com base em uma imagem que define a distribuição do Linux e a versão do sistema operacional. As imagens podem incluir configurações e aplicativos pré-instalados. O Azure Marketplace fornece várias imagens internas e de terceiros para as distribuições e os ambientes de aplicativo mais comuns ou você pode criar suas próprias imagens personalizadas adequadas às suas necessidades. Este artigo fornece detalhes sobre como usar a ferramenta de software livre [Packer](https://www.packer.io/) para definir e criar imagens personalizadas no Azure.


## <a name="create-supporting-azure-resources"></a>Criar recursos de suporte do Azure
Durante o processo de build, o Packer cria recursos temporários do Azure conforme cria a VM de origem. Para capturar essa VM de origem para uso como uma imagem, você deve definir um grupo de recursos e uma conta de armazenamento. O resultado do processo de build do Packer é armazenado nesse grupo de recursos e nessa conta de armazenamento.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```

Em seguida, crie uma conta de armazenamento com [az storage account create](/cli/azure/storage/account#create). Os nomes da conta de armazenamento devem ser exclusivos, ter entre 3 e 24 caracteres e conter apenas números e letras minúsculas. O exemplo a seguir cria uma conta de armazenamento chamada *mystorageaccount*:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --sku Standard_LRS
```


## <a name="create-azure-credentials"></a>Criar as credenciais do Azure
O Packer se autentica no Azure usando uma entidade de serviço. Uma entidade de serviço do Azure é uma identidade de segurança que você pode usar com aplicativos, serviços e ferramentas de automação como o Packer. Você controla e define as permissões referentes a quais operações a entidade de serviço pode executar no Azure.

Crie uma entidade de serviço com [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) e gere as credenciais necessárias para o Packer:

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

Um exemplo da saída dos comandos anteriores é o seguinte:

```azurecli
"f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
"0e760437-bf34-4aad-9f8d-870be799c55d",
"72f988bf-86f1-41af-91ab-2d7cd011db47"
```

Para se autenticar no Azure, você também precisa obter sua ID de assinatura do Azure com [az account show](/cli/azure/account#show):

```azurecli
az account show --query [id] --output tsv
```

Você pode usar a saída desses dois comandos na próxima etapa.


## <a name="define-packer-template"></a>Definir modelo do Packer
Para criar imagens, você cria um modelo como um arquivo JSON. No modelo, você define construtores e provisionadores que executam o processo de build real. O Packer tem um [provisionador do Azure](https://www.packer.io/docs/builders/azure.html) que permite definir recursos do Azure, como as credenciais da entidade de serviço criadas na etapa anterior.

Crie um arquivo chamado *ubuntu.json* e cole o conteúdo a seguir. Insira seus próprios valores para o seguinte:

| Parâmetro       | Onde obter |
|-----------------|----------------------------------------------------|
| *client_id*      | Primeira linha da saída do comando create `az ad sp` – *appId* |
| *client_secret*  | Segunda linha da saída do comando create `az ad sp` – *password* |
| *tenant_id*      | Terceira linha da saída do comando create `az ad sp` – *tenant* |
| *subscription_id* | Saída do comando `az account show` |
| *storage_account* | Nome especificado em `az storage account create` |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "resource_group_name": "myResourceGroup",
    "storage_account": "mystorageaccount",

    "capture_container_name": "images",
    "capture_name_prefix": "packer",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04.0-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Este modelo cria uma imagem do Ubuntu 16.04 LTS, instala o NGINX e, em seguida, desprovisiona a VM.

> [!NOTE]
> Se você expandir este modelo para provisionar as credenciais do usuário, ajuste o comando do provisionador que desprovisiona o agente do Azure para que ele indique `-deprovision`, em vez de `deprovision+user`.
> O sinalizador `+user` remove todas as contas de usuário da VM de origem.


## <a name="build-packer-image"></a>Criar uma imagem do Packer
Se você ainda não tiver o Packer instalado no computador local, [siga as instruções de instalação do Packer](https://www.packer.io/docs/install/index.html).

Crie a imagem especificando o arquivo de modelo do Packer da seguinte maneira:

```bash
./packer build ubuntu.json
```

Um exemplo da saída dos comandos anteriores é o seguinte:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> Location          : 'East US'
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : engineering
==> azure-arm:  ->> task : image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> DeploymentName    : 'pkrdphlz1xtcy8n'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> DeploymentName    : 'pkrdphlz1xtcy8n'
==> azure-arm: Getting the VM's IP address ...
==> azure-arm:  -> ResourceGroupName   : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> PublicIPAddressName : 'packerPublicIP'
==> azure-arm:  -> NicName             : 'packerNic'
==> azure-arm:  -> Network Connection  : 'PublicEndpoint'
==> azure-arm:  -> IP Address          : '13.90.250.248'
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /tmp/packer-shell529418469
    azure-arm: Get:1 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
    azure-arm: Hit:2 http://azure.archive.ubuntu.com/ubuntu xenial InRelease
    azure-arm: Get:3 http://azure.archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
    azure-arm: Get:4 http://azure.archive.ubuntu.com/ubuntu xenial-backports InRelease [102 kB]
    [snip]
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine's properties ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm:  -> OS Disk           : 'https://mystorageaccount.blob.core.windows.net/images/pkroshlz1xtcy8n.vhd'
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm: Capturing image ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm:  -> ComputeName       : 'pkrvmhlz1xtcy8n'
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-hlz1xtcy8n'
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : 'https://mystorageaccount.blob.core.windows.net/images/pkroshlz1xtcy8n.vhd'
Build 'azure-arm' finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

StorageAccountLocation: eastus
OSDiskUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd
OSDiskUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd?se=2017-07-08T20%3A57%3A53Z&sig=yl1yl3I2gKnO0I%2B7paw%2FQzKT5dawf5i%2B
LPmATMt5ot4%3D&sp=r&sr=b&sv=2015-02-21
TemplateUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.643f37d7-5a5d-43bf-96ed-2d598ada6e65.json
TemplateUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.643f37d7-5a5d-43bf-96ed-2d598ada6e65.json?se=2017-07-08T20%3A57%3A53Z&sig=GB1iSl0hhw1ZYG4nl%2BCfR9WEaquCF
OEhNtKlvp%2B5TdE%3D&sp=r&sr=b&sv=2015-02-21
```


## <a name="create-azure-image"></a>Criar uma imagem do Azure
O resultado do processo de build do Packer é um VHD (disco rígido virtual) na conta de armazenamento especificada. Crie uma Imagem do Azure com base nesse VHD com [az image create](/cli/azure/image#create) e especifique o caminho `OSDiskUri` indicado ao final da saída do build do Packer. O seguinte exemplo cria uma Imagem chamada `myImage`:

```azurecli
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --os-type linux \
    --source https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.643f37d7-5a5d-43bf-96ed-2d598ada6e65.vhd
```

Essa Imagem pode ser usada para criar VMs em sua assinatura do Azure. Você não está limitado a criar uma VM no mesmo grupo de recursos da Imagem de origem.


## <a name="create-vm-from-azure-image"></a>Criar uma VM com base em uma Imagem do Azure
Agora você pode criar uma VM com base na Imagem com [az vm create](/cli/azure/vm#create). Especifique a Imagem criada com o parâmetro `--image`. O seguinte exemplo cria uma VM chamada *myVM* com base em *myImage* e gera as chaves SSH, caso ainda não existam:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

São necessários alguns minutos para criar a VM. Depois que a VM for criada, amote o `publicIpAddress` exibido pela CLI do Azure. Esse endereço é usado para acessar o site do NGINX em um navegador da Web.

Para permitir o tráfego da web para acessar sua VM, abra a porta 80 da Internet com [az vm open-port](/cli/azure/vm#open-port):

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Testar a VM e o NGINX
Agora, abra um navegador da Web e digite `http://publicIpAddress` na barra de endereços. Forneça seu próprio endereço de IP público do processo de criação da máquina virtual. A página padrão do NGINX é exibida como no seguinte exemplo:

![Site padrão NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você usou o Packer para criar uma imagem de VM com o NGINX já instalado. Use essa imagem de VM junto com os fluxos de trabalho de implantação existentes para, por exemplo, implantar o aplicativo em VMs criadas com base na Imagem com o Ansible, Chef ou Puppet.

Para obter outros modelos de exemplo do Packer para outras distribuições do Linux, consulte [este repositório GitHub](https://github.com/hashicorp/packer/tree/master/examples/azure).
