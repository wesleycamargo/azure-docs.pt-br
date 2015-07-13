<properties 
   pageTitle="Gerenciar conjuntos de registros DNS e registros no DNS do Azure | Microsoft Azure" 
   description="Gerenciando conjuntos de registros DNS e registros no DNS do Azure ao hospedar seu domínio no DNS do Azure. Todos os comandos do PowerShell para operações em conjuntos de registros e registros." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/01/2015"
   ms.author="joaoma"/>

# Como gerenciar os registros DNS

Este guia mostrará como gerenciar conjuntos de registro e registros de zona DNS.

É importante compreender a diferença entre os conjuntos de registros DNS e registros DNS individuais. Um conjunto de registros é a coleção de registros em uma zona com o mesmo nome e o mesmo tipo. Para obter mais detalhes, consulte [Noções básicas sobre conjuntos de registros e registros](../dns-getstarted-create-recordset#Understanding-record-sets-and-records).

## Criar um conjunto de registros

Conjuntos de registros são criados usando o cmdlet New-AzureDnsRecordSet. Você precisa especificar o nome do conjunto de registros, a zona, o TTL (Time-to-Live, tempo de vida) e o tipo de registro.

>[AZURE.NOTE]O nome do conjunto de registros deve ser um nome relativo, exceto o nome da zona. Por exemplo, o nome de conjunto de registros “www” na zona "contoso.com" criará um conjunto de registros com o nome totalmente qualificado “www.contoso.com”.

>Para um registro definido no vértices da zona, use "@" como o nome do conjunto de registro, incluindo as aspas. O nome totalmente qualificado do conjunto de registros é igual ao nome da zona, nesse caso, "contoso.com".

O DNS do Azure dá suporte aos seguintes tipos de registros: A, AAAA, CNAME, MX, NS, SOA, SRV, TXT. Conjuntos de registros do tipo SOA são criados automaticamente com cada zona, eles não podem ser criados separadamente.

	PS C:\> $rs = New-AzureDnsRecordSet -Name www -Zone $zone -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

Se um conjunto de registros já existir, o comando falhará a menos que o comutador -Substituir seja usado. A opção “-Substituir” vai disparar um prompt de confirmação, que pode ser suprimido usando o comutador -Forçar.

No exemplo acima, a zona é especificada usando um objeto de zona, conforme retornado por Get-AzureDnsZone ou New-AzureDnsZone. Como alternativa, você também pode especificar a zona pelo nome da zona e nome do grupo de recursos:

	PS C:\> $rs = New-AzureDnsRecordSet -Name www –ZoneName contoso.com –ResourceGroupName MyAzureResourceGroup -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

New-AzureDnsRecordSet retorna um objeto local que representa o conjunto de registros criado no DNS do Azure.

>[AZURE.NOTE]Conjuntos de registros CNAME não podem coexistir com outros conjuntos de registros com o mesmo nome. Por exemplo, você não pode criar um CNAME com o nome relativo “www” e um registro A com o nome relativo “www” ao mesmo tempo. Uma vez que o apex de zona (nome = “@”) sempre contém os conjuntos de registro NS e SOA criados quando a zona é criada, isso significa que você não pode criar um conjunto de registros CNAME no apex da zona. Essas restrições são provenientes dos padrões DNS, elas não são limitações do DNS do Azure.

## Obter um conjunto de registros
Para recuperar um conjunto de registros existente, use 'Get-AzureDnsRecordSet', especificando o nome relativo do conjunto de registros, o tipo de registro e a zona:

	PS C:\> $rs = Get-AzureDnsRecordSet -Name www –RecordType A -Zone $zone

Assim como acontece com New-AzureDnsRecordSet, o nome do registro deve ser um nome relativo, ou seja, excluindo o nome da zona. A zona pode ser especificada usando um objeto de zona (como acima) ou pelo nome da zona e o nome do grupo de recursos:

	PS C:\> $rs = Get-AzureDnsRecordSet –Name www –RecordType A -Zonename contoso.com -ResourceGroupName MyAzureResourceGroup

Get-AzureDnsRecordSet retorna um objeto local que representa o conjunto de registros criado no DNS do Azure.

## Listar os conjuntos de registros
Ao omitir os parâmetros –Name e/ou – RecordType, Get-AzureDnsRecordSet também pode ser usado para listar conjuntos de registros:

### Opção 1 
Listar todos os conjuntos de registros. Isso retornará todos os conjuntos de registros, independentemente do nome ou o tipo de registro:

	PS C:\> $list = Get-AzureDnsRecordSet -Zone $zone
### Opção 2 

Listar os conjuntos de registros de um tipo de registro específico. Isso retornará todos os conjuntos de registros correspondentes ao tipo de registro determinado (nesse caso, registros A):

	PS C:\> $list = Get-AzureDnsRecordSet –RecordType A -Zone $zone 

Em ambos os casos acima, a zona pode ser especificada usando um objeto de zona (conforme mostrado) ou especificando os parâmetros – ZoneName e –ResourceGroupName.

## Adicionar um registro a um conjunto de registros
Registros são adicionados aos conjuntos de registros usando o cmdlet Add-AzureDnsRecordConfig. Esta é uma operação offline. Apenas o objeto local que representa o conjunto de registros é alterado.

Os parâmetros para adicionar registros a um conjunto de registros variam dependendo do tipo de conjunto de registros. Por exemplo, ao usar um conjunto de registros do tipo “A”, você só poderá especificar os registros com o parâmetro “IPv4Address”.

Registros adicionais podem ser adicionados a cada conjunto de registros por meio de chamadas adicionais a Add-AzureDnsRecordConfig. Você pode adicionar até 100 registros em qualquer conjunto de registros. No entanto, os conjuntos de registros do tipo CNAME podem conter no máximo 1 registro e um conjunto de registros não pode conter dois registros idênticos. Conjuntos de registros vazios (sem nenhum registro) podem ser criados, mas não aparecem nos servidores de nome do DNS do Azure.

Depois que o conjunto de registros contém a coleção desejada de registros, ele precisa ser confirmado usando o cmdlet Set-AzureDnsRecordSet, que substitui o conjunto de registros existente definido no DNS do Azure com o conjunto de registros fornecido. Os exemplos a seguir mostram como criar um conjunto de registros de cada tipo de registro que contém um único registro.

### Criar um conjunto de registros A com registro único

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

A sequência de operações para criar um registro pode também ser “redirecionada”, passando o conjunto de registros usando o pipe ao invés de um parâmetro. Por exemplo:

	PS C:\> New-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60 | Add-AzureDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureDnsRecordSet

### Criar um conjunto de registros AAAA com um registro único

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Criar um conjunto de registros CNAME com um registro único

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-cname" -RecordType CNAME -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Criar um conjunto de registros MX com um registro único
Neste exemplo, usamos o nome do conjunto de registros "@" para criar o registro MX no vértice da zona (por exemplo, "contoso.com"). Isso é comum para os registros MX.

	PS C:\> $rs = New-AzureDnsRecordSet -Name "@" -RecordType MX -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Criar um conjunto de registros NS com um registro único

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs
### Criar um conjunto de registros SRV com um registro único

Se estiver criando um registro SRV na raiz da zona, basta especificar _serviço e _protocolo no nome do registro, não é necessário incluir também “.@” no nome do registro

	PS C:\> $rs = New-AzureDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Criar um conjunto de registros TXT com um registro único

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

## Modificando conjuntos de registros existentes
Modificar conjuntos de registros existentes segue um padrão semelhante à criação de registros. A sequência de operações é a seguinte:

1.	Recupere o conjunto de registros existente usando Get-AzureDnsRecordSet.
2.	Modificar o conjunto de registros, adicionando registros, removendo registros, alterando os parâmetros de registro ou alterando o TTL do conjunto de registros. Essas alterações são offline, apenas o objeto local que representa o conjunto de registros é alterado.
3.	Confirme as alterações usando o cmdlet Set-AzureDnsRecordSet. Isso substitui o conjunto de registros existente no DNS do Azure pelo conjunto de registros fornecido.

Isso é mostrado pelos exemplos a seguir:

### Atualizar um registro em um conjunto de registros existente
Para este exemplo, iremos alterar o endereço IP de um registro A existente:

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-a" -RecordType A -Zone $zone 
	PS C:\> $rs.Records[0].Ipv4Address = "134.170.185.46"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

O cmdlet Set-AzureDnsRecordSet usa verificações de “etag” para garantir que alterações simultâneas não sejam substituídas. Use o sinalizador “-Substituir” para suprimir essas verificações. Consulte Etags e marcas para obter mais informações.

### Modificar o registro SOA

>[AZURE.NOTE]Você não pode adicionar ou remover registros do conjunto de registros SOA criado automaticamente no apex da zona (nome = “@”), mas você pode modificar os parâmetros no registro SOA e o TTL do conjunto de registros.

O exemplo a seguir mostra como alterar a propriedade “Email” do registro SOA:

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "@" -RecordType SOA -Zone $zone
	PS C:\> $rs.Records[0].Email = "admin.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

### Modificar registros NS no apex da zona

>[AZURE.NOTE]Não é possível adicionar, remover ou modificar os registros no conjunto de registros NS criados automaticamente no apex da zona (nome = “@”). A única alteração permitida é modificar o TTL do conjunto de registros.

O exemplo a seguir mostra como alterar a propriedade TTL do conjunto de registros NS:

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "@" -RecordType NS -Zone $zone
	PS C:\> $rs.Ttl = 300
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

### Adicionar registros em um conjunto de registros existente
Neste exemplo, adicionamos dois registros MX adicionais ao conjunto de registros existente:

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-mx" -RecordType MX -Zone $zone
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

## Remover um registro em um conjunto de registros existente

Registros podem ser removidos de um conjunto de registros usando Remove-AzureDnsRecordConfig. Observe que o registro que está sendo removido deve ser uma correspondência exata com um registro existente, em todos os parâmetros. Alterações devem ser confirmadas usando Set-AzureDnsRecordSet.

Remover o último registro de um conjunto de registros não exclui o conjunto de registros. Consulte [Excluir um conjunto de registros](#delete-a-record-set) abaixo para obter mais informações.


	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-a" -RecordType A –Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

A sequência de operações para remover um conjunto de registros pode também ser “redirecionada”, passando o conjunto de registros usando o pipe ao invés de um parâmetro. Por exemplo:

	PS C:\> Get-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureDnsRecordSet
### Remover o registro AAAA de um conjunto de registros

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-aaaa" -RecordType AAAA –Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Remover o registro CNAME de um conjunto de registros

Como um conjunto de registros CNAME pode conter no máximo um registro, remover esse registro deixa um conjunto de registros vazio.

	PS C:\> $rs =  Get-AzureDnsRecordSet -name "test-cname" -RecordType CNAME –Zone $zone	
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Remover o registro MX de um conjunto de registros

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-mx" -RecordType 'MX' –Zone $zone	
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Remover o registro NS de um conjunto de registros
	
	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Remover o registro SRV de um conjunto de registros

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### Remover o registro TXT de um conjunto de registros

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

## Excluir um conjunto de registros
Os conjuntos de registro podem ser excluídos usando o cmdlet Remove-AzureDnsRecordSet.

>[AZURE.NOTE]Você não pode excluir os conjuntos de registro SOA e NS no apex da zona (nome = “@”) que são criados automaticamente quando a zona é criada. Eles serão excluídos automaticamente ao excluir a zona.

Use um dos três métodos a seguir para remover um conjunto de registros:
### Opção 1
Especifique todos os parâmetros por nome:

	PS C:\> Remove-AzureDnsRecordSet -Name "test-a" -RecordType A -Zonename "contoso.com" -ResourceGroupName MyAzureResourceGroup [-Force]
O comutador opcional “-Forçar” pode ser usado para suprimir o prompt de confirmação.

### Opção 2
Especifique o conjunto de registros por nome e tipo, especifique a zona pelo objeto:

	PS C:\> Remove-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### Opção 3
Especifique o conjunto de registros por objeto:

	PS C:\> Remove-AzureDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Especificar o conjunto de registros usando um objeto habilita as verificações de “etag” para garantir que as alterações simultâneas não sejam excluídas. O sinalizador opcional “-Substituir” suprime essas verificações. Consulte [Etags e marcas](../dns-getstarted-create-dnszone#Etags-and-tags) para obter mais informações.

O objeto do conjunto de registros também pode ser redirecionado em vez de ser passado como um parâmetro:

	PS C:\> Get-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureDnsRecordSet [-Overwrite] [-Force]

##Consulte também

[Introdução à criação de conjuntos de registros e registros](../dns-getstarted-create-recordset)<BR> [Executar operações em zonas DNS](../dns-operations-dnszones)<BR> [Automatizar operações usando o SDK do .NET](../dns-sdk)
 

<!---HONumber=62-->