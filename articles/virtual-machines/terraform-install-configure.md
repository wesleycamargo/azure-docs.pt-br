---
title: Instalar e configurar o Terraform para provisionar VMs e outra infraestrutura no Azure | Microsoft Docs
description: "Saiba como instalar e configurar o Terraform para a criação de recursos do Azure"
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
ms.openlocfilehash: aa82dda778af927db0a204eb88bae445c086e309
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017

---

# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Instalar e configurar o Terraform para provisionar VMs e outra infraestrutura no Azure 
Este artigo fornece detalhes das etapas necessárias para instalar e configurar o Terraform para provisionar recursos como máquinas virtuais no Azure. Você aprenderá a criar e usar as credenciais do Azure para permitir que o Terraform provisione recursos da nuvem de forma segura.

O Terraform da HashiCorp fornece uma maneira fácil de definir e implantar a infraestrutura de nuvem usando uma linguagem de modelagem personalizada chamada HCL. Essa linguagem personalizada é [fácil de escrever e de entender](terraform-create-complete-vm.md). Além disso, por meio do comando “terraform plan”, o Terraform permite visualizar as alterações na infraestrutura antes de confirmá-las. Siga as etapas abaixo para começar a usar o Terraform com o Azure.

## <a name="installing-terraform"></a>Instalando o Terraform
Para instalar o Terraform, [baixe](https://www.terraform.io/downloads.html) o pacote apropriado para seu sistema operacional em um diretório de instalação separado. O download contém um único arquivo executável, para o qual também deve ser definido um PATH global. Encontre instruções sobre como definir o PATH no Linux e Mac [nesta página](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux), enquanto [esta página](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows) contém instruções para definir o PATH no Windows. Verifique a instalação executando o comando “terraform” – você deverá ver uma lista das opções disponíveis do Terraform como a saída.

Em seguida, você precisa permitir o acesso do Terraform à sua assinatura do Azure para executar o provisionamento de infraestrutura.

## <a name="setting-up-terraform-access-to-azure"></a>Configurando o acesso do Terraform ao Azure
Para permitir que o Terraform provisione recursos no Azure, você precisa criar duas entidades no Azure AAD (Azure Active Directory) – Aplicativo do AAD e Entidade de Serviço do AAD. Em seguida, use os identificadores dessas entidades nos scripts do Terraform. A Entidade de Serviço é uma instância local de um Aplicativo do AAD global. Ter uma Entidade de Serviço permite um controle de acesso local granular aos recursos globais.

Há várias maneiras de criar o Aplicativo do AAD e a Entidade de Serviço do AAD. A maneira mais fácil e rápida hoje é usar a CLI 2.0 do Azure, que [pode ser baixada e instalada no Windows/Linux/Mac](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Você também pode usar o PowerShell ou a CLI 1.0 do Azure para criar a infraestrutura de segurança necessária. As instruções abaixo mostram como configurar o Terraform para o Azure usando todas essas abordagens.

### <a name="windowslinuxmac-users-using-azure-cli-20"></a>Usuários do Windows/Linux/Mac usando a CLI 2.0 do Azure
Depois de baixar e instalar a [CLI 2.0 do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), faça logon para administrar sua assinatura do Azure emitindo o seguinte comando

```
az login
```

>[!NOTE]
>Se você estiver usando as Nuvens do Azure China, Azure Alemanha ou Azure Governamental, primeiro você precisa configurar a CLI do Azure para trabalhar com essa Nuvem. Você pode fazer isso executando:

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

Se você tiver várias Assinaturas do Azure, seus detalhes serão retornados pelo comando `az login`. Defina a variável de ambiente `SUBSCRIPTION_ID` para manter o valor do campo `id` retornado da Assinatura que você deseja usar. 

Defina a Assinatura que você deseja usar para esta sessão.

```
az account set --subscription="${SUBSCRIPTION_ID}"
```

Consulte a conta para obter os valores da ID da Assinatura e ID do Locatário.

```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Em seguida, crie credenciais separadas para o Terraform.

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Isso gera a client_id, o client_secret (senha), o sp_name e o tenant. Anote a **client_id** e o **client_secret**.

Confirme suas credenciais (entidade de serviço) abrindo um novo shell e executando os seguintes comandos, substituindo os valores retornados de **sp_name**, **client_secret** e **tenant**:

```
az login --service-principal -u SP_NAME -p CLIENT_SECRET --tenant TENANT
az vm list-sizes --location westus
```

### <a name="windows-users-using-powershell"></a>Usuários do Windows que usam o PowerShell
Se você estiver usando um computador Windows para escrever e executar os scripts do Terraform e preferir usar o PowerShell para tarefas de configuração, precisará configurar o computador com as ferramentas certas do PowerShell. Para fazer isso, (1) [instale as ferramentas do Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps) e (2) baixe e execute o [script azure-setup.ps1](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1) no console do PowerShell. Para executar o script azure-setup.ps1, baixe-o, execute o comando “./azure-setup.ps1 setup” no console do PowerShell e faça logon em sua assinatura do Azure com privilégios administrativos. Em seguida, forneça um nome de aplicativo (cadeia de caracteres arbitrária, obrigatória) quando solicitado e (opcionalmente) forneça uma senha forte. Caso você não forneça a senha, a senha forte será gerada para você usando as bibliotecas de segurança do .Net.

### <a name="linuxmac-users-using-azure-cli-10"></a>Usuários do Linux/Mac que usam a CLI 1.0 do Azure
Para começar a usar o Terraform em computadores Linux ou em Macs com a CLI 1.0 do Azure, você precisa garantir que as bibliotecas apropriadas estão instaladas no computador. Para fazer isso, (1) [instale as ferramentas da CLI xPlat do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli), (2) [baixe e instale](https://stedolan.github.io/jq/download/) o processador JSON jq e (3) baixe e execute o script bash [azure-setup.sh](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) no console. Para executar o script azure-setup.sh, baixe-o, execute o comando “./azure-setup setup” no console e faça logon em sua assinatura do Azure com privilégios administrativos. Em seguida, forneça um nome de aplicativo (cadeia de caracteres arbitrária, obrigatória) e (opcionalmente) forneça uma senha forte, quando solicitado. Caso você não forneça a senha, a senha forte será gerada para você usando as bibliotecas de segurança do .Net.

Todos os scripts acima criam um Aplicativo e uma Entidade de Serviço do AAD, fornecendo à Entidade de Serviço um acesso no nível de colaborador ou de proprietário na assinatura. Devido ao alto nível de acesso concedido, você sempre deve proteger as informações de segurança geradas por esses scripts. Anote todas as quatro informações de segurança fornecidas por esses scripts: client_id, client_secret, subscription_id e tenant_id. 

## <a name="setting-environment-variables"></a>Configurando variáveis de ambiente
Com a Entidade de Serviço do AAD criada e configurada, você precisa informar o Terraform da ID do Locatário, ID da Assinatura, ID do Cliente e do Segredo do Cliente a serem usados. Faça isso inserindo esses valores nos scripts do Terraform (conforme descrito no [próxima seção](terraform-create-complete-vm.md)). Como alternativa, também defina as seguintes variáveis de ambiente (e evite o check-in/compartilhamento acidentais de suas credenciais):

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Veja abaixo um script de shell de exemplo que pode ser usado para definir essas variáveis:

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_client_id
export ARM_CLIENT_SECRET=your_client_secret
export ARM_TENANT_ID=your_tenant_id
```

Além disso, se você estiver usando o Terraform com o Azure Governamental, Azure Alemanha ou Azure China, você precisará definir a variável ENVIRONMENT de forma adequada.

## <a name="next-steps"></a>Próximas etapas
Agora, você instalou o Terraform e configurou as credenciais do Azure para iniciar a implantação da infraestrutura em sua assinatura do Azure. Em seguida, saiba como [criar a infraestrutura com o Terraform](terraform-create-complete-vm.md).
