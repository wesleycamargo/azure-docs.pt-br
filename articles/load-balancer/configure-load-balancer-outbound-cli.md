---
title: Configurar regras de saída e de balanceamento de carga usando a CLI do Azure
titlesuffix: Azure Load Balancer
description: Este artigo mostra como configurar regras de saída e de balanceamento de carga em um Standard Load Balancer usando a CLI do Azure.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: kumud
ms.openlocfilehash: f28088a1a0586964092a0b5f86ce8bf0f95402cd
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281940"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Configurar regras de saída e de balanceamento de carga no Standard Load Balancer usando a CLI do Azure

Este início rápido mostra como configurar regras de saída no Standard Load Balancer usando a CLI do Azure.  

Quando você terminar, o recurso Load Balancer conterá dois front-ends e regras associadas a eles: um para entrada e outro para saída.  Cada front-end tem uma referência a um endereço IP público e este cenário usa o um endereço IP público diferente para a entrada, ao contrário da saída.   A regra de balanceamento de carga fornece apenas balanceamento de carga de entrada e a regra de saída controla a NAT de saída fornecida para a VM.  Este guia de início rápido usa dois back-end separados pools, um para entrada e outra para saída, para ilustrar o recurso e permitir flexibilidade para esse cenário.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O seguinte exemplo cria um grupo de recursos chamado *myresourcegroupoutbound* no local *eastus2*:

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Criar rede virtual
Crie uma rede virtual chamada *myvnetoutbound* com uma sub-rede chamada *mysubnetoutbound* em *myresourcegroupoutbound* usando [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Criar um endereço IP Público de entrada 

Para acessar seu aplicativo Web na Internet, você precisará de um endereço IP público para o balanceador de carga. Um balanceador de carga padrão só oferece suporte a endereços IP públicos padrão. Use [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) para criar um endereço IP Público Padrão chamado *mypublicipinbound* em *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Criar um endereço IP público de saída 

Criar um endereço IP Standard para o uso de configuração de saída de front-end do Load Balancer [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Criar o Azure Load Balancer

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:
  - Um IP de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  - Um pool de back-end em que o IP de front-end envia a carga balanceada tráfego de rede.
  - Um pool de back-end para conectividade de saída. 
  - Uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
  - Uma regra de entrada de balanceador de carga que define como o tráfego é distribuído para as VMs.
  - Uma regra de saída de balanceador de carga que define como o tráfego é distribuído das VMs.

### <a name="create-load-balancer"></a>Criar balanceador de carga

Criar um balanceador de carga com o endereço IP entrada usando [criar az network lb](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) denominado *lb* que inclui uma configuração de IP de entrada de front-end e um pool de back-end *bepoolinbound*que está associado com o endereço IP público *mypublicipinbound* que você criou na etapa anterior.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>Criar pool de saída

Criar um pool de endereços de back-end adicionais para definir a conectividade de saída para um pool de VMs com [Criar pool de endereços de balanceamento de carga de rede de az](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) com o nome *bepooloutbound*.  Criar um pool separado de saída fornece máxima flexibilidade, mas você pode omitir essa etapa e usar apenas a entrada *bepoolinbound* também.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Criar o IP de front-end de saída
Crie a configuração de IP de front-end de saída para o Load Balancer com [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest), que inclui a configuração de IP de front-end de saída chamada *myfrontendoutbound*, associado ao endereço IP público *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Criar uma investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para se certificar de que ela pode enviar o tráfego de rede. A instância de máquina virtual com verificações de investigação com falha é removida do balanceador de carga até ele ficar online novamente e as verificações de investigação determinarem sua integridade. Crie uma investigação de integridade com [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) para monitorar a integridade das máquinas virtuais. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Criar uma regra de balanceamento de carga

Uma regra de balanceador de carga define a configuração de IP de front-end para o tráfego de entrada e o pool de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Crie uma regra de balanceador de carga *myinboundlbrule* com [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) para escutar a porta 80 no pool de front-end *myfrontendinbound* e enviar o tráfego de rede com a carga balanceada ao pool de endereços de back-end *bepool* também usando a porta 80. 

>[!NOTE]
>Essa regra de balanceamento de carga desabilita a (S)NAT de saída automática como resultado dessa regra com o parâmetro --disable-outbound-snat. A NAT de saída é fornecida apenas pela regra de saída.

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepoolinbound \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Criar uma regra de saída

Uma regra de saída define o IP público de front-end, representado pelo *myfrontendoutbound* de front-end, que será usado para todo o tráfego do NAT de saída, bem como o pool de back-end ao qual essa regra se aplica.  Crie uma regra de saída *myoutboundrule* para a conversão de rede de saída de todas as máquinas virtuais (configurações de IP da NIC) no pool de back-end *bepool*.  O comando abaixo também altera o tempo limite ocioso de saída de 4 para 15 minutos e aloca 10.000 portas SNAT em vez de 1.024.  Examine [Regras de saída](https://aka.ms/lboutboundrules) para obter mais detalhes.

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepooloutbound
```

Se você não quiser usar um pool separado de saída, você pode alterar o argumento de pool de endereços no comando anterior para especificar *bepoolinbound* em vez disso.  É recomendável usar pools separados para flexibilidade e facilitar a leitura da configuração resultante.

Neste ponto, você pode prosseguir com a adição da sua VM ao pool de back-end *bepoolinbound* __e__ *bepooloutbound* atualizando a configuração de IP da NIC respectivo recursos usando [az network nic ip-config-pool de endereços adicionar](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Próximos passos
Neste artigo, você criou um Standard Load Balancer, configurou regras de tráfego de entrada e de balanceador de carga e configurou uma investigação de integridade para as VMs no pool de back-end. Para saber mais sobre o Azure Load Balancer, continue nos tutoriais do Azure Load Balancer.

> [!div class="nextstepaction"]
> [Tutoriais do balanceador de carga do Azure](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
