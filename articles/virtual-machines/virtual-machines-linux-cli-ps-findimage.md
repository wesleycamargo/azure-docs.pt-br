---
title: Selecionar imagens da VM do Linux com a CLI do Azure | Microsoft Docs
description: "Saiba como determinar o editor, a oferta e SKU para imagens ao criar uma máquina virtual Linux com o modelo de implantação do Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/15/2017
ms.author: rasquill
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: e9be064425ae6b9048098333cc664310e7128314
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-find-linux-vm-images-with-the-azure-cli"></a>Como localizar imagens da VM Linux com a CLI do Azure
Este tópico descreve como localizar editores, ofertas, SKUs e versões de cada local no qual você pode implantar. 


## <a name="use-azure-cli-20"></a>Usar a CLI 2.0 do Azure

Depois de ter [instalado a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2), use o comando `az vm image list` para ver uma lista armazenada em cache de imagens de VM populares. Por exemplo, o exemplo do comando `az vm image list -o table` a seguir mostra como:

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>Localizar todas as imagens atuais

Para obter a lista atual de todas as imagens, use o comando `az vm image list` com a opção `--all`. Diferentemente dos comandos da CLI do Azure 1.0, o comando `az vm image list --all` retorna todas as imagens em **westus** por padrão (a menos que você especifique um argumento `--location` específico), então o comando `--all` leva algum tempo para concluir. Se você pretende investigar interativamente, use `az vm image list --all > allImages.json`, que retorna uma lista de todas as imagens disponíveis atualmente no Azure e a armazena como um arquivo para uso local. 

Você poderá especificar uma dentre várias opções para restringir sua pesquisa para um local, oferta, editor ou SKU específico, se você já tiver um ou mais em mente. Se você não especificar um local, os valores de **westus** serão retornados.

### <a name="find-specific-images"></a>Localizar imagens específicas

Use `az vm image list` com um filtro para localizar informações específicas. O exemplo a seguir exibe as **ofertas** que estão disponíveis para **Debian** (lembre-se de que sem a opção `--all`, ele pesquisa apenas no cache local de imagens comuns):

```azurecli
az vm image list --offer Debian -o table --all
```

A saída será algo semelhante a: 
```
Offer   Publisher   Sku   Urn                              Version
------  ---------   ---   -------------------------------  -------------
Debian  credativ    8     credativ:Debian:8:8.0.201701180  8.0.201701180

<list shortened for the example>
```

Você pode executar filtros semelhantes nas opções **--publisher** e **--sku**. Você pode até mesmo executar correspondências parciais em um filtro, por exemplo, pesquisar por **--offer Deb** para localizar todas as imagens Debian ou **--publisher Micr** para localizar todas as imagens publicadas pela Microsoft.

Se você souber o local em que está implantando, você poderá usar os resultados gerais da pesquisa de imagem junto com os comandos `az vm image list-skus`, `az vm image list-offers` e `az vm image list-publishers` para localizar exatamente o que você deseja e em que local ele pode ser implantado. Por exemplo, se no exemplo anterior você sabe que `credativ` tem uma oferta de Debian, você pode usar `--location` e outras opções para localizar exatamente o que você deseja. O exemplo a seguir procura por uma imagem de Debian 8 em **westeurope**:

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

E a saída é:

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="use-azure-cli-10"></a>Usar a CLI do Azure 1.0 

> [!NOTE]
> Este artigo descreve como navegar e selecionar imagens da máquina virtual usando uma instalação da CLI do Azure 1.0 ou do Azure PowerShell que dá suporte ao modelo de implantação do Azure Resource Manager. Como pré-requisito, altere para o modo do Resource Manager. Com a CLI do Azure, insira esse modo digitando `azure config mode arm`. 
> 

A maneira mais rápida de localizar uma imagem é chamar o comando `azure vm image list` e informar o local, o nome do editor (não diferencia maiúsculas de minúsculas!) e uma oferta – se você souber a oferta. Por exemplo, a lista a seguir é apenas uma pequena amostra - várias listas são bastante longas - se você souber que "Canonical" é um editor para a oferta "UbuntuServer".

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

A coluna **Urn** será o formulário que você passa para `azure vm quick-create`.

Muitas vezes, no entanto, você ainda não saiba o que está disponível. Nesse caso, você pode navegar por imagens usando o comando `azure vm image list-publishers` e especificando um local de data center no prompt. O exemplo a seguir lista todos os editores de imagem no local Oeste dos EUA (passar o argumento de local removendo os espaços dos locais padrão e escrevendo-os em minúsculas)

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

Essas listas podem ser muito longas, portanto, a lista de exemplo acima é apenas um trecho. Vamos supor que percebi que Canonical é, na verdade, um editor de imagem no local Oeste dos EUA. Agora você pode encontrar suas ofertas chamando `azure vm image list-offers` e passar o local e o editor em solicitações, como no exemplo a seguir:

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

Agora sabemos que, na região Oeste dos EUA, Canonical publica a oferta **UbuntuServer** no Azure. Mas quais SKUs? Para obter esses valores, você chama `azure vm image list-skus` e responde à solicitação com o local, editor e oferta que você descobriu.

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

Com essas informações, agora você pode localizar exatamente a imagem que deseja, invocando a chamada original na parte superior.

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

## <a name="next-steps"></a>Próximas etapas
Agora você pode escolher exatamente a imagem que deseja usar. Para criar uma máquina virtual rapidamente usando as informações de URN que você acabou de encontrar ou para usar um modelo com essas informações de URN, consulte [Criar uma VM Linux usando a CLI do Azure](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

