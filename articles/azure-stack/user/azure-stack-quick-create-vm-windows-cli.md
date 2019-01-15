---
title: Criar uma máquina virtual do Windows no Azure Stack usando a CLI do Azure | Microsoft Docs
description: Saiba como criar uma VM Windows no Azure Stack usando a CLI do Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 1/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 8838172313ec40298ccd564a6a3f84314461a9b1
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54301970"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Início rápido: criar uma máquina virtual Windows Server usando a CLI do Azure no Azure Stack

‎*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode criar uma máquina virtual Windows Server 2016 usando a CLI do Azure. Siga as etapas neste artigo para criar e usar uma máquina virtual. Este artigo fornece as seguintes etapas:

* Conecte-se à máquina virtual com um cliente remoto.
* Instalar o servidor web IIS e exibir a página inicial padrão.
* Limpe seus recursos.

## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que o operador do Azure Stack é adicionado a **Windows Server 2016** imagem no Marketplace do Azure Stack.

* O Azure Stack requer uma versão específica da CLI do Azure para criar e gerenciar os recursos. Se você não tiver a CLI do Azure configurado para o Azure Stack, siga as etapas a serem [instalar e configurar a CLI do Azure](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual você pode implantar e gerenciar recursos do Azure Stack. Em seu ambiente do Azure Stack, execute as [criar grupo de az](/cli/azure/group#az-group-create) comando para criar um grupo de recursos.

>[!NOTE]
 Valores são atribuídos a todas as variáveis nos exemplos de código. No entanto, você pode atribuir novos valores se você quiser.

O exemplo a seguir cria um grupo de recursos chamado myResourceGroup no local de local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Criar uma máquina virtual (VM) usando o [criar vm az](/cli/azure/vm#az-vm-create) comando. O exemplo a seguir cria uma VM chamada myVM. Este exemplo usa Demouser para um nome de usuário administrativo e Demouser@123 como a senha do usuário. Altere esses valores para algo apropriado para seu ambiente.

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

Quando a VM é criada, o **PublicIPAddress** parâmetro na saída contém o endereço IP público para a máquina virtual. Anote esse endereço, pois precisará dele para acessar a máquina virtual.

## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web

Como essa VM será para executar o servidor web do IIS, você precisará abrir a porta 80 para tráfego da Internet.

Use o [az vm open-port](/cli/azure/vm#open-port) comando para abrir a porta 80.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

Use o seguinte comando para criar uma conexão de área de trabalho remota para sua máquina virtual. Substitua o "Endereço IP público" com o endereço IP de sua máquina virtual. Quando solicitado, insira o nome de usuário e senha que você usou para a máquina virtual.

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

Limpe todos os recursos que você não precisa mais. Use o [exclusão de grupo az](/cli/azure/group#az-group-delete) de comando para remover o grupo de recursos, a máquina virtual, e todos os recursos relacionados.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina de virtual básica do Windows Server. Para saber mais sobre máquinas virtuais do Azure Stack, continue [considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md).
