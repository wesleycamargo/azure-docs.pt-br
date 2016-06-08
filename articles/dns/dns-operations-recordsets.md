<properties 
   pageTitle="Gerenciar conjuntos de registros DNS e registros no DNS do Azure | Microsoft Azure" 
   description="Gerenciando conjuntos de registros DNS e registros no DNS do Azure ao hospedar seu domínio no DNS do Azure. Todos os comandos do PowerShell para operações em conjuntos de registros e registros." 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmon" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/06/2016"
   ms.author="cherylmc"/>

# Gerenciar registros DNS e conjuntos de registros usando o PowerShell



> [AZURE.SELECTOR]
- [Portal do Azure](dns-operations-recordsets-portal.md)
- [CLI do Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



Este artigo mostra como gerenciar conjuntos de registro e registros de zona DNS usando o Azure PowerShell.

É importante compreender a diferença entre os conjuntos de registros DNS e registros DNS individuais. Um conjunto de registros é a coleção de registros em uma zona com o mesmo nome e o mesmo tipo. Para saber mais, confira [Noções básicas sobre conjuntos de registros e registros](dns-getstarted-create-recordset-portal.md).

Para gerenciar seus conjuntos de registros e registros, você precisará instalar a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell. Para saber mais sobre como trabalhar com o PowerShell, confira [Usar o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).



## Criar um novo conjunto de registros e um registro

Para criar um conjunto de registros no Portal do Azure, confira [Criar registros DNS usando o Portal do Azure](dns-getstarted-create-recordset.md).

## Obter um conjunto de registros

Para recuperar um conjunto de registros existente, use `Get-AzureRmDnsRecordSet`. Especifique o nome relativo do conjunto de registros, o tipo de registro e a zona.

	$rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Assim como acontece com `New-AzureRmDnsRecordSet`, o nome do registro deve ser um nome relativo, ou seja, excluindo o nome da zona.

A zona pode ser especificada usando o nome da zona e o nome do grupo de recursos ou usando um objeto de zona:

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone
	
`Get-AzureRmDnsRecordSet` retorna um objeto local que representa o conjunto de registros criado no DNS do Azure.

## Listar os conjuntos de registros

`Get-AzureRmDnsRecordSet` também pode ser usado para listar os conjuntos de registros, omitindo os parâmetros *–Name* e/ou *–RecordType*.

### Para listar todos os conjuntos de registros 

Este exemplo retornará todos os conjuntos de registros, independentemente do nome ou do tipo de registro:

	$list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### Para listar os conjuntos de registros de um tipo de registro específico.

Este exemplo retornará todos os conjuntos de registros correspondentes ao tipo de registro específico. Nesse caso, registros A.

	$list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 

A zona pode ser especificada usando os parâmetros *–ZoneName* e *–ResourceGroupName* (conforme mostrado) ou especificando um objeto de zona:

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$list = Get-AzureRmDnsRecordSet -Zone $zone

## Adicionar um registro a um conjunto de registros

Os registros são adicionados aos conjuntos de registros com o cmdlet `Add-AzureRmDnsRecordConfig`. Esta é uma operação offline. Apenas o objeto local que representa o conjunto de registros é alterado.

Os parâmetros para adicionar registros a um conjunto de registros variam dependendo do tipo de conjunto de registros. Por exemplo, ao usar um conjunto de registros do tipo *A*, você só poderá especificar os registros com o parâmetro *-IPv4Address*.

Registros adicionais podem ser adicionados a cada conjunto de registros por meio de chamadas adicionais a `Add-AzureRmDnsRecordConfig`. Você pode adicionar até 20 registros em qualquer conjunto de registros. No entanto, os conjuntos de registros do tipo *CNAME* podem conter no máximo um registro, e um conjunto de registros não pode conter dois registros idênticos. Conjuntos de registros vazios (sem nenhum registro) podem ser criados, mas não aparecem nos servidores de nome do DNS do Azure.

Depois que o conjunto de registros contiver a coleção de registros desejada, ele precisará ser confirmado com o cmdlet `Set-AzureRmDnsRecordSet`. Depois que um conjunto de registros for confirmado, ele substituirá o registro existente definido no DNS do Azure pelo conjunto de registros fornecido.

### Para criar um conjunto de registros com um registro único

	$rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

A sequência de operações para criar um registro pode também ser "redirecionada", passando o conjunto de registros usando o pipe ao invés de um parâmetro. Por exemplo:

	New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Exemplos adicionais de tipo de registro

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## Modificar conjuntos de registros existentes

Modificar conjuntos de registros existentes segue um padrão semelhante à criação de registros. A sequência de operações é a seguinte:

1.	Recupere o conjunto de registros existente usando `Get-AzureRmDnsRecordSet`.

2.	Modifique o conjunto de registros, adicionando registros, removendo registros, alterando os parâmetros de registro ou alterando o TTL do conjunto de registros. Essas alterações são offline, apenas o objeto local que representa o conjunto de registros é alterado.

3.	Confirme as alterações usando o cmdlet `Set-AzureRmDnsRecordSet`. Isso substituirá o conjunto de registros existente no DNS do Azure pelo conjunto de registros fornecido.


### Para atualizar um registro em um conjunto de registros existente

Neste exemplo, alteraremos o endereço IP de um registro A existente:

	$rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Ipv4Address = "134.170.185.46"
	Set-AzureRmDnsRecordSet -RecordSet $rs 

O cmdlet `Set-AzureRmDnsRecordSet` usa verificações de *etag* para garantir que alterações simultâneas não sejam substituídas. Use o sinalizador *-Overwrite* para suprimir essas verificações. Confira [Sobre Etags e marcas](dns-getstarted-creatednszone.md#tagetag) para saber mais.

### Para modificar um registro SOA

Você não pode adicionar ou remover registros do conjunto de registros SOA criado automaticamente no apex da zona (nome = "@"), mas você pode modificar qualquer um dos parâmetros no registro SOA (exceto por "Host") e o TTL do conjunto de registros.

O exemplo a seguir mostra como alterar a propriedade *Email* do registro SOA:

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Email = "admin.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs 

### Para modificar registros NS no apex da zona

Não é possível adicionar, remover ou modificar os registros no conjunto de registros NS criados automaticamente no apex da zona (nome = "@"). A única alteração permitida é modificar o TTL do conjunto de registros.

O exemplo a seguir mostra como alterar a propriedade TTL do conjunto de registros NS:

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Ttl = 300
	Set-AzureRmDnsRecordSet -RecordSet $rs 

### Para adicionar registros a um conjunto de registros existente

Neste exemplo, adicionaremos dois registros MX adicionais ao conjunto de registros existente:

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	Set-AzureRmDnsRecordSet -RecordSet $rs 

## Remover um registro em um conjunto de registros existente

Os registros podem ser removidos de um conjunto de registros usando `Remove-AzureRmDnsRecordConfig`. Observe que o registro que está sendo removido deve ser uma correspondência exata com um registro existente, em todos os parâmetros. As alterações devem ser confirmadas usando `Set-AzureRmDnsRecordSet`.

Remover o último registro de um conjunto de registros não exclui o conjunto de registros. Confira [Excluir um conjunto de registros](#delete-a-record-set) abaixo para saber mais.


	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

A sequência de operações para remover um conjunto de registros pode também ser "redirecionada", passando o conjunto de registros usando o pipe ao invés de um parâmetro. Por exemplo:

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Remover o registro AAAA de um conjunto de registros

	$rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Remover o registro CNAME de um conjunto de registros

Como um conjunto de registros CNAME pode conter no máximo um registro, remover esse registro deixa um conjunto de registros vazio.

	$rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Remover o registro MX de um conjunto de registros

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Remover o registro NS de um conjunto de registros
	
	$rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Remover o registro SRV de um conjunto de registros

	$rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### Remover o registro TXT de um conjunto de registros

	$rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	Set-AzureRmDnsRecordSet -RecordSet $rs

## Excluir um conjunto de registros

Conjuntos de registros podem ser excluídos usando o cmdlet `Remove-AzureRmDnsRecordSet`. Você não pode excluir os conjuntos de registro SOA e NS no apex da zona (nome = "@") que foram criados automaticamente quando a zona foi criada. Eles serão excluídos automaticamente se a zona for excluída.

Use um dos três métodos a seguir para remover um conjunto de registros:

### Especificar todos os parâmetros por nome

A opção *-Force* opcional pode ser usada para suprimir o prompt de confirmação.

	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### Especificar o conjunto de registros por nome e tipo e especificar a zona pelo objeto

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### Especifique o conjunto de registros por objeto:

	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Especificar o conjunto de registros usando um objeto habilita as verificações de etag para garantir que as alterações simultâneas não sejam excluídas. O sinalizador opcional *-Overwrite* suprime essas verificações. Consulte [Etags e marcas](dns-getstarted-create-dnszone.md#tagetag) para obter mais informações.

O objeto do conjunto de registros também pode ser redirecionado em vez de ser passado como um parâmetro:

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## Próximas etapas

Para saber mais sobre o Azure DNS, confira [Visão geral do Azure DNS](dns-overview.md). Para saber mais sobre como automatizar o DNS, confira [Criar zonas DNS e conjuntos de registros usando o SDK do .NET](dns-sdk.md).

Se você quiser trabalhar com registros DNS reversos, confira [Como gerenciar registros DNS reversos](dns-reverse-dns-record-operations-ps.md).
 

<!---HONumber=AcomDC_0518_2016-->