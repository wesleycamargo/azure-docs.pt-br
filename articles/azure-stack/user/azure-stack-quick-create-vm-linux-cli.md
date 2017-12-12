---
title: "Criar uma máquina virtual Linux usando a CLI do Azure na pilha do Azure | Microsoft Docs"
description: "Crie uma máquina virtual Linux com CLI na pilha do Azure."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 21F7D599-1FEC-4827-A5C3-06495C5F53A4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: ea0bc72c03c7c51f79b838493eb2f6d3efe4f8f7
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Criar uma máquina virtual Linux usando a CLI do Azure na pilha do Azure

*Aplica-se a: sistemas integrados de pilha do Azure*

CLI do Azure é usado para criar e gerenciar recursos de pilha do Azure a partir da linha de comando. Esses detalhes de início rápido usando a CLI do Azure para criar uma máquina virtual Linux na pilha do Azure.  Depois que a VM é criada, um servidor web está instalado e a porta 80 é aberta para permitir o tráfego da web.

## <a name="prerequisites"></a>Pré-requisitos 

* Certifique-se de que seu operador de pilha do Azure adicionou a imagem "Ubuntu Server 16.04 LTS" para o marketplace de pilha do Azure. 

* A pilha do Azure requer uma versão específica de CLI do Azure para criar e gerenciar os recursos. Se você não tiver a CLI do Azure configurada para a pilha do Azure, entrar para o [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou um cliente com Windows externo se você estiver [conectado por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) e siga as etapas para [instalar e configurar a CLI do Azure](azure-stack-connect-cli.md).

* Uma chave SSH pública com o nome id_rsa.pub deve ser criada no diretório .ssh do seu perfil de usuário do Windows. Para obter informações detalhadas sobre a criação de chaves SSH, consulte [chaves Criando SSH no Windows](../../virtual-machines/linux/ssh-from-windows.md). 

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
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Uma vez concluído, o comando será parâmetros de saída para sua máquina virtual.  Anote o *PublicIPAddress*, uma vez que você pode usar isso para se conectar e gerenciar sua máquina virtual.

## <a name="open-port-80-for-web-traffic"></a>Abra a porta 80 para tráfego da Web

Por padrão, somente as conexões de SSH são permitidas em máquinas virtuais Linux implantadas no Azure. Se essa VM for se transformar em um servidor Web, você precisará abrir a porta 80 na Internet. Use o comando [az vm open-port](/cli/azure/vm#open-port) para abrir a porta desejada.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>SSH em sua VM

De um sistema com o SSH instalado, use o comando a seguir para se conectar à máquina virtual. Se estiver usando o Windows, o [Putty](http://www.putty.org/) poderá ser usado para criar a conexão. Certifique-se de substituir o endereço IP público correto de sua máquina virtual. Em nosso exemplo acima, o endereço IP foi 192.168.102.36.

```bash
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Instalar o NGINX

Use o seguinte script bash para atualizar fontes de pacote e instalar o pacote mais recente do NGINX. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Exibir a página de boas-vindas do NGINX

Com o NGINX instalado e a porta 80 que agora está aberta na sua VM da Internet, você pode usar um navegador da Web de sua escolha para exibir a página de boas-vindas do NGINX padrão. Certifique-se de usar o *publicIPAdress* que você documentou acima para visitar a página padrão. 

![Site padrão NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Este guia de início rápido, você implantou uma simple máquina virtual do Linux. Para saber mais sobre as máquinas virtuais de pilha do Azure, continuar [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).

