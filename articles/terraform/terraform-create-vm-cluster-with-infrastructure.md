---
title: Criar um cluster de VMs com Terraform e HCL
description: "Usar os módulos do Terraform e Linguagem de Configuração HashiCorp (HCL) para criar um cluster de máquina virtual Linux com um balanceador de carga no Azure"
keywords: "terraform, devops, máquina virtual, rede, módulos"
author: tomarcher
manager: routlaw
ms.service: virtual-machines-linux
ms.custom: devops
ms.topic: article
ms.date: 11/13/2017
ms.author: tarcher
ms.openlocfilehash: 2435d694e6a1671a234d02f90860e5cafe98c2df
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Criar um cluster de VMs com Terraform e HCL

Este tutorial demonstra como criar um pequeno cluster de cálculo usando a [Linguagem de Configuração Hashicorp](https://www.terraform.io/docs/configuration/syntax.html) (HCL). A configuração cria um balanceador de carga, duas VMs Linux em um [conjunto de disponibilidade](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) e todos os recursos de rede necessários.

Neste tutorial, você:

> [!div class="checklist"]
> * Configurar a autenticação do Azure
> * Criar um arquivo de configuração Terraform
> * Inicializar Terraform
> * Criar um plano de execução Terraform
> * Aplicar o plano de execução Terraform

## <a name="1-set-up-azure-authentication"></a>1. Configurar a autenticação do Azure

> [!NOTE]
> Se você [usar variáveis de ambiente do Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables) ou executar este tutorial no [Azure Cloud Shell](terraform-cloud-shell.md), ignore esta seção.

Nesta seção, você pode gerar uma entidade de serviço do Azure e dois arquivos de configuração de Terraform que contém as credenciais da entidade de segurança.

1. [Configure uma entidade de serviço do Azure AD](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) para permitir que o Terraform provisione recursos no Azure. Ao criar a entidade de segurança, anote os valores para o ID de assinatura, o locatário, a appId e a senha.

2. Abra um prompt de comando.

3. Crie um diretório vazio no qual armazenar os arquivos Terraform.

4. Crie um novo arquivo que contém suas declarações de variáveis. Você pode nomear o arquivo como com uma extensão `.tf`.

5. Copie o seguinte código ao seu arquivo de declaração de variável:

  ```tf
  variable subscription_id {}
  variable tenant_id {}
  variable client_id {}
  variable client_secret {}
  
  provider "azurerm" {
      subscription_id = "${var.subscription_id}"
      tenant_id = "${var.tenant_id}"
      client_id = "${var.client_id}"
      client_secret = "${var.client_secret}"
  }
  ```

6. Crie um novo arquivo que contém os valores para as variáveis de Terraform. É comum nomear o arquivo de variável Terraform `terraform.tfvars` como Terraform carrega automaticamente qualquer arquivo chamado `terraform.tfvars` (ou um padrão de `*.auto.tfvars`) se estiver presente no diretório atual. 

7. Copie o seguinte código ao seu arquivo de declaração de variável. Certifique-se de substituir os espaços reservados da seguinte forma: para `subscription_id`, use a ID de assinatura do Azure especificada durante a execução `az account set`. Para `tenant_id`, use o `tenant` valor retornado de `az ad sp create-for-rbac`. Para `client_id`, use o `appId` valor retornado de `az ad sp create-for-rbac`. Para `client_secret`, use o `password` valor retornado de `az ad sp create-for-rbac`.

  ```tf
  subscription_id = "<azure-subscription-id>"
  tenant_id = "<tenant-returned-from-creating-a-service-principal>"
  client_id = "<appId-returned-from-creating-a-service-principal>"
  client_secret = "<password-returned-from-creating-a-service-principal>"
  ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Criar um arquivo de configuração Terraform

Nesta seção, você deve criar um arquivo que contém definições de recursos para sua infraestrutura.

1. Crie um arquivo chamado `main.tf`. 

2. Copiar definições de recursos de exemplo a seguir em um arquivo recém-criado `main.tf`: 

  ```tf
  resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
  }

  resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"
  }

  resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    virtual_network_name = "${azurerm_virtual_network.test.name}"
    address_prefix       = "10.0.2.0/24"
  }

  resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    public_ip_address_allocation = "static"
  }

  resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.test.id}"
    }
  }

  resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = "${azurerm_resource_group.test.name}"
    loadbalancer_id     = "${azurerm_lb.test.id}"
    name                = "BackEndAddressPool"
  }

  resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = "${azurerm_subnet.test.id}"
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
    }
  }

  resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = "${azurerm_resource_group.test.location}"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
  }

  resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
  }

  resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = "${azurerm_resource_group.test.location}"
    availability_set_id   = "${azurerm_availability_set.avset.id}"
    resource_group_name   = "${azurerm_resource_group.test.name}"
    network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
      managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
    }

    os_profile {
      computer_name  = "hostname"
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

## <a name="3-initialize-terraform"></a>3. Inicializar Terraform 

O [comando terraform init](https://www.terraform.io/docs/commands/init.html) é usado para inicializar um diretório que contém os arquivos de configuração Terraform - os arquivos criados com as seções anteriores. Você deve sempre executar o comando `terraform init` depois de gravar uma nova configuração de Terraform. 

> [!TIP]
> O comando `terraform init` é idempotente, que significa que ele pode ser chamado várias vezes ao mesmo tempo em que produz o mesmo resultado. Portanto, se você estiver trabalhando em um ambiente de colaboração e você achar que os arquivos de configuração podem ter sido alterados, é sempre uma boa ideia para chamar o comando `terraform init` antes de executar ou aplicar um plano.

Para inicializar Terraform, execute o seguinte comando:

  ```cmd
  terraform init
  ```

  ![Inicializando Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Criar um plano de execução Terraform

O [comando de plano do terraform](https://www.terraform.io/docs/commands/plan.html) é usado para criar um plano de execução. Para gerar um plano de execução, Terraform agrega todos os arquivos `.tf` no diretório atual. 

Se você estiver trabalhando em um ambiente de colaboração em que a configuração pode ser alterada entre a hora em que você criar o plano de execução e o tempo de aplicar o plano de execução, você deve usar o [do comando de plano terraform-parâmetro out](https://www.terraform.io/docs/commands/plan.html#out-path) para salvar o plano de execução em um arquivo. Caso contrário, se você estiver trabalhando em um ambiente de única pessoa, você pode omitir o parâmetro `-out`.

Se o nome do seu arquivo de variáveis Terraform não é `terraform.tfvars` e não segue o padrão `*.auto.tfvars`, é necessário especificar o nome de arquivo usando o parâmetro de arquivo [ - var do comando de plano terraform](https://www.terraform.io/docs/commands/plan.html#var-file-foo) ao executar o comando `terraform plan`.

Quando processar o comando `terraform plan`, Terraform performa uma atualização e determina quais ações são necessárias para obter o estado desejado especificado nos arquivos de configuração.

Se você não precisa salvar o plano de execução, execute o seguinte comando:

  ```cmd
  terraform plan
  ```

Se você precisar salvar o plano de execução, execute o seguinte comando (substituindo o caminho &lt; > espaço reservado com o caminho de saída desejada):

  ```cmd
  terraform plan -out=<path>
  ```

![Criar um plano de execução Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Aplicar o plano de execução Terraform

A etapa final deste tutorial é usar o [comando de aplicar terraform](https://www.terraform.io/docs/commands/apply.html) para aplicar o conjunto de ações geradas pelo comando `terraform plan`.

Se você deseja aplicar o plano de execução mais recente, execute o seguinte comando:

  ```cmd
  terraform apply
  ```

Se você deseja aplicar um plano de execução salvo anteriormente, execute o seguinte comando (substituindo o caminho &lt; > espaço reservado com o caminho que contém o plano de execução salvo):

  ```cmd
  terraform apply <path>
  ```

![Aplicar um plano de execução Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Próximas etapas

- Procurar a lista de [módulos do Azure Terraform](https://registry.terraform.io/modules/Azure)
- Criar um [conjunto de dimensionamento de máquinas virtuais com o Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)