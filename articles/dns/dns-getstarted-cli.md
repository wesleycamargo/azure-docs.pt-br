---
title: 'Início Rápido: Criar uma zona DNS do Azure e o registro usando a CLI do Azure'
description: Início Rápido - Saiba como criar uma zona e registro DNS no DNS do Azure. Este é uma guia passo a passo para criar e gerenciar sua primeira zona e registro DNS usando a CLI do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 7/16/2018
ms.author: victorh
ms.openlocfilehash: 3fb39558ff99c35786dedc133a9d1d1a450b5928
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090115"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Início Rápido: Criar uma zona DNS do Azure e o registro usando a CLI do Azure

Este artigo explica as etapas de criação de sua primeira zona e registro DNS usando a CLI do Azure, que está disponível para Windows, Mac e Linux. Você também pode executar essas etapas usando o [portal do Azure](dns-getstarted-portal.md) ou o [Azure PowerShell](dns-getstarted-powershell.md).

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS. Por fim, para publicar sua zona DNS na Internet, você precisa configurar os servidores de nome para o domínio. Cada uma dessas etapas é descrita abaixo.

O DNS do Azure agora também oferece suporte a zonas de DNS privado (atualmente em visualização pública). Para saber mais sobre as zonas DNS privadas, consulte [Usar o Azure DNS para domínios privados](private-dns-overview.md). Para obter um exemplo de como criar uma zona de DNS privado, consulte [Introdução às zonas privadas do DNS do Azure usando a CLI](./private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, crie um grupo de recursos para conter a zona DNS:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada usando o comando `az network dns zone create` . Para ver a ajuda desse comando, digite `az network dns zone create -h`.

O exemplo a seguir cria uma zona DNS chamada *contoso.com* no grupo de recursos *MyResourceGroup*. Use o exemplo para criar uma zona DNS, substituindo os valores pelos seus próprios.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```

## <a name="create-a-dns-record"></a>Criar um registro DNS

Para criar um registro DNS, use o comando `az network dns record-set [record type] add-record`. Para obter ajuda, por exemplo, com os registros A, veja `azure network dns record-set A add-record -h`.

O exemplo a seguir cria um registro com o nome relativo "www" na Zona DNS "contoso.com", no grupo de recursos "MyResourceGroup". O nome totalmente qualificado do conjunto de registros é “www.contoso.com”. O tipo de registro é "A", com o endereço IP "1.2.3.4", e um TTL padrão de 3600 segundos (1 hora) é usado.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

## <a name="view-records"></a>Exibir registros

Para listar os registros DNS em sua zona, use:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="update-name-servers"></a>Atualizar servidores de nome

Quando você estiver satisfeito com a configuração de sua zona e registros DNS, configure seu nome de domínio para usar os servidores de nome DNS do Azure. Isso permite que outros usuários na Internet encontrem os registros DNS.

Os servidores de nomes de sua zona são fornecidos pelo comando `az network dns zone show`. Para ver os nomes do servidor de nome, use a saída em JSON, conforme mostra o exemplo a seguir.

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Esses servidores de nome devem ser configurados com o registrador de nome de domínio (onde você adquiriu o nome de domínio). Seu registrador oferecerá a opção de configurar os servidores de nome do domínio. Para saber mais, confira [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Excluir todos os recursos
 
Quando não forem mais necessários, você poderá excluir todos os recursos criados neste início rápido ao excluir o grupo de recursos:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Agora que você criou sua primeira zona e registro DNS usando a CLI do Azure, pode criar registros para um aplicativo Web em um domínio personalizado.

> [!div class="nextstepaction"]
> [Criar registros DNS para um aplicativo Web em um domínio personalizado](./dns-web-sites-custom-domain.md)
