<properties 
   pageTitle="Gerenciar conjuntos de registros DNS e registros no DNS do Azure usando a CLI | Microsoft Azure" 
   description="Gerenciando conjuntos de registros DNS e registros no DNS do Azure ao hospedar seu domínio no DNS do Azure. Todos os comandos da CLI para operações em conjuntos de registros e registros." 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/10/2015"
   ms.author="joaoma"/>

# Como gerenciar registros DNS usando a CLI

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)

Este guia mostrará como gerenciar conjuntos de registro e registros de zona DNS.

É importante compreender a diferença entre os conjuntos de registros DNS e registros DNS individuais. Um conjunto de registros é a coleção de registros em uma zona com o mesmo nome e o mesmo tipo. Para obter mais detalhes, consulte [Noções básicas sobre conjuntos de registros e registros](dns-getstarted-create-recordset.md#Understanding-record-sets-and-records).

## Criar um conjunto de registros

Os conjuntos de registros são criados usando o comando `azure network dns record-set create`. Você precisa especificar o nome do conjunto de registros, a zona, o TTL (Time-to-Live, tempo de vida) e o tipo de registro.

>[AZURE.NOTE]O nome do conjunto de registros deve ser um nome relativo, exceto o nome da zona. Por exemplo, o nome de conjunto de registros “www” na zona "contoso.com" criará um conjunto de registros com o nome totalmente qualificado “www.contoso.com”.

>Para um registro definido no vértices da zona, use "@" como o nome do conjunto de registro, incluindo as aspas. O nome totalmente qualificado do conjunto de registros é igual ao nome da zona, nesse caso, "contoso.com".

O DNS do Azure dá suporte aos seguintes tipos de registros: A, AAAA, CNAME, MX, NS, SOA, SRV, TXT. Conjuntos de registros do tipo SOA são criados automaticamente com cada zona, eles não podem ser criados separadamente. Observe que [o tipo de registro SPF foi substituído por padrões DNS em favor da criação de registros SPF usando o tipo de registro TXT](http://tools.ietf.org/html/rfc7208#section-3.1).

	azure network dns record-set create myresourcegroup contoso.com  www  A --ttl 300


>[AZURE.IMPORTANT]Conjuntos de registros CNAME não podem coexistir com outros conjuntos de registros com o mesmo nome. Por exemplo, você não pode criar um CNAME com o nome relativo “www” e um registro A com o nome relativo “www” ao mesmo tempo. Uma vez que o apex de zona (nome = “@”) sempre contém os conjuntos de registro NS e SOA criados quando a zona é criada, isso significa que você não pode criar um conjunto de registros CNAME no apex da zona. Essas restrições são provenientes dos padrões DNS, elas não são limitações do DNS do Azure.

### Registros curinga

O DNS do Azure dá suporte a [registros curinga](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Eles são retornados para qualquer consulta com um nome correspondente (a menos que haja uma correspondência mais próxima de um conjunto de registros não curinga).

>[AZURE.NOTE]Para criar um conjunto de registros curinga, use o nome do conjunto de registros "*", ou um nome cujo primeiro rótulo seja "*", por exemplo, "*.foo".

>Conjuntos de registros curinga têm suporte para todos os tipos de registro, exceto NS e SOA.

## Obter um conjunto de registros
Para recuperar um conjunto de registros existente, use `azure network dns record-set show`, especificando o grupo de recursos, o nome da zona, o nome relativo do conjunto de registros e o tipo de registro:

	azure network dns record-set show myresourcegroup contoso.com www A


## Listar os conjuntos de registros

Você pode listar todos os registros em uma Zona DNS com o comando `azure network dns record-set list`:

### Opção 1 
Listar todos os conjuntos de registros. Isso retornará todos os conjuntos de registros, independentemente do nome ou o tipo de registro:

	azure network dns record-set list myresourcegroup contoso.com

### Opção 2 

Listar os conjuntos de registros de um tipo de registro específico. Isso retornará todos os conjuntos de registros correspondentes ao tipo de registro determinado (nesse caso, registros A):


	azure network dns record-set list myresourcegroup contoso.com A 

Em ambos os casos, você especificará o nome do grupo de recursos e o nome da zona.

## Adicionar um registro a um conjunto de registros

Os registros são adicionados aos conjuntos de registros com `azure network dns record-set add-record`.

Os parâmetros para adicionar registros a um conjunto de registros variam dependendo do tipo de conjunto de registros. Por exemplo, ao usar um conjunto de registros do tipo “A”, você só poderá especificar os registros com o parâmetro "-a `<IPv4 address>`".

Os exemplos a seguir mostram como criar um conjunto de registros de cada tipo de registro que contém um único registro.

### Criar um conjunto de registros A com registro único

Para criar um conjunto de registros, use `azure network dns record-set create`, especificando o grupo de recursos, o nome da zona, o nome relativo do conjunto de registros, o tipo de registro e a vida útil (TTL):
	
	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

>[AZURE.NOTE]Se o parâmetro – ttl não estiver definido, o valor padrão é 4 (em segundos).


Depois de criar o conjunto de registros A, adicione endereços IPv4 ao conjunto de registro com `azure network dns record-set add-record`:

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1 

### Criar um conjunto de registros AAAA com um registro único

	azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

### Criar um conjunto de registros CNAME com um registro único

	azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300
	
	azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"

>[AZURE.NOTE]Os registros CNAME permitem apenas um valor de cadeia de caracteres único.

### Criar um conjunto de registros MX com um registro único

Neste exemplo, usamos o nome do conjunto de registros "@" para criar o registro MX no vértice da zona (por exemplo, "contoso.com"). Isso é comum para os registros MX.

	azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

	azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


### Criar um conjunto de registros NS com um registro único

	azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300
	
	azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com" 
	
### Criar um conjunto de registros SRV com um registro único

Se estiver criando um registro SRV na raiz da zona, basta especificar \_serviço e \_protocolo no nome do registro, não é necessário incluir também “.@” no nome do registro

	
	azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300 

	azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com" 

### Criar um conjunto de registros TXT com um registro único

	azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record" 


## Modificando conjuntos de registros existentes


Isso é mostrado pelos exemplos a seguir:

### Atualizar um registro em um conjunto de registros existente

Para este exemplo, adicionaremos outro endereço IP (1.2.3.4) a um conjunto de registros (www) existente:

	azure network dns record-set add-record  myresourcegroup contoso.com  A
	-a 1.2.3.4
	info:    Executing command network dns record-set add-record
	Record set name: www
	+ Looking up the dns zone "contoso.com"
	+ Looking up the DNS record set "www"
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
	data:    Name                            : www	
	data:    Type                            : Microsoft.Network/dnszones/a
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:        IPv4 address                : 1.2.3.4
	data:
	info:    network dns record-set add-record command OK


Você usará `azure network dns record-set delete-record` para remover um valor existente de um conjunto de registros:
 
	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
	info:    Executing command network dns record-set delete-record
	+ Looking up the DNS record set "www"
	Delete DNS record? [y/n] y
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## Remover um registro em um conjunto de registros existente

Os registros podem ser removidos de um conjunto de registros usando `azure network dns record-set delete-record` Lembre-se de que o registro que está sendo removido deve ser uma correspondência exata de um registro existente, em todos os parâmetros.

Remover o último registro de um conjunto de registros não exclui o conjunto de registros. Consulte [Excluir um conjunto de registros](#delete-a-record-set) abaixo para obter mais informações.


	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

	azure network dns record-set delete myresourcegroup contoso.com www A

### Remover o registro AAAA de um conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### Remover o registro CNAME de um conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
	

### Remover o registro MX de um conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### Remover o registro NS de um conjunto de registros
	
	azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### Remover o registro SRV de um conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com" 

### Remover o registro TXT de um conjunto de registros

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## Excluir um conjunto de registros
Os conjuntos de registro podem ser excluídos usando o cmdlet Remove-AzureDnsRecordSet.

>[AZURE.NOTE]Você não pode excluir os conjuntos de registro SOA e NS no apex da zona (nome = “@”) que são criados automaticamente quando a zona é criada. Eles serão excluídos automaticamente ao excluir a zona.

No exemplo abaixo, o conjunto de registro A "test-a" será removido da zona DNS de contoso.com:

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A 

O comutador opcional “-q” pode ser usado para suprimir o prompt de confirmação.


##Consulte também

[Delegar um domínio ao DNS do Azure](dns-domain-delegation.md)<BR> [Gerenciar zonas DNS](dns-operations-dnszones-cli.md)<BR> [Automatizar operações usando o SDK do .NET](dns-sdk.md)
 

<!---HONumber=Nov15_HO4-->