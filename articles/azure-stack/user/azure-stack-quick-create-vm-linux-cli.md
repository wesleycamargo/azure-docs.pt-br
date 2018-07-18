---
title: Criar uma máquina virtual Linux usando a CLI do Azure na pilha do Azure | Microsoft Docs
description: Crie uma máquina virtual Linux com CLI na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 21F7D599-1FEC-4827-A5C3-06495C5F53A4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 90b36183ba32e75e06d434098d26cb10f3736373
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Início rápido: criar uma máquina virtual do servidor Linux usando a CLI do Azure na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode criar uma máquina virtual de Ubuntu Server 16.04 LTS usando a CLI do Azure. Siga as etapas neste artigo para criar e usar uma máquina virtual. Este artigo fornece as etapas para:

* Conecte-se à máquina virtual com um cliente remoto.
* Instalar o servidor de web NGINX e exibir a página inicial padrão.
* Limpe os recursos não utilizados.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma imagem do Linux no mercado de pilha do Azure**

   Por padrão, o marketplace do Azure pilha não contêm uma imagem do Linux. Obter o operador de pilha do Azure para fornecer o **Ubuntu Server 16.04 LTS** imagem que você precisa. O operador pode usar as etapas descritas no [baixar itens do marketplace do Azure para o Azure pilha](../azure-stack-download-azure-marketplace-item.md) artigo.

* A pilha do Azure requer uma versão específica da CLI do Azure para criar e gerenciar os recursos. Se você não tiver a CLI do Azure configurada para a pilha do Azure, entrar para o [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou um cliente com Windows externo se você estiver [conectado por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) e siga as etapas para [ instalar e configurar a CLI do Azure](azure-stack-version-profiles-azurecli2.md).

* Uma chave SSH pública com o id_rsa.pub nome salvo no diretório .ssh do seu perfil de usuário do Windows. Para obter informações detalhadas sobre como criar chaves SSH, consulte [chaves Criando SSH no Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico em que você pode implantar e gerenciar recursos da pilha do Azure. O kit de desenvolvimento ou a pilha do Azure integrada ao sistema, execute o [criar grupo az](/cli/azure/group#az_group_create) comando para criar um grupo de recursos.

>[!NOTE]
 Os valores são atribuídos para todas as variáveis nos exemplos de código. No entanto, você pode atribuir novos valores se desejar.

O exemplo a seguir cria um grupo de recursos denominado myResourceGroup no local a local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Criar uma máquina virtual usando o [criar vm az](/cli/azure/vm#az_vm_create) comando. O exemplo a seguir cria uma VM chamada myVM. Este exemplo usa Demouser para um nome de usuário administrativo e Demouser@123 como a senha do usuário. Altere esses valores para algo que é apropriado para seu ambiente.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

O endereço IP público é retornado no **PublicIpAddress** parâmetro. Anote esse endereço, porque você precisa para acessar a máquina virtual.

## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web

Como esta máquina virtual para executar o servidor web do IIS, você precisa abrir a porta 80 para tráfego de Internet. Use o comando [az vm open-port](/cli/azure/vm#open-port) para abrir a porta desejada.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Usar o SSH para se conectar à máquina virtual

Em um computador cliente com SSH instalado, conecte-se à máquina virtual. Se você estiver trabalhando em um cliente do Windows, use [Putty](http://www.putty.org/) para criar a conexão. Para se conectar à máquina virtual, use o seguinte comando:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Instalar o servidor de web NGINX

Para atualizar recursos do pacote e instalar o pacote NGINX mais recente, execute o seguinte script:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Exibir a página de boas-vindas do NGINX

Com NGINX instalado e a porta 80 aberta na sua máquina virtual, você pode acessar o servidor web usando o endereço IP público da máquina virtual. Abra um navegador da web e navegue até ```http://<public IP address>```.

![Página de boas-vindas NGINX web server](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Limpar recursos

Limpe os recursos que você não precisa mais. Você pode usar o [excluir grupo de az](/cli/azure/group#az_group_delete) comando para remover esses recursos. Para excluir o grupo de recursos e todos os seus recursos, execute o seguinte comando:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Este guia de início rápido, você implantado uma máquina de virtual de servidor Linux básico com um servidor web. Para saber mais sobre as máquinas virtuais de pilha do Azure, continuar [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).
