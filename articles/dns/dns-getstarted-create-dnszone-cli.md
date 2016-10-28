<properties
   pageTitle="Criar uma zona DNS usando a CLI| Microsoft Azure"
   description="Saiba como criar zonas DNS para o DNS do Azure passo a passo para iniciar a hospedagem do seu domínio DNS usando a CLI"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# Criar uma zona do Azure DNS usando a CLI


> [AZURE.SELECTOR]
- [Portal do Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [CLI do Azure](dns-getstarted-create-dnszone-cli.md)


Este artigo explicará as etapas para criar uma zona DNS usando a CLI. Você também pode criar uma zona DNS usando o PowerShell ou o portal do Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## Antes de começar

Essas instruções usam a CLI do Microsoft Azure. Certifique-se de atualizar para a versão mais recente da CLI (0.9.8 ou posterior) do Azure para usar os comandos do DNS do Azure. Digite `azure -v`para verificar qual versão da CLI do Azure está instalada atualmente em seu computador.

## Etapa 1: configurar a CLI do Azure

### 1\. Instalar a CLI do Azure.

Você pode instalar a CLI do Azure para Windows, Linux ou Mac. As etapas a seguir devem ser concluídas antes de poder gerenciar o DNS do Azure usando a CLI do Azure. Mais informações estão disponíveis em [Instalar a CLI do Azure](../xplat-cli-install.md). Os comandos DNS exigem a CLI do Azure versão 0.9.8 ou posterior.

Todos os comandos do provedor de rede na CLI podem ser encontrados usando o seguinte comando:

	azure network

### 2\. Mudar para o modo CLI

O DNS do Azure usa o Azure Resource Manager. Mude para o modo CLI para usar os comandos ARM.

	azure config mode arm

### 3\. Entre na sua conta do Azure

Você deverá se autenticar com suas credenciais. Lembre-se de que você só pode usar contas ORGID.

    azure login -u "username"

### 4\. Selecionar a assinatura

Escolha quais das suas assinaturas do Azure deseja usar.

    azure account set "subscription name"

### 5\. Criar um grupos de recursos

O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. No entanto, como todos os recursos de DNS são globais, não regionais, a escolha do local do grupo de recursos não afeta o DNS do Azure.

Você pode ignorar esta etapa se está usando um grupo de recursos existente.

    azure group create -n myresourcegroup --location "West US"


### 6\. Registrar

O serviço de DNS do Azure é gerenciado pelo provedor de recursos Microsoft.Network. Sua assinatura do Azure precisa ser registrada para usar esse provedor de recursos antes de poder usar o DNS do Azure. Essa operação deve ser executa apenas uma vez para cada assinatura.

	azure provider register --namespace Microsoft.Network


## Etapa 2: criar uma zona DNS

Uma zona DNS é criada usando o comando `azure network dns zone create`. Opcionalmente, você pode criar uma zona DNS com marcas. As marcas consistem em uma lista de pares de nome/valor e são usadas pelo Azure Resource Manager na rotulagem de recursos para fins de cobrança ou agrupamento. Para obter mais informações sobre marcas, consulte [Usando marcas para organizar os recursos do Azure](../resource-group-using-tags.md).

No Azure DNS, os nomes de zona devem ser especificados sem terminar em **'.'**. Por exemplo, como "**contoso.com**", em vez de "**contoso.com.**".


### Para criar uma zona DNS

O exemplo a seguir cria uma zona DNS chamada *contoso.com* no grupo de recursos chamado *MyResourceGroup*.

Use o exemplo para criar sua zona DNS, substituindo os valores pelos seus próprios.

    azure network dns zone create myresourcegroup contoso.com

### Para criar uma zona DNS e marcas.

A CLI do Azure DNS dá suporte a marcas de zonas DNS especificadas usando o parâmetro opcional *-Tag*. O exemplo a seguir mostra como criar uma zona DNS com duas marcas, project = demo e env = test.

Use o exemplo a seguir para criar uma zona DNS e marcas, substituindo os valores pelos seus próprios.

	azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## Exibir registros

Criar uma zona DNS também cria os seguintes registros DNS:

- O registro SOA (“Start of Authority”, Início de Autoridade). Ele está presente na raiz de cada zona DNS.

- Os registros NS (name server, servidor de nomes) autoritativos. Eles mostram quais servidores de nome estão hospedando a zona. O DNS do Azure usa um pool de servidores de nomes; dessa forma servidores de nomes diferentes podem ser atribuídos a diferentes zonas no DNS do Azure. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter mais informações.

Para exibir esses registros, use `azure network dns-record-set show`.<BR> *Uso: network dns record-set show <grupo-de-recursos> <nome-zona-dns> <nome> <tipo>*


No exemplo abaixo, se você executar o comando com o grupo de recursos *myresourcegroup*, registre o nome do conjunto como *"@"* (para um registro raiz) e digite *SOA*. Isso produzirá a seguinte saída:


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
<BR> Para exibir os registros de NS criados com a zona, use o seguinte comando:

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

>[AZURE.NOTE] Conjuntos de registos na raiz (ou *apex*) de uma zona DNS usam **@** como o nome do conjunto de registros.

## Teste

Você pode testar a zona do DNS usando ferramentas do DNS como nslookup, DIG ou o cmdlet do PowerShell `Resolve-DnsName`.

Se você ainda não delegou seu domínio para usar a nova zona no DNS do Azure, você precisa direcionar a consulta DNS diretamente para um dos servidores de nome para a zona. Os servidores de nomes da zona são fornecidos nos registros de NS, conforme listado em "azure network dns-record-set show" acima. Certifique-se de substituir os valores corretos para sua zona no comando a seguir.

O exemplo a seguir usa DIG para consultar o domínio contoso.com usando os servidores de nome atribuídos à zona DNS. A consulta deve apontar para um nome de servidor para o qual usamos *@<servidor de nomes para a zona>* e o nome da zona usando DIG.

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

Após criar uma zona DNS, crie [conjuntos de registros e registros](dns-getstarted-create-recordset-cli.md) para iniciar a resolução de nomes do seu domínio da Internet.

<!---HONumber=AcomDC_1005_2016-->