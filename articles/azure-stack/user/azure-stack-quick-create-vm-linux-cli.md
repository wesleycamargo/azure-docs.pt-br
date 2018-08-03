---
title: Criar uma máquina virtual Linux usando a CLI do Azure no Azure Stack | Microsoft Docs
description: Crie uma máquina virtual Linux com a CLI do Azure Stack.
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
ms.openlocfilehash: f6f2860b1ae1e88495e2dad3916a0216bf5d0726
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428784"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Início rápido: criar uma máquina virtual do servidor Linux usando a CLI do Azure no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode criar uma máquina virtual do Ubuntu Server 16.04 LTS, usando a CLI do Azure. Siga as etapas neste artigo para criar e usar uma máquina virtual. Este artigo fornece as etapas para:

* Conecte-se à máquina virtual com um cliente remoto.
* Instalar o servidor web NGINX e exibir a página inicial padrão.
* Limpe recursos não utilizados.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma imagem do Linux no marketplace do Azure Stack**

   Por padrão, o Azure Stack marketplace não contêm uma imagem do Linux. Obter o operador do Azure Stack para fornecer a **Ubuntu Server 16.04 LTS** imagem que você precisa. O operador pode usar as etapas descritas a [baixar itens do marketplace do Azure para o Azure Stack](../azure-stack-download-azure-marketplace-item.md) artigo.

* O Azure Stack requer uma versão específica da CLI do Azure para criar e gerenciar os recursos. Se você não tiver a CLI do Azure configurado para o Azure Stack, entre na [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou um cliente de externo com base no Windows se você estiver [conectados por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) e siga as etapas para [ instalar e configurar a CLI do Azure](azure-stack-version-profiles-azurecli2.md).

* Uma chave SSH pública com o id_rsa nome salvo no diretório. SSH do seu perfil de usuário do Windows. Para obter informações detalhadas sobre como criar chaves SSH, consulte [criação de chaves SSH no Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual você pode implantar e gerenciar recursos do Azure Stack. Do seu kit de desenvolvimento ou o Azure Stack integrada ao sistema, execute as [criar grupo de az](/cli/azure/group#az-group-create) comando para criar um grupo de recursos.

>[!NOTE]
 Valores são atribuídos a todas as variáveis nos exemplos de código. No entanto, você pode atribuir novos valores se você quiser.

O exemplo a seguir cria um grupo de recursos chamado myResourceGroup no local de local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Criar uma máquina virtual usando o [criar vm az](/cli/azure/vm#az-vm-create) comando. O exemplo a seguir cria uma VM chamada myVM. Este exemplo usa Demouser para um nome de usuário administrativo e Demouser@123 como a senha do usuário. Altere esses valores para algo apropriado para seu ambiente.

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

O endereço IP público é retornado na **PublicIpAddress** parâmetro. Anote esse endereço, pois precisará dele para acessar a máquina virtual.

## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web

Porque essa máquina virtual será executado o servidor web do IIS, você precisará abrir a porta 80 para tráfego da Internet. Use o comando [az vm open-port](/cli/azure/vm#open-port) para abrir a porta desejada.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Use o SSH para se conectar à máquina virtual

Em um computador cliente com o SSH instalado, conecte-se à máquina virtual. Se você estiver trabalhando em um cliente Windows, use [Putty](http://www.putty.org/) para criar a conexão. Para se conectar à máquina virtual, use o seguinte comando:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Instalar o servidor web NGINX

Para atualizar os recursos de pacote e instalar o pacote mais recente do NGINX, execute o seguinte script:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Exibir a página de boas-vindas do NGINX

Com o NGINX instalado e a porta 80 está aberta na sua máquina virtual, você pode acessar o servidor web usando o endereço IP público da máquina virtual. Abra um navegador da web e navegue até ```http://<public IP address>```.

![Página de boas-vindas do NGINX web server](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Limpar recursos

Limpe todos os recursos que você não precisa mais. Você pode usar o [exclusão de grupo az](/cli/azure/group#az-group-delete) comando para remover esses recursos. Para excluir o grupo de recursos e todos os seus recursos, execute o seguinte comando:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina virtual de servidor Linux básica com um servidor web. Para saber mais sobre máquinas virtuais do Azure Stack, continue [considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md).
