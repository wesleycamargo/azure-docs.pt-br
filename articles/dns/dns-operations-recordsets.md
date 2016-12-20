---
title: Gerenciar conjuntos de registros DNS e registros usando o portal do Azure| Microsoft Docs
description: "Gerenciando conjuntos de registros DNS e registros no DNS do Azure ao hospedar seu domínio no DNS do Azure. Todos os comandos do PowerShell para operações em conjuntos de registros e registros."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: e3f7967843b3d9e38c79b45d90e333192fd3a900

---

# <a name="manage-dns-records-and-record-sets-by-using-powershell"></a>Gerenciar registros DNS e conjuntos de registros usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal do Azure](dns-operations-recordsets-portal.md)
> * [CLI do Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Este artigo mostra como gerenciar os conjuntos de registros e os registros de sua zona DNS usando o Windows PowerShell.

É importante compreender a diferença entre os conjuntos de registros DNS e registros DNS individuais. Um conjunto de registros é a coleção de registros em uma zona com o mesmo nome e tipo. Para obter mais informações, consulte [Criar conjuntos de registros DNS e registros usando o portal do Azure](dns-getstarted-create-recordset-portal.md).

Para gerenciar seus conjuntos de registros e registros, você precisará da versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md): Para saber mais sobre como trabalhar com o PowerShell, consulte [Usar o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

## <a name="create-a-new-record-set-and-a-record"></a>Criar um novo conjunto de registros e um registro

Para criar um conjunto de registros usando o PowerShell, consulte [Criar conjuntos de registros DNS e registros usando o PowerShell](dns-getstarted-create-recordset.md).

## <a name="get-a-record-set"></a>Obter um conjunto de registros

Para recuperar um conjunto de registros existente, use `Get-AzureRmDnsRecordSet`. Especifique o nome relativo do conjunto de registros, o tipo de registro e a zona.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

Assim como no `New-AzureRmDnsRecordSet`, o nome do registro deve ser um nome relativo, ou seja, deve excluir o nome da zona.

Você pode especificar a zona usando o nome da zona e o nome do grupo de recursos, ou usando um objeto da zona:

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -Zone $zone
```

`Get-AzureRmDnsRecordSet` retorna um objeto local que representa o conjunto de registros criado no DNS do Azure.

## <a name="list-record-sets"></a>Listar os conjuntos de registros

Você também pode usar`Get-AzureRmDnsRecordSet` para listar os conjuntos de registros se omitir os parâmetros *-Name* e/ou *-RecordType*.

### <a name="to-list-all-record-sets"></a>Para listar todos os conjuntos de registros

Este exemplo retorna todos os conjuntos de registros, independentemente do nome ou do tipo de registro:

```powershell
$list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="to-list-record-sets-of-a-given-record-type"></a>Para listar os conjuntos de registros de um tipo de registro específico.

Este exemplo retorna todos os conjuntos de registros que correspondem ao tipo de registro dado. Neste caso, o conjunto de registros retornado são os registros "A":

```powershell
$list = Get-AzureRmDnsRecordSet -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

A zona pode ser especificada usando os parâmetros `-ZoneName` e `-ResourceGroupName` (conforme mostrado) ou especificando um objeto da zona:

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$list = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-a-record-set"></a>Adicionar um registro a um conjunto de registros

Você adiciona registros aos conjuntos de registros usando o cmdlet `Add-AzureRmDnsRecordConfig` . Esta é uma operação offline. Apenas o objeto local que representa o conjunto de registros é alterado.

Os parâmetros para adicionar registros a um conjunto de registros variam dependendo do tipo de conjunto de registros. Por exemplo, ao usar um conjunto de registros do tipo "A", você só pode especificar os registros com o parâmetro *-IPv4Address*.

Os registros adicionais podem ser adicionados a cada conjunto de registros por meio de chamadas adicionais para `Add-AzureRmDnsRecordConfig`. Você pode adicionar até 20 registros em qualquer conjunto de registros. No entanto, os conjuntos de registros do tipo "CNAME" podem conter, no máximo, um registro e um conjunto de registros não pode conter dois registros idênticos. Os conjuntos de registros vazios (sem nenhum registro) podem ser criados, mas não aparecem nos servidores de nome do DNS do Azure.

Depois do conjunto de registros conter a coleção de registros desejada, ele precisará confirmar usando o cmdlet `Set-AzureRmDnsRecordSet` . Depois do conjunto de registros ter sido confirmado, ele substituirá o conjunto de registros existente no DNS do Azure.

### <a name="to-create-an-a-record-set-with-a-single-record"></a>Para criar um conjunto de registros com um registro único

```powershell
$rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

A sequência de operações para criar um registro também pode ser *canalizada*, significando que você passa o objeto do conjunto de registros usando o canal, em vez de passá-lo como um parâmetro. Por exemplo:

```powershell
New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet
```

### <a name="additional-record-type-examples"></a>Exemplos adicionais de tipo de registro

[!INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="modify-existing-record-sets"></a>Modificar conjuntos de registros existentes

As etapas para modificar um conjunto de registros existente são semelhantes às etapas realizadas ao criar os registros. A sequência de operações é a seguinte:

1. Recupere o conjunto de registros existente usando `Get-AzureRmDnsRecordSet`.
2. Modifique o conjunto de registros adicionando os registros, removendo os registros, alterando os parâmetros do registro ou alterando o TTL (tempo de vida) do conjunto de registros. Esta é uma operação offline. Apenas o objeto local que representa o conjunto de registros é alterado.
3. Confirme as alterações usando o cmdlet `Set-AzureRmDnsRecordSet` . Isso substitui o conjunto de registros existente no DNS do Azure.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Para atualizar um registro em um conjunto de registros existente

Neste exemplo, alteramos o endereço IP de um registro "A" existente:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Records[0].Ipv4Address = "134.170.185.46"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

O cmdlet `Set-AzureRmDnsRecordSet` usa as verificações do etag para garantir que as alterações simultâneas não sejam substituídas. Use o sinalizador *-Overwrite* para suprimir essas verificações. Para obter mais informações, consulte [Sobre etags e marcas](dns-getstarted-create-dnszone.md#tagetag).

### <a name="to-modify-an-soa-record"></a>Para modificar um registro SOA

Você não pode adicionar ou remover registros do conjunto de registros SOA criado automaticamente no apex da zona (nome = "@"). No entanto, você pode modificar qualquer um dos parâmetros no registro SOA (exceto por "Host") e o TTL do conjunto de registros.

O exemplo a seguir mostra como alterar a propriedade *Email* do registro SOA:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar registros NS no apex da zona

Não é possível adicionar, remover nem modificar os registros no conjunto de registros NS criado automaticamente no apex da zona (nome = "@"). A única mudança permitida é a modificação do TTL do conjunto de registros.

O exemplo a seguir mostra como alterar a propriedade TTL do conjunto de registros NS:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Ttl = 300
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-add-records-to-an-existing-record-set"></a>Para adicionar registros a um conjunto de registros existente

Neste exemplo, adicionamos dois registros MX adicionais ao conjunto de registros existente:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
Set-AzureRmDnsRecordSet -RecordSet $rs
```

## <a name="remove-a-record-from-an-existing-record-set"></a>Remover um registro em um conjunto de registros existente

Os registros podem ser removidos de um conjunto de registros usando `Remove-AzureRmDnsRecordConfig`. O registro que está sendo removido deve ser uma correspondência exata com um registro existente, em todos os parâmetros. As alterações devem ser confirmadas usando `Set-AzureRmDnsRecordSet`.

Remover o último registro de um conjunto de registros não exclui o conjunto de registros. Consulte [Excluir um conjunto de registros](#delete-a-record-set) abaixo para saber mais.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

A sequência de operações para remover um registro de um conjunto de registros também pode ser canalizada, significando que você passa o objeto do conjunto de registros usando o canal, em vez de passá-lo como um parâmetro. Por exemplo:

```powershell
Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet
```

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Remover um registro AAAA de um conjunto de registros

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-a-cname-record-from-a-record-set"></a>Remover um registro CNAME de um conjunto de registros

Como um conjunto de registros CNAME pode conter, no máximo, um registro, remover esse registro deixa um conjunto de registros vazio.

```powershell
$rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-mx-record-from-a-record-set"></a>Remover um registro MX de um conjunto de registros

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-ns-record-from-record-set"></a>Remover um registro NS de um conjunto de registros

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-srv-record-from-a-record-set"></a>Remover um registro SRV de um conjunto de registros

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-a-txt-record-from-a-record-set"></a>Remover um registro TXT de um conjunto de registros

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

## <a name="delete-a-record-set"></a>Excluir um conjunto de registros

Os conjuntos de registros podem ser excluídos usando o cmdlet `Remove-AzureRmDnsRecordSet` . Não é possível excluir os conjuntos de registros SOA e NS no apex da zona (nome = "@") que foram criados automaticamente quando a zona foi criada. Eles serão excluídos automaticamente se a zona for excluída.

Use um dos três métodos a seguir para remover um conjunto de registros:

### <a name="specify-all-the-parameters-by-name"></a>Especificar todos os parâmetros pelo nome

O argumento *-Force* opcional pode ser usado para suprimir o prompt de confirmação.

```powershell
Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]
```

### <a name="specify-the-record-set-by-name-and-type-and-specify-the-zone-by-object"></a>Especificar o conjunto de registros pelo nome e tipo, e especificar a zona pelo objeto

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]
```

### <a name="specify-the-record-set-by-object"></a>Especificar o conjunto de registros pelo objeto

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordSet -RecordSet $rs [-Overwrite] [-Force]
```

Quando você especifica o conjunto de registros usando um objeto, ele habilita as verificações do etag para garantir que as alterações simultâneas não sejam excluídas. O sinalizador *-Overwrite* opcional suprime essas verificações. Consulte [Etags e marcas](dns-getstarted-create-dnszone.md#tagetag) para obter mais informações.

O objeto do conjunto de registros também pode ser redirecionado em vez de ser passado como um parâmetro:

```powershell
Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o DNS do Azure, consulte [Visão geral do DNS do Azure](dns-overview.md). Para saber mais sobre como automatizar o DNS, consulte [Criar zonas DNS e conjuntos de registros usando o SDK do .NET](dns-sdk.md).

Para obter mais informações sobre os registros DNS reversos, consulte [Como gerenciar os registros DNS reversos para seus serviços usando o PowerShell](dns-reverse-dns-record-operations-ps.md).



<!--HONumber=Nov16_HO3-->


