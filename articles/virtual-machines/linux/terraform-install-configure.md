---
title: Instalar e configurar o Terraform para provisionar VMs e outra infraestrutura no Azure | Microsoft Docs
description: Saiba como instalar e configurar o Terraform para criar recursos do Azure
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
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: dada9c70eef2adb2704e276a5401509581e37538
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Instalar e configurar o Terraform para provisionar VMs e outra infraestrutura no Azure
 
O Terraform fornece uma maneira fácil de definir, visualizar e implantar a infraestrutura de nuvem usando uma [linguagem de modelagem simples](https://www.terraform.io/docs/configuration/syntax.html). Este artigo descreve as etapas necessárias para usar o Terraform para provisionar recursos no Azure. 

> [!TIP]
Para saber mais sobre como usar Terraform com o Azure, visite o [Terraform Hub](/azure/terraform). O Terraform é instalado por padrão no [Cloud Shell](/azure/terraform/terraform-cloud-shell). Usando o Cloud Shell, você poderá ignorar as partes de instalação/configuração deste documento.

## <a name="install-terraform"></a>Instalar o Terraform

Para instalar o Terraform, [baixe](https://www.terraform.io/downloads.html) o pacote apropriado para seu sistema operacional para um diretório de instalação separado. O download contém um único arquivo executável, para o qual também deve definir um caminho global. Para obter instruções sobre como definir o caminho no Linux e no Mac, acesse [esta página da Web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Para obter instruções sobre como definir o caminho no Windows, acesse [esta página da Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). 

Verifique sua configuração de caminho com o comando `terraform`. Você deverá ver uma lista das opções do Terraform disponíveis como saída:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Configurar o acesso do Terraform ao Azure

Configure [uma entidade de serviço do Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli) para permitir que o Terraform provisione recursos no Azure. A entidade de serviço concede aos seus scripts Terraform que usam as credenciais a capacidade de provisionar recursos na sua assinatura do Azure.

Há várias maneiras de criar um aplicativo Azure AD e uma entidade de serviço do Azure AD. A maneira mais fácil e rápida hoje é usar a CLI 2.0 do Azure, que [pode ser baixada e instalada no Windows/Linux/Mac](/cli/azure/install-azure-cli).

Entre para administrar sua assinatura do Azure emitindo o seguinte comando:

   `az login`

Se você tiver várias assinaturas do Azure, seus detalhes serão retornados pelo comando `az login`. Defina a variável de ambiente `SUBSCRIPTION_ID` para manter o valor do campo `id` retornado da assinatura que você deseja usar. 

Defina a assinatura que você deseja usar para essa sessão.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Consulte a conta para obter os valores de ID da assinatura e ID do locatário.

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Em seguida, crie credenciais separadas para o Terraform.

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

A appId, a senha, o sp_name e o locatário são retornados. Anote a appId e a senha.

Para testar suas credenciais, abra um novo shell e execute o seguinte comando, usando os valores retornados para sp_name, senha e locatário:

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>Configurar as variáveis de ambiente do Terraform

Configure o Terraform para usar a ID do Locatário, ID da Assinatura, ID do Cliente e segredo do cliente da entidade de serviço ao criar recursos do Azure. Você também pode definir o ambiente se estiver trabalhando com uma nuvem do Azure diferente do público do Azure. Defina as seguintes variáveis de ambiente, que são usadas automaticamente pelos [módulos do Azure Terraform](https://registry.terraform.io/modules/Azure).

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID
- ARM_ENVIRONMENT

Você pode usar esse exemplo de script shell para definir essas variáveis:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Executar um script de exemplo

Crie um arquivo `test.tf` em um diretório vazio e cole o script a seguir. 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Salve o arquivo e execute `terraform init`. Esse comando baixa os módulos do Azure necessários para criar um grupo de recursos do Azure. Você verá esta saída:

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Visualize o script com `terraform plan` e crie o grupo de recursos `testResouceGroup` com `terraform apply`:

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Próximas etapas

Você instalou o Terraform e configurou as credenciais do Azure para poder começar a implantar infraestrutura na sua assinatura do Azure. Em seguida, você testou sua instalação por meio da criação de um grupo de recursos vazio do Azure.

> [!div class="nextstepaction"]
> [Criar uma VM do Azure com o Terraform](terraform-create-complete-vm.md)

