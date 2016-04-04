<properties
   pageTitle="Criar uma VM Linux na CLI | Microsoft Azure"
   description="Crie uma nova VM Linux no Microsoft Azure usando a CLI do Azure no Mac, Linux ou Windows."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="03/04/2016"
   ms.author="v-livech"/>


# Criar uma VM Linux na CLI do Azure para desenvolvimento e teste

Este tópico mostra como criar rapidamente uma VM Linux em um ambiente básico do Azure usando o comando `azure vm quick-create` na [CLI do Azure](../xplat-cli-install.md) para avaliação, teste e outros cenários de curta duração. A VM é acessada por senha e é aberta para a Internet. Para cenários de produção ou de execução mais longa, você deve criar uma [VM Linux protegida usando modelos do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md).

## Pré-requisitos

Você precisará de uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e da [CLI do Azure](../xplat-cli-install.md) colocada no modo recurso digitando `azure config mode arm` e conectada ao Azure digitando `azure login` e seguindo as solicitações. Versão atual: 0.9.16.

## Resumo rápido do comando

Há apenas um comando a ser emitido:

1. `azure vm quick-create`

Nos exemplos de comando a seguir, substitua os valores entre &lt; e &gt; pelos valores do seu próprio ambiente.

## Criar a VM Linux

No comando a seguir, você pode usar qualquer imagem desejada, mas este exemplo usa `canonical:ubuntuserver:14.04.2-LTS:latest` para criar uma VM rapidamente. (Para localizar uma imagem no Marketplace, [procure uma imagem](virtual-machines-linux-cli-ps-findimage.md) ou [carregue sua própria imagem personalizada](virtual-machines-linux-create-upload-generic.md)). Você verá algo semelhante ao que se segue.

```bash
# Create the Linux VM using prompts
username@macbook$ azure vm quick-create
info:    Executing command vm quick-create
Resource group name: quickcreate
Virtual machine name: quickcreate
Location name: westus
Operating system Type [Windows, Linux]: linux
ImageURN (in the format of "publisherName:offer:skus:version") or a VHD link to the user image: canonical:ubuntuserver:14.04.2-LTS:latest
User name: ops
Password: *********
Confirm password: *********
+ Looking up the VM "quickcreate"
info:    Using the VM Size "Standard_D1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli133501687
info:    Could not find the storage account "cli1301687", trying to create new one
+ Creating storage account "cli133501687" in "westus"
+ Looking up the storage account cli133501687
+ Looking up the NIC "quick-westu-1363648838-nic"
info:    An nic with given name "quick-westu-1363648838-nic" not found, creating a new one
+ Looking up the virtual network "quick-westu-1363648838-vnet"
info:    Preparing to create new virtual network and subnet
\ Creating a new virtual network "quick-westu-1363648838-vnet" [address prefix: "10.0.0.0/16"] with subnet "quick-westu-13636488+8-snet" [address prefix: "10.0.1.0/24"]
+ Looking up the virtual network "quick-westu-1363648838-vnet"
+ Looking up the subnet "quick-westu-1363648838-snet" under the virtual network "quick-westu-1363648838-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "quick-westu-1363648838-pip"
info:    PublicIP with given name "quick-westu-1363648838-pip" not found, creating a new one
+ Creating public ip "quick-westu-1363648838-pip"
+ Looking up the public ip "quick-westu-1363648838-pip"
+ Creating NIC "quick-westu-1363648838-nic"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Creating VM "quickcreate"
+ Looking up the VM "quickcreate"
+ Looking up the NIC "quick-westu-1363648838-nic"
+ Looking up the public ip "quick-westu-1363648838-pip"
data:    Id                              :/subscriptions/<guid>/resourceGroups/quickcreate/providers/Microsoft.Compute/virtualMachines/quickcreate
data:    ProvisioningState               :Succeeded
data:    Name                            :quickcreate
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_D1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :canonical
data:        Offer                       :ubuntuserver
data:        Sku                         :14.04.2-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :cli350d386daac1f01c-os-1457063387485
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli1361687.blob.core.windows.net/vhds/cli350d386daac1f01c-os-1457063387485.vhd
data:
data:    OS Profile:
data:      Computer Name                 :quickcreate
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-32-E9-66
data:          Provisioning State        :Succeeded
data:          Name                      :quick-westu-1363648838-nic
data:          Location                  :westus
data:            Public IP address       :137.135.33.58
data:            FQDN                    :quick-westu-1363648838-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://cli13601687.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

Agora, é possível executar SSH na VM na porta SSH 22 padrão.

## Passo a passo detalhado

O `azure vm quick-create` cria rapidamente uma VM para que possa fazer logon e começar a trabalhar. No entanto, o ambiente não é complexo, de modo que se quiser personalizá-lo, você pode [usar um modelo do Azure Resource Manager para criar uma implantação específica rapidamente](virtual-machines-linux-cli-deploy-templates.md) ou pode [criar seu próprio ambiente personalizado para uma VM Linux usando os comandos da CLI do Azure diretamente](virtual-machines-linux-cli-deploy-templates.md).

O exemplo acima cria:

- uma conta de Armazenamento do Azure para manter o arquivo .vhd que é a imagem da VM
- uma Rede Virtual do Azure e a sub-rede para fornecer conectividade à VM
- uma NIC (Placa de Interface de Rede) virtual para associar a VM à rede
- Um endereço IP público e o prefixo do subdomínio para fornecer um endereço de Internet para uso externo

E, em seguida, cria a VM Linux dentro desse ambiente. Essa VM é exposta diretamente à Internet e só é protegida por um nome de usuário e uma senha.

## Próximas etapas

Agora que você criou uma VM Linux rapidamente a ser usada para fins de teste ou demonstração, você pode criar um ambiente de execução mais seguro com uma VM Linux no Azure ao:

- [Criar uma VM Linux no Azure usando modelos do Azure](virtual-machines-linux-cli-deploy-templates.md)
- [Criar uma VM Linux protegida por SSH no Azure usando modelos do Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Criar uma VM Linux no Azure usando a CLI do Azure e personalizando a infraestrutura](virtual-machines-linux-create-cli-complete.md)

bem como qualquer número de implantação de infraestrutura, configuração e ferramentas de orquestração patenteadas e de software livre.

<!---HONumber=AcomDC_0323_2016-->