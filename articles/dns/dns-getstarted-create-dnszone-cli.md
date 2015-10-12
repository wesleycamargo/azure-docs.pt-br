<properties
   pageTitle="Introdução ao DNS do Azure usando a CLI | Microsoft Azure"
   description="Saiba como criar zonas DNS para o DNS do Azure passo a passo para iniciar a hospedagem do seu domínio DNS usando a CLI"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2015"
   ms.author="joaoma"/>

# Introdução ao DNS do Azure usando a CLI



> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)
- [Azure Powershell steps](dns-getstarted-create-dnszone.md)


O domínio "contoso.com" pode conter uma série de registros DNS, como “mail.contoso.com” (para um servidor de email) e “www.contoso.com” (para um site da Web). Uma zona DNS é usada para hospedar os registros DNS para um domínio específico.

Para iniciar a hospedagem de seu domínio, você primeiro precisa criar uma zona DNS. Qualquer registro DNS criado para um determinado domínio estará dentro de uma zona DNS do domínio.

Essas instruções usam a CLI do Microsoft Azure. Certifique-se de atualizar para a versão mais recente da CLI do Azure para usar os comandos do DNS do Azure.

## Configurar a CLI do Azure

### Etapa 1

Instalar a CLI do Azure. Você pode instalar a CLI do Azure para Windows, Linux ou Mac. As etapas a seguir devem ser concluídas antes de poder gerenciar o DNS do Azure usando a CLI do Azure. Mais informações estão disponíveis em [Instalar a CLI do Azure](../xplat-cli-install.md). Todos os comandos do provedor de rede na CLI podem ser encontrados usando o seguinte comando:

	Azure network


>[AZURE.IMPORTANT]Os comandos DNS exigem a CLI do Azure versão 0.9.8 ou superior. Digite `azure -v`para verificar qual versão da CLI do Azure está instalada atualmente em seu computador.
 
### Etapa 2

O DNS do Azure usa o Gerenciador de Recursos do Azure. Configure a alternar CLI para usar comandos ARM e DNS.

	Azure config mode arm

### Etapa 3

Entre na sua conta do Azure.

    Azure login -u "username"

Você deverá se autenticar com suas credenciais. Lembre-se de que você só pode usar contas ORGID.

### Etapa 4
Escolha quais das suas assinaturas do Azure deseja usar.

    Azure account set "subscription name"

Para ver uma lista das assinaturas disponíveis, use o comando “azure account list”.

### Etapa 5

Crie um grupo de recursos (pule esta etapa se você estiver usando um grupo de recursos existente)

    Azure group create -n myresourcegroup --location "West US"

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. No entanto, como todos os recursos de DNS são globais, não regionais, a escolha do local do grupo de recursos não afeta o DNS do Azure.

### Etapa 6

O serviço de DNS do Azure é gerenciado pelo provedor de recursos Microsoft.Network. Sua assinatura do Azure precisa ser registrada para usar esse provedor de recursos antes de poder usar o DNS do Azure. Essa operação deve ser executa apenas uma vez para cada assinatura.

	Azure provider register --namespace Microsoft.Network

## Marcas

Marcas são diferentes das Etags. As marcas são uma lista de pares nome-valor e são usadas pelo Gerenciador de Recursos do Azure para fins de cobrança ou agrupamentos. Para obter mais informações sobre marcas, consulte [Usando marcas para organizar os recursos do Azure](resource-group-using-tags.md). A CLI do DNS do Azure dá suporte a marcas em zonas e conjuntos de registros especificados usando o parâmetro opcional “-Tag”. O exemplo a seguir mostra como criar uma zona DNS com duas marcas, “project = demo” e “env = test”:

	Azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

## Criar uma zona DNS

Uma zona DNS é criada usando o comando `azure network dns zone create`. No exemplo a seguir, é possível criar uma zona DNS chamada "contoso.com" no grupo de recursos chamado 'MyResourceGroup':

    Azure network dns zone create -n contoso.com -g myresourcegroup


>[AZURE.NOTE]No DNS do Azure, os nomes de zona devem ser especificados sem um encerramento '.', por exemplo, como "contoso.com", em vez de "contoso.com".


A zona DNS foi criada no DNS do Azure. Criar uma zona DNS também cria os seguintes registros DNS:

- O registro SOA (“Start of Authority”, Início de Autoridade). Ele está presente na raiz de cada zona DNS.
- Os registros NS (name server, servidor de nomes) autoritativos. Eles mostram quais servidores de nome estão hospedando a zona. O DNS do Azure usa um pool de servidores de nomes; dessa forma servidores de nomes diferentes podem ser atribuídos a diferentes zonas no DNS do Azure. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter mais informações.

Para exibir esses registros, use "azure network dns-record-set show":

	Usage: network dns record-set show <resource-group> <dns-zone-name> <name> <type>


No exemplo abaixo, executando o comando com o grupo de recursos "myresourcegroup", registre de nome do conjunto como "@" (para um registro raiz) e o tipo "SOA" produzirá a saída a seguir:
 

	azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/SOA
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    SOA record:
	data:      Email                         : msnhst.microsoft.com
	data:      Expire time                   : 604800
	data:      Host                          : edge1.azuredns-cloud.net
	data:      Minimum TTL                   : 300
	data:      Refresh time                  : 900
	data:      Retry time                    : 300
	data:                                    :
<BR> Para exibir os registros de NS criados, use o seguinte comando:

	azure network dns record-set show myresourcegroup "contoso.com" "@" NS
	info:    Executing command network dns-record-set show
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-05.azure-dns.com
	data:        Name server domain name     : ns2-05.azure-dns.net
	data:        Name server domain name     : ns3-05.azure-dns.org
	data:        Name server domain name     : ns4-05.azure-dns.info
	data:
	info:    network dns-record-set show command OK

>[AZURE.NOTE]Conjuntos de registos na raiz (ou “apex”) de uma zona DNS usam "@" como o nome do conjunto de registros.

Depois de criar sua primeira zona DNS, você pode testá-la usando ferramentas DNS como nslookup, DIG ou o cmdlet **Resolve-DnsName** PowerShell. Se você ainda não delegou seu domínio para usar a nova zona no DNS do Azure, você precisa direcionar a consulta DNS diretamente para um dos servidores de nome para a zona. Os servidores de nomes da zona são fornecidos nos registros de NS, conforme listado em "azure network dns-record-set show" acima. Certifique-se de substituir os valores corretos para sua zona no comando a seguir.

O exemplo a seguir usa DIG para consultar o domínio contoso.com usando os servidores de nome atribuídos à zona DNS. A consulta deve apontar para um nome de servidor para o qual usamos `@<name server for the zone>` e o nome da zona usando DIG.

	 <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
	(1 server found)
	global options: +cmd
 	Got answer:
	->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
 	flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
 	WARNING: recursion requested but not available

 	OPT PSEUDOSECTION:
 	EDNS: version: 0, flags:; udp: 4000
  	QUESTION SECTION:
	contoso.com.                        IN      A

 	AUTHORITY SECTION:
	contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
	msnhst.microsoft.com. 6 900 300 604800 300

	Query time: 93 msec
	SERVER: 208.76.47.5#53(208.76.47.5)
	WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
	MSG SIZE  rcvd: 120

## Próximas etapas


[Começar a criar conjuntos de registro e registros](dns-getstarted-create-recordset-cli.md)<BR> [Como gerenciar as zonas DNS](dns-operations-dnszones-cli.md)<BR> [Como gerenciar registros DNS](dns-operations-recordsets-cli.md)<BR> [Automatizar operações do Azure com o SDK do .NET](dns-sdk.md)<BR> [Referência da API REST do DNS do Azure](https://msdn.microsoft.com/library/azure/mt163862.aspx)

<!---HONumber=Oct15_HO1-->