<properties
   pageTitle="Criar uma VM do Linux no Azure usando a CLI| Microsoft Azure"
   description="Criar uma VM do Linux no Azure usando a CLI."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="05/03/2016"
   ms.author="v-livech"/>


# Criar uma VM do Linux no Azure usando a CLI

> [AZURE.NOTE] Se tiver alguns minutos, ajude-nos a melhorar a documentação de VMs do Linux do Azure, respondendo a essa [pesquisa rápida](https://aka.ms/linuxdocsurvey) sobre suas experiências. Cada resposta nos ajuda a realizar seu trabalho.

Este artigo mostra como implantar rapidamente uma nova Máquina Virtual do Linux no Azure usando o comando `azure vm quick-create` da CLI do Azure. O comando `quick-create` implanta uma Máquina Virtual cercada de uma infraestrutura básica que você pode usar para criar um protótipo ou testar um conceito muito rapidamente (pense nisso como a maneira mais rápida para um shell de bash Linux). O artigo requer uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e [a CLI do Azure](../xplat-cli-install.md) conectada (`azure login`) e no modo do gerenciador de recursos (`azure config mode arm`). Você também pode implantar rapidamente uma VM do Linux usando o [Portal do Azure](virtual-machines-linux-quick-create-portal.md).

## Resumo rápido do comando

Um comando para implantar uma VM do CoreOS e conectar sua chave SSH:

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
```

## Implantar a VM do Linux

Usando o mesmo comando acima, o seguinte mostra cada prompt junto com a saída esperada, mas usa o alias RHEL para criar uma VM do RedHat Enteprise Linux 7.2.

## Usar um alias ImageURN

O comando `quick-create` da CLI do Azure tem aliases mapeados para as distribuições do sistema operacional mais comuns. A tabela a seguir lista os aliases de distribuição (a partir da CLI do Azure versão 0.10). Todas as implantações que usam `quick-create` assumem o padrão de VMs gravadas em backup no armazenamento SSD, oferecendo uma experiência de alto desempenho.

| Alias | Editor | Oferta | SKU | Versão |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS | OpenLogic | Centos | 7,2 | mais recente |
| CoreOS | CoreOS | CoreOS | Estável | mais recente |
| Debian | credativ | Debian | 8 | mais recente |
| openSUSE | SUSE | openSUSE | 13\.2 | mais recente |
| RHEL | Redhat | RHEL | 7,2 | mais recente |
| SLES | SLES | SLES | 12-SP1 | mais recente |
| UbuntuLTS | Canônico | UbuntuServer | 14\.04.4-LTS | mais recente |



Para a opção **ImageURN** (`-Q`) usaremos `RHEL` para implantar uma VM do RedHat Enterprise Linux 7.2. (Esses sete aliases representam uma pequena parte do sistema operacional disponível no Azure; encontre mais imagens no Marketplace [procurando uma imagem](virtual-machines-linux-cli-ps-findimage.md), ou você pode [carregar sua própria imagem personalizada](virtual-machines-linux-create-upload-generic.md)).

No passo a passo do comando a seguir, substitua os prompts pelos valores de seu próprio ambiente; usamos valores de "exemplo".

Siga os prompts e digite seus próprios nomes

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q RHEL
```

A saída deve ser semelhante ao bloco de saída a seguir.

```bash
info:    Executing command vm quick-create
Resource group name: rhel-quick
Virtual machine name: rhel
Location name: westus
Operating system Type [Windows, Linux]: linux
User name: ops
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "rhel"
info:    Verifying the public key SSH file: /Users/ops/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli1630678171193501687
info:    Could not find the storage account "cli1630678171193501687", trying to create new one
+ Creating storage account "cli1630678171193501687" in "westus"
+ Looking up the storage account cli1630678171193501687
+ Looking up the NIC "rhel-westu-1630678171-nic"
info:    An nic with given name "rhel-westu-1630678171-nic" not found, creating a new one
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
info:    Preparing to create new virtual network and subnet
+ Creating a new virtual network "rhel-westu-1630678171-vnet" [address prefix: "10.0.0.0/16"] with subnet "rhel-westu-1630678171-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "rhel-westu-1630678171-vnet"
+ Looking up the subnet "rhel-westu-1630678171-snet" under the virtual network "rhel-westu-1630678171-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "rhel-westu-1630678171-pip"
info:    PublicIP with given name "rhel-westu-1630678171-pip" not found, creating a new one
+ Creating public ip "rhel-westu-1630678171-pip"
+ Looking up the public ip "rhel-westu-1630678171-pip"
+ Creating NIC "rhel-westu-1630678171-nic"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the storage account clisto909893658rhel
+ Creating VM "rhel"
+ Looking up the VM "rhel"
+ Looking up the NIC "rhel-westu-1630678171-nic"
+ Looking up the public ip "rhel-westu-1630678171-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/rhel-quick/providers/Microsoft.Compute/virtualMachines/rhel
data:    ProvisioningState               :Succeeded
data:    Name                            :rhel
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :RedHat
data:        Offer                       :RHEL
data:        Sku                         :7.2
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic5abbc145c0242c1-os-1462425492101
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1630678171193501687.blob.core.windows.net/vhds/clic5abbc145c0242c1-os-1462425492101.vhd
data:
data:    OS Profile:
data:      Computer Name                 :rhel
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-0F-DD
data:          Provisioning State        :Succeeded
data:          Name                      :rhel-westu-1630678171-nic
data:          Location                  :westus
data:            Public IP address       :104.42.236.196
data:            FQDN                    :rhel-westu-1630678171-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto909893658rhel.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Agora, você pode usar o SSH em sua VM na porta SSH 22 padrão, e o FQDN (nome de domínio totalmente qualificado) listado na saída acima. (Você também pode usar o endereço IP listado).

```bash
ssh ops@rhel-westu-1630678171-pip.westus.cloudapp.azure.com
```
O processo de logon deve ter uma aparência semelhante à seguinte:

```bash
The authenticity of host 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com (104.42.236.196)' can't be established.
RSA key fingerprint is 0e:81:c4:36:2d:eb:3c:5a:dc:7e:65:8a:3f:3e:b0:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'rhel-westu-1630678171-pip.westus.cloudapp.azure.com,104.42.236.196' (RSA) to the list of known hosts.
[ops@rhel ~]$ ls -a
.  ..  .bash_logout  .bash_profile  .bashrc  .cache  .config  .ssh
```

## Próximas etapas

O `azure vm quick-create` é a maneira de implantar rapidamente uma VM para que você possa fazer logon em um shell bash e começar a trabalhar. O uso de `vm quick-create` não oferece os benefícios adicionais de um ambiente complexo. Para implantar uma VM do Linux personalizada para sua infraestrutura, você pode seguir qualquer um dos artigos abaixo.

- [Usar um modelo do Azure Resource Manager para criar uma implantação específica](virtual-machines-linux-cli-deploy-templates.md)
- [Criar seu próprio ambiente personalizado para uma VM do Linux usando os comandos da CLI do Azure diretamente](virtual-machines-linux-create-cli-complete.md).
- [Criar uma VM do Linux Protegida por SSH no Azure usando Modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

Esses artigos o ajudarão a criar uma infraestrutura do Azure, bem como quaisquer ferramentas de implantação da infraestrutura patenteadas e de fonte aberta, de configuração e orquestração.

<!---HONumber=AcomDC_0817_2016-->