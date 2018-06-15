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
ms.date: 04/23/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 381c1c37b0675d97adc058979a5d9b5c4fd2cc8b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32155149"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Início rápido: criar uma máquina virtual Windows Server usando a CLI do Azure na pilha do Azure

‎*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode criar uma máquina de virtual do Windows Server 2016 usando a CLI do Azure. Siga as etapas neste artigo para criar e usar uma máquina virtual. Este artigo fornece as seguintes etapas:

* Conecte-se à máquina virtual com um cliente remoto.
* Instale o servidor web do IIS e exibir a página inicial padrão.
* Limpe seus recursos.

## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que o operador de pilha do Azure adicionada a **Windows Server 2016** imagem no mercado de pilha do Azure.

* A pilha do Azure requer uma versão específica de CLI do Azure para criar e gerenciar os recursos. Se você não tiver a CLI do Azure configurada para a pilha do Azure, siga as etapas para [instalar e configurar a CLI do Azure](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico em que você pode implantar e gerenciar recursos da pilha do Azure. Em seu ambiente de pilha do Azure, execute o [criar grupo az](/cli/azure/group#az_group_create) comando para criar um grupo de recursos.

>[!NOTE]
 Os valores são atribuídos para todas as variáveis nos exemplos de código. No entanto, você pode atribuir novos valores se desejar.

O exemplo a seguir cria um grupo de recursos denominado myResourceGroup no local a local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Criar uma máquina virtual (VM) usando o [criar vm az](/cli/azure/vm#az_vm_create) comando. O exemplo a seguir cria uma VM chamada myVM. Este exemplo usa Demouser para um nome de usuário administrativo e Demouser@123 como a senha do usuário. Altere esses valores para algo que é apropriado para seu ambiente.

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

Quando a VM é criada, o **PublicIPAddress** parâmetro na saída contém o endereço IP público para a máquina virtual. Anote esse endereço, porque você precisa para acessar a máquina virtual.

## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web

Como essa VM para executar o servidor web do IIS, você precisa abrir a porta 80 para tráfego de Internet.

Use o [az vm abrir portas](/cli/azure/vm#open-port) comando para abrir a porta 80.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

Use o seguinte comando para criar uma conexão de área de trabalho remota à sua máquina virtual. Substitua o "Endereço IP público" com o endereço IP da máquina virtual. Quando solicitado, insira o nome de usuário e a senha que você usou para a máquina virtual.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Instalar o IIS usando o PowerShell

Agora que você fez logon na máquina virtual, você pode usar o PowerShell para instalar o IIS. Inicie o PowerShell na máquina virtual e execute o seguinte comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Exibir a página de boas-vindas do IIS

Você pode usar um navegador da web de sua escolha para exibir a página de boas-vinda do IIS padrão. Use o endereço IP público documentado na seção anterior para visitar a página padrão.

![Site do IIS padrão](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Limpar recursos

Limpe os recursos que você não precisa mais. Use o [excluir grupo de az](/cli/azure/group#az_group_delete) de comando para remover o grupo de recursos, a máquina virtual, e todos os recursos relacionados.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Este guia de início rápido, você implantado uma básica da máquina virtual do Windows Server. Para saber mais sobre as máquinas virtuais de pilha do Azure, continuar [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).
