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
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: aa0926de32dd85f4460bbfa84b7a6007e2199d51
ms.contentlocale: pt-br
ms.lasthandoff: 08/28/2017

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

# create a resource group if it doesn't exist
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
 para inicializar o provedor de Terraform do Azure. Em seguida, altere o diretório para **terraformscripts** e emita o seguinte comando:
```
terraform plan
```
Usamos o comando `plan` do Terraform, que examina os recursos definidos nos scripts. Ele compara-os com as informações de estado salvas pelo Terraform e, em seguida, gera a execução planejada _sem_ de fato criar recursos no Azure. 

Depois de executar o comando anterior, você deverá ver algo semelhante à seguinte tela:

![Plano do Terraform](./media/terraform/tf_plan2.png)

Se tudo parecer correto, provisione esse novo grupo de recursos no Azure executando o seguinte: 
```
terraform apply
```
No portal do Azure, você deverá ver o novo grupo de recursos vazio chamado `terraformtest`. Na seção abaixo, você adicionará uma máquina virtual e toda sua infraestrutura de suporte para essa máquina virtual a esse grupo de recursos.

## <a name="provision-an-ubuntu-vm-with-terraform"></a>Provisionar uma VM Ubuntu com o Terraform
Vamos estender o script do Terraform criado com os detalhes necessários para provisionar uma máquina virtual executando Ubuntu. Os recursos que você provisionar nas seções a seguir são:

* Uma rede com uma única sub-rede
* Uma placa de interface de rede 
* Uma conta de armazenamento para o diagnóstico da máquina virtual
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

    tags {
        environment = "Terraform Demo"
    }
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
        environment = "Terraform Demo"
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

    tags {
        environment = "Terraform Demo"
    }
}
~~~~
Os trechos de script anteriores criam um IP público e um adaptador de rede que usa o IP público criado. Observe as referências a subnet_id e public_ip_address_id. O Terraform tem uma inteligência interna para entender que o adaptador de rede tem uma dependência dos recursos que precisam ser criados antes da criação do adaptador de rede.

~~~~
# create a random id
resource "random_id" "randomId" {
  keepers = {
    # Generate a new id only when a new resource group is defined
    resource_group = "${azurerm_resource_group.helloterraform.name}"
  }

  byte_length = 8
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "West US"
    account_type = "Standard_LRS"

    tags {
        environment = "Terraform Demo"
    }
}
~~~~
Aqui, você criou uma conta de armazenamento. Essa conta de armazenamento é onde a máquina virtual armazena seus detalhes de diagnóstico.

~~~~
# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_DS1_v2"

    os_profile {
        computer_name = "hostname"
        admin_username = "azureuser"
        admin_password = ""
    }

    os_profile_linux_config {
        disable_password_authentication = true

        ssh_keys {
            path = "/home/azureuser/.ssh/authorized_keys"
            key_data = "... INSERT OPENSSH PUBLIC KEY HERE ..."
        }
    }

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "16.04.0-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        managed_disk_type = "Premium_LRS"
        create_option = "FromImage"
    } 

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
~~~~
Por fim, o trecho de código anterior cria uma máquina virtual que utiliza todos os recursos já provisionados. Eles são uma conta de armazenamento para os diagnósticos de máquina virtual, um adaptador de rede com IP público e sub-rede especificados e o grupo de recursos já criado. Observe a propriedade vm_size, na qual o script especifica um SKU DS1v2 do Azure Standard.

Você deve fornecer uma chave pública SSH. Coloque o valor da chave pública na seção **... INSIRA A CHAVE PÚBLICA OPENSSH AQUI...**  acima. Você pode usar um par de chave ssh existente ou seguir a documentação do [Windows](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows) ou [Linux/macOS](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys) para gerar o par de chaves.

### <a name="execute-the-script"></a>Execute o script
Com o script completo salvo, saia para o console/linha de comando e digite o seguinte:
```
terraform apply
```
Depois de algum tempo, os recursos, incluindo uma máquina virtual, aparecem no grupo de recursos `terraformtest` no portal do Azure.

## <a name="complete-terraform-script"></a>Script completo do Terraform

Para conveniência, abaixo está o script completo do Terraform que provisiona toda a infraestrutura discutida neste artigo.

```
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

# create a virtual network
resource "azurerm_virtual_network" "helloterraformnetwork" {
    name = "acctvn"
    address_space = ["10.0.0.0/16"]
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"

    tags {
        environment = "Terraform Demo"
    }
}

# create subnet
resource "azurerm_subnet" "helloterraformsubnet" {
    name = "acctsub"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    virtual_network_name = "${azurerm_virtual_network.helloterraformnetwork.name}"
    address_prefix = "10.0.2.0/24"
}

# create public IP
resource "azurerm_public_ip" "helloterraformips" {
    name = "terraformtestip"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
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

    tags {
        environment = "Terraform Demo"
    }
}

# create a random id
resource "random_id" "randomId" {
  keepers = {
    # Generate a new id only when a new resource group is defined
    resource_group = "${azurerm_resource_group.helloterraform.name}"
  }

  byte_length = 8
}

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    location = "West US"
    account_type = "Standard_LRS"

    tags {
        environment = "Terraform Demo"
    }
}

# create virtual machine
resource "azurerm_virtual_machine" "helloterraformvm" {
    name = "terraformvm"
    location = "West US"
    resource_group_name = "${azurerm_resource_group.helloterraform.name}"
    network_interface_ids = ["${azurerm_network_interface.helloterraformnic.id}"]
    vm_size = "Standard_DS1_v2"

    os_profile {
        computer_name = "hostname"
        admin_username = "azureuser"
        admin_password = ""
    }

    os_profile_linux_config {
        disable_password_authentication = true

        ssh_keys {
            path = "/home/azureuser/.ssh/authorized_keys"
            key_data = "... INSERT OPENSSH PUBLIC KEY HERE ..."
        }
    }

    storage_image_reference {
        publisher = "Canonical"
        offer = "UbuntuServer"
        sku = "16.04.0-LTS"
        version = "latest"
    }

    storage_os_disk {
        name = "myosdisk"
        managed_disk_type = "Premium_LRS"
        create_option = "FromImage"
    } 

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.helloterraformstorage.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Você criou a infraestrutura básica no Azure usando o Terraform. Para obter cenários mais complexos, incluindo exemplos que usam balanceadores de carga e conjuntos de dimensionamento de máquinas virtuais, confira os vários [exemplos do Terraform para o Azure](https://github.com/hashicorp/terraform/tree/master/examples). Para obter uma lista atualizada de provedores do Azure com suporte, consulte a [Documentação do Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

