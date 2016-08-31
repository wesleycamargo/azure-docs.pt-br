<properties 
   pageTitle="Gerenciar zonas DNS usando o PowerShell | Microsoft Azure" 
   description="Você pode gerenciar zonas DNS usando o Azure Powershell. Como atualizar, excluir e criar zonas DNS no DNS do Azure" 
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

# Como gerenciar as zonas DNS usando o PowerShell

> [AZURE.SELECTOR]
- [CLI do Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)



Este artigo mostrará como gerenciar sua zona DNS usando o PowerShell. Para usar essas etapas, você precisará instalar a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager (1.0 ou posterior). Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell.


## Criar uma nova zona DNS

Para criar uma zona DNS, consulte [Criar uma zona DNS usando o PowerShell](dns-getstarted-create-dnszone.md).

## Obter uma zona DNS

Para recuperar uma zona DNS, use o cmdlet `Get-AzureRmDnsZone`. Esta operação retornará um objeto de zona DNS correspondente a uma zona existente no DNS do Azure. O objeto contém dados sobre a zona (como o número de conjuntos de registros), mas não contém os conjuntos de registro em si.

	$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

## Listar as zonas DNS

Omitindo o nome da zona de `Get-AzureRmDnsZone`, você pode enumerar todas as zonas em um grupo de recursos. Essa operação retorna uma matriz de objetos de zona.

	$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

## Atualizar uma zona DNS

As alterações a um recurso da zona DNS podem ser feitas usando o `Set-AzureRmDnsZone`. Isso não atualiza nenhum dos conjuntos de registros DNS dentro da zona (consulte [Como gerenciar registros DNS](dns-operations-recordsets.md)). Ele só é usado para atualizar as propriedades do recurso da zona em si. Isso é atualmente limitado às “marcas” do Gerenciador de Recursos do Azure para o recurso de zona. Consulte [Etags e marcas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obter mais informações.

Use um dos dois métodos a seguir para atualizar a zona DNS:

### Especifique a zona usando o nome da zona e o grupo de recursos

	Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### Especifique a zona usando um objeto de $zone

Especifique a zona usando um objeto de $zone de `Get-AzureRmDnsZone`. Ao usar `Set-AzureRmDnsZone` com um objeto $zone, verificações de Etag serão usadas para garantir que as alterações simultâneas não sejam substituídas. Você pode usar a opção *-Substituir* para suprimir essas verificações. Consulte [Etags e marcas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obter mais informações.


	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	<..modify $zone.Tags here...>
	Set-AzureRmDnsZone -Zone $zone [-Overwrite]


## Excluir uma zona DNS

As zonas DNS podem ser excluídas usando o cmdlet Remove-AzureRmDnsZone.
 
Antes de excluir uma zona DNS no DNS do Azure, você precisará excluir todos os conjuntos de registros, exceto os registros NS e SOA na raiz da zona, que foram criados automaticamente quando a zona foi criada.

Use um dos dois métodos a seguir para remover uma zona DNS:

### Especifique a zona usando o nome da zona e o nome do grupo de recursos

Esta operação tem um comutador opcional *-Forçar* que suprime o prompt para confirmar que deseja remover a zona DNS.

	Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

### Especifique a zona usando um objeto de $zone 

Especifique a zona usando um objeto de $zone de `Get-AzureRmDnsZone`. Esta operação tem um comutador opcional *-Forçar* que suprime o prompt para confirmar que deseja remover a zona DNS. Assim como com `Set-AzureRmDnsZone`, especificar a zona usando um objeto $zone habilita as verificações de Etag para garantir que as alterações simultâneas não sejam excluídas. <BR> O sinalizador opcional *-Substituir* suprime essas verificações. Consulte [Etags e marcas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obter mais informações.

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]



O objeto de zona também pode ser redirecionado em vez de ser passado como um parâmetro:

	Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## Próximas etapas

Após criar uma zona DNS, crie [conjuntos de registros e registros](dns-getstarted-create-recordset.md) para iniciar a resolução de nomes do seu domínio da Internet.

<!---HONumber=AcomDC_0817_2016-->