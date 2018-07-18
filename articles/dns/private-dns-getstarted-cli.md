---
title: Introdução às Zonas Privadas do DNS do Azure usando a CLI do Azure 2.0 | Microsoft Docs
description: Saiba como criar uma zona e um registro de DNS privado no DNS do Azure. Este é uma guia passo a passo para criar e gerenciar sua primeira zona e registro de DNS privado usando a CLI do Azure 2.0.
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: d10f3201adb972468d8de7e66a940232ed19562d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-azure-cli-20"></a>Introdução às Zonas Privadas do DNS do Azure usando a CLI do Azure 2.0

> [!div class="op_single_selector"]
> * [PowerShell](private-dns-getstarted-powershell.md)
> * [CLI 2.0 do Azure](private-dns-getstarted-cli.md)

Este artigo explica as etapas de criação de sua primeira zona e registro de DNS privado usando a CLI do Azure 2.0 de plataforma cruzada, que está disponível para Windows, Mac e Linux. Também é possível executar essas etapas usando o Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS. Para publicar uma zona de DNS privado em sua rede virtual, você deve especificar a lista de redes virtuais que podem resolver registros na zona.  Chamamos essas redes de “redes virtuais de resolução”.  Também é possível especificar uma rede virtual para a qual o DNS do Azure mantenha registros de nome do host sempre que uma VM for criada, mudar de IP ou for destruída.  Chamamos essa rede de “rede virtual de registro”.

Essas instruções pressupõem que você já instalou e entrou na CLI do Azure 2.0, além de já ter instalado a extensão da CLI necessária que oferece suporte a Zonas Privadas. Para obter ajuda, confira [Como gerenciar as zonas DNS usando a CLI do Azure 2.0](dns-operations-dnszones-cli.md).

## <a name="to-installuse-azure-dns-private-zones-feature-public-preview"></a>Para instalar/usar o recurso de Zonas Privadas do DNS do Azure (Visualização Pública)
O recurso de Zonas Privadas do DNS do Azure é liberado em Visualização Pública por meio de uma extensão da CLI do Azure. Instalar o “dns” da extensão da CLI do Azure 

```
az extension add --name dns
``` 

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, um grupo de recursos é criado para conter a zona DNS. O código a seguir mostra o comando.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-private-zone"></a>Criar uma zona de DNS privado

Uma zona de DNS privado é criada usando o comando `az network dns zone create`. Para ver a ajuda desse comando, digite `az network dns zone create --help`.

O exemplo a seguir cria uma zona de DNS privado chamada *contoso.local* no grupo de recursos *MyResourceGroup* e a disponibiliza (a vincula) na rede virtual *MyAzureVnet* usando o parâmetro resolution-vnets. Use o exemplo para criar uma zona DNS, substituindo os valores pelos seus próprios.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --resolution-vnets MyAzureVnet
```

Observação: no exemplo acima, a rede virtual “MyAzureVnet” pertence ao mesmo Grupo de recursos e Assinatura como a zona privada. Se precisar vincular uma rede virtual que pertence a um Grupo de recursos ou Assinatura diferente, você precisa especificar a ID completa do Azure Resource Manager em vez de apenas o nome da rede virtual para o parâmetro --resolution-vnets. 

Se precisar que o Azure crie automaticamente os registros de nome de host na zona, use o parâmetro *registration-vnets* em vez de *resolution-vnets*.  Redes virtuais de registro são habilitadas automaticamente para a resolução.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --registration-vnets MyAzureVnet
```

## <a name="create-a-dns-record"></a>Criar um registro DNS

Para criar um registro DNS, use o comando `az network dns record-set [record type] add-record`. Para obter ajuda, por exemplo, com os registros A, veja `azure network dns record-set A add-record --help`.

O exemplo a seguir cria um registro com o nome relativo “ip1” na Zona DNS “contoso.local”, no grupo de recursos “MyResourceGroup”. O nome totalmente qualificado do conjunto de registros é “ip1.contoso.local”. O tipo de registro é “A”, com o endereço IP “10.0.0.1”.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.local -n ip1 -a 10.0.0.1
```

Para outros tipos de registros, para conjuntos de registros com mais de um registro, para valores de TTL alternativos e para modificar registros existentes, veja [Gerenciar registros DNS e conjuntos de registros usando a CLI do Azure 2.0](dns-operations-recordsets-cli.md).

## <a name="view-records"></a>Exibir registros

Para listar os registros DNS em sua zona, use:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="get-a-dns-private-zone"></a>Obter uma zona de DNS privado do Azure

Para recuperar uma zona de DNS privado, use `az network dns zone show`. Para obter ajuda, consulte `az network dns zone show --help`.

O exemplo a seguir retorna a zona DNS *contoso.local* e seus dados associados do grupo de recursos *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group MyResourceGroup --name contoso.local
```

O exemplo a seguir é a resposta.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.local",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.local",
  "nameServers": null,
  "numberOfRecordSets": 1,
  "registrationVirtualNetworks": [],
  "resolutionVirtualNetworks": [
    {
      "additionalProperties": {},
      "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAzureVnet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
  "resourceGroup": "MyResourceGroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Private"
}
```

Observe que registros DNS não são retornados por `az network dns zone show`. Para listar registros DNS, use `az network dns record-set list`.


## <a name="list-dns-zones"></a>Listar as zonas DNS

Para enumerar zonas DNS, use `az network dns zone list`. Para obter ajuda, consulte `az network dns zone list --help`.

Especificar o grupo de recursos lista apenas as zonas dentro do grupo de recursos:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Omitir o grupo de recursos lista todas as zonas na assinatura:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Atualizar uma zona DNS

As alterações a um recurso da zona DNS podem ser feitas usando o `az network dns zone update`. Para obter ajuda, consulte `az network dns zone update --help`.

Esse comando não atualiza nenhum dos conjuntos de registros DNS dentro da zona (consulte [Como gerenciar registros DNS](dns-operations-recordsets-cli.md)). Ele só é usado para atualizar as propriedades do recurso da zona em si. Para zonas privada, é possível atualizar as redes virtuais de registro ou resolução vinculadas a uma zona. 

O exemplo a seguir mostra como atualizar a rede virtual de resolução vinculada a uma zona de DNS privado. A rede virtual de resolução vinculada existente é substituída pela nova rede virtual especificada.

```azurecli
az network dns zone update --resource-group MyResourceGroup --name contoso.local --zone-type Private --resolution-vnets MyNewAzureVnet
```

## <a name="delete-a-dns-zone"></a>Excluir uma zona DNS

As zonas DNS podem ser excluídas por meio do `az network dns zone delete`. Para obter ajuda, consulte `az network dns zone delete --help`.

> [!NOTE]
> Excluir uma zona DNS também excluirá todos os registros DNS na zona. Essa operação não pode ser desfeita. Se a zona DNS estiver em uso, serviços que usam a zona falharão quando a zona for excluída.
>
>Para se proteger contra a exclusão acidental da zona, consulte [Proteger registros e zonas DNS](dns-protect-zones-recordsets.md).

Esse comando solicita uma confirmação. A opção `--yes` opcional suprime esse prompt.

O exemplo a seguir mostra como excluir a zona *contoso.local* do grupo de recursos *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.local
```

## <a name="delete-all-resources"></a>Excluir todos os recursos
 
Para excluir todos os recursos criados neste artigo, execute as seguintes etapas:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o DNS do Azure, veja [Visão geral do DNS do Azure](dns-overview.md).

Para saber mais sobre como gerenciar as zonas DNS no DNS do Azure, consulte [Gerenciar zonas DNS no DNS do Azure usando a CLI do Azure 2.0](dns-operations-dnszones-cli.md).

Para saber mais sobre como gerenciar os registros DNS no DNS do Azure, veja [Gerenciar registros DNS e conjuntos de registros no DNS do Azure usando a CLI do Azure 2.0](dns-operations-recordsets-cli.md).
