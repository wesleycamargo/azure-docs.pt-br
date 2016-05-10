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
   ms.date="04/29/2016"
   ms.author="v-livech"/>


# Criar uma VM do Linux no Azure usando a CLI

Este artigo mostra como implantar rapidamente uma nova Máquina Virtual do Linux no Azure usando o comando `azure vm quick-create` da CLI do Azure. O comando `quick-create` implanta uma Máquina Virtual cercada de uma infraestrutura básica que você pode usar para criar um protótipo ou testar um conceito muito rapidamente. Pense nisso como a maneira mais rápida para um shell bash do Linux. O artigo requer uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e [a CLI do Azure](../xplat-cli-install.md) conectada (`azure login`) e no modo do gerenciador de recursos (`azure config mode arm`). Você também pode implantar rapidamente uma VM do Linux usando o [Portal do Azure](virtual-machines-linux-quick-create-portal.md).

## Resumo rápido do comando

Um comando para implantar a VM e conectar sua chave SSH

```
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

## Implantar a VM do Linux

Usando o mesmo comando acima, mostraremos cada prompt, juntamente com a saída que você deve esperar ver.

Para ImageURN, usaremos `canonical:ubuntuserver:14.04.2-LTS:latest` para implantar uma VM do Ubuntu 14.04. (Para localizar uma imagem no Marketplace, [pesquise uma imagem](virtual-machines-linux-cli-ps-findimage.md) ou [carregue sua própria imagem personalizada](virtual-machines-linux-create-upload-generic.md).)

No passo a passo do comando a seguir, substitua os prompts pelos valores de seu próprio ambiente; usamos valores de "exemplo". A saída deve ser semelhante ao bloco de saída a seguir.

Siga os prompts e digite seus próprios nomes

```bash
azure vm quick-create -M ~/.ssh/azure_id_rsa.pub
```

Saída

```bash
info:    Executing command vm quick-create
Resource group name: exampleRGname
Virtual machine name: exampleVMname
Location name: westus
Operating system Type [Windows, Linux]: Linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: Canonical:UbuntuServer:14.04.4-LTS:latest
User name: ahmet
Password: ************************************************
Confirm password: ************************************************
+ Looking up the VM "exampleVMname"
info:    Verifying the public key SSH file: /home/ahmet/.ssh/azure_id_rsa.pub
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli38948918364134011018
info:    Could not find the storage account "cli38948918364134011018", trying to create new one
+ Creating storage account "cli38948918364134011018" in "westus"
+ Looking up the storage account cli38948918364134011018
+ Looking up the NIC "examp-westu-3894891836-nic"
info:    An nic with given name "examp-westu-3894891836-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-3894891836-vnet"
info:    Preparing to create new virtual network and subnet
| Creating a new virtual network "examp-westu-3894891836-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-3894891836-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-3894891836-vnet"
+ Looking up the subnet "examp-westu-3894891836-snet" under the virtual network "examp-westu-3894891836-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-3894891836-pip"
info:    PublicIP with given name "examp-westu-3894891836-pip" not found, creating a new one
+ Creating public ip "examp-westu-3894891836-pip"
+ Looking up the public ip "examp-westu-3894891836-pip"
+ Creating NIC "examp-westu-3894891836-nic"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Creating VM "exampleVMname"
+ Looking up the VM "exampleVMname"
+ Looking up the NIC "examp-westu-3894891836-nic"
+ Looking up the public ip "examp-westu-3894891836-pip"
data:    Id                              :/subscriptions/<**subscriptionsID**>/resourceGroups/exampleRGname/providers/Microsoft.Compute/virtualMachines/exampleVMname
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMname
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_D1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clife36db80ae0539d2-os-1460152163612
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://<**subscriptionsID**>.blob.core.windows.net/vhds/clife36db80ae0539d2-os-1460152163612.vhd
data:
data:    OS Profile:
data:      Computer Name                 :exampleVMname
data:      User Name                     :ahmet
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-4C-B2
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-3894891836-nic
data:          Location                  :westus
data:            Public IP address       :13.88.22.244
data:            FQDN                    :examp-westu-3894891836-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://<**subscriptionsID**>.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Agora, você pode usar o SSH em sua VM na porta SSH 22 padrão e o endereço IP público (ou o nome de domínio totalmente qualificado - FQDN) listado na saída acima.

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.88.22.244
```

## Próximas etapas

O `azure vm quick-create` é a maneira de implantar rapidamente uma VM para que você possa fazer logon em um shell bash e começar a trabalhar. O uso de `vm quick-create` não oferece os benefícios adicionais de um ambiente complexo. Para implantar uma VM do Linux personalizada para sua infraestrutura, você pode seguir qualquer um dos artigos abaixo.

- [Usar um modelo do Azure Resource Manager para criar uma implantação específica](virtual-machines-linux-cli-deploy-templates.md)
- [Criar seu próprio ambiente personalizado para uma VM do Linux usando comandos da CLI do Azure diretamente](virtual-machines-linux-cli-deploy-templates.md).
- [Criar uma VM do Linux no Azure usando Modelos](virtual-machines-linux-cli-deploy-templates.md)
- [Criar uma VM do Linux Protegida por SSH no Azure usando Modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Criar uma VM do Linux usando a CLI do Azure](virtual-machines-linux-create-cli-complete.md)

Esses artigos o ajudarão a criar uma infraestrutura do Azure, bem como quaisquer ferramentas de implantação da infraestrutura patenteadas e de fonte aberta, de configuração e orquestração.

<!---HONumber=AcomDC_0504_2016-->