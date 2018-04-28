---
title: Criar uma máquina virtual do Windows na pilha do Azure usando a CLI do Azure | Microsoft Docs
description: Saiba como criar uma VM do Windows na pilha do Azure usando a CLI do Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/19/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 5665af14b9b0d0705b68c8a27c593b19c31b053e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-stack-using-azure-cli"></a>Início rápido: criar uma máquina virtual do Windows na pilha do Azure usando a CLI do Azure

CLI do Azure é usado para criar e gerenciar recursos de pilha do Azure a partir da linha de comando. Este artigo mostra como usar a CLI do Azure para criar e acessar uma máquina virtual do Windows Server 2016 na pilha do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que seu operador de pilha do Azure adicionou a imagem de "Windows Server 2016" para o marketplace de pilha do Azure.

* A pilha do Azure requer uma versão específica de CLI do Azure para criar e gerenciar os recursos. Se você não tiver a CLI do Azure configurada para a pilha do Azure, siga as etapas para [instalar e configurar a CLI do Azure](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico em qual pilha Azure recursos são implantados e gerenciados. O kit de desenvolvimento ou a pilha do Azure integrada ao sistema, execute o [criar grupo az](/cli/azure/group#az_group_create) comando para criar um grupo de recursos. Os valores são atribuídos para todas as variáveis neste documento, você pode usar esses valores ou atribuir novos valores. O exemplo a seguir cria um grupo de recursos denominado myResourceGroup no local a local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com o comando [az vm create](/cli/azure/vm#az_vm_create). O exemplo a seguir cria uma VM chamada myVM. Este exemplo usa Demouser para um nome de usuário administrativo e Demouser@123 como a senha. Atualize esses valores para algo apropriado para seu ambiente. Esses valores são necessários para se conectar à máquina virtual.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Quando a VM é criada, o *PublicIPAddress* parâmetro é de saída. Anote esse endereço, porque você precisa acessar a máquina virtual.

## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web

Por padrão, apenas as conexões RDP são permitidas para uma máquina virtual do Windows implantada na pilha do Azure. Se essa VM for se transformar em um servidor Web, você precisará abrir a porta 80 na Internet. Use o comando [az vm open-port](/cli/azure/vm#open-port) para abrir a porta desejada.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

Use o seguinte comando para criar uma sessão de área de trabalho remota com a máquina virtual. Substitua o endereço IP pelo endereço IP público de sua máquina virtual. Quando solicitado, insira as credenciais usadas ao criar a máquina virtual.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Instalar o IIS usando o PowerShell

Depois de fazer logon VM do Azure, você pode usar uma única linha do PowerShell para instalar o IIS e habilitar a regra de firewall local para permitir o tráfego da web. Abra um promt do PowerShell e execute o seguinte comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Exibir a página de boas-vindas do IIS

Você pode usar um navegador da web de sua escolha para exibir a página de boas-vinda do IIS padrão. Use o endereço IP público documentado na seção anterior para visitar a página padrão.

![Site do IIS padrão](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não é mais necessário, você pode usar o [excluir grupo de az](/cli/azure/group#az_group_delete) de comando para remover o grupo de recursos, a VM, e todos os recursos relacionados.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Este guia de início rápido, você implantou a máquina virtual do Windows simple. Para saber mais sobre as máquinas virtuais de pilha do Azure, continuar [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).
