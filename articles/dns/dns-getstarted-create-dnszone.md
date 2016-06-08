<properties
   pageTitle="Introdução ao DNS do Azure | Microsoft Azure"
   description="Saiba como criar zonas DNS para o DNS do Azure. Esse é um passo a passo para criar sua primeira zona DNS para iniciar a hospedagem do seu domínio DNS usando o PowerShell."
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
   ms.date="05/09/2016"
   ms.author="cherylmc"/>

# Você também pode criar uma zona DNS usando o PowerShell.

> [AZURE.SELECTOR]
- [Portal do Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [CLI do Azure](dns-getstarted-create-dnszone-cli.md)

Este artigo explicará as etapas para criar uma zona DNS usando a CLI. Você também pode criar uma zona DNS usando o PowerShell ou o portal do Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>Sobre Etags e marcas

### <a name="etags"></a>ETags

Suponha que duas pessoas ou dois processos tentem modificar um registro DNS ao mesmo tempo. Qual vence? E o vencedor sabe que ele acabou de substituir as alterações criadas por outra pessoa?

O DNS do Azure usa as Etags para tratar alterações simultâneas para o mesmo recurso com segurança. Cada recurso DNS (zona ou conjunto de registros) tem uma Etag associada a ele. Sempre que um recurso é recuperado, a Etag também é recuperada. Ao atualizar um recurso, você tem a opção de devolver a Etag para que o DNS do Azure possa verificar se a Etag no servidor é correspondente. Uma vez que cada atualização a um recurso resulta em uma Etag sendo gerada novamente, uma incompatibilidade de Etag indica que ocorreu uma alteração simultânea. As Etags também são usadas ao criar um novo recurso para garantir que o recurso ainda não existe.

Por padrão, o PowerShell do DNS do Azure usa as Etags bloquear alterações simultâneas às zonas e conjuntos de registro. A opção *-Substituir* pode ser usada para suprimir as verificações de Etag. Nesse caso, as alterações simultâneas que ocorrerem serão substituídas.

No nível da API REST do DNS do Azure, as Etags são especificadas usando cabeçalhos HTTP. Seu comportamento é descrito na tabela a seguir:

|Cabeçalho|Comportamento|
|------|--------|
|Nenhum|PUT sempre terá êxito (nenhuma verificação de Etag)|
|If-match <etag>|PUT só terá êxito se o recurso existir e a Etag corresponder|
|If-match * | PUT só terá êxito se houver recursos|
|If-none-match * |	PUT só terá êxito se não houver recursos|

### <a name="tags"></a>Marcas

Marcas são diferentes das Etags. As marcas consistem em uma lista de pares de nome/valor e são usadas pelo Azure Resource Manager na rotulagem de recursos para fins de cobrança ou agrupamento. Para obter mais informações sobre marcas, consulte [Usando marcas para organizar os recursos do Azure](../resource-group-using-tags.md).

O PowerShell do DNS do Azure dá suporte a marcas em zonas e conjuntos de registros especificados usando o parâmetro opcional `-Tag`.


## Antes de começar

Antes de começar a configurar, verifique se você tem os itens a seguir.
	
- Uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
	
- Você precisará instalar a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager (1.0 ou posterior). Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell.

## Etapa 1: Entrar

Abra o console do PowerShell e conecte-se à sua conta. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

Use o exemplo a seguir para ajudar com a conexão:

	Login-AzureRmAccount

Verificar as assinaturas da conta.

	Get-AzureRmSubscription 

Especifique a assinatura que você quer usar.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## Etapa 2: Criar um grupo de recursos

O Azure Resource Manager requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. No entanto, como todos os recursos de DNS são globais, não regionais, a escolha do local do grupo de recursos não afeta o DNS do Azure.

Você pode ignorar esta etapa se está usando um grupo de recursos existente.

	New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## Etapa 3 - Registrar

O serviço de DNS do Azure é gerenciado pelo provedor de recursos Microsoft.Network. Sua assinatura do Azure precisa ser registrada para usar esse provedor de recursos antes de poder usar o DNS do Azure. Essa operação deve ser executa apenas uma vez para cada assinatura.

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## Etapa 4: Criar uma zona DNS

Uma zona DNS é criada usando o cmdlet `New-AzureRmDnsZone`. Há exemplos abaixo para criar uma zona DNS com ou sem marcas. Para obter mais informações sobre marcas, consulte a seção sobre [marcas](#tags) neste artigo.

>[AZURE.NOTE] No Azure DNS, os nomes de zona devem ser especificados sem terminar em **'.'**. Por exemplo, como "**contoso.com**", em vez de "**contoso.com.**".

### Para criar uma zona DNS

O exemplo a seguir cria uma zona DNS chamada *contoso.com* no grupo de recursos chamado *MyResourceGroup*. Use o exemplo para criar uma zona DNS, substituindo os valores pelos seus próprios.

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### Para criar uma zona DNS com marcas

O exemplo a seguir mostra como criar uma zona DNS com duas marcas, *project = demo* e *env = test*. Use o exemplo para criar uma zona DNS, substituindo os valores pelos seus próprios.

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## Exibir registros

Criar uma zona DNS também cria os seguintes registros DNS:

- O registro *SOA “Start of Authority”, Início de Autoridade*. Ele está presente na raiz de cada zona DNS.

- Os registros NS (name server, servidor de nomes) autoritativos. Eles mostram quais servidores de nome estão hospedando a zona. DNS do Azure usa um pool de servidores de nomes; dessa forma servidores de nomes diferentes podem ser atribuídos a diferentes zonas no DNS do Azure. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter mais informações.

Para exibir esses registros, use `Get-AzureRmDnsRecordSet`:

	Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
	RecordType        : SOA
	Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
	Tags              : {}

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
	RecordType        : NS
	Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
	Tags              : {}


Conjuntos de registos na raiz (ou *apex*) de uma zona DNS usam **@** como o nome do conjunto de registros.


## Teste

Você pode testar a zona do DNS usando ferramentas do DNS como nslookup, dig ou o [cmdlet do PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Se você ainda não delegou seu domínio para usar a nova zona no DNS do Azure, você precisará direcionar a consulta DNS diretamente para um dos servidores de nome para a zona. Os servidores de nomes da zona são fornecidos nos registros de NS, conforme listado em `Get-AzureRmDnsRecordSet` acima. Certifique-se de substituir os valores corretos para sua zona para o comando a seguir.

	nslookup
	> set type=SOA
	> server ns1-01.azure-dns.com
	> contoso.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	contoso.com
        	primary name server = ns1-01.azure-dns.com
        	responsible mail addr = msnhst.microsoft.com
        	serial  = 1
        	refresh = 900 (15 mins)
        	retry   = 300 (5 mins)
        	expire  = 604800 (7 days)
        	default TTL = 300 (5 mins)


## Próximas etapas

Após criar uma zona DNS, crie [conjuntos de registros e registros](dns-getstarted-create-recordset.md) para iniciar a resolução de nomes do seu domínio da Internet.

<!---HONumber=AcomDC_0525_2016-->