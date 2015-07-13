<properties
	pageTitle="Assinatura do Microsoft Azure e limites de serviços, cotas e restrições"
	description="Fornece uma lista de assinaturas comuns do Azure e limites de serviço, cotas e restrições. Isso inclui informações sobre como aumentar os limites junto com os valores máximos."
	services=""
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="mollybos"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2015"
	ms.author="jroth"/>

# Assinatura do Azure e limite de serviços, cotas e restrições

## Visão geral

Este documento especifica alguns dos limites mais comuns do Microsoft Azure. Observe que isso não cobre atualmente todos os serviços do Azure. Com o tempo, esses limites serão ampliados e atualizados para uma maior cobertura da plataforma.

> [AZURE.NOTE]Se você deseja aumentar o limite acima do **Limite Padrão**, você pode [abrir uma solicitação no suporte ao cliente online sem custo](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Os limites não podem ser aumentados acima do valor de **Limite máximo** nas tabelas abaixo. Se não houver nenhuma coluna **Limite máximo**, o recurso especificado não tem limites ajustáveis.

## Limites e o Gerenciador de Recursos do Azure

Agora é possível combinar vários recursos do Azure em um único Grupo de Recursos do Azure. Ao usar os Grupos de Recursos, limites que antes eram globais passam a ser gerenciados em nível regional com o Gerenciador de Recursos do Azure. Para obter mais informações sobre os Grupos de Recursos do Azure, consulte [Usando grupos de recursos para gerenciar recursos do Azure](resource-group-portal.md).

Nos limites abaixo, uma nova tabela foi adicionada para refletir quaisquer diferenças nos limites ao usar o Gerenciador de Recursos do Azure. Por exemplo, há uma tabela de **Limites de Assinatura** e uma tabela de **Limites de Assinatura – Gerenciador de Recursos do Azure**. Quando um limite se aplica a ambos os cenários, ele é mostrado apenas na primeira tabela. A menos que indicado de outro modo, os limites são globais em todas as regiões.

> [AZURE.NOTE]É importante enfatizar que as cotas para recursos nos Grupos de Recursos do Azure são acessíveis de acordo com a região pela assinatura e não de acordo com a assinatura, assim como acontece com as cotas de gerenciamento de serviço. Vamos usar cotas de núcleos como exemplo. Se você precisar solicitar um aumento de cota com suporte para núcleos, você precisa decidir quantos núcleos deseja usar em quais regiões e, em seguida, fazer uma solicitação específica de cotas de núcleos do Grupo de Recursos do Azure para as quantidades e regiões desejadas. Portanto, se precisar usar 30 núcleos na Europa Ocidental para executar seu aplicativo lá, você deve solicitar especificamente 30 núcleos na Europa Ocidental. Mas você não terá um aumento na cota de núcleos em nenhuma outra região – somente a Europa Ocidental terá a cota de 30 núcleos. <!-- --> Como resultado, pode ser útil pensar em decidir quais devem ser as cotas do Grupo de Recursos do Azure para a carga de trabalho em uma determinada região e solicitar essa quantidade em cada região na qual esteja considerando a possibilidade de implantação. Consulte [Solucionando problemas de implantação](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues) para obter mais ajuda ao descobrir suas cotas atuais para regiões específicas.

## Limites de assinatura

[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]


## Limites de Assinatura – Gerenciador de Recursos do Azure 

Os limites a seguir se aplicam ao usar o Gerenciador de Recursos do Azure e os Grupos de Recursos do Azure. Limites que não foram alterados com o Gerenciador de Recursos do Azure não estão listados abaixo. Consulte a tabela anterior para obter esses limites.

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


## Limites do Grupo de Recursos

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


## Limites de Máquinas Virtuais

[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


## Limites de Máquinas Virtuais – Gerenciador de Recursos do Azure

Os limites a seguir se aplicam ao usar o Gerenciador de Recursos do Azure e os Grupos de Recursos do Azure. Limites que não foram alterados com o Gerenciador de Recursos do Azure não estão listados abaixo. Consulte a tabela anterior para obter esses limites.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


## Limites de rede

[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]


## Limites de Rede – Gerenciador de Recursos do Azure

Os limites a seguir se aplicam ao usar o Gerenciador de Recursos do Azure e os Grupos de Recursos do Azure. Limites que não foram alterados com o Gerenciador de Recursos do Azure não estão listados abaixo. Consulte a tabela anterior para obter esses limites.

[AZURE.INCLUDE [azure-virtual-network-limits-azure-resource-manager](../includes/azure-virtual-network-limits-azure-resource-manager.md)]


## Limites de Armazenamento Padrão

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

## Limites de Armazenamento Premium

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Recurso</th>
   <th align="left" valign="middle">Limite padrão</th>
</tr>
<tr>
   <td valign="middle"><p>Capacidade Total do Disco por conta</p></td>
   <td valign="middle"><p>35 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Capacidade Total de Instantâneos por conta</p></td>
   <td valign="middle"><p>10 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Largura de banda máxima por conta (entrada + saída)</p></td>
   <td valign="middle"><p>50 Gbps</p></td>
</tr>
</table>


## Limites de Armazenamento – Gerenciador de Recursos do Azure

Os limites a seguir se aplicam ao usar o Gerenciador de Recursos do Azure e os Grupos de Recursos do Azure. Limites que não foram alterados com o Gerenciador de Recursos do Azure não estão listados abaixo. Consulte a tabela anterior para obter esses limites.

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


## Limites de Serviços de Nuvem

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


## Limites de Aplicativos Web (Sites)

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]


## Limites de visualização de lote

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]


## Limites do Banco de Dados de Documentos

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


## Limites do Mobile Engagement

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


## Limites de banco de dados SQL

[AZURE.INCLUDE [azure-sql-database-limits](../includes/azure-sql-database-limits.md)]


## Limites de serviços de mídia

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]


## Limites de barramento de serviço

[AZURE.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]


## Limites do Active Directory

[AZURE.INCLUDE [azure-active-directory-limits](../includes/azure-active-directory-limits.md)]


## Limites do RemoteApp

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

## Limites do Sistema StorSimple

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]
 
## Consulte também

[Entendendo os limites e aumentos do Azure](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Tamanhos de máquinas virtuais e serviços de nuvem do Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)

<!---HONumber=62-->