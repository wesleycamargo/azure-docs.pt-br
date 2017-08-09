---
title: "Criar a infraestrutura básica no Azure usando o Terraform | Microsoft Docs"
description: Saiba como criar recursos do Azure usando o Terraform
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 9660a95b440c2e4311829979e270d9f10099f624
ms.contentlocale: pt-br
ms.lasthandoff: 08/02/2017

---

# <a name="create-basic-infrastructure-in-azure-by-using-terraform"></a>Criar a infraestrutura básica no Azure usando o Terraform
Este artigo descreve as etapas necessárias para provisionar uma máquina virtual, junto com a infraestrutura subjacente, no Azure. Você aprenderá a gravar scripts do Terraform e a visualizar as alterações antes de fazê-las na sua infraestrutura de nuvem. Você também aprenderá a criar a infraestrutura no Azure usando o Terraform.

Para começar, crie um arquivo chamado \terraform_azure101.tf no editor de texto de sua preferência (Visual Studio Code/Sublime/Vim/etc.). O nome exato do arquivo não é importante, pois o Terraform aceita o nome da pasta como parâmetro: todos os scripts na pasta são executados. Cole o seguinte código no novo arquivo:

~~~~
# Configure the Microsoft Azure Provider
# NOTE: if you defined these values as environment variables, you do not have to include this block
provider "azurerm" {
  subscription_id = "your_subscription_id_from_script_execution"
  client_id       = "your_appId_from_script_execution"
  client_secret   = "your_password_from_script_execution"
  tenant_id       = "your_tenant_id_from_script_execution"
}

# create a resource group 
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}
~~~~
Na seção `provider` do script, você informa o Terraform para usar um provedor do Azure para provisionar recursos no script. Para obter os valores de subscription_id, appId, senha e tenant_id, veja o guia [Instalar e configurar o Terraform](terraform-install-configure.md). Se você tiver criado variáveis de ambiente para os valores nesse bloco, não precisará incluí-las. 

O recurso `azurerm_resource_group` instrui o Terraform a criar um novo grupo de recursos. Você pode ver mais tipos de recursos que estão disponíveis no Terraform mais adiante neste artigo.

## <a name="execute-the-script"></a>Execute o script
Depois de salvar o script, saia da linha de comando/console e digite o seguinte:
```
terraform init
```
para inicializar o provedor de Terraform do Azure. Depois, digite o seguinte:
```
terraform plan terraformscripts
```
Vamos supor que `terraformscripts` seja a pasta em que o script foi salvo. Usamos o comando `plan` do Terraform, que examina os recursos definidos nos scripts. Ele compara-os com as informações de estado salvas pelo Terraform e, em seguida, gera a execução planejada _sem_ de fato criar recursos no Azure. 

Depois de executar o comando anterior, você deverá ver algo semelhante à seguinte tela:

![Plano do Terraform](linux/media/terraform/tf_plan2.png)

Se tudo parecer correto, provisione esse novo grupo de recursos no Azure executando o seguinte: 
```
terraform apply terraformscripts
```
No portal do Azure, você deverá ver o novo grupo de recursos vazio chamado `terraformtest`. Na seção abaixo, você adicionará uma máquina virtual e toda sua infraestrutura de suporte para essa máquina virtual a esse grupo de recursos.

## <a name="provision-an-ubuntu-vm-with-terraform"></a>Provisionar uma VM Ubuntu com o Terraform
Vamos estender o script do Terraform criado com os detalhes necessários para provisionar uma máquina virtual executando Ubuntu. Os recursos que você provisionar nas seções a seguir são:

* Uma rede com uma única sub-rede
* Uma placa de interface de rede 
* Uma conta de armazenamento com um contêiner de armazenamento
* Um IP público
* Uma máquina virtual que utiliza todos os recursos anteriores 

Para obter uma documentação completa de cada um dos recursos do Azure Terraform, consulte a [documentação do Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

A versão completa do [script de provisionamento](#complete-terraform-script) também é fornecida para conveniência.

### <a name="extend-the-terraform-script"></a>Estender o script do Terraform
Estenda o script criado com os seguintes recursos: 
~~~~
# create a virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "acctvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "acctsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}
~~~~
O script anterior cria uma rede virtual e uma sub-rede dentro daquela rede virtual. Observe a referência ao grupo de recursos que você já criou por meio de “${azurerm_resource_group.helloterraform.name}” tanto na definição de rede virtual quanto na de sub-rede.

~~~~
# create public IP
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}
~~~~
Os trechos de script anteriores criam um IP público e um adaptador de rede que usa o IP público criado. Observe as referências a subnet_id e public_ip_address_id. O Terraform tem uma inteligência interna para entender que o adaptador de rede tem uma dependência dos recursos que precisam ser criados antes da criação do adaptador de rede.

~~~~
# create a random id
resource "random_id" "randomId" {
  byte_length = 4
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "tfstorage${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}
~~~~
Aqui, você criou uma conta de armazenamento e definiu um contêiner de armazenamento dentro dessa conta. Essa conta de armazenamento é o local em que você armazena os VHDs (discos rígidos virtuais) para a máquina virtual prestes a ser criada.

~~~~
# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
        admin_username = "testadmin"
        admin_password = "Password1234!"
    }

    os_profile_linux_config {
        disable_password_authentication = false
    }

    tags {
        environment = "staging"
    }
}
~~~~
Por fim, o trecho de código anterior cria uma máquina virtual que utiliza todos os recursos já provisionados. Eles são uma conta de armazenamento e um contêiner para um VHD, um adaptador de rede com IP público e sub-rede especificados e o grupo de recursos já criado. Observe a propriedade vm_size, na qual o script especifica um SKU A0 do Azure.

### <a name="execute-the-script"></a>Execute o script
Com o script completo salvo, saia para o console/linha de comando e digite o seguinte:
```
terraform apply terraformscripts
```
Depois de algum tempo, os recursos, incluindo uma máquina virtual, aparecem no grupo de recursos `terraformtest` no portal do Azure.

## <a name="complete-terraform-script"></a>Script completo do Terraform

Para conveniência, abaixo está o script completo do Terraform que provisiona toda a infraestrutura discutida neste artigo.

```
variable "resourcesname" {
  default = "helloterraform"
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
  subscription_id = "XXX"
  client_id       = "XXX"
  client_secret   = "XXX"
  tenant_id       = "XXX"
}

# create a resource group if it doesn't exist
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}

# create virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "tfvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "tfsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}


# create public IPs
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "TerraformDemo"
    }
}

# create network interface
resource "azurerm_network_interface" "helloterraformnic" {
    name = "tfni"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    ip_configuration {
        name = "testconfiguration1"
        subnet_id = "${azurerm_subnet.helloterraformsubnet.id}"
        private_ip_address_allocation = "static"
        private_ip_address = "10.0.2.5"
        public_ip_address_id = "${azurerm_public_ip.helloterraformips.id}"
    }
}

# create a random id
resource "random_id" "randomId" {
  byte_length = 4
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "tfstorage${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "westus"
    account_type = "Standard_LRS"

    tags {
        environment = "staging"
    }
}

# create storage container
resource "azurerm_storage_container" "helloterraformstoragestoragecontainer" {
    name = "vhd"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    storage_account_name = "${azurerm_storage_account.helloterraformstorage.name}"
    container_access_type = "private"
    depends_on = ["azurerm_storage_account.helloterraformstorage"]
}

# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_A0"

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "14.04.2-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        vhd_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}${azurerm_storage_container.helloterraformstoragestoragecontainer.name}/myosdisk.vhd"
        caching = "ReadWrite"
        create_option = "FromImage"
    }

    os_profile {
        computer_name = "hostname"
        admin_username = "testadmin"
        admin_password = "Password1234!"
    }

    os_profile_linux_config {
        disable_password_authentication = false
    }

    tags {
        environment = "staging"
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Você criou a infraestrutura básica no Azure usando o Terraform. Para obter cenários mais complexos, incluindo exemplos que usam balanceadores de carga e conjuntos de dimensionamento de máquinas virtuais, confira os vários [exemplos do Terraform para o Azure](https://github.com/hashicorp/terraform/tree/master/examples). Para obter uma lista atualizada de provedores do Azure com suporte, consulte a [Documentação do Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

