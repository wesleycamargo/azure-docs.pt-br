---
title: "Pré-requisitos do OpenShift no Azure | Microsoft Docs"
description: "Pré-requisitos para implantar o OpenShift no Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 0c90b8a6d17fa293b6708d942afd35e1333623cb
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="common-prerequisites-for-openshift-in-azure"></a>Pré-requisitos comuns para OpenShift no Azure

Ao implantar o OpenShift no Azure, há alguns pré-requisitos comuns, independentemente de se você estiver implantando a Origem do OpenShift ou o OpenShift Container Platform.

A instalação do OpenShift é feita por meio de guias estratégicos do Ansible. O Ansible usa SSH para se conectar a todos os hosts que farão parte do cluster para concluir as etapas de instalação.
Quando a conexão SSH é iniciada para os hosts remotos, não é possível inserir uma frase secreta. Por esse motivo, a Chave privada não pode ter uma frase secreta associada a ela ou a implantação falhará.
Como todas as VMs são implantadas por meio de modelos do Resource Manager, a mesma Chave pública é usada para acessar todas as VMs. Precisamos inserir a Chave privada correspondente na VM que está executando todos os guias estratégicos também.
Para fazer isso com segurança, usamos um Azure Key Vault para passar a Chave privada para a VM.

Se houver uma necessidade de armazenamento persistente para contêineres, então Volumes Persistentes (PVs) serão necessários. Esses PVs precisam ter suporte por alguma forma de armazenamento persistente. O OpenShift oferece suporte a discos do Azure (VHDs) para essa funcionalidade, mas, primeiro, deve ser configurado como o Provedor de Nuvem. Nesse modelo, o OpenShift:

- Criará um objeto VHD em uma conta de Armazenamento do Microsoft Azure
- Montará o VHD em uma VM e formatará o volume
- Montará o volume no Pod

Para que isso funcione, o OpenShift precisa de permissões para executar as tarefas anteriores no Azure. Para fazer isso, uma Entidade de serviço é necessária. A Entidade de serviço (SP) é uma conta de segurança no Azure Active Directory que tem as permissões para recursos.
A Entidade de serviço deve ter acesso a Contas de armazenamento e máquinas virtuais que compõem o cluster. Se todos os recursos de cluster do OpenShift forem implantados em um único Grupo de recursos, a SP pode receber permissões para esse Grupo de recursos.

Este guia descreve como criar os artefatos associados aos pré-requisitos.

> [!div class="checklist"]
> * Crie um KeyVault para gerenciar chaves SSH para o cluster OpenShift.
> * Crie uma Entidade de serviço para uso pelo Provedor de nuvem do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure 
Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela ou clique em **Experimentar** para usar o Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. É recomendável que um grupo de recursos dedicado seja usado para hospedar o Key Vault – separado do grupo de recursos em que os recursos de cluster do OpenShift serão implantados. 

O exemplo abaixo cria um grupo de recursos denominado *keyvaultrg* no local *eastus*.

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Criar um cofre de chave
Crie um KeyVault para armazenar as chaves de SSH para o cluster com o comando [keyvault az creata](/cli/azure/keyvault#create). O nome do Key Vault deve ser globalmente exclusivo.

O exemplo abaixo cria um keyvault denominado *keyvault* no grupo de recursos *keyvaultrg*.

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Criar uma chave SSH 
Uma chave SSH é necessária para proteger o acesso ao cluster de origem OpenShift. Crie um par de chaves SSH usando o comando `ssh-keygen` (no Linux ou Mac).
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> O par de chaves SSH que você cria não deve ter uma senha.

Para obter mais informações sobre as chaves SSH no Windows, confira [Como criar chaves SSH no Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-ssh-private-key-in-key-vault"></a>Armazenar chave privada SSH no cofre de chaves
A implantação de OpenShift usa a chave SSH criada para proteger o acesso ao mestre OpenShift. Para habilitar a implantação para recuperar a chave SSH com segurança, armazene a chave no Key Vault usando o comando a seguir:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço 
O OpenShift se comunica com o Azure usando um nome de usuário e i,a senha ou uma entidade de serviço. Uma entidade de serviço do Azure é uma identidade de segurança que você pode usar com aplicativos, serviços e ferramentas de automação como o OpenShift. Você controla e define as permissões referentes a quais operações a entidade de serviço pode executar no Azure. Para aprimorar a segurança, em vez de apenas fornecer um nome de usuário e uma senha, este exemplo cria uma entidade de serviço básica.

Crie uma entidade de serviço com [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) e gere as credenciais necessárias para o OpenShift.

O exemplo a seguir cria uma entidade de serviço e atribui permissões de Colaborador a um grupo de recursos denominado myResourceGroup. Se usar o Windows, execute ```az group show --name myResourceGroup --query id``` separadamente e use a saída para alimentar a opção de escopos --.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Anote a propriedade appId retornada do comando.
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",            
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Não crie uma senha não segura.  Execute a orientação [Restrições e regras de senha do Azure AD](/azure/active-directory/active-directory-passwords-policy).

Para obter mais informações sobre entidades de serviço, confira [Criar entidade de serviço do Azure com a CLI 2.0 do Azure](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="next-steps"></a>Próximas etapas

Este artigo abordou os seguintes tópicos:
> [!div class="checklist"]
> * Crie um KeyVault para gerenciar chaves SSH para o cluster OpenShift.
> * Crie uma Entidade de serviço para uso pelo Provedor de nuvem do Azure.

Agora, Implante um cluster di OpenShift

- [Implantar a origem do OpenShift](./openshift-origin.md)
- [Implantar o OpenShift Container Platform](./openshift-container-platform.md)

