---
title: Use o Terraform e crie um conjunto de dimensionamento da VM do Azure usando HCL.
description: "Use o Terraform para configurar e controlar a versão de um conjunto de dimensionamento de máquinas virtuais do Azure completo com uma rede virtual e discos gerenciados anexados."
keywords: "terraform, devops, conjunto de dimensionamento, máquina virtual, rede, módulos"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/04/2017
ms.topic: article
ms.openlocfilehash: 7a4e21d547b3d2b2399f9f68b1babd9f82a421b7
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="use-terraform-to-plan-and-create-a-networked-azure-vm-scale-set-with-managed-storage"></a>Usar o Terraform para planejar e criar um conjunto de dimensionamento de VM do Azure em rede com armazenamento gerenciado

Neste artigo, você usará p [Terraform](https://www.terraform.io/) para criar e implantar um [conjunto de dimensionamento de máquinas virtuais do Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) com Managed Disks usando o HCL ([Hashicorp Configuration Language](https://www.terraform.io/docs/configuration/syntax.html)).  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar sua implantação do Terraform
> * Usar variáveis e saídas para a implantação do Terraform 
> * Criar e implantar a infraestrutura de rede
> * Criar e implantar um conjunto de dimensionamento de máquinas virtuais e anexá-lo à rede
> * Criar e implantar um jumpbox para se conectar às VMs via SSH

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

- [Instalar o Terraform e configurar o acesso ao Azure](/azure/virtual-machines/linux/terraform-install-configure)
- [Crie um par de chaves SSH](/azure/virtual-machines/linux/mac-create-ssh-keys) caso você ainda não tenha um.

## <a name="create-the-file-structure"></a>Criar a estrutura do arquivo

Crie três novos arquivos em um diretório vazio com os seguintes nomes:

- `variables.tf` Esse arquivo contém os valores das variáveis usadas no modelo.
- `output.tf` Esse arquivo descreve as configurações que serão exibidas após a implantação.
- `vmss.tf` Esse código para a infraestrutura do conjunto de dimensionamento de máquinas virtuais.

## <a name="create-the-variables-and-output-definitions"></a>Criar as definições de variáveis e de saída

Nesta etapa, você definirá as variáveis que personalizam os recursos criados pelo Terraform.

Edite o arquivo `variables.tf`, copie o código a seguir e salve as alterações.

```tf 
variable "location" {
  description = "The location where resources will be created"
  default     = "West US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources will be created"
  default     = "myResourceGroup"
}
```

Edite o arquivo `output.tf` e copie o código a seguir para expor o nome de domínio totalmente qualificado para as máquinas virtuais. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definir a infraestrutura de rede em um modelo

Nesta etapa, você criará a seguinte infraestrutura de rede em um novo grupo de recursos do Azure: 

  - Uma VNET com o espaço de endereço de 10.0.0.0/16 
  - Uma sub-rede com o espaço de endereço de 10.0.2.0/24
  - Dois endereços IP públicos. Um usado pelo balanceador de carga do conjunto de dimensionamento de máquinas virtuais, outro usado para conectar ao jumpbox SSH.


Edite e copie o seguinte código no arquivo `vmss.tf`: 

```tf
resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> É uma boa ideia marcar os recursos que estão sendo implantados no Azure para facilitar sua identificação no futuro.

## <a name="create-the-network-infrastructure"></a>Criar a infraestrutura de rede

Inicialize o ambiente de Terraform executando o seguinte comando no diretório em que você criou os arquivos `.tf`:

```bash
terraform init 
```

Em seguida, execute o comando a seguir para implantar a infraestrutura no Azure.

```bash
terraform apply
```

Verifique se o FQDN do endereço IP público corresponde à sua configuração: ![VMSS terraform FQDN para endereço IP público](./media/tf-create-vmss-step4-fqdn.png)


O grupo de recursos deve ter os seguintes recursos: ![recursos de rede do terraform VMSS](./media/tf-create-vmss-step4-rg.png)


## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Editar a infraestrutura para adicionar o conjunto de dimensionamento de máquinas virtuais

Nesta etapa, você adicionará os seguintes recursos ao modelo:

- Um Azure Load Balancer e regras para atender ao aplicativo e anexá-lo no endereço IP público configurado anteriormente.
- Um pool de endereços de back-end do Azure, que será atribuído ao balanceador de carga 
- Uma porta de investigação de integridade usada pelo aplicativo e configurada no balanceador de carga 
- Um conjunto de dimensionamento de máquinas virtuais localizado por trás do balanceador de carga e em execução na vnet implantada anteriormente
- [Nginx](http://nginx.org/) nos nós de dimensionamento de máquina virtual usando uma extensão de script personalizado.

Adicione o seguinte código ao final do arquivo `vmss.tf`.

```tf
resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
    resource_group_name            = "${azurerm_resource_group.vmss.name}"
    loadbalancer_id                = "${azurerm_lb.vmss.id}"
    name                           = "http"
    protocol                       = "Tcp"
    frontend_port                  = "${var.application_port}"
    backend_port                   = "${var.application_port}"
    backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
    frontend_ip_configuration_name = "PublicIPAddress"
    probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
    }
  }

  extension { 
    name = "vmssextension"
    publisher = "Microsoft.OSTCExtensions"
    type = "CustomScriptForLinux"
    type_handler_version = "1.2"
    settings = <<SETTINGS
    {
        "commandToExecute": "sudo apt-get -y install nginx"
    }
    SETTINGS
  }

  tags {
    environment = "codelab"
  }
}
```

Personalize a implantação adicionando o seguinte código ao `variables.tf`:

```tf 
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Implantar o conjunto de dimensionamento de máquinas virtuais no Azure

Execute o seguinte comando para visualizar a implantação do conjunto de dimensionamento de máquinas virtuais:

```bash
terraform plan
```

A saída do comando deve ser semelhante àquela mostrada a seguir.
![Terraform add vmss plan](./media/tf-create-vmss-step6-plan884d3aefd9708a711bc09a66e85eb149c23a3ccff959655ec00418168b2bd481.png)

Em seguida, implante os recursos adicionais no Azure: 

```bash
terraform apply 
```

O conteúdo do grupo de recursos deve parecer com:

![Terraform vm scaleset resource group](./media/tf-create-vmss-step6-apply.png)

Abra um navegador e conecte-se ao FQDN que foi retornado pelo comando. 

## <a name="add-an-ssh-jumpbox-to-the-existing-network"></a>Adicionar um jumpbox SSH à rede existente 

Nesta etapa, você configurará os seguintes recursos:
- Um adaptador de rede conectado à mesma sub-rede que o conjunto de dimensionamento de máquinas virtuais.
- Uma máquina virtual conectada a este adaptador de rede. Este “jumpbox” pode ser acessado remotamente. Uma vez conectado, é possível usar o SSH em qualquer máquina virtual no conjunto de dimensionamento.



Adicione o seguinte código ao final do arquivo `vmss.tf`:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Edite `outputs.tf` e adicione o código a seguir para exibir o nome do host do jumpbox quando a implantação for concluída:

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>Implantar o jumpbox

Implante o jumpbox.

```bash
terraform apply 
```

Depois que a implantação for concluída, o conteúdo do grupo de recursos será semelhante a:

![Terraform vm scaleset resource group](./media/tf-create-create-vmss-step8.png)

> [!NOTE]
> O logon com senha está desabilitado na jumpbox e no conjunto de dimensionamento de máquinas virtuais que você implantou. Faça logon com o SSH para acessar as VMs.

## <a name="clean-up-the-environment"></a>Limpar o ambiente

Os comandos a seguir excluem todos os recursos criados neste tutorial:

```bash
terraform destroy
```

Digite `yes` quando for solicitado para confirmar a exclusão dos recursos. O processo de destruição leva alguns minutos para ser concluído.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um conjunto de dimensionamento de máquinas virtuais no Azure usando o Terraform. Você aprendeu como:

> [!div class="checklist"]
> * Inicializar a implantação do Terraform
> * Usar variáveis e saídas para a implantação do Terraform 
> * Criar e implantar uma infraestrutura de rede
> * Criar e implantar um conjunto de dimensionamento de máquinas virtuais e anexá-lo a um ambiente existente
> * Criar e implantar um jumpbox para se conectar às VMs via SSH 
