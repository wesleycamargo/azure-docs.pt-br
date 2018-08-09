---
title: Início Rápido – Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – CLI do Azure | Microsoft Docs
description: Saiba como usar a CLI do Azure para criar um Gateway de Aplicativo do Azure que direciona o tráfego da Web para máquinas virtuais em um pool de back-end.
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 282f6d965ea85b25f1eada1a63897734c6c7b298
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435257"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – CLI do Azure

Com o Gateway de Aplicativo do Azure, você pode direcionar seu tráfego de web de aplicativo para recursos específicos designando ouvintes para portas, criando regras, e adicionando recursos a um pool de back-end.

Este início rápido mostra como usar a CLI do Azure para criar rapidamente o gateway de aplicativo com duas máquinas virtuais em seu pool de back-end. Em seguida, teste-o para verificar se ele está funcionando corretamente.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido requer que você execute a CLI do Azure versão 2.0.4 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Você sempre precisa criar recursos em um grupo de recursos. Criar um grupo de recursos usando [az group create](/cli/azure/group#az-group-create). 

O exemplo a seguir cria um grupo de recursos denominado *myResourceGroupAG* no local *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Criar recursos da rede 

Você precisa criar uma rede virtual para o gateway de aplicativo conseguir se comunicar com outros recursos. Você pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicativo. Duas sub-redes são criadas neste exemplo: uma para o gateway de aplicativo e a outra para as máquinas virtuais. 

Criar a rede virtual e a sub-rede usando [az network vnet create](/cli/azure/vnet#az-vnet-create). Criar o endereço IP público usando [az network public-ip create](/cli/azure/public-ip#az-public-ip-create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, você cria duas máquinas virtuais para serem usadas como servidores de back-end para o gateway do aplicativo. 

### <a name="create-two-virtual-machines"></a>Criar duas máquinas virtuais

Você também pode instalar o NGINX nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito. Você pode usar um arquivo de configuração cloud-init para instalar o NGINX e executar um aplicativo Node.js “Olá, Mundo” em uma máquina virtual Linux. 

No shell atual, crie um arquivo chamado cloud-init.txt e copie e cole a seguinte configuração no shell. Certifique-se de copiar todo o arquivo cloud-init corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Crie as interfaces de rede com [az network create](/cli/azure/network/nic#az-network-nic-create). Crie as máquinas virtuais com [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>Criar o gateway de aplicativo

Crie um gateway de aplicativo usando [az network application-gateway create](/cli/azure/application-gateway#az-application-gateway-create). Quando você cria um gateway de aplicativo usando a CLI do Azure, você pode especificar informações de configuração, como configurações de HTTP, sku e capacidade. Os endereços IP privados das interfaces de rede são adicionados como servidores no pool de back-end do gateway de aplicativo.

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

O gateway de aplicativo pode levar até 30 minutos para ser criado. Depois de criar o gateway de aplicativo, você pode ver esses recursos dele:

- *appGatewayBackendPool* - Um gateway de aplicativo deve ter pelo menos um pool de endereços de back-end.
- *appGatewayBackendHttpSettings* - Especifica que a porta 80 e um protocolo HTTP são usados para comunicação.
- *appGatewayHttpListener* - O ouvinte padrão associado ao *appGatewayBackendPool*.
- *appGatewayFrontendIP* - Atribui *myAGPublicIPAddress* ao *appGatewayHttpListener*.
- *rule1* - A regra padrão de roteamento que está associada ao *appGatewayHttpListener*.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicativo

Não é necessário instalar o NGINX para criar o gateway de aplicativo, mas você o instalou neste início rápido para verificar se o gateway de aplicativo foi criado com êxito. Para obter o endereço IP público do gateway de aplicativo, use [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Copie o endereço IP público e cole-o na barra de endereços do seu navegador.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

![Teste o gateway de aplicativo](./media/quick-create-cli/application-gateway-nginxtest.png)

Quando atualizar o navegador, você deverá ver o nome da outra VM ser exibido.

## <a name="clean-up-resources"></a>Limpar recursos

Primeiro, explore os recursos que foram criados com o gateway de aplicativo e, quando não forem mais necessários, use o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gerenciar o tráfego da web com um gateway de aplicativo usando a CLI do Azure](./tutorial-manage-web-traffic-cli.md)

