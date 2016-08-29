<properties 
   pageTitle="Criar zonas DNS e conjuntos de registros do DNS do Azure usando o SDK do .NET | Microsoft Azure" 
   description="Como criar zonas DNS e conjuntos de registros para o DNS do Azure usando o SDK do .NET." 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/16/2016"
   ms.author="cherylmc"/>


# Criar zonas DNS e conjuntos de registros usando o SDK do .NET

Você pode automatizar operações para criar, excluir ou atualizar zonas DNS, conjuntos de registros e registros usando o SDK do DNS com a biblioteca de gerenciamento do DNS .NET. Um projeto completo do Visual Studio está disponível [aqui.](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

## Pacotes NuGet e declarações de namespace

Para usar o cliente DNS, você precisará instalar o pacote NuGet **Biblioteca de gerenciamento do DNS do Azure** e adicionar os namespaces de gerenciamento do DNS a seu projeto.
 
1. No **Visual Studio**, abra um projeto ou um novo projeto.

2. Vá para **Ferramentas** **>** **Gerenciador de Pacotes do NuGet** **>** **Console do Gerenciador de Pacotes**.

3. Baixe a Biblioteca de Gerenciamento do DNS do Azure.

		using Microsoft.Azure;
		using Microsoft.Azure.Management.Dns;
		using Microsoft.Azure.Management.Dns.Models;

## Inicializar o cliente de gerenciamento do DNS

*DnsManagementClient* contém os métodos e as propriedades necessárias para gerenciar conjuntos de registros e zonas DNS. Para que o cliente possa acessar sua assinatura, você precisará configurar as permissões corretas e gerar um token AWT. Confira [Autenticação de solicitações de Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790557.aspx) para saber mais.

	// get a token for the AAD application (see the article link above for code)
	string jwt = GetAToken();

	// make the TokenCloudCredentials using subscription ID and token
	TokenCloudCredentials tcCreds = new TokenCloudCredentials(subID, jwt);

	// make the DNS management client
	DnsManagementClient dnsClient = new DnsManagementClient(tcCreds);

## Criar ou atualizar uma zona DNS

Para criar uma zona DNS, um objeto de "Zona" é criado e passado para *dnsClient.Zones.CreateOrUpdate*. Como as zonas DNS não estão vinculadas a uma região específica, o local é definido como "global".<BR> O DNS do Azure dá suporte a [Etags](dns-getstarted-create-dnszone.md) de simultaneidade otimista. "Etag" é uma propriedade da Zona. "IfNoneMatch" é uma propriedade em ZoneCreateOrUpdateParameters.

	// To create a DNS zone
	Zone z = new Zone("global");
	z.Properties = new ZoneProperties();
	z.Tags.Add("dept", "shopping");
	z.Tags.Add("env", "production");
	ZoneCreateOrUpdateParameters zoneParams = new ZoneCreateOrUpdateParameters(z);
	ZoneCreateOrUpdateResponse responseCreateZone = 
	dnsClient.Zones.CreateOrUpdate("myresgroup", "myzone.com", zoneParams);



## Criar ou atualizar registros DNS e conjuntos de registros

Os registros DNS são gerenciados como um conjunto de registros. Um conjunto de registros é um conjunto de registros com o mesmo nome e tipo de registro dentro de uma zona. Para criar ou atualizar um conjunto de registros, um objeto "RecordSet" é criado e passado para *dnsClient.RecordSets.CreateOrUpdate*. Observe que o nome do conjunto de registros é relativo no nome da zona em vez de ser o nome DNS totalmente qualificado. O local é definido como "global".<BR> O DNS do Azure dá suporte a [Etags](dns-getstarted-create-dnszone.md) de simultaneidade otimista. "Etag" é uma propriedade de RecordSet. "IfNoneMatch" é uma propriedade em RecordSetCreateOrUpdateParameters.
    


	// To create record sets
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
	
    
## Obter zonas e conjuntos de registros

As coleções de *Zones* e *RecordSets* fornecem a capacidade de obter zonas e conjuntos de registros, respectivamente. RecordSets são identificados por seu tipo, nome e zona e grupo de recursos nos quais existem. As zonas são identificadas por seu nome e o grupo de recursos em que existem.

	ZoneGetResponse getZoneResponse = 
	dnsClient.Zones.Get("myresgroup", "myzone.com");
	RecordGetResponse getRSResp = 
	dnsClient.RecordSets.Get("myresgroup", 
	"myzone.com", "www", RecordType.A);

## Listar zonas e conjuntos de registros

Para listar zonas, use o método *List* na coleção Zones. Para listar os conjuntos de registros, use os métodos *List* ou *ListAll* na coleção RecordSets. O método *List* difere do método *ListAll*, pois ele retorna apenas os conjuntos de registro de um tipo específico.

O exemplo a seguir mostra como obter uma lista de zonas DNS e conjuntos de registros.


	ZoneListResponse zoneListResponse = dnsClient.Zones.List("myresgroup", new ZoneListParameters());
	foreach (Zone zone in zoneListResponse.Zones)
	{
    	RecordListResponse recordSets = 
                 			dnsClient.RecordSets.ListAll("myresgroup", "myzone.com", new RecordSetListParameters());

    // do something like write out each record set
	}


## Próximas etapas

[Projeto de exemplo do Visual Studio SDK](http://download.microsoft.com/download/2/A/C/2AC64449-1747-49E9-B875-C71827890126/AzureDnsSDKExample_2015_05_05.zip)

<!---HONumber=AcomDC_0817_2016-->