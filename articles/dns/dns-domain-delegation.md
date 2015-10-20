<properties
   pageTitle="Delegar seu domínio ao Azure DNS | Microsoft Azure"
   description="Entenda como alterar a delegação de domínio e usar servidores de nomes do Azure DNS para fornecer hospedagem do domínio."
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
   ms.date="09/22/2015"
   ms.author="joaoma"/>


# Delegar domínio ao Azure DNS

O Azure DNS é um serviço de hospedagem para domínios DNS. Para que consultas DNS para um domínio acessem o Azure DNS, o domínio deve ser delegado ao Azure DNS por meio do domínio-pai. Esta página explica como funciona a delegação de domínio e como delegar domínios ao DNS do Azure.


## Como funciona a delegação de DNS

### Zonas e domínios

Um domínio é um nome exclusivo no sistema de nome de domínio, por exemplo "contoso.com". Um registrador de domínio é uma empresa que pode fornecer nomes de domínio da Internet. Eles verificarão se o domínio de Internet que você deseja usar está disponível e permitirão que você o compre. Uma vez registrado o nome de domínio, você será seu o proprietário legal. Se você já tiver um domínio da Internet, você usará o registrador de domínio atual para delegar para o Azure DNS.

>[AZURE.NOTE]Para obter mais informações sobre quem possui um nome de domínio específico ou sobre como comprar um domínio, consulte [Gerenciamento de domínio de Internet no Azure AD](https://msdn.microsoft.com/library/azure/hh969248.aspx).

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Por exemplo, o domínio "contoso.com" pode conter uma série de registros DNS, como “mail.contoso.com” (para um servidor de email) e “www.contoso.com” (para um site da Web).

O Azure DNS permite hospedar uma zona DNS e, portanto, gerenciar os registros DNS para um domínio no Azure. Tenha em mente que o Azure DNS não é um registrador de domínio.

O Sistema de nomes de domínio é uma hierarquia de domínios. A hierarquia começa no domínio “raiz”, cujo nome é simplesmente “.”. Abaixo dele vêm domínios de nível superior, como “com”, “net”, “org”, “uk” ou “jp”. Abaixo desses estão domínios de segundo nível, como “org.uk” ou “co.jp”. E assim por diante.

Os domínios na hierarquia de DNS são hospedados usando zonas DNS separadas. Essas zonas são distribuídas globalmente, hospedadas por servidores de nomes DNS pelo mundo.

### Resolução e delegação

Existem dois tipos de servidores DNS:

- Um servidor DNS _autoritativo_ hospeda zonas DNS. Ele responde a consultas DNS apenas para registros nessas zonas.
- Um servidor DNS _recursivo_ não hospeda zonas DNS. Ele responde a todas as consultas DNS, chamando os servidores DNS autoritativos para coletar os dados de que precisa.

Os clientes DNS em PCs ou dispositivos móveis normalmente chamam um servidor DNS recursivo para executar consultas DNS das quais os aplicativos cliente precisam.

Quando um servidor DNS recursivo recebe uma consulta para um registro DNS como “www.contoso.com”, ele precisa localizar o servidor de nomes que hospeda a zona para o domínio “contoso.com”. Para fazer isso, ele começa em servidores-raiz de nomes e daí localiza os servidores de nomes que hospeda a zona “com”. Em seguida, ele consulta os servidores de nomes “com” para localizar os servidores de nomes que hospedam a zona “contoso.com”. Finalmente, é possível consultar esses servidores de nomes para “www.contoso.com”.

Isso é chamado de resolução de nome DNS (estritamente, resolução de DNS inclui etapas adicionais, como as CNAMEs a seguir, mas isso não é importante para compreender como funciona a delegação de DNS.)

Como uma zona-pai “aponta” os servidores de nomes para uma zona-filho? Ele faz isso usando um tipo especial de registro DNS, chamado de registro NS (NS significa name server, “servidor de nomes”). Por exemplo, a zona raiz contém registros NS para “com”, mostrando os servidores de nomes da zona “com”. A zona “com”, por sua vez, contém registros NS para “contoso.com”, mostrando os servidores de nomes da zona “contoso.com”. Configurar os registros NS para uma zona-filho em uma zona-pai é chamado de delegar o domínio.


![Servidor de nomes DNS](./media/dns-domain-delegation/image1.png)

Cada delegação realmente tem duas cópias dos registros NS — uma na zona-pai apontando para a zona-filho e outra na própria zona-filho. A zona “contoso.com” contém os registros NS para contoso.com (juntamente com os registros NS em “com”). Estes são chamados de registros NS autoritativos e ficam no ápice da zona-filho.


## Delegando um domínio ao Azure DNS

Depois de criar sua zona DNS no Azure DNS, você precisa configurar os registros NS na zona-pai para tornar o Azure DNS a origem autoritativa para resolução de nomes em sua zona. Para domínios comprados de um registrador, seu registrador oferecerá a opção de configurar esses registros NS.

>[AZURE.NOTE]Você não precisa ter um domínio para criar uma zona DNS com esse nome de domínio no Azure DNS. No entanto, você precisa ter o domínio para poder configurar a delegação para o Azure DNS com o registrador.

Por exemplo, suponha que você compre o domínio “contoso.com” e crie uma zona com o nome de contoso.com no Azure DNS. Como o proprietário do domínio, seu registrador oferecerá a opção de configurar os endereços de servidor de nomes (ou seja, registros NS) para seu domínio. O registrador armazenará esses registros NS no domínio pai, neste caso, “.com”. Os clientes em todo o mundo, em seguida, serão direcionados ao seu domínio na zona DNS do Azure ao tentar resolver os registros DNS em “contoso.com”.

Para configurar a delegação, você precisa saber os nomes de servidor de nomes para a zona. O Azure DNS aloca os servidores de nomes de um pool de cada vez que uma zona é criada e armazena-os nos registros NS autoritativos que são criados automaticamente dentro de sua região. Portanto, para ver os nomes de servidor de nomes, basta obter esses registros.

Usando o Azure PowerShell, os registros NS autoritativos podem ser obtidos conforme descrito a seguir (o nome do Registro “@” é usado para fazer referência a registros no ápice da zona).

	PS C:\> $zone = Get-AzureDnsZone –Name contoso.com –ResourceGroupName MyAzureResourceGroup
	PS C:\> Get-AzureDnsRecordSet –Name “@” –RecordType NS –Zone $zone

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
	RecordType        : NS
	Records           : {ns1-04.azure-dns.com, ns2-04.azure-dns.net, ns3-04.azure-dns.org,
                     ns4-04.azure-dns.info}
	Tags              : {}

Neste exemplo, servidores de nomes “ns1-04.azure-dns.com”, “ns2-04.azure-dns .net”, “ns3-04.azure-dns.org” e “ns4-04.azure-dns.info” foram atribuídos à zona “contoso.com”.

Cada registrador tem suas próprias ferramentas de gerenciamento de DNS para alterar os registros de servidor de nomes para um domínio. Na página de gerenciamento do DNS do registrador, edite os registros NS e substitua-os por aqueles criados pelo Azure DNS.

Após concluir a delegação, você pode verificar se a resolução de nomes está funcionando, usando uma ferramenta como “nslookup” para consultar o registro SOA da zona (que também é criado automaticamente quando a zona é criada).

Observe que você não precisa especificar os servidores de nomes DNS do Azure, pois o processo normal de resolução DNS localizará os servidores de nomes automaticamente se a delegação tiver sido configurada corretamente.

	PS C:\> nslookup –type=SOA contoso.com

	Server: ns1-04.azure-dns.com
	Address: 208.76.47.4

	contoso.com
	primary name server = ns1-04.azure-dns.com
	responsible mail addr = msnhst.microsoft.com
	serial = 1
	refresh = 900 (15 mins)
	retry = 300 (5 mins)
	expire = 604800 (7 days)
	default TTL = 300 (5 mins)

## Delegar subdomínios no DNS do Azure

Tendo configurado e delegado o ‘contoso.com’ no DNS do Azure, suponha que você deseja configurar uma zona filho separada, a 'partners.contoso.com'. Isso segue um processo semelhante a uma delegação normal:

1. Crie a zona filho 'partners.contoso.com' no DNS do Azure.
2. Pesquise os registros NS de autoridade na zona filho para obter os servidores de nomes que hospedam a zona filho no DNS do Azure.
3. Delegue a zona filho configurando registros NS na zona pai que aponta para a zona filho.

A única diferença é que, na etapa 3, os registros NS devem ser criados na zona pai ‘contoso.com’ no DNS do Azure, em vez de serem configurados por meio de um registrador de domínio.

O exemplo do PowerShell a seguir demonstra isso. Primeiro, criamos as zonas pai e filho — elas podem estar no mesmo grupo de recursos ou em grupos de recursos diferentes.

	PS C:\> $parent = New-AzureDnsZone -Name contoso.com -ResourceGroupName RG1
	PS C:\> $child = New-AzureDnsZone -Name partners.contoso.com -ResourceGroupName RG1

Em seguida, recuperamos os registros NS autoritativos da zona filho conforme mostrado no exemplo a seguir.

	PS C:\> $child_ns_recordset = Get-AzureDnsRecordSet -Zone $child -Name "@" -RecordType NS

Finalmente, criamos um conjunto de registros NS correspondente na zona pai para concluir a delegação (observe que o nome do conjunto de registros na zona pai coincide com o nome da zona filho, neste caso "parceiros"):

	PS C:\> $parent_ns_recordset = New-AzureDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
	PS C:\> $parent_ns_recordset.Records = $child_ns_recordset.Records
	PS C:\> Set-AzureDnsRecordSet -RecordSet $parent_ns_recordset

Da mesma forma que é feito ao delegar usando um registrador, podemos verificar que tudo esteja configurado corretamente examinando o registro SOA da zona filho.

	PS C:\> nslookup –type=SOA partners.contoso.com

	Server: ns1-08.azure-dns.com
	Address: 208.76.47.8

	partners.contoso.com
		primary name server = ns1-08.azure-dns.com
		responsible mail addr = msnhst.microsoft.com
		serial = 1
		refresh = 900 (15 mins)
		retry = 300 (5 mins)
		expire = 604800 (7 days)
		default TTL = 300 (5 mins)

## Próximas etapas

[Gerenciar zonas DNS](../dns-operations-dnszones)

[Gerenciar registros DNS](../dns-operations-recordsets)

[Visão geral do Gerenciador de Tráfego](../traffic-manager-overview)

[Automatizar operações do Azure com o SDK do .NET](../dns-sdk)

[Referência da API REST do Azure DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)

<!---HONumber=Oct15_HO3-->