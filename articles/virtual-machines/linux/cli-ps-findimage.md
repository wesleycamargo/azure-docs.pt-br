---
title: Selecionar imagens da VM Linux com a CLI do Azure | Microsoft Docs
description: Saiba como usar a CLI do Azure para determinar o editor, oferta, SKU e versão de imagens de VM do Marketplace.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/28/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33ffb9d1685f3d76e884ae0d90545f659b5ec87c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953337"
---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Como localizar imagens de VMs Linux no Azure Marketplace com a CLI do Azure
Este tópico descreve como usar a CLI do Azure para localizar imagens de VM no Azure Marketplace. Use essas informações para especificar uma imagem do Marketplace quando você criar uma VM programaticamente com a CLI, os modelos do Gerenciador de Recursos ou outras ferramentas.

Procure também imagens e ofertas disponíveis usando a vitrine do [Azure Marketplace](https://azuremarketplace.microsoft.com/), o [portal do Azure](https://portal.azure.com) ou o [Azure PowerShell](../windows/cli-ps-findimage.md). 

Certifique-se de que você instalou a versão mais recente [CLI do Azure](/cli/azure/install-azure-cli) e são registrados uma conta do Azure (`az login`).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="list-popular-images"></a>Listar imagens populares

Execute o comando [az vm image list](/cli/azure/vm/image#az_vm_image_list) sem a opção `--all` para ver uma lista de imagens de VM populares no Azure Marketplace. Por exemplo, execute o comando a seguir para exibir uma lista armazenada em cache de imagens populares em formato de tabela:

```azurecli
az vm image list --output table
```

A saída inclui o URN da imagem (o valor na coluna *Urn*). Ao criar uma VM com uma das imagens populares do Marketplace, você poderá especificar como alternativa o *UrnAlias*, uma forma abreviada, como *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Localizar imagens específicas

Para localizar uma imagem de VM específica no mercado, use o comando `az vm image list` com a opção `--all`. Essa versão do comando leva algum tempo para concluir e pode retornar uma saída longa, portanto, você geralmente filtra a lista por `--publisher` ou outro parâmetro. 

O exemplo a seguir exibe todas as ofertas para Debian (lembre-se de que sem a opção `--all`, ele pesquisa apenas no cache local de imagens comuns):

```azurecli
az vm image list --offer Debian --all --output table 

```

Resultado parcial: 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
Debian   credativ     7                  credativ:Debian:7:7.0.201602010                  7.0.201602010
Debian   credativ     7                  credativ:Debian:7:7.0.201603020                  7.0.201603020
Debian   credativ     7                  credativ:Debian:7:7.0.201604050                  7.0.201604050
Debian   credativ     7                  credativ:Debian:7:7.0.201604200                  7.0.201604200
Debian   credativ     7                  credativ:Debian:7:7.0.201606280                  7.0.201606280
Debian   credativ     7                  credativ:Debian:7:7.0.201609120                  7.0.201609120
Debian   credativ     7                  credativ:Debian:7:7.0.201611020                  7.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
...
```

Aplique filtros semelhantes com as opções `--location`, `--publisher` e `--sku`. Você pode até mesmo executar correspondências parciais em um filtro, assim como procurar por `--offer Deb` para localizar todas as imagens do Debian.

Se você não especificar um local específico com a opção `--location`, os valores para o local padrão serão retornados. (Defina um local diferente do padrão executando `az configure --defaults location=<location>`.)

Por exemplo, o comando a seguir lista todos as SKUs do Debian 8 no local Europa Ocidental:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Resultado parcial:

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
...
```

## <a name="navigate-the-images"></a>Navegar pelas imagens 
Outra maneira de localizar uma imagem em um local é executar os comandos [az vm image list-publishers](/cli/azure/vm/image#az_vm_image_list_publishers), [az vm image list-offers](/cli/azure/vm/image#az_vm_image_list_offers) e [az vm image list-skus](/cli/azure/vm/image#az_vm_image_list_skus) em sequência. Com esses comandos, você determina estes valores:

1. Liste os editores de imagem.
2. Para um determinado editor, liste suas ofertas.
3. Para uma determinada oferta, liste seus SKUs.

Em seguida, para uma SKU selecionada, você pode escolher uma versão para implantar.

Por exemplo, o comando a seguir lista os editores de imagem na localização Oeste dos EUA:

```azurecli
az vm image list-publishers --location westus --output table
```

Resultado parcial:

```
Location    Name
----------  ----------------------------------------------------
westus      1e
westus      4psa
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      Acronis
westus      Acronis.Backup
westus      actian_matrix
westus      actifio
westus      activeeon
westus      adatao
...
```
Use essas informações para encontrar as ofertas do editor específico. Por exemplo, se *Canonical* for um editor de imagem na localização Oeste dos EUA, localize as ofertas dele executando `azure vm image list-offers`. Passe a localização e o editor como no exemplo a seguir:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Saída:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
westus      Ubuntu_Snappy_Core
westus      Ubuntu_Snappy_Core_Docker
```
Você vê que, na região Oeste dos EUA, o Canonical publica a oferta *UbuntuServer* no Azure. Mas quais SKUs? Para obter esses valores, execute `azure vm image list-skus` e defina a localização, o editor e a oferta que você descobriu:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Saída:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-DAILY-LTS
westus      12.04.5-LTS
westus      12.10
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-beta
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      16.10
westus      16.10-DAILY
westus      17.04
westus      17.04-DAILY
westus      17.10-DAILY
```

Por fim, use o comando `az vm image list` para encontrar uma versão específica do SKU desejado, por exemplo, *16.04-LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

Saída:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611220  16.04.201611220
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611300  16.04.201611300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612050  16.04.201612050
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612140  16.04.201612140
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612210  16.04.201612210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201701130  16.04.201701130
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702020  16.04.201702020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702200  16.04.201702200
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702210  16.04.201702210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702240  16.04.201702240
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703020  16.04.201703020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703030  16.04.201703030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703070  16.04.201703070
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703270  16.04.201703270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703280  16.04.201703280
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703300  16.04.201703300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705080  16.04.201705080
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705160  16.04.201705160
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706100  16.04.201706100
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706191  16.04.201706191
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707210  16.04.201707210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707270  16.04.201707270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708030  16.04.201708030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708110  16.04.201708110
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708151  16.04.201708151
```

Agora você pode escolher exatamente a imagem que deseja usar anotando o valor do URN. Passe esse valor com o parâmetro `--image` quando criar uma VM com o comando [az vm create](/cli/azure/vm#az_vm_create). Lembre-se de que é possível substituir, como opção, o número de versão no URN por "mais recente". Essa versão é sempre a versão mais recente da imagem. 

Se você implantar uma VM com um modelo do Gerenciador de Recursos, defina os parâmetros de imagem individualmente nas propriedades `imageReference`. Consulte a [referência de modelo](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Exibir propriedades de plano
Para exibir as informações do plano de compra de uma imagem, execute o comando [az vm image show](/cli/azure/image#az_image_show). Se a propriedade `plan` na saída não for `null`, isso significa que a imagem tem termos que você precisa aceitar antes da implantação programática.

Por exemplo, a imagem do Canonical Ubuntu Server 16.04 LTS não tem termos adicionais, porque as informações de `plan` são `null`:

```azurecli
az vm image show --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --version 16.04.201801260
```

Saída:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/16.04.201801260",
  "location": "westus",
  "name": "16.04.201801260",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Executar um comando semelhante para a imagem de RabbitMQ Certified by Bitnami mostra as seguintes `plan` propriedades: `name`, `product` e `publisher`. (Algumas imagens também têm uma propriedade `promotion code`.) Para implantar essa imagem, consulte as seções a seguir para aceitar os termos e habilitar a implantação programática.

```azurecli
az vm image show --location westus --publisher bitnami --offer rabbitmq --sku rabbitmq --version 3.7.1807171506
```
Saída:

```
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1807171506",
  "location": "westus",
  "name": "3.7.1807171506",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

### <a name="accept-the-terms"></a>Aceitar os termos
Para exibir e aceitar os termos de licença, use o comando [az vm image accept-terms](/cli/azure/vm/image?#az_vm_image_accept_terms). Quando aceita os termos, você habilita a implantação programática na sua assinatura. Você só precisa aceitar os termos uma vez por assinatura para a imagem. Por exemplo: 

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

A saída inclui um `licenseTextLink` para os termos da licença e indica que o valor de `accepted` é `true`:

```
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2018-02-22T04:06:28.7641907Z",
  "signature": "WVIEA3LAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNKLNLWI",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

### <a name="deploy-using-purchase-plan-parameters"></a>Implantar usando os parâmetros de plano de compra
Depois de aceitar os termos da imagem, você pode implantar uma VM na assinatura. Para implantar a imagem usando o comando `az vm create`, forneça parâmetros para o plano de compra, além de um URN para a imagem. Por exemplo, para implantar uma VM com a imagem do RabbitMQ Certified by Bitnami:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami

```



## <a name="next-steps"></a>Próximas etapas
Para criar uma máquina virtual rapidamente usando as informações de imagem, consulte [Criar e gerenciar VMs do Linux usando a CLI do Azure](tutorial-manage-vm.md).
