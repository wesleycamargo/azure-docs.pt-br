---
title: Introdução às zonas privadas do DNS do Azure usando o PowerShell | Microsoft Docs
description: Saiba como criar uma zona e registro de DNS privado no DNS do Azure. Este é uma guia passo a passo para criar e gerenciar sua primeira zona e registro de DNS privado usando o PowerShell.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d9e5c9b8caa5349fbcda9b71f7524fb9e99b976c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Introdução às zonas privadas do DNS do Azure usando o PowerShell

Este artigo explica as etapas para criar sua primeira zona e registro de DNS privado usando o Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS. Para publicar uma zona de DNS privado em sua rede virtual, você deve especificar a lista de redes virtuais que podem resolver registros na zona.  Chamamos essas redes de “redes virtuais de resolução”.  Também é possível especificar uma rede virtual para a qual o DNS do Azure mantenha registros de nome do host sempre que uma VM for criada, mudar de IP ou for destruída.  Chamamos essa rede de “rede virtual de registro”.

# <a name="get-the-preview-powershell-modules"></a>Obter os módulos de versão prévia do PowerShell
Essas instruções pressupõem que você já instalou e entrou no Azure PowerShell, incluindo a garantia de que você tem os módulos necessários para o recurso de Zona privada. 

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, um grupo de recursos é criado para conter a zona DNS. O exemplo a seguir mostra o comando.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-private-zone"></a>Criar uma zona de DNS privado

Uma zona DNS é criada usando o cmdlet `New-AzureRmDnsZone` junto com um valor de “Privado” para o parâmetro ZoneType. O exemplo a seguir cria uma zona DNS chamada *contoso.local* no grupo de recursos chamado *MyResourceGroup* e a disponibiliza na rede virtual chamada *MyAzureVnet* . Use o exemplo para criar uma zona DNS, substituindo os valores pelos seus próprios.

Observe que, se o parâmetro ZoneType for omitido, a zona será criada como uma Zona pública, portanto, ele é necessário se você precisar criar uma Zona privada. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

Se você precisar que o Azure crie automaticamente os registros de nome de host na zona, use o parâmetro *RegistrationVirtualNetworkId* em vez de *ResolutionVirtualNetworkId*.  Redes virtuais de registro são habilitadas automaticamente para a resolução.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```

## <a name="create-a-dns-record"></a>Criar um registro DNS

Você cria conjuntos de registros usando o cmdlet `New-AzureRmDnsRecordSet`. O exemplo a seguir cria um registro com o nome relativo "db" na Zona DNS "contoso.local", no grupo de recursos "MyResourceGroup". O nome totalmente qualificado do conjunto de registros é "db.contoso.local". O tipo de registro é "A", com o endereço IP "10.0.0.4" e a TTL de 3600 segundos.

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

Para outros tipos de registros, para conjuntos de registros com mais de um registro, e para modificar registros existentes, consulte [Gerenciar registros DNS e conjuntos de registros usando o Azure PowerShell](dns-operations-recordsets.md). 

## <a name="view-records"></a>Exibir registros

Para listar os registros DNS em sua zona, use:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

# <a name="list-dns-private-zones"></a>Listar zonas de DNS privado

Omitindo o nome da zona de `Get-AzureRmDnsZone`, você pode enumerar todas as zonas em um grupo de recursos. Essa operação retorna uma matriz de objetos de zona.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Omitindo o nome da zona e o nome do grupo de recursos do `Get-AzureRmDnsZone`, você pode enumerar todas as regiões na assinatura do Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-private-zone"></a>Atualizar uma zona de DNS privado

As alterações a um recurso da zona DNS podem ser feitas usando o `Set-AzureRmDnsZone`. Este cmdlet não atualiza nenhum dos conjuntos de registros DNS dentro da zona (consulte [Como gerenciar registros DNS](dns-operations-recordsets.md)). Ele só é usado para atualizar as propriedades do recurso da zona em si. As propriedades da zona gravável estão limitadas a [“marcas” do Azure Resource Manager para o recurso de zona](dns-zones-records.md#tags), assim como os parâmetros 'RegistrationVirtualNetworkId' e 'ResolutionVirtualNetworkId' para Zonas privadas.

O exemplo a seguir substitui a Rede virtual de registro vinculada a uma zona por uma nova MyNewAzureVnet.

Observe que você não deve especificar o parâmetro ZoneType para atualização, ao contrário de quando o cria. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -RegistrationVirtualNetworkId @($vnet.Id)
```

O exemplo a seguir substitui a Rede virtual de resolução vinculada a uma zona por uma nova chamada “MyNewAzureVnet”.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ResolutionVirtualNetworkId @($vnet.Id)
```

## <a name="delete-a-dns-private-zone"></a>Excluir uma zona de DNS privado

Zonas de DNS privado podem ser excluídas usando o cmdlet `Remove-AzureRmDnsZone`, tal como zonas públicas.

> [!NOTE]
> Excluir uma zona DNS também excluirá todos os registros DNS na zona. Essa operação não pode ser desfeita. Se a zona DNS estiver em uso, serviços que usam a zona falharão quando a zona for excluída.
>
>Para se proteger contra a exclusão acidental da zona, consulte [Proteger registros e zonas DNS](dns-protect-zones-recordsets.md).

Use um dos dois métodos a seguir para excluir uma zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Especifique a zona usando o nome da zona e o nome do grupo de recursos

```powershell
Remove-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Especifique a zona usando um objeto de $zone

Você pode especificar a zona a ser excluído usando um `$zone` objeto retornado por `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

O objeto de zona também pode ser redirecionado em vez de ser passado como um parâmetro:

```powershell
Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsZone

```

## <a name="confirmation-prompts"></a>Prompts de confirmação

Os cmdlets `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` e `Remove-AzureRmDnsZone` oferecem suporte aos prompts de confirmação.

`New-AzureRmDnsZone` e `Set-AzureRmDnsZone` solicitam confirmação caso a variável de preferência do PowerShell `$ConfirmPreference` tenha um valor `Medium` ou inferior. Dado o impacto potencialmente alto da exclusão de uma zona DNS, o `Remove-AzureRmDnsZone` cmdlet solicita confirmação se a `$ConfirmPreference` variável do PowerShell tem qualquer valor diferente de `None`.

Desde o valor padrão para `$ConfirmPreference` é `High`, apenas `Remove-AzureRmDnsZone` solicitará uma confirmação por padrão.

Você pode substituir a configuração `$ConfirmPreference` atual usando o parâmetro `-Confirm`. Se você especificar `-Confirm` ou `-Confirm:$True`, o cmdlet solicitará uma confirmação antes de executar. Se você especificar `-Confirm:$False`, o cmdlet não solicitará uma confirmação.

Para obter mais informações sobre `-Confirm` e `$ConfirmPreference`, consulte [Sobre as Variáveis de Preferência](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).


## <a name="delete-all-resources"></a>Excluir todos os recursos

Para excluir todos os recursos criados neste artigo, execute as seguintes etapas:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as zonas DNS privadas, consulte [Usar o Azure DNS para domínios privados](private-dns-overview.md).

Leia mais sobre alguns [cenários de Zona privada](./private-dns-scenarios.md) comuns que podem ser efetuados com Zonas privadas no DNS do Azure.

Para saber mais sobre como gerenciar os registros DNS no DNS do Azure, consulte [Gerenciar registros DNS e conjuntos de registros no DNS do Azure usando o PowerShell](dns-operations-recordsets.md).

