---
title: Executar Ansible com Back no Azure Cloud Shell
description: Saiba como executar várias tarefas do Ansible com Bash no Azure Cloud Shell
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/07/2018
ms.topic: article
ms.openlocfilehash: 6bfac47e4afa41b4c75a8d33b4eea1ff5103296d
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050887"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Executar Ansible com Back no Azure Cloud Shell

Neste tutorial, você aprenderá a usar o Bash no Cloud Shell para configurar uma assinatura do Azure como seu workspace Ansible. 

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure** - Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Configure o Azure Cloud Shell**: se você é novo no Azure Cloud Shell, o artigo [Guia de início rápido do Bash no Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) ilustra como iniciar e configurar o Cloud Shell. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuração automática de credencial

Quando conectado ao Cloud Shell, o Ansible autentica com o Azure para gerenciar a infraestrutura sem qualquer configuração adicional. Se você tiver mais de uma assinatura, poderá escolher com qual assinatura o Ansible deverá trabalhar exportando a variável de ambiente `AZURE_SUBSCRIPTION_ID`. Para listar todas as suas assinaturas do Azure, execute o seguinte comando:

```azurecli-interactive
az account list
```

Usando o **id** da assinatura com a qual você deseja trabalhar, defina o **AZURE_SUBSCRIPTION_ID** da seguinte forma:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Verificar a configuração
Para verificar a configuração bem-sucedida, use o Ansible para criar um grupo de recursos.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Criar uma máquina virtual básica no Azure com Ansible](/azure/virtual-machines/linux/ansible-create-vm)