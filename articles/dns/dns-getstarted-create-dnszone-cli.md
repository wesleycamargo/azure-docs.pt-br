---
title: Criar uma zona DNS usando a CLI do Azure 2.0 | Microsoft Docs
description: "Saiba como criar zonas DNS no DNS do Azure. Este é um guia passo a passo para criar e gerenciar sua primeira zona DNS usando a CLI do Azure 2.0."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 00a4dfbe070129ce6dc84f006d793007d55dba3f
ms.lasthandoff: 03/01/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-20"></a>Criar uma zona DNS do Azure usando a CLI do Azure 2.0

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [CLI 1.0 do Azure](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-getstarted-create-dnszone-cli.md)

Este artigo explica as etapas de criação de uma zona DNS usando a CLI interplataforma do Azure, que está disponível para Windows, Mac e Linux. Você também pode criar uma zona DNS usando o [Azure PowerShell](dns-getstarted-create-dnszone.md) ou o [portal do Azure](dns-getstarted-create-dnszone-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa

Você pode concluir a tarefa usando uma das seguintes versões da CLI:

* [CLI do Azure 1.0](dns-getstarted-create-dnszone-cli-nodejs.md) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos.
* [CLI do Azure 2.0](dns-getstarted-create-dnszone-cli.md) – nossa próxima geração de CLI para o modelo de implantação de gerenciamento de recursos.

## <a name="introduction"></a>Introdução

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Configurar a CLI do Azure 2.0 para o DNS do Azure

### <a name="before-you-begin"></a>Antes de começar

Antes de começar a configurar, verifique se você tem os itens a seguir.

* Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

* Instale a versão mais recente da CLI do Azure, disponível para Windows, Linux ou Mac. Mais informações estão disponíveis em [Instalar a CLI do Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Entre na sua conta do Azure

Abra uma janela do console e autentique com suas credenciais. Para obter mais informações, confira Fazer logon no Azure na CLI do Azure

```azurecli
az login
```

### <a name="select-the-subscription"></a>Selecionar a assinatura

Verificar as assinaturas da conta.

```azurecli
az account list
```

### <a name="choose-which-of-your-azure-subscriptions-to-use"></a>Escolha quais das suas assinaturas do Azure deseja usar.

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>Criar um grupos de recursos

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. No entanto, como todos os recursos de DNS são globais, não regionais, a escolha do local do grupo de recursos não afeta o DNS do Azure.

Você pode ignorar esta etapa se está usando um grupo de recursos existente.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada usando o comando `az network dns zone create` . Para ver a ajuda desse comando, digite `az network dns zone create --help`.

O exemplo a seguir cria uma zona DNS chamada *contoso.com* no grupo de recursos chamado *MyResourceGroup*. Use o exemplo para criar uma zona DNS, substituindo os valores pelos seus próprios.

```azurecli
az network dns zone create --resource-group myresourcegroup --name contoso.com
```

## <a name="verify-your-dns-zone"></a>Verificar sua zona DNS

### <a name="view-records"></a>Exibir registros

Criar uma zona DNS também cria os seguintes registros DNS:

* O registro SOA ( *Start of Authority* , Início de Autoridade). Esse registro está presente na raiz de todas as zonas DNS.
* Os registros NS (name server, servidor de nomes) autoritativos. Esses registros mostram quais servidores de nome estão hospedando a zona. DNS do Azure usa um pool de servidores de nomes; dessa forma servidores de nomes diferentes podem ser atribuídos a diferentes zonas no DNS do Azure. Para saber mais, confira [delegar um domínio ao DNS do Azure](dns-domain-delegation.md).

Para exibir esses registros, use `az network dns record-set list`:

```azurecli
az network dns record-set list --resource-group MyResourceGroup --zone-name contoso.com
```

A resposta para `az network dns record-set list` é mostrada abaixo:

```json
[
  {
    "etag": "510f4711-8b7f-414e-b8d5-c0383ea3d62e",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@",
    "metadata": null,
    "name": "@",
    "nsRecords": [
      {
        "nsdname": "ns1-04.azure-dns.com."
      },
      {
        "nsdname": "ns2-04.azure-dns.net."
      },
      {
        "nsdname": "ns3-04.azure-dns.org."
      },
      {
        "nsdname": "ns4-04.azure-dns.info."
      }
    ],
    "resourceGroup": "myresourcegroup",
    "ttl": 172800,
    "type": "Microsoft.Network/dnszones/NS"
  },
  {
    "etag": "0e48e82f-f194-4d3e-a864-7e109a95c73a",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@",
    "metadata": null,
    "name": "@",
    "resourceGroup": "myresourcegroup",
    "soaRecord": {
      "email": "azuredns-hostmaster.microsoft.com",
      "expireTime": 2419200,
      "host": "ns1-04.azure-dns.com.",
      "minimumTtl": 300,
      "refreshTime": 3600,
      "retryTime": 300,
      "serialNumber": 1
    },
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/SOA"
  }
]
```

> [!NOTE]
> Conjuntos de registos na raiz (ou *apex*) de uma zona DNS usam **@** como o nome do conjunto de registros.

### <a name="test-name-servers"></a>Testar servidores de nome

Você pode testar se a zona DNS está presente nos servidores de nome DNS do Azure usando ferramentas de DNS, tais como nslookup, dig ou o cmdlet PowerShell `Resolve-DnsName`.

Se você ainda não delegou seu domínio para usar a nova zona no DNS do Azure, você precisa direcionar a consulta DNS diretamente para um dos servidores de nome para a zona. Os servidores de nomes da zona são fornecidos nos registros de NS, conforme mostrado em `az network dns record-set list`.

O exemplo a seguir usa 'dig' para consultar o domínio contoso.com usando os servidores de nome atribuídos à zona DNS. Substitua os valores corretos para sua zona.

```
  > dig @ns1-01.azure-dns.com contoso.com
  
  <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
(1 server found)
global options: +cmd
  Got answer:
->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
  flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
  WARNING: recursion requested but not available

  OPT PSEUDOSECTION:
  EDNS: version: 0, flags:; udp: 4000
  QUESTION SECTION:
contoso.com.                        IN      A

  AUTHORITY SECTION:
contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

Query time: 93 msec
SERVER: 208.76.47.5#53(208.76.47.5)
WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
MSG SIZE  rcvd: 120
```

## <a name="next-steps"></a>Próximas etapas

Depois de criar uma zona DNS, [crie conjuntos de registros e registros DNS](dns-getstarted-create-recordset-cli.md) em sua zona.


