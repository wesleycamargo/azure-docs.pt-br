<properties
   pageTitle="Introdução ao DNS do Azure | Microsoft Azure"
   description="Saiba como criar zonas DNS para o DNS do Azure. Esse é um passo a passo para criar sua primeira zona DNS para iniciar a hospedagem do seu domínio DNS usando o PowerShell."
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="joaoma"/>

# Introdução ao DNS do Azure usando o Powershell


> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)
- [PowerShell](dns-getstarted-create-dnszone.md)

O domínio "contoso.com" pode conter uma série de registros DNS, como “mail.contoso.com” (para um servidor de email) e “www.contoso.com” (para um site da Web). Uma zona DNS é usada para hospedar os registros DNS para um domínio específico.<BR><BR> Para iniciar a hospedagem de seu domínio, precisamos primeiro criar uma zona DNS. Qualquer registro DNS criado para um determinado domínio estará dentro de uma zona DNS do domínio.<BR><BR> Essas instruções usam o Microsoft Azure PowerShell. Certifique-se de atualizar para a versão mais recente do Azure PowerShell para usar os cmdlets do DNS do Azure. As mesmas etapas também podem ser executadas usando a interface de linha de comando do Microsoft Azure, a API REST ou o SDK.<BR><BR>

## Configurar o PowerShell do DNS do Azure

As etapas a seguir devem ser concluídas antes de poder gerenciar o DNS do Azure usando o PowerShell do Azure.

### Etapa 1
O DNS do Azure usa o ARM (Gerenciador de Recursos do Azure). Alterne para o modo PowerShell para usar os cmdlets do ARM. Mais informações estão disponíveis em [Usando o Windows PowerShell com o Gerenciador de Recursos](powershell-azure-resource-manager.md).<BR><BR>

		PS C:\> Switch-AzureMode -Name AzureResourceManager

Se for exibido uma mensagem aviso informando “O cmdlet Switch-AzureMode foi substituído e será removido em uma versão futura”, ele deverá ser ignorado.

### Etapa 2
 Faça logon na sua conta do Azure.<BR><BR>

		PS C:\> Add-AzureAccount

Você deverá se autenticar com suas credenciais.<BR>

### Etapa 3
Escolha quais das suas assinaturas do Azure deseja usar.<BR>


		PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Para ver uma lista das assinaturas disponíveis, use o cmdlet “Get-AzureSubscription”.<BR>

### Etapa 4
Crie um grupo de recursos (pule esta etapa se você estiver usando um grupo de recursos existente)<BR>

		PS C:\> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"


O Gerenciador de Recursos do Azure requer que todos os grupos de recursos especifiquem um local. Ele é usado como o local padrão para os recursos do grupo de recursos em questão. No entanto, como todos os recursos de DNS são globais, não regionais, a escolha do local do grupo de recursos não afeta o DNS do Azure.<BR>

### Etapa 5

O serviço de DNS do Azure é gerenciado pelo provedor de recursos Microsoft.Network. Sua assinatura do Azure precisa ser registrada para usar esse provedor de recursos antes de poder usar o DNS do Azure. Essa operação deve ser executa apenas uma vez para cada assinatura.

	PS c:> Register-AzureProvider -ProviderNamespace Microsoft.Network



## Etags e marcas
### ETags
Suponha que duas pessoas ou dois processos tentem modificar um registro DNS ao mesmo tempo. Qual vence? E o vencedor sabe o que ele acabou de substituir alterações criadas por outra pessoa?<BR><BR> O DNS do Azure usa as Etags para tratar alterações simultâneas para o mesmo recurso com segurança. Cada recurso DNS (zona ou conjunto de registros) tem uma Etag associada a ele. Sempre que um recurso é recuperado, a Etag também é recuperada. Ao atualizar um recurso, você tem a opção de devolver a Etag para que o DNS do Azure possa verificar se a Etag no servidor é correspondente. Uma vez que cada atualização a um recurso resulta em uma Etag sendo gerada novamente, uma incompatibilidade de Etag indica que ocorreu uma alteração simultânea. As Etags também são usadas ao criar um novo recurso para garantir que o recurso ainda não existe.

Por padrão, o PowerShell do DNS do Azure usa as Etags bloquear alterações simultâneas às zonas e conjuntos de registro. A opção “-Substituir” pode ser usada para suprimir as verificações de Etag. Nesse caso, as alterações simultâneas que ocorrerem serão substituídas.<BR><BR> No nível da API REST do DNS do Azure, as Etags são especificadas usando cabeçalhos HTTP. Seu comportamento é descrito na tabela a seguir:

|Cabeçalho|Comportamento|
|------|--------|
|Nenhum|PUT sempre terá êxito (nenhuma verificação de Etag)|
|If-match <etag>|PUT só terá êxito se o recurso existir e a Etag corresponder|
|If-match * |PUT só terá êxito se houver recursos|
|If-none-match |* PUT só terá êxito se não houver recursos|

### Marcas
Marcas são diferentes das Etags. As marcas são uma lista de pares nome-valor e são usadas pelo Gerenciador de Recursos do Azure para fins de cobrança ou agrupamentos. Para obter mais informações sobre marcas, consulte Usando marcas para organizar os recursos do Azure. O PowerShell do DNS do Azure dá suporte a marcas em zonas e conjuntos de registros especificados usando o parâmetro opcional “-Marca”. O exemplo a seguir mostra como criar uma zona DNS com duas marcas, “project = demo” e “env = test”:

	PS C:\> New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )


## Criar uma zona DNS
Uma zona DNS é criada usando o cmdlet New-AzureDnsZone. No exemplo a seguir, vamos criar uma zona DNS chamada "contoso.com" no grupo de recursos chamado 'MyResourceGroup':<BR>

		PS C:\> New-AzureDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

>[AZURE.NOTE]No DNS do Azure, os nomes de zona devem ser especificados sem terminar em '.'. Por exemplo, como "contoso.com", em vez de "contoso.com.".<BR>


A zona DNS foi criada no DNS do Azure. Criar uma zona DNS também cria os seguintes registros DNS:<BR>



- O registro SOA (“Start of Authority”, Início de Autoridade). Ele está presente na raiz de cada zona DNS.
- Os registros NS (name server, servidor de nomes) autoritativos. Eles mostram quais servidores de nome estão hospedando a zona. DNS do Azure usa um pool de servidores de nomes; dessa forma servidores de nomes diferentes podem ser atribuídos a diferentes zonas no DNS do Azure. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter mais informações.<BR>

Para exibir esses registros, use Get-AzureDnsRecordSet:

		PS C:\> Get-AzureDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

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

>[AZURE.NOTE]Conjuntos de registos na raiz (ou “apex”) de uma zona DNS usam "@" como o nome do conjunto de registros.


Depois de criar sua primeira zona DNS, você pode testá-la usando ferramentas DNS como nslookup, dig ou o [cmdlet Resolve-DnsName PowerShell](https://technet.microsoft.com/pt-BR/library/jj590781.aspx).<BR>

Se você ainda não delegou seu domínio para usar a nova zona no DNS do Azure, você precisará direcionar a consulta DNS diretamente para um dos servidores de nome para a zona. Os servidores de nomes da zona são fornecidos nos registros NS, conforme listado por Get-AzureDnsRecordSet acima. Certifique-se de substituir os valores corretos para a zona no comando a seguir.<BR>

		C:\> nslookup
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


[Começar a criar conjuntos de registro e registros](dns-getstarted-create-recordset.md)<BR> [Como gerenciar as zonas DNS](dns-operations-dnszones.md)<BR> [Como gerenciar registros DNS](dns-operations-recordsets.md)<BR> [Automatizar operações do Azure com o SDK do .NET](dns-sdk.md)<BR> [Referência da API REST do DNS do Azure](https://msdn.microsoft.com/library/azure/mt163862.aspx)
 

<!---HONumber=Nov15_HO4-->