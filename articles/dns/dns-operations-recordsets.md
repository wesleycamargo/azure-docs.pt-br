<properties 
   pageTitle="Gerenciar conjuntos de registros DNS e registros no DNS do Azure | Microsoft Azure" 
   description="Gerenciando conjuntos de registros DNS e registros no DNS do Azure ao hospedar seu domínio no DNS do Azure. Todos os comandos do PowerShell para operações em conjuntos de registros e registros." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carmon" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="03/04/2016"
   ms.author="joaoma"/>

# Como gerenciar registros DNS usando o PowerShell


> [AZURE.SELECTOR]
- [CLI do Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Este guia mostrará como gerenciar conjuntos de registro e registros de zona DNS usando o Azure PowerShell.

É importante compreender a diferença entre os conjuntos de registros DNS e registros DNS individuais. Um conjunto de registros é a coleção de registros em uma zona com o mesmo nome e o mesmo tipo. Para obter mais detalhes, consulte [Noções básicas sobre conjuntos de registros e registros](../dns-getstarted-create-recordset#Understanding-record-sets-and-records).

## Criar um conjunto de registros

Os conjuntos de registros são criados usando o cmdlet New-AzureRmDnsRecordSet. Você precisa especificar o nome do conjunto de registros, a zona, o TTL (Time-to-Live, tempo de vida) e o tipo de registro.

O nome do conjunto de registros deve ser um nome relativo, exceto o nome da zona. Por exemplo, o nome de conjunto de registros "www" na zona "contoso.com" criará um conjunto de registros com o nome totalmente qualificado "www.contoso.com".

Para um registro definido no vértices da zona, use "@" como o nome do conjunto de registro, incluindo as aspas. O nome totalmente qualificado do conjunto de registros é igual ao nome da zona, nesse caso, "contoso.com".

O DNS do Azure dá suporte aos seguintes tipos de registros: A, AAAA, CNAME, MX, NS, SOA, SRV, TXT. Conjuntos de registros do tipo SOA são criados automaticamente com cada zona, eles não podem ser criados separadamente.

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]

Se um conjunto de registros já existir, o comando falhará a menos que o comutador -Substituir seja usado. A opção "-Substituir" vai disparar um prompt de confirmação, que pode ser suprimido usando o comutador -Forçar.

No exemplo acima, a zona é especificada usando o nome da zona e o nome do grupo de recursos. Como alternativa, você pode especificar um objeto de zona, como retornado por Get-AzureRmDnsZone ou New-AzureRmDnsZone.

	PS C:\> $zone = Get-AzureRmDnsZone -ZoneName contoso.com –ResourceGroupName MyAzureResourceGroup
	PS C:\> $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

New-AzureRmDnsRecordSet retorna um objeto local que representa o conjunto de registros criado no DNS do Azure.

>[AZURE.IMPORTANT] Conjuntos de registros CNAME não podem coexistir com outros conjuntos de registros com o mesmo nome. Por exemplo, você não pode criar um CNAME com o nome relativo "www" e um registro A com o nome relativo "www" ao mesmo tempo. Uma vez que o apex de zona (nome = "@") sempre contém os conjuntos de registro NS e SOA criados quando a zona é criada, isso significa que você não pode criar um conjunto de registros CNAME no apex da zona. Essas restrições são provenientes dos padrões DNS, elas não são limitações do DNS do Azure.

### Registros curinga

O DNS do Azure dá suporte a [registros curinga](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Eles são retornados para qualquer consulta com um nome correspondente (a menos que haja uma correspondência mais próxima de um conjunto de registros não curinga).

Para criar um conjunto de registros curinga, use o nome do conjunto de registros "*", ou um nome cujo primeiro rótulo seja "*", por exemplo, "*.foo".

Conjuntos de registros curinga têm suporte para todos os tipos de registro, exceto NS e SOA.

## Obter um conjunto de registros

Para recuperar um conjunto de registros existente, use ‘Get-AzureRmDnsRecordSet’, especificando o nome relativo do conjunto de registros, o tipo de registro e a zona:

	PS C:\> $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Assim como acontece com New-AzureRmDnsRecordSet, o nome do registro deve ser um nome relativo, ou seja, sem o nome da zona. A zona pode ser especificada usando o nome da zona e o nome do grupo de recursos ou usando um objeto de zona:

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone
	
Get-AzureRmDnsRecordSet retorna um objeto local que representa o conjunto de registros criado no DNS do Azure.

## Listar os conjuntos de registros
Ao omitir os parâmetros –Name e/ou –RecordType, Get-AzureRmDnsRecordSet também pode ser usado para listar conjuntos de registros:

### Opção 1 

Listar todos os conjuntos de registros. Isso retornará todos os conjuntos de registros, independentemente do nome ou o tipo de registro:

	PS C:\> $list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### Opção 2 

Listar os conjuntos de registros de um tipo de registro específico. Isso retornará todos os conjuntos de registros correspondentes ao tipo de registro determinado (nesse caso, registros A):

	PS C:\> $list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 

Nas duas opções acima, a zona pode ser especificada usando os parâmetros – ZoneName e –ResourceGroupName (conforme mostrado) ou especificando um objeto de zona:

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	PS C:\> $list = Get-AzureRmDnsRecordSet -Zone $zone

## Adicionar um registro a um conjunto de registros

Os registros são adicionados aos conjuntos de registros usando o cmdlet Add-AzureRmDnsRecordConfig. Esta é uma operação offline. Apenas o objeto local que representa o conjunto de registros é alterado.

Os parâmetros para adicionar registros a um conjunto de registros variam dependendo do tipo de conjunto de registros. Por exemplo, ao usar um conjunto de registros do tipo "A", você só poderá especificar os registros com o parâmetro "IPv4Address".

Os outros registros podem ser adicionados a cada conjunto de registros por meio de chamadas adicionais a Add-AzureRmDnsRecordConfig. Você pode adicionar até 20 registros em qualquer conjunto de registros. No entanto, os conjuntos de registros do tipo CNAME podem conter no máximo 1 registro e um conjunto de registros não pode conter dois registros idênticos. Conjuntos de registros vazios (sem nenhum registro) podem ser criados, mas não aparecem nos servidores de nome do DNS do Azure.

Quando o conjunto de registros contiver a coleção de registros desejada, precisará ser confirmado usando o cmdlet Set-AzureRmDnsRecordSet, que substitui o conjunto de registros existente definido no DNS do Azure pelo conjunto de registros fornecido. Os exemplos a seguir mostram como criar um conjunto de registros de cada tipo de registro que contém um único registro.

### Criar um conjunto de registros A com registro único

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

A sequência de operações para criar um registro pode também ser "redirecionada", passando o conjunto de registros usando o pipe ao invés de um parâmetro. Por exemplo:

	PS C:\> New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Criar um conjunto de registros AAAA com um registro único

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Criar um conjunto de registros CNAME com um registro único

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Criar um conjunto de registros MX com um registro único

Neste exemplo, usamos o nome do conjunto de registros "@" para criar o registro MX no vértice da zona (por exemplo, "contoso.com"). Isso é comum para os registros MX.

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "@" -RecordType MX -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Criar um conjunto de registros NS com um registro único

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Criar um conjunto de registros SRV com um registro único

Se estiver criando um registro SRV na raiz da zona, basta especificar \_serviço e \_protocolo no nome do registro, não é necessário incluir também ".@" no nome do registro

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Criar um conjunto de registros TXT com um registro único

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

## Modificando conjuntos de registros existentes

Modificar conjuntos de registros existentes segue um padrão semelhante à criação de registros. A sequência de operações é a seguinte:

1.	Recupere o conjunto de registros existente usando Get-AzureRmDnsRecordSet.
2.	Modificar o conjunto de registros, adicionando registros, removendo registros, alterando os parâmetros de registro ou alterando o TTL do conjunto de registros. Essas alterações são offline, apenas o objeto local que representa o conjunto de registros é alterado.
3.	Confirme as alterações usando o cmdlet Set-AzureRmDnsRecordSet. Isso substitui o conjunto de registros existente no DNS do Azure pelo conjunto de registros fornecido.

Isso é mostrado pelos exemplos a seguir:

### Atualizar um registro em um conjunto de registros existente

Para este exemplo, iremos alterar o endereço IP de um registro A existente:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> $rs.Records[0].Ipv4Address = "134.170.185.46"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

O cmdlet Set-AzureRmDnsRecordSet usa verificações de "etag" para garantir que alterações simultâneas não sejam substituídas. Use o sinalizador "-Substituir" para suprimir essas verificações. Consulte Etags e marcas para obter mais informações.

### Modificar o registro SOA

>[AZURE.NOTE] Você não pode adicionar ou remover registros do conjunto de registros SOA criado automaticamente no apex da zona (nome = "@"), mas você pode modificar qualquer um dos parâmetros no registro SOA (exceto por "Host") e o TTL do conjunto de registros.

O exemplo a seguir mostra como alterar a propriedade "Email" do registro SOA:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> $rs.Records[0].Email = "admin.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

### Modificar registros NS no apex da zona

>[AZURE.NOTE] Não é possível adicionar, remover ou modificar os registros no conjunto de registros NS criados automaticamente no apex da zona (nome = "@"). A única alteração permitida é modificar o TTL do conjunto de registros.

O exemplo a seguir mostra como alterar a propriedade TTL do conjunto de registros NS:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> $rs.Ttl = 300
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

### Adicionar registros em um conjunto de registros existente

Neste exemplo, adicionamos dois registros MX adicionais ao conjunto de registros existente:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

## Remover um registro em um conjunto de registros existente

Os registros podem ser removidos de um conjunto de registros usando Remove-AzureRmDnsRecordConfig. Observe que o registro que está sendo removido deve ser uma correspondência exata com um registro existente, em todos os parâmetros. As alterações devem ser confirmadas usando Set-AzureRmDnsRecordSet.

Remover o último registro de um conjunto de registros não exclui o conjunto de registros. Consulte [Excluir um conjunto de registros](#delete-a-record-set) abaixo para obter mais informações.


	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

A sequência de operações para remover um conjunto de registros pode também ser "redirecionada", passando o conjunto de registros usando o pipe ao invés de um parâmetro. Por exemplo:

	PS C:\> Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### Remover o registro AAAA de um conjunto de registros

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Remover o registro CNAME de um conjunto de registros

Como um conjunto de registros CNAME pode conter no máximo um registro, remover esse registro deixa um conjunto de registros vazio.

	PS C:\> $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Remover o registro MX de um conjunto de registros

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Remover o registro NS de um conjunto de registros
	
	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Remover o registro SRV de um conjunto de registros

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### Remover o registro TXT de um conjunto de registros

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

## Excluir um conjunto de registros
Os conjuntos de registro podem ser excluídos usando o cmdlet Remove-AzureRmDnsRecordSet.

>[AZURE.NOTE] Você não pode excluir os conjuntos de registro SOA e NS no apex da zona (nome = "@") que são criados automaticamente quando a zona é criada. Eles serão excluídos automaticamente ao excluir a zona.

Use um dos três métodos a seguir para remover um conjunto de registros:

### Opção 1

Especifique todos os parâmetros por nome:

	PS C:\> Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]

O comutador opcional "-Forçar" pode ser usado para suprimir o prompt de confirmação.

### Opção 2

Especifique o conjunto de registros por nome e tipo, especifique a zona pelo objeto:

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### Opção 3

Especifique o conjunto de registros por objeto:

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Especificar o conjunto de registros usando um objeto habilita as verificações de "etag" para garantir que as alterações simultâneas não sejam excluídas. O sinalizador opcional "-Substituir" suprime essas verificações. Consulte [Etags e marcas](../dns-getstarted-create-dnszone#Etags-and-tags) para obter mais informações.

O objeto do conjunto de registros também pode ser redirecionado em vez de ser passado como um parâmetro:

	PS C:\> Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

##Consulte também

[Introdução à criação de conjuntos de registros e de registros](dns-getstarted-create-recordset.md)<BR> [Gerenciar zonas DNS](dns-operations-dnszones.md)<BR> [Automatizar operações usando o SDK do .NET](dns-sdk.md)
 

<!---HONumber=AcomDC_0309_2016-->