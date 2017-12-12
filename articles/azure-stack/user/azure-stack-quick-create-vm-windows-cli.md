---
title: "Criar uma máquina virtual do Windows na pilha do Azure usando a CLI do Azure | Microsoft Docs"
description: Saiba como criar uma VM do Windows na pilha do Azure usando a CLI do Azure
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 36930cc38b6c1933b58651f6c63e7d5d453c447d
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-windows-virtual-machine-on-azure-stack-using-azure-cli"></a>Criar uma máquina virtual do Windows na pilha do Azure usando a CLI do Azure

CLI do Azure é usado para criar e gerenciar recursos de pilha do Azure a partir da linha de comando. Esses detalhes de guia usando a CLI do Azure para criar uma máquina de virtual do Windows Server 2016 na pilha do Azure. Depois que a máquina virtual é criada, irá se conectar com a área de trabalho remota, instalar o IIS, e exibir o site padrão. 

## <a name="prerequisites"></a>Pré-requisitos 

* Certifique-se de que seu operador de pilha do Azure adicionou a imagem de "Windows Server 2016" para o marketplace de pilha do Azure.  

* A pilha do Azure requer uma versão específica de CLI do Azure para criar e gerenciar os recursos. Se você não tiver a CLI do Azure configurada para a pilha do Azure, siga as etapas para [instalar e configurar a CLI do Azure](azure-stack-connect-cli.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico em qual pilha Azure recursos são implantados e gerenciados. O kit de desenvolvimento ou a pilha do Azure integrada ao sistema, execute o [criar grupo az](/cli/azure/group#create) comando para criar um grupo de recursos. Atribuímos valores para todas as variáveis neste documento, você pode usá-los como estão ou atribuir um valor diferente. O exemplo a seguir cria um grupo de recursos denominado myResourceGroup no local a local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma VM com o comando [az vm create](/cli/azure/vm#create). O exemplo a seguir cria uma VM chamada myVM. Este exemplo usa Demouser para um nome de usuário administrativo e Demouser@123 como a senha. Atualize esses valores para algo apropriado para seu ambiente. Esses valores são necessários para se conectar à máquina virtual.

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

Quando a VM é criada, anote o *PublicIPAddress* parâmetro que é a saída, que você usará para acessar a máquina virtual.
 
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

Agora que você fez logon na VM do Azure, pode usar uma única linha do PowerShell para instalar o IIS e habilitar a regra de firewall local para permitir o tráfego da Web. Abra um promt do PowerShell e execute o seguinte comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Exibir a página de boas-vindas do IIS

Com o IIS instalado e a porta 80 que agora está aberta na sua VM da Internet, você pode usar um navegador da Web de sua escolha para exibir a página de boas-vindas do IIS padrão. Certifique-se de usar o endereço IP público que você documentou acima para visitar a página padrão. 

![Site do IIS padrão](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png) 

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Este guia de início rápido, você implantou a máquina virtual do Windows simple. Para saber mais sobre as máquinas virtuais de pilha do Azure, continuar [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).
