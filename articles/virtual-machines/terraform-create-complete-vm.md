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
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 8b8b3b0b46f79058ee69b9a2014581df433f8d3f
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017

---

# <a name="create-basic-infrastructure-in-azure-using-terraform"></a>Criar a infraestrutura básica no Azure usando o Terraform
Este artigo fornece detalhes das etapas necessárias para provisionar uma máquina virtual, junto com a infraestrutura subjacente, no Azure. Você aprenderá a criar scripts do Terraform e a visualizar as alterações antes de fazê-las na infraestrutura de nuvem. Você também aprenderá a criar a infraestrutura no Azure usando o Terraform.

Para começar, no editor de texto de sua preferência (Visual Studio Code/Sublime/Vim/etc.), crie um arquivo chamado _terraform_azure101.tf_. O nome exato do arquivo não é importante, pois o Terraform aceita o nome da pasta como parâmetro – todos os scripts na pasta são executados. Cole o seguinte código no novo arquivo:

~~~~
# Configure the Microsoft Azure Provider
# NOTE: if you defined these values as environment variables, you do not have to include this block
provider "azurerm" {
  subscription_id = "your_subscription_id_from_script_execution"
  client_id       = "your_client_id_from_script_execution"
  client_secret   = "your_client_secret_from_script_execution"
  tenant_id       = "your_tenant_id_from_script_execution"
}

# create a resource group 
resource "azurerm_resource_group" "helloterraform" {
    name = "terraformtest"
    location = "West US"
}
~~~~
Na seção “provedor” do script, você informa o Terraform para usar um provedor do Azure para provisionar recursos no script. Consulte o guia [Instalando e configurando o Terraform](terraform-install-configure.md) para obter valores para subscription_id, client_id, client_secret e tenant_id. Observe também que, se você criou variáveis de ambiente para os valores neste bloco, não precisará incluí-los. 

O recurso “azure_rm_resource_group” instrui o Terraform a criar um novo grupo de recursos. Você verá mais tipos de recursos disponíveis no Terraform abaixo.

## <a name="executing-the-script"></a>Executando o script
Com o script salvo, saia para o console/linha de comando e digite
```
terraform plan terraformscripts
```
Acima, supomos que “terraformscripts” é a pasta em que o script foi salvo. Usamos o comando “plan” do Terraform, que examina os recursos definidos nos scripts, compara-os com as informações de estado salvas pelo Terraform e, depois, gera a execução planejada _sem_, de fato, criar recursos no Azure. 

Você deverá ver algo semelhante à seguinte tela depois de executar o comando acima

![Imagem do Plano do Terraform](linux/media/terraform/tf_plan2.png)

Tudo parece correto. Vá em frente e provisione esse novo grupo de recursos no Azure executando 
```
terraform apply terraformscripts
```
Se você observar no portal do Azure agora, deverá ver o novo grupo de recursos vazio chamado “terraformtest”. Na seção abaixo, você adicionará uma Máquina Virtual e toda sua infraestrutura de suporte para essa máquina virtual a esse grupo de recursos.

## <a name="provisioning-ubuntu-vm-with-terraform"></a>Provisionando uma VM Ubuntu com o Terraform
Vamos estender o script do Terraform que criamos acima com os detalhes necessários para provisionar uma máquina virtual que executa o Ubuntu. A lista de recursos que você provisionará nas seções abaixo é: uma rede com uma única sub-rede, uma placa de interface de rede, uma conta de armazenamento com um contêiner de armazenamento, um IP público e uma máquina virtual que utiliza todos os recursos acima. Para obter uma documentação completa de cada um dos recursos do Azure Terraform, consulte a [documentação do Terraform](https://www.terraform.io/docs/providers/azurerm/index.html).

A versão completa do [script de provisionamento](#complete-terraform-script) também é fornecida para conveniência.

### <a name="extending-the-terraform-script"></a>Estendendo o script do Terraform
Estenda o script criado acima com os seguintes recursos: 
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
O script acima cria uma rede virtual e uma sub-rede dentro da rede virtual. Observe a referência ao grupo de recursos que você já criou por meio de “${azurerm_resource_group.helloterraform.name}” na definição de rede virtual e de sub-rede.

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
Os trechos de script acima criam um IP público e um adaptador de rede que usa o IP público criado. Observe as referências a subnet_id e public_ip_address_id. O Terraform tem uma inteligência interna para entender que o adaptador de rede tem uma dependência dos recursos que precisa ser criada antes da criação do adaptador de rede.

~~~~
# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name = "helloterraformstorage"
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
Aqui, você criou uma conta de armazenamento e definiu um contêiner de armazenamento nessa conta de armazenamento – esse é o local em que você armazena VHDs para a máquina virtual prestes a ser criada.

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
Por fim, o trecho acima cria uma máquina virtual que utiliza todos os recursos que já provisionamos: a conta de armazenamento e o contêiner para um VHD (disco rígido virtual), o adaptador de rede com IP público e sub-rede especificados, bem como o grupo de recursos que você já criou. Observe a propriedade vm_size, na qual o script especifica um SKU A0 do Azure.

### <a name="executing-the-script"></a>Executando o script
Com o script completo salvo, saia para o console/linha de comando e digite
```
terraform apply terraformscripts
```
Depois de algum tempo, você deverá ver os recursos, incluindo uma máquina virtual, exibidos no grupo de recursos “terraformtest” no portal do Azure.

## <a name="complete-terraform-script"></a>Script completo do Terraform

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

# create storage account
resource "azurerm_storage_account" "helloterraformstorage" {
    name = "helloterraformstorage"
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
Você criou a infraestrutura básica no Azure usando o Terraform. Para obter cenários mais complexos, incluindo exemplos sobre como usar balanceadores de carga e conjuntos de dimensionamento de máquinas virtuais, confira os vários [exemplos do Terraform para o Azure](https://github.com/hashicorp/terraform/tree/master/examples). A [documentação do Terraform](https://www.terraform.io/docs/providers/azurerm/index.html) contém uma lista completa e atualizada de provedores do Azure com suporte.
