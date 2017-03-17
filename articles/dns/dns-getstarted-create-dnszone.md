---
title: "Introdução ao Azure DNS | Microsoft Docs"
description: "Saiba como criar zonas DNS no DNS do Azure. Este é uma guia passo a passo para criar e gerenciar sua primeira zona DNS usando o Azure PowerShell."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: d78583b7-e669-435c-819b-7605cf791b0e
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 798717eca5180e445b14351bbfc694ed206ee9dc
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-using-powershell"></a>Você também pode criar uma zona DNS usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [CLI 1.0 do Azure](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-getstarted-create-dnszone-cli.md)

Este artigo explica as etapas para criar uma zona DNS usando o Azure PowerShell. Você também pode criar uma zona DNS usando a plataforma cruzada [CLI do Azure](dns-getstarted-create-dnszone-cli.md) ou o [Portal do Azure](dns-getstarted-create-dnszone-portal.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada usando o cmdlet `New-AzureRmDnsZone` . O exemplo a seguir cria uma zona DNS chamada *contoso.com* no grupo de recursos chamado *MyResourceGroup*. Use o exemplo para criar uma zona DNS, substituindo os valores pelos seus próprios.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="verify-your-dns-zone"></a>Verificar sua zona DNS

### <a name="view-records"></a>Exibir registros

Criar uma zona DNS também cria os seguintes registros DNS:

* O registro SOA ( *Start of Authority* , Início de Autoridade). Esse registro está presente na raiz de todas as zonas DNS.
* Os registros NS (name server, servidor de nomes) autoritativos. Esses registros mostram quais servidores de nome estão hospedando a zona. DNS do Azure usa um pool de servidores de nomes; dessa forma servidores de nomes diferentes podem ser atribuídos a diferentes zonas no DNS do Azure. Para saber mais, confira [delegar um domínio ao DNS do Azure](dns-domain-delegation.md).

Para exibir esses registros, use `Get-AzureRmDnsRecordSet`:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 172800
Etag              : f573237b-088c-424a-b53c-08567d87d049
RecordType        : NS
Records           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
Metadata          : 

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 3600
Etag              : bf88a27d-0eec-4847-ad42-f0c83b9a2c32
RecordType        : SOA
Records           : {[ns1-01.azure-dns.com.,azuredns-hostmaster.microsoft.com,3600,300,2419200,300]}
Metadata          : 
```

> [!NOTE]
> Conjuntos de registos na raiz (ou *apex*) de uma zona DNS usam **@** como o nome do conjunto de registros.

### <a name="test-name-servers"></a>Testar servidores de nome

Você pode testar se sua zona DNS está presente nos servidores de nome DNS do Azure usando ferramentas de DNS, tais como nslookup, dig ou o [cmdlet Resolve-DnsName do PowerShell](https://technet.microsoft.com/library/jj590781.aspx).

Se você ainda não delegou seu domínio para usar a nova zona no DNS do Azure, você precisa direcionar a consulta DNS diretamente para um dos servidores de nome para a zona. Os servidores de nomes da zona são fornecidos nos registros de NS, conforme listado em `Get-AzureRmDnsRecordSet` acima. Substitua os valores corretos para a zona no exemplo a seguir:

```
nslookup
> set type=SOA
> server ns1-01.azure-dns.com
> contoso.com

Server: ns1-01.azure-dns.com
Address:  40.90.4.1

contoso.com
        primary name server = ns1-01.azure-dns.com
        responsible mail addr = azuredns-hostmaster.microsoft.com
        serial  = 1
        refresh = 3600 (1 hour)
        retry   = 300 (5 mins)
        expire  = 2419200 (28 days)
        default TTL = 300 (5 mins)
```

## <a name="next-steps"></a>Próximas etapas

Após criar uma zona DNS, [crie conjuntos de registros e registros](dns-getstarted-create-recordset.md) para criar registros DNS para seu domínio da Internet.

