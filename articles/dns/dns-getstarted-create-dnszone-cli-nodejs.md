---
title: Criar uma zona DNS usando a CLI do Azure 1.0 | Microsoft Docs
description: "Saiba como criar zonas DNS no DNS do Azure. Este é um guia passo a passo para criar e gerenciar sua primeira zona DNS usando a CLI do Azure 1.0."
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
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 1e8645e8554bc8f8ec72a6df98bfe9ffc5be13b3
ms.lasthandoff: 02/27/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-10"></a>Criar uma zona DNS do Azure usando a CLI do Azure 1.0

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

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada usando o comando `azure network dns zone create` . Para ver a ajuda desse comando, digite `azure network dns zone create -h`.

O exemplo a seguir cria uma zona DNS chamada *contoso.com* no grupo de recursos chamado *MyResourceGroup*. Use o exemplo para criar uma zona DNS, substituindo os valores pelos seus próprios.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="verify-your-dns-zone"></a>Verificar sua zona DNS

### <a name="view-records"></a>Exibir registros

Criar uma zona DNS também cria os seguintes registros DNS:

* O registro SOA ( *Start of Authority* , Início de Autoridade). Esse registro está presente na raiz de todas as zonas DNS.
* Os registros NS (name server, servidor de nomes) autoritativos. Esses registros mostram quais servidores de nome estão hospedando a zona. DNS do Azure usa um pool de servidores de nomes; dessa forma servidores de nomes diferentes podem ser atribuídos a diferentes zonas no DNS do Azure. Para saber mais, confira [delegar um domínio ao DNS do Azure](dns-domain-delegation.md).

Para exibir esses registros, use `azure network dns-record-set list`:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> Conjuntos de registos na raiz (ou *apex*) de uma zona DNS usam **@** como o nome do conjunto de registros.

### <a name="test-name-servers"></a>Testar servidores de nome

Você pode testar se a zona DNS está presente nos servidores de nome DNS do Azure usando ferramentas de DNS, tais como nslookup, dig ou o cmdlet PowerShell `Resolve-DnsName`.

Se você ainda não delegou seu domínio para usar a nova zona no DNS do Azure, você precisa direcionar a consulta DNS diretamente para um dos servidores de nome para a zona. Os servidores de nomes da zona são fornecidos nos registros de NS, conforme mostrado em `azure network dns record-set list`.

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


