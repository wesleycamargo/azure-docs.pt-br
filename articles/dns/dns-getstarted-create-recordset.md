<properties
   pageTitle="Criar um conjunto de registros e registros para uma zona DNS usando o PowerShell | Microsoft Azure"
   description="Como criar registros de host para o DNS do Azure. Configuração dos conjuntos de registros e registros usando o PowerShell"
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
   ms.date="05/06/2016"
   ms.author="cherylmc"/>



# Criar conjuntos de registros DNS e registros usando o PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI do Azure](dns-getstarted-create-recordset-cli.md)

Este artigo o guiará durante a criação de registros e conjuntos de registros usando o PowerShell. Depois de criar a zona DNS, você precisa adicionar os registros DNS para seu domínio. Para fazer isso, primeiro você precisa entender os registros DNS e os conjuntos de registros.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## Antes de começar

Verifique se você instalou a última versão dos cmdlets do PowerShell do Azure Resource Manager. Confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell.

## Criar registro e um conjunto de registros

Nesta seção, mostraremos como criar registros e um conjunto de registros.


### 1\. Conecte-se as suas assinaturas 

Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

	Login-AzureRmAccount

Verificar as assinaturas da conta.

	Get-AzureRmSubscription 

Especifique a assinatura que você quer usar.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Para saber mais sobre como trabalhar com o PowerShell, confira [Usar o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).


### 2\. Criar um conjunto de registros

Os conjuntos de registros são criados usando o cmdlet `New-AzureRmDnsRecordSet`. Ao criar um conjunto de registros, você precisará especificar o nome do conjunto de registros, a zona, o TTL (vida útil) e o tipo de registro.

Para criar um conjunto de registros em vértices da zona (nesse caso, "contoso.com"), use o nome do Registro "@", incluindo as aspas. Isso é uma convenção comum do DNS.

O exemplo a seguir cria um conjunto de registros que tem o nome relativo *www* na Zona DNS *contoso.com*. O nome totalmente qualificado dos registros será *www.contoso.com*, o tipo de registro é *A* e o TTL é 60 segundos. Após concluir esta etapa, você terá um conjunto de registros *Web* vazio que é atribuído à variável *$rs*.

	$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### Se um conjunto de registros já existir

Se um conjunto de registros já existir, o comando falhará, a menos que a opção comutador *-Overwrite* seja usada. A opção *-Overwrite* disparará um prompt de confirmação, que pode ser suprimido usando a opção *-Force*.


	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


No exemplo acima, a zona é especificada usando o nome da zona e o nome do grupo de recursos. Como alternativa, você pode especificar um objeto de zona, conforme retornado por `Get-AzureRmDnsZone` ou `New-AzureRmDnsZone`.

	$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet` retorna um objeto local que representa o conjunto de registros criado no DNS do Azure.

### 3\. Adicionar um registro

Para poder usar o conjunto de registros *www* recém-criado, você precisará adicionar registros a ele. Você pode adicionar registros IPv4 *A* ao conjunto de registros *www* usando o exemplo a seguir. O exemplo depende da variável que você definiu, $rs, na etapa anterior.

Adicionar registros a um conjunto de registros usando `Add-AzureRmDnsRecordConfig` é uma operação offline. Apenas a variável local *$rs* é atualizada.


	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### 4\. Confirmar as alterações

Confirme as alterações no conjunto de registros. Use `Set-AzureRmDnsRecordSet` para carregar as alterações no conjunto de registros para o DNS do Azure.

	Set-AzureRmDnsRecordSet -RecordSet $rs

### 5\. Recuperar o conjunto de registros

Você pode recuperar o conjunto de registros do DNS do Azure usando `Get-AzureRmDnsRecordSet`, conforme mostrado no exemplo a seguir.


	Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : MyAzureResourceGroup
	Ttl               : 3600
	Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
	RecordType        : A
	Records           : {134.170.185.46, 134.170.188.221}
	Tags              : {}


Você também pode usar nslookup ou outras ferramentas DNS para consultar o novo conjunto de registros.

Se ainda não tiver delegado o domínio para os servidores de nome do DNS do Azure, você precisará especificar o endereço do servidor de nomes da zona explicitamente.


	nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221

## Exemplos adicionais de tipo de registro


Os exemplos a seguir mostram como criar um conjunto de registros de cada tipo de registro, cada um contendo um único registro.

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## Próximas etapas

[Como gerenciar as zonas DNS](dns-operations-dnszones.md)

[Como gerenciar os registros DNS](dns-operations-recordsets.md)

[Automatizar operações do Azure com o SDK do .NET](dns-sdk.md)
 

<!---HONumber=AcomDC_0518_2016-->