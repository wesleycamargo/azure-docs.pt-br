---
title: Uso de cloud-init para personalizar uma VM do Linux durante a criação | Microsoft Docs
description: Uso de cloud-init para personalizar uma VM do Linux durante a criação.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: v-livech

---
# Uso de cloud-init para personalizar uma VM do Linux durante a criação
Este artigo mostra como criar um script de inicialização de nuvem para definir o nome do host, atualizar os pacotes instalados e gerenciar contas de usuário. Os scripts de inicialização de nuvem são chamados durante a criação da VM na CLI do Azure.

## Pré-requisitos
Os pré-requisitos são: [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/), [chaves pública e privada SSH](virtual-machines-linux-mac-create-ssh-keys.md) e a [CLI do Azure](../xplat-cli-install.md) alternada para o modo Azure Resource Manager usando `azure config mode arm`.

## Comandos rápidos
Crie um script cloud-init.txt que define o nome do host, atualiza todos os pacotes e adiciona um usuário sudo ao Linux.

```bash
#cloud-config
hostname: exampleServerName
apt_upgrade: true
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

Crie uma VM do Linux usando a inicialização de nuvem para configurá-la durante a inicialização.

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

```

## Introdução
Quando você inicia uma nova VM do Linux, você obtém uma VM padrão do Linux sem nada personalizado nem pronta para suas necessidades. A [inicialização de nuvem](https://cloudinit.readthedocs.org) é uma maneira padrão de inserir um script ou definições de configuração nessa VM do Linux como se ela estivesse sendo inicializada pela primeira vez.

No Azure, há três maneiras diferentes de fazer alterações em uma VM do Linux como se ela estivesse sendo implantada ou inicializada.

* Insira scripts usando a inicialização de nuvem.
* Insira scripts usando a [Extensão VMAccess](virtual-machines-linux-using-vmaccess-extension.md) do Azure.
* Um modelo do Azure usando a inicialização de nuvem.
* Um modelo do Azure usando [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md).

Para inserir scripts a qualquer momento após a inicialização:

* SSH para executar comandos diretamente
* Insira scripts usando a [Extensão VMAccess](virtual-machines-linux-using-vmaccess-extension.md) do Azure de forma obrigatória ou em um modelo do Azure
* Ferramentas de gerenciamento de configuração, como Ansible, Salt, Chef e Puppet.

> [!NOTE]
> \: a Extensão VMAccess executa um script como raiz da mesma forma que com o uso do SSH. No entanto, o uso a extensão de VM habilita vários recursos oferecidos pelo Azure que podem ser úteis, dependendo do cenário.
> 
> 

### Disponibilidade de inicialização de nuvem em aliases de imagem de criação rápida de uma VM do Azure:
| Alias | Editor | Oferta | SKU | Versão | Inicialização de nuvem |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7,2 |mais recente |não |
| CoreOS |CoreOS |CoreOS |Estável |mais recente |sim |
| Debian |credativ |Debian |8 |mais recente |não |
| openSUSE |SUSE |openSUSE |13\.2 |mais recente |não |
| RHEL |Redhat |RHEL |7,2 |mais recente |não |
| UbuntuLTS |Canônico |UbuntuServer |14\.04.4-LTS |mais recente |sim |

A Microsoft está trabalhando com parceiros para incluir a inicialização de nuvem e trabalhar nas imagens que eles fornecem para o Azure.

## Passo a passo detalhado
### Adição de um script cloud-init à criação de VM com a CLI do Azure
Para iniciar um script de inicialização de nuvem ao criar uma VM no Azure, especifique o arquivo de inicialização de nuvem usando o comutador `--custom-data` da CLI do Azure.

```bash
azure group create cloudinitexample westus
```

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud-init.txt

```

### Criação de um script cloud-init para definir o nome do host de uma VM do Linux
Uma das configurações mais simples e mais importantes para qualquer VM do Linux seria o nome do host. Podemos defini-la com facilidade usando o cloud-init com este script.

#### Script de inicialização de nuvem de exemplo chamado `cloud_config_hostname.txt`.
``` bash
#cloud-config
hostname: exampleServerName
```

Durante a inicialização da VM, esse script de inicialização de nuvem define o nome do host como `exampleServerName`.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_hostname.txt

```

Faça logon e verifique o nome do host das novas VMs.

```bash
ssh exampleVM
hostname
exampleServerName
```

### Criação de um script cloud-init para atualizar o Linux
Por segurança, é recomendável que a VM do Ubuntu seja atualizada na primeira inicialização. Usando cloud-init, podemos fazer isso com o script a seguir, dependendo da distribuição do Linux que você esteja usando.

#### Script de inicialização de nuvem de exemplo `cloud_config_apt_upgrade.txt` para a família Debian
```bash
#cloud-config
apt_upgrade: true
```

Depois que o Linux for inicializado, todos os pacotes instalados serão atualizados por meio de `apt-get`.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_apt_upgrade.txt
```

Faça logon e verifique se todos os pacotes foram atualizados.

```bash
ssh exampleVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

### Criação de um script cloud-init para adicionar um usuário ao Linux
Uma das primeiras tarefas em qualquer nova VM do Linux é adicionar um usuário para você ou para evitar o uso de `root`. Chaves SSH são uma melhor prática de segurança e usabilidade e são adicionadas ao arquivo `~/.ssh/authorized_keys` com esse script de inicialização de nuvem.

#### Script de inicialização de nuvem de exemplo `cloud_config_add_users.txt` para a família Debian
```bash
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==exampleuser@slackwarelaptop
```

Depois que o Linux for inicializado, todos os usuários listados serão criados e adicionados ao grupo sudo.

```bash
azure vm create \
--resource-group cloudinitexample \
--name cloudinitexample \
--location westus \
--os-type Linux \
--nic-name cloudinitnicexample \
--vnet-name cloudinitvnetexample \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name cloudinitvsubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
--admin-username ahmet \
--custom-data cloud_config_add_users.txt
```

Faça logon e verifique o usuário recém-criado.

```bash
cat /etc/group
```

Saída

```bash
root:x:0:
<snip />
sudo:x:27:exampleUser
<snip />
exampleUser:x:1000:
```

## Próximas etapas
A inicialização de nuvem está se tornando uma maneira padrão de modificar sua VM do Linux na inicialização. O Azure também tem extensões de VM, que permitem modificar a VM do Linux na inicialização ou durante sua execução. Por exemplo, você pode usar a VMAccessExtension do Azure para redefinir informações de SSH ou de usuário durante a execução da VM. Com a inicialização de nuvem, será necessária uma reinicialização para redefinir a senha.

[Sobre os recursos e extensões de máquina virtual](virtual-machines-linux-extensions-features.md)

[Gerenciar usuários, SSH e verificar ou reparar discos em VMs do Linux do Azure usando a extensão VMAccess](virtual-machines-linux-using-vmaccess-extension.md)

<!---HONumber=AcomDC_0831_2016-->