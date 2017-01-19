---
title: Criar registros e um conjunto de registros para uma zona DNS usando o PowerShell | Microsoft Docs
description: "Como criar registros de host para o DNS do Azure. Configuração dos conjuntos de registros e registros usando o PowerShell"
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
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: fe82db80f0efc02809bbd898e990860c7bcce8da

---

# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>Criar registros e conjuntos de registros DNS usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI do Azure](dns-getstarted-create-recordset-cli.md)

Este artigo explica o processo de criação de registros e conjuntos de registros usando o Windows PowerShell. Depois de criar a zona DNS, você adiciona os registros DNS para seu domínio. Para fazer isso, primeiro você precisa entender os registros DNS e conjuntos de registros.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="verify-that-you-have-the-latest-version-of-powershell"></a>Verifique se você tem a última versão do PowerShell

Verifique se você instalou a última versão dos cmdlets do PowerShell do Azure Resource Manager. Confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar os cmdlets do PowerShell.

## <a name="create-a-record-set-and-record"></a>Criar registro e um conjunto de registros

Esta seção descreve como criar um registro e um conjunto de registros.

### <a name="1-connect-to-your-subscription"></a>1. Conecte-se as suas assinaturas

Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

```powershell
Login-AzureRmAccount
```

Verificar as assinaturas da conta.

```powershell
Get-AzureRmSubscription
```

Especifique a assinatura que você quer usar.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

Para saber mais sobre como trabalhar com o PowerShell, confira [Usar o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

### <a name="2-create-a-record-set"></a>2. Criar um conjunto de registros

Você cria conjuntos de registros usando o cmdlet `New-AzureRmDnsRecordSet`. Ao criar um conjunto de registros, você precisa especificar o nome do conjunto de registros, a zona, o TTL (vida útil) e o tipo de registro.

Para criar um conjunto de registros em vértices da zona (nesse caso, "contoso.com"), use o nome do registro "@",, incluindo as aspas. Isso é uma convenção comum do DNS.

O exemplo a seguir cria um conjunto de registros com o nome relativo “www” na Zona DNS “contoso.com”. O nome totalmente qualificado do conjunto de registros é “www.contoso.com”. O tipo de registro é “A” e o TTL é 60 segundos. Depois de concluir esta etapa, você terá um conjunto de registros “www” vazio que é atribuído à variável *$rs*.

```powershell
$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60
```

#### <a name="if-a-record-set-already-exists"></a>Se um conjunto de registros já existir

Se já houver um conjunto de registros, o comando falhará, a menos que a opção *-Overwrite* seja usada. A opção *-Overwrite* dispara um prompt de confirmação, que pode ser suprimido usando a opção *-Force*.

```powershell
$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]
```

Neste exemplo, é possível especificar a zona usando o nome da zona e o nome do grupo de recursos. Como alternativa, você pode especificar um objeto de zona, conforme retornado por `Get-AzureRmDnsZone` ou `New-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -Zone $zone [-Tag $tags] [-Overwrite] [-Force]
```

`New-AzureRmDnsRecordSet` retorna um objeto local que representa o conjunto de registros criado no DNS do Azure.

### <a name="3-add-a-record"></a>3. Adicionar um registro

Para usar o conjunto de registros “www” recém-criado, você precisa adicionar registros a ele. Você pode adicionar os registros *A* do IPv4 ao conjunto de registros "www" usando o seguinte exemplo. O exemplo depende da variável *$rs* que você definiu na etapa anterior.

Adicionar registros a um conjunto de registros usando `Add-AzureRmDnsRecordConfig` é uma operação offline. Apenas a variável local *$rs* é atualizada.

```powershell
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221
```

### <a name="4-commit-the-changes"></a>4. Confirmar as alterações

Confirme as alterações no conjunto de registros. Use `Set-AzureRmDnsRecordSet` para carregar as alterações ao conjunto de registros no DNS do Azure.

```powershell
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="5-retrieve-the-record-set"></a>5. Recuperar o conjunto de registros

Você pode recuperar o conjunto de registros do DNS do Azure usando `Get-AzureRmDnsRecordSet`, conforme mostrado no exemplo a seguir.

    Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : MyAzureResourceGroup
    Ttl               : 3600
    Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
    RecordType        : A
    Records           : {134.170.185.46, 134.170.188.221}
    Tags              : {}


Você também pode usar a ferramenta nslookup ou outras ferramentas DNS para consultar o novo conjunto de registros.

Se ainda não tiver delegado o domínio para os servidores de nome DNS do Azure, você precisará especificar o nome, servidor e endereço da zona explicitamente.

    nslookup www.contoso.com ns1-01.azure-dns.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    Name:    www.contoso.com
    Addresses:  134.170.185.46
                134.170.188.221

## <a name="create-a-record-set-of-each-type-with-a-single-record"></a>Criar um conjunto de registros de cada tipo com um único registro

Os exemplos a seguir mostram como criar um conjunto de registros de cada tipo de registro. Cada conjunto de registros contém um único registro.

[!INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="next-steps"></a>Próximas etapas

[Como gerenciar as zonas DNS usando o PowerShell](dns-operations-dnszones.md)

[Gerenciar registros DNS e conjuntos de registros usando o PowerShell](dns-operations-recordsets.md)

[Automatizar operações do Azure com o SDK do .NET](dns-sdk.md)



<!--HONumber=Dec16_HO2-->


