<properties 
   pageTitle="Operações em zonas DNS | Microsoft Azure" 
   description="Você pode gerenciar zonas DNS usando o Azure Powershell. Como atualizar, excluir e criar zonas DNS no DNS do Azure" 
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
   ms.date="03/17/2016"
   ms.author="joaoma"/>

# Como gerenciar as zonas DNS usando o PowerShell

> [AZURE.SELECTOR]
- [CLI do Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)


Este guia mostrará como gerenciar sua zona DNS. Ele ajudará a entender a sequência de operações a serem realizadas para administrar sua zona DNS.


## Criar uma nova zona DNS

Para criar uma nova zona DNS para hospedar seu domínio, use o cmdlet New-AzureRmDnsZone:

		PS C:\> $zone = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [–Tag $tags] 

A operação cria uma nova zona DNS no DNS do Azure e retorna um objeto local correspondente a essa zona. Opcionalmente, você pode especificar uma matriz de marcas do Gerenciador de Recursos do Azure. Para obter mais informações, consulte [Etags e marcas](../dns-getstarted-create-dnszone.md#Etags-and-tags).

O nome da zona deve ser exclusivo dentro do grupo de recursos e a zona não deve existir, caso contrário, a operação falhará.

O mesmo nome de zona pode ser reutilizado em outro grupo de recursos ou uma assinatura do Azure diferente. Quando várias zonas compartilharem o mesmo nome, a cada instância serão atribuídos endereços de servidor de nome diferentes, e somente uma instância pode ser delegada do domínio pai. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter mais informações.

## Obter uma zona DNS

Para recuperar uma zona DNS, use o cmdlet Get-AzureRmDnsZone:

		PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

A operação retornará um objeto de zona DNS correspondente a uma zona existente no DNS do Azure. Esse objeto contém dados sobre a zona (como o número de conjuntos de registros), mas não contém os conjuntos de registro em si.

## Listar as zonas DNS

Omitindo o nome da zona de Get-AzureRmDnsZone, você pode enumerar todas as zonas em um grupo de recursos:

	PS C:\> $zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

Essa operação retorna uma matriz de objetos de zona.

## Atualizar uma zona DNS

As alterações a um recurso de zona DNS podem ser feitas usando Set-AzureRmDnsZone. Isso não atualiza nenhum dos conjuntos de registros DNS dentro da zona (consulte [Como gerenciar registros DNS](dns-operations-recordsets.md)). Ele só é usado para atualizar as propriedades do recurso da zona em si. Isso é atualmente limitado às “marcas” do Gerenciador de Recursos do Azure para o recurso de zona. Consulte [Etags e marcas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obter mais informações.

Use um dos dois métodos a seguir para atualizar a zona DNS:

### Opção 1
 
Especifique a zona usando o nome da zona e o grupo de recursos.

	PS C:\> Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### Opção 2
Especifique a zona usando um objeto de $zone de Get-AzureRmDnsZone:

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> <..modify $zone.Tags here...>
	PS C:\> Set-AzureRmDnsZone -Zone $zone [-Overwrite]

Ao usar Set-AzureRmDnsZone com um objeto $zone, verificações de “Etag” serão usadas para garantir que as alterações simultâneas não sejam substituídas. Você pode usar a opção “-Substituir” para suprimir essas verificações. Consulte [Etags e marcas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obter mais informações.

## Excluir uma zona DNS

As zonas DNS podem ser excluídas usando o cmdlet Remove-AzureRmDnsZone.
 
Antes de excluir uma zona DNS no DNS do Azure, você precisará excluir todos os conjuntos de registros, exceto os registros NS e SOA na raiz da zona, que foram criados automaticamente quando a zona foi criada.

Use um dos dois métodos a seguir para remover uma zona DNS:

### Opção 1

Especifique a zona usando o nome da zona e o nome do grupo de recursos:

	PS C:\> Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

Esta operação tem um comutador opcional “-Forçar” que suprime o prompt para confirmar que deseja remover a zona DNS.

### Opção 2

Especifique a zona usando um objeto de $zone de Get-AzureRmDnsZone:

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]

O comutador “-Forçar” é o mesmo da Opção 1.

Assim como com “Set-AzureRmDnsZone”, especificar a zona usando um objeto $zone habilita as verificações de “etag” para garantir que as alterações simultâneas não sejam excluídas. <BR> O sinalizador opcional '-Substituir ' suprime essas verificações. Consulte [Etags e marcas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obter mais informações.

O objeto de zona também pode ser redirecionado em vez de ser passado como um parâmetro:

	PS C:\> Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## Próximas etapas


[Gerenciar registros DNS](dns-operations-recordsets.md)

[Automatizar operações usando o SDK do .NET](dns-sdk.md)

<!---HONumber=AcomDC_0323_2016-->