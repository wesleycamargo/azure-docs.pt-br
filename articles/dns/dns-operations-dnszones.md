---
title: Gerenciar zonas DNS usando o PowerShell | Microsoft Docs
description: "Você pode gerenciar zonas DNS usando o Azure Powershell. Como atualizar, excluir e criar zonas DNS no DNS do Azure"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1136b0e378cec14a9c899b61ec1dd21ff6720ba5

---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Como gerenciar as zonas DNS usando o PowerShell

> [!div class="op_single_selector"]
> * [CLI do Azure](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

Este artigo mostrará como gerenciar sua zona DNS usando o PowerShell. Para usar essas etapas, você precisará instalar a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager (1.0 ou posterior). Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar os cmdlets do PowerShell.

## <a name="create-a-new-dns-zone"></a>Criar uma nova zona DNS

Para criar uma zona DNS, consulte [Criar uma zona DNS usando o PowerShell](dns-getstarted-create-dnszone.md).

## <a name="get-a-dns-zone"></a>Obter uma zona DNS

Para recuperar uma zona DNS, use o cmdlet `Get-AzureRmDnsZone` . Esta operação retornará um objeto de zona DNS correspondente a uma zona existente no DNS do Azure. O objeto contém dados sobre a zona (como o número de conjuntos de registros), mas não contém os conjuntos de registro em si.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
```

## <a name="list-dns-zones"></a>Listar as zonas DNS

Omitindo o nome da zona de `Get-AzureRmDnsZone`, você pode enumerar todas as zonas em um grupo de recursos. Essa operação retorna uma matriz de objetos de zona.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

## <a name="update-a-dns-zone"></a>Atualizar uma zona DNS

As alterações a um recurso da zona DNS podem ser feitas usando o `Set-AzureRmDnsZone`. Isso não atualiza nenhum dos conjuntos de registros DNS dentro da zona (consulte [Como gerenciar registros DNS](dns-operations-recordsets.md)). Ele só é usado para atualizar as propriedades do recurso da zona em si. Isso é atualmente limitado às “marcas” do Gerenciador de Recursos do Azure para o recurso de zona. Consulte [Etags e marcas](dns-getstarted-create-dnszone.md#tagetag) para obter mais informações.

Use um dos dois métodos a seguir para atualizar a zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Especifique a zona usando o nome da zona e o grupo de recursos

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]
```

### <a name="specify-the-zone-using-a-zone-object"></a>Especifique a zona usando um objeto de $zone

Especifique a zona usando um objeto de $zone de `Get-AzureRmDnsZone`. Ao usar `Set-AzureRmDnsZone` com um objeto $zone, verificações de Etag serão usadas para garantir que as alterações simultâneas não sejam substituídas. Você pode usar a opção *-Substituir* para suprimir essas verificações. Consulte [Etags e marcas](dns-getstarted-create-dnszone.md#tagetag) para obter mais informações.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
<..modify $zone.Tags here...>
Set-AzureRmDnsZone -Zone $zone [-Overwrite]
```

## <a name="delete-a-dns-zone"></a>Excluir uma zona DNS

As zonas DNS podem ser excluídas usando o cmdlet Remove-AzureRmDnsZone.

Antes de excluir uma zona DNS no DNS do Azure, você precisará excluir todos os conjuntos de registros, exceto os registros NS e SOA na raiz da zona, que foram criados automaticamente quando a zona foi criada.

Use um dos dois métodos a seguir para remover uma zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Especifique a zona usando o nome da zona e o nome do grupo de recursos

Esta operação tem uma opção `-Force` opcional que suprime o aviso para confirmar se você deseja remover a zona DNS.

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]
```

### <a name="specify-the-zone-using-a-zone-object"></a>Especifique a zona usando um objeto de $zone

Especifique a zona usando um objeto de $zone de `Get-AzureRmDnsZone`. Esta operação tem uma opção `-Force` opcional que suprime o aviso para confirmar se você deseja remover a zona DNS. Assim como com `Set-AzureRmDnsZone`, especificar a zona usando um objeto $zone habilita as verificações de Etag para garantir que as alterações simultâneas não sejam excluídas.

O sinalizador `-Overwrite` opcional suprime essas verificações. Consulte [Etags e marcas](dns-getstarted-create-dnszone.md#tagetag) para obter mais informações.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]
```

O objeto de zona também pode ser redirecionado em vez de ser passado como um parâmetro:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]
```

## <a name="next-steps"></a>Próximas etapas

Após criar uma zona DNS, crie [conjuntos de registros e registros](dns-getstarted-create-recordset.md) para iniciar a resolução de nomes do seu domínio da Internet.




<!--HONumber=Dec16_HO2-->


