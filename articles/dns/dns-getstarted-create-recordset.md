<properties
   pageTitle="Criar um conjunto de registros e registros para uma zona DNS | Microsoft Azure"
   description="Como criar registros de host para o DNS do Azure. Configuração dos conjuntos de registros e registros usando o PowerShell"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="Adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma"/>


# Criar registros DNS

Depois de criar a zona DNS, você precisa adicionar os registros DNS para seu domínio. Para fazer isso, primeiro você precisa entender os registros DNS e conjuntos de registros.


## Noções básicas sobre conjuntos de registros e registros
Cada registro DNS tem um nome e um tipo.

Um nome _totalmente qualificado_ inclui o nome da zona, enquanto um nome _relativo_ não. Por exemplo, o nome relativo do registro “www” na zona "contoso.com" fornece o nome totalmente qualificado do registro “www.contoso.com”.

>[AZURE.NOTE]No DNS do Azure, os registros são especificados usando nomes relativos.

Há vários tipos de registros de acordo com os dados que eles contêm. O tipo mais comum é um registro "A", que mapeia um nome para um endereço IPv4. Outro tipo é um registro “MX”, que mapeia um nome para um servidor de email.

O DNS do Azure dá suporte a todos os tipos de registro DNS comuns: A, AAAA, CNAME, MX, NS, SOA, SRV e TXT.

Às vezes, você precisa criar mais de um registro DNS com um determinado nome e tipo. Por exemplo, suponha que o site www.contoso.com seja hospedado em dois endereços IP diferentes. Isso requer dois registros A diferentes, um para cada endereço IP:

	www.contoso.com.		3600	IN	A	134.170.185.46
	www.contoso.com.		3600	IN	A	134.170.188.221

Este é um exemplo de um conjunto de registros. Um conjunto de registros é a coleção de registros DNS de uma zona com o mesmo nome e o mesmo tipo. A maioria dos conjuntos de registros contêm um único registro, mas exemplos como o mostrado acima no qual um conjunto de registros contém mais de um registro de exemplos não são incomuns. (Conjuntos de registros do tipo SOA e CNAME são uma exceção, os padrões do DNS não permitem vários registros com o mesmo nome para esses tipos.)

O tempo de vida, ou TTL, especifica quanto tempo cada registro é armazenado em cache pelos clientes antes de ser consultado novamente. No exemplo acima, o TTL tem 3600 segundos ou 1 hora. O TTL é especificado para o conjunto de registros, não para cada registro, portanto, o mesmo valor é usado para todos os registros no conjunto de registros.

>[AZURE.NOTE]O DNS do Azure gerencia registros DNS usando conjuntos de registros.



## Criar conjuntos de registros e registros

No exemplo a seguir, mostraremos como criar um conjunto de registros e registros. Vamos usar o tipo de registro de DNS 'A', para outros tipos de registro, consulte [Como gerenciar registros DNS](dns-operations-recordsets.md)


### Etapa 1

Crie o conjunto de registros e atribua a uma variável $rs:

	PS C:>$rs = New-AzureDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

O conjunto de registros tem o nome relativo “www” na zona DNS "contoso.com", então, o nome totalmente qualificado dos registros será “www.contoso.com”. O tipo de registro é “A” e o TTL é 60 segundos.

>[AZURE.NOTE]Para criar um conjunto de registros em vértices da zona (nesse caso, "contoso.com"), use o nome do Registro "@", incluindo as aspas. Isso é uma convenção comum do DNS.

O conjunto de registros está vazio e precisamos adicionar registros para poder usar o conjunto de registros "www" recém-criado.<BR>

### Etapa 2

Adicione registros do IPv4 ao conjunto de registros "www" usando a variável $rs atribuída ao criar o conjunto de registros na etapa 1:

	PS C:> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
	PS C:> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

Adicionar registros a um conjunto de registros usando Add-AzureDnsRecordConfig é uma operação offline. Somente a variável local $rs é atualizada.

### Etapa 3
Confirme as alterações no conjunto de registros. Use Set-AzureDnsRecordSet para carregar as alterações no conjunto de registros para o DNS do Azure:


	Set-AzureDnsRecordSet -RecordSet $rs

As alterações foram concluídas. Você pode recuperar o conjunto de registros do DNS do Azure usando Get-AzureDnsRecordSet:


	PS C:> Get-AzureDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : MyAzureResourceGroup
	Ttl               : 3600
	Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
	RecordType        : A
	Records           : {134.170.185.46, 134.170.188.221}
	Tags              : {}



Você também pode usar nslookup ou outras ferramentas DNS para consultar o novo conjunto de registros.

>[AZURE.NOTE]Assim como acontece ao criar a zona, se você ainda não tiver delegado o domínio para os servidores de nome do DNS do Azure, você precisará especificar o endereço do servidor de nomes da zona explicitamente.


	C:> nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221



## Próximas etapas
[Como gerenciar as zonas DNS](dns-operations-dnszones.md)

[Como gerenciar os registros DNS](dns-operations-recordsets.md)<BR>

[Automatizar operações do Azure com o SDK do .NET](dns-sdk.md)
 

<!---HONumber=July15_HO4-->