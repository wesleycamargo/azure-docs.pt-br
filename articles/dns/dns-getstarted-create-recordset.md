---
title: Criar registros e um conjunto de registros para uma zona DNS usando o PowerShell | Microsoft Docs
description: Como criar registros de host no DNS do Azure. Configurar conjuntos de registros e registros usando o PowerShell
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a8068c5a-f248-4e97-be97-8bd0d79eeffd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 67b780d66eac4199b0a2367f575477191542cfa7
ms.lasthandoff: 02/27/2017

---

# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>Criar registros e conjuntos de registros DNS usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI 1.0 do Azure](dns-getstarted-create-recordset-cli-nodejs.md)
> * [CLI 2.0 do Azure](dns-getstarted-create-recordset-cli.md)

Este artigo explica o processo de criação de registros e conjuntos de registros usando o Azure PowerShell.

## <a name="introduction"></a>Introdução

Antes de criar registros DNS no DNS do Azure, primeiro você precisa entender como o DNS do Azure organiza registros DNS em conjuntos de registros DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para obter mais informações sobre os registros DNS no DNS do Azure, confira [Zonas e registros DNS](dns-zones-records.md).

## <a name="create-a-record-set-and-record"></a>Criar registro e um conjunto de registros

Esta seção descreve como criar registros DNS no Azure DNS. Os exemplos pressupõem que você já [instalou o Azure PowerShell, iniciou uma sessão e criou uma zona DNS](dns-getstarted-create-dnszone.md).

Todos os exemplos nesta página usam o tipo 'A' de registro de DNS. Para obter outros tipos de registro e mais detalhes sobre como gerenciar registros DNS e conjuntos de registros, confira [Gerenciar registros DNS e conjuntos de registros usando o PowerShell](dns-operations-recordsets.md).

Se o novo registro tem o mesmo nome e tipo de um registro existente, você precisa [adicioná-lo ao conjunto de registros existente](#add-a-record-to-an-existing-record-set). Se o novo registro tem um nome e tipo diferente para todos os registros existentes, você precisa [criar um novo conjunto de registros](#create-records-in-a-new-record-set). 

### <a name="create-records-in-a-new-record-set"></a>Criar registros em um novo conjunto de registros

Você cria conjuntos de registros usando o cmdlet `New-AzureRmDnsRecordSet`. Ao criar um conjunto de registros, você precisa especificar o nome do conjunto de registros, a zona, o TTL (vida útil), o tipo de registro e os registros a serem criados.

Para criar um conjunto de registros em vértices da zona (nesse caso, "contoso.com"), use o nome do Registro "@", incluindo as aspas. Isso é uma convenção comum do DNS.

O exemplo a seguir cria um novo conjunto de registros com o nome relativo "www" na Zona DNS "contoso.com". O nome totalmente qualificado do conjunto de registros é “www.contoso.com”. O tipo de registro é "A" e o TTL é 3600 segundos. O conjunto de registros contém um único registro, com o endereço IP "1.2.3.4"

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

Se precisar criar um novo conjunto de registros que contém mais de um registro, primeiro você precisa criar uma matriz local que contém os registros a serem adicionados.  Isso é enviado para `New-AzureRmDnsRecordSet` da seguinte maneira:

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

### <a name="add-a-record-to-an-existing-record-set"></a>Adicionar um registro a um conjunto de registros existente

Para adicionar um registro a um conjunto de registros existente, siga estas três etapas:

1. Obter o conjunto de registros existente

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Adicione o novo registro ao conjunto de registros local. Esta é uma operação offline.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Confirme a mudança no serviço DNS do Azure 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

### <a name="verify-name-resolution"></a>Verificar a resolução de nome

Você pode testar se os registros DNS estão presentes nos servidores de nome DNS do Azure usando ferramentas de DNS, tais como nslookup, dig ou o [cmdlet Resolve-DnsName PowerShell](https://technet.microsoft.com/library/jj590781.aspx).

Se você ainda não delegou seu domínio para usar a nova zona no DNS do Azure, você precisa [direcionar a consulta DNS diretamente para um dos servidores de nome para a zona](dns-getstarted-create-dnszone.md#test-name-servers). Substitua os valores corretos para a zona dos seus registros no exemplo a seguir:

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>Próximas etapas

Saiba como [delegar seu nome de domínio para os servidores de nome DNS do Azure](dns-domain-delegation.md)

Saiba como [gerenciar zonas DNS usando o PowerShell](dns-operations-dnszones.md).

Saiba como [gerenciar registros DNS e conjuntos de registros usando o PowerShell](dns-operations-recordsets.md).



