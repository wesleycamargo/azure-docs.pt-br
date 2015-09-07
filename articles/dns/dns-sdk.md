<properties 
   pageTitle="Automatizar operações do DNS e dos conjuntos de registros usando o SDK do .net | Microsoft Azure"
	description="Usando o SDK do .NET para automatizar todas as operações de DNS para o DNS do Azure."
	services="dns"
	documentationCenter="na"
	authors="joaoma"
	manager="adinah"
	editor=""/>

<tags
   ms.service="dns"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/20/2015"
	ms.author="joaoma"/>
# Criar zonas DNS e conjuntos de registros usando o SDK do .NET
Você pode automatizar operações para criar, excluir ou atualizar zonas DNS, conjuntos de registros e registros usando o SDK do DNS com a biblioteca de gerenciamento do DNS .NET. Um projeto completo do Visual Studio está disponível [aqui.](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

## Pacotes NuGet e declarações do namespace
Para usar o cliente DNS, é necessário instalar o pacote NuGet "Biblioteca de gerenciamento do DNS do Azure" e adicionar os namespaces de gerenciamento do DNS ao seu projeto. Vá para o Visual Studio, abra um projeto ou um novo projeto e acesse ferramentas, console do gerenciador de pacote Nuget. Baixe a biblioteca de gerenciamento do DNS do Azure:

	using Microsoft.Azure;
	using Microsoft.Azure.Management.Dns;
	using Microsoft.Azure.Management.Dns.Models;

## Inicializando o cliente de gerenciamento do DNS

DnsManagementClient contém os métodos e propriedades necessárias para gerenciar conjuntos de registros e zonas DNS. Para que o cliente possa acessar sua assinatura, é necessário configurar as permissões corretas e gerar um token AWT, consulte "Autenticando solicitações do gerenciador de recursos do Azure" para obter mais detalhes.

	// get a token for the AAD application (see linked article for code)
	string jwt = GetAToken();

	// make the TokenCloudCredentials using subscription ID and token
	TokenCloudCredentials tcCreds = new TokenCloudCredentials(subID, jwt);

	// make the DNS management client
	DnsManagementClient dnsClient = new DnsManagementClient(tcCreds);

## Criar ou atualizar uma zona DNS

Para criar uma zona DNS, um objeto de zona é criado e passado para dnsClient.Zones.CreateOrUpdate. Como as zonas DNS não estão vinculadas a uma região específica, o local é definido como "global".<BR>

Criar uma zona DNS:

	// create a DNS zone
	Zone z = new Zone("global");
	z.Properties = new ZoneProperties();
	z.Tags.Add("dept", "shopping");
	z.Tags.Add("env", "production");
	ZoneCreateOrUpdateParameters zoneParams = new ZoneCreateOrUpdateParameters(z);
	ZoneCreateOrUpdateResponse responseCreateZone = 
	dnsClient.Zones.CreateOrUpdate("myresgroup", "myzone.com", zoneParams);


O DNS do Azure dá suporte a simultaneidade otimista chamada [Etags](dns-getstarted-create-dnszone.md#Etags-and-tags). A Etag é uma propriedade da zona e IfNoneMatch é uma propriedade em ZoneCreateOrUpdateParameters.

## Criar ou atualizar registros DNS
Os registros DNS são gerenciados como um conjunto de registros. Um conjunto de registros é o conjunto de registros com o mesmo nome e tipo de registro dentro de uma zona. Para criar ou atualizar um conjunto de registros, um objeto RecordSet é criado e passado para dnsClient.RecordSets.CreateOrUpdate. Observe que o nome do conjunto de registros é relativo no nome da zona em vez de ser o nome DNS totalmente qualificado. Novamente, o local é definido como "global".
    
fazer alguns conjuntos de registros

	// make some records sets
	RecordSet rsWwwA = new RecordSet("global");
	rsWwwA.Properties = new RecordProperties(3600);
	rsWwwA.Properties.ARecords = new List<ARecord>();
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.4"));
	rsWwwA.Properties.ARecords.Add(new ARecord("1.2.3.5"));
	RecordCreateOrUpdateParameters recordParams = 
								new RecordCreateOrUpdateParameters(rsWwwA);
	RecordCreateOrUpdateResponse responseCreateA = 
								dnsClient.RecordSets.CreateOrUpdate("myresgroup", 
	"myzone.com", "www", RecordType.A, recordParams);
	
    
O DNS do Azure dá suporte a [Etags](dns-getstarted-create-dnszone.md#Etags-and-tags) de simultaneidade otimista. A Etag é que uma propriedade de RecordSet e IfNoneMatch é uma propriedade em RecordSetCreateOrUpdateParameters.

## Obtendo zonas e RecordSets
As coleções de zonas e RecordSets fornecem a capacidade de obter zonas e conjuntos de registros respectivamente. RecordSets são identificados por seu tipo, nome e a zona (e grupo de recursos) nos quais existem. As zonas são identificadas por seu nome e o grupo de recursos em que existem.

	ZoneGetResponse getZoneResponse = 
	dnsClient.Zones.Get("myresgroup", "myzone.com");
	RecordGetResponse getRSResp = 
	dnsClient.RecordSets.Get("myresgroup", 
	"myzone.com", "www", RecordType.A);

##Listando zonas e RecordSets

Para listar as zonas, use o método List na coleção Zones. Para listar os conjuntos de registros, use os métodos List ou ListAll na coleção RecordSets. O método List difere do método ListAll pois ele retorna apenas os conjuntos de registro do tipo especificado.

O exemplo a seguir mostra como obter uma lista de zonas DNS e conjuntos de registro:


	ZoneListResponse zoneListResponse = dnsClient.Zones.List("myresgroup", new ZoneListParameters());
	foreach (Zone zone in zoneListResponse.Zones)
	{
    	RecordListResponse recordSets = 
                 			dnsClient.RecordSets.ListAll("myresgroup", "myzone.com", new RecordSetListParameters());

    // do something like write out each record set
	}
## Próximas etapas

[O que é o Gerenciador de Tráfego?](traffic-manager-overview.md)

[O que é o DNS do Azure?](dns-overview.md)

[Projeto de exemplo do Visual Studio SDK](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

<!---HONumber=August15_HO9-->