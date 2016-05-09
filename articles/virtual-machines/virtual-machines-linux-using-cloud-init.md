<properties
    pageTitle="Uso de cloud-init para personalizar uma VM do Linux durante a criação | Microsoft Azure"
    description="Uso de cloud-init para personalizar uma VM do Linux durante a criação."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/22/2016"
    ms.author="v-livech"
/>

# Uso de cloud-init para personalizar uma VM do Linux durante a criação

Neste artigo, criaremos scripts cloud-init para definir o nome do host, atualizar os pacotes instalados e gerenciar contas de usuário. Em seguida, iniciaremos esses scripts cloud-init durante a criação da VM do Linux usando [a CLI do Azure](../xplat-cli-install.md).

## Pré-requisitos

Os pré-requisitos são: [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/), [chaves públicas e privadas do SSH](virtual-machines-linux-mac-create-ssh-keys.md), um grupo de recursos do Azure onde as VMs do Linux serão iniciadas e a CLI do Azure instalada e alternada para o modo ARM usando `azure config mode arm`.

## Introdução

Quando você inicia uma nova VM do Linux, obtém uma VM do Linux padrão com nada personalizado ou pronto para suas necessidades. [Cloud-init](https://cloudinit.readthedocs.org) é uma maneira padrão de inserir um script ou configurações nessa VM do Linux como se ela estivesse sendo inicializada pela primeira vez.

No Azure, há três maneiras diferentes de fazer alterações em uma VM do Linux como se ela estivesse sendo inicializada.

- Você pode inserir scripts com cloud-init.
- Você pode inserir scripts usando a [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) do Azure.
- Você pode especificar configurações personalizadas em um modelo do Azure e usá-lo para iniciar e personalizar sua VM do Linux, o que inclui suporte ao cloud-ini, bem como à extensão de VM CustomScript e muitas outras.

Para inserir scripts a qualquer momento, você pode:

- usar o SSH para executar comandos diretamente, usando a [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) do Azure de forma imperativa ou em um modelo do Azure, ou pode usar ferramentas de gerenciamento de configuração comuns, como Ansible, Salt, Chef e Puppet, que funcionam sobre SSH depois que a VM conclui a inicialização.

OBSERVAÇÃO: embora uma [CustomScriptExtention](virtual-machines-linux-extensions-customscript.md) simplesmente execute um script como raiz da mesma forma que aconteceria se você estivesse usando o SSH, o uso da extensão de VM habilita diversos recursos oferecidos pelo Azure e que poderão ser úteis, dependendo do cenário.

## Comandos rápidos

```bash
# Create a hostname cloud-init script
#cloud-config
hostname: exampleServerName

# Create an update Linux on first boot cloud-init script for Debian Family
#cloud-config
apt_upgrade: true

# Create an add a user cloud-init script
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa
AAAAB3NzaC1yc2EAAAADAQABAAABAQDf0q4PyG0doiBQYV7OlOxbRjle<snip />== exampleuser@slackwarelaptop

```

## Passo a passo detalhado

### Adição de um script cloud-init à criação de VM com a CLI do Azure

Para iniciar um script cloud-init ao criar uma VM no Azure, especifique o arquivo cloud-init usando a opção `--custom-data` da CLI do Azure.

OBSERVAÇÃO: embora este artigo discuta o uso da opção `--custom-data` para arquivos cloud-init, você também pode passar um código arbitrário ou arquivos usando essa opção. Se a VM do Linux já souber o que fazer com esses arquivos, eles serão automaticamente executados.

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_init_script.txt
```

### Criação de um script cloud-init para definir o nome do host de uma VM do Linux

Uma das configurações mais simples e mais importantes para qualquer VM do Linux seria o nome do host. Podemos defini-la com facilidade usando o cloud-init com este script.

#### Script cloud-init de exemplo chamado `cloud_config_hostname.txt`.

``` bash
#cloud-config
hostname: exampleServerName
```

Durante a inicialização da VM, esse script cloud-init define o nome do host como `exampleServerName`.

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_hostname.txt
```

Faça logon e verifique o nome do host das novas VMs.

```bash
bill@slackware$ ssh exampleVM
bill@ubuntu$ hostname
bill@ubuntu$ exampleServerName
```

### Criação de um script cloud-init para atualizar o Linux

Por segurança, convém que sua VM do Ubuntu seja atualizada na primeira inicialização. Usando cloud-init, podemos fazer isso com o script a seguir, dependendo da distribuição do Linux que você esteja usando.

#### Script cloud-init de exemplo `cloud_config_apt_upgrade.txt` para a família Debian

```bash
#cloud-config
apt_upgrade: true
```

Depois que a nova VM do Linux tiver sido inicializada, ela atualizará imediatamente todos os pacotes instalados via `apt-get`.

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_apt_upgrade.txt
```

Faça logon e verifique se todos os pacotes foram atualizados.

```bash
bill@slackware$ ssh exampleVM
bill@ubuntu$ sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

### Criação de um script cloud-init para adicionar um usuário ao Linux

Uma das primeiras tarefas em qualquer VM do Linux nova é adicionar um usuário para você ou para evitar o uso de `root`. Isso será ótimo por motivos de segurança e de usabilidade depois que você adicionar sua chave pública SSH ao arquivo `~/.ssh/authorized_keys` do usuário para logons SSH seguros e sem senha.

#### Script cloud-init de exemplo `cloud_config_add_users.txt` para família Debian

```bash
#cloud-config
users:
  - name: exampleUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa
AAAAB3NzaC1yc2EAAAADAQABAAABAQDf0q4PyG0doiBQYV7OlOxbRjle<snip />== exampleuser@slackwarelaptop
```

Depois que a nova VM do Linux tiver sido inicializada, criará o novo usuário e o adicionará ao grupo sudo.

```bash
bill@slackware$ azure vm create \
--resource-group exampleRG \
--name exampleVM \
--location westus \
--admin-username exampleAdminUserName \
--os-type Linux \
--nic-name exampleNIC \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--custom-data cloud_config_add_users.txt
```

Faça logon e verifique o usuário recém-criado.

```bash
bill@slackware$ cat /etc/group
root:x:0:
<snip />
sudo:x:27:exampleUser
<snip />
exampleUser:x:1000:
```

<!---HONumber=AcomDC_0427_2016-->