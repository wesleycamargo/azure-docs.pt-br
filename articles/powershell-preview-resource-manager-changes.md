<properties
	pageTitle="Alterações do Gerenciador de Recursos do Azure PowerShell 1.0 Preview | Microsoft Azure"
	description="Descreve as alterações nos cmdlets do Gerenciador de Recursos que foram feitas para o Azure PowerShell 1.0 Preview."
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="na"
	ms.date="01/26/2016"
	ms.author="gauravbh;tomfitz"/>

#Mudanças nos cmdlets do PowerShell para gerenciamento do Gerenciador de Recursos do Azure

Como parte da versão de visualização do Azure PowerShell 1.0, fizemos algumas melhorias nos cmdlets de gerenciamento. Esses aprimoramentos são adicionais às alterações de nome de cmdlet que fazem parte da 1.0 Preview. Alguns desses aprimoramentos envolvem grandes alterações e podem danificar seus scripts existentes. Esperamos que sua experiência seja aprimorada por essas alterações. Gostaríamos de receber seus comentários sobre essas alterações e incorporaremos esse feedback no Azure PowerShell 1.0. Experimente os novos cmdlets e envie seus comentários.

##Implantação de modelo separado dos grupos de recursos

No versão 0.9.8, todos os parâmetros de implantação de modelo também estavam presentes nos cmdlets de grupo de recursos. Assim, em New-AzureResourceGroup, você podia criar um novo grupo de recursos, bem como fornecer detalhes sobre os modelos que deseja implantar. A mesma funcionalidade de implantação de modelo também estava presente em New-AzureResourceGroupDeployment. Na 1.0 Preview, separamos essa funcionalidade. Agora, New-AzureRMResourceGroup fornecerá a funcionalidade de criação de novos grupos de recursos e New-AzureRmResourceGroupDeployment fornecerá a funcionalidade de implantação de modelo. Você pode usar o vinculamento para usá-los juntos. Isso facilita a compreensão e o uso de cmdlets.

##Cmdlets de log de auditoria consolidados

Para logs de auditoria, tivemos vários cmdlets para obter logs em vários escopos; por exemplo, Get-AzureResourceProviderLog, Get-AzureResourceGroupLog, Get-AzureSubscriptionIdLog e Get-AzureResourceLog. Para obter logs, frequentemente era necessário executar uma combinação de cmdlets do log. Essa experiência não era ideal. Consolidamos essa funcionalidade em um único cmdlet que pode ser chamado em escopos diferentes com o uso de parâmetros. Agora, você pode chamar Get-AzureRmLog com o parâmetro apropriado para especificar o escopo.

Na versão 0.9.8, para obter os logs para um determinado grupo de recursos, você executaria algo isto:

    Get-AzureResourceGroupLog -ResourceGroup <resource-group-name>

Para obter os logs para um recurso específico, você faria o seguinte:

     Get-AzureResourceLog -ResourceId
     /subscriptions/#######-####-####-####-############/resourcegroups/<resource-group-name>/providers/<provider-namespace>/
     <resource-name>

Na versão 1.0 preview, você obterá as mesmas informações ao executar os cmdlets abaixo. Para obter os logs para um grupo de recursos, você executará:

     Get-AzureRmLog -ResourceGroup <resource-group-name>
     
Para obter os logs para um recurso específico, você executará:

     Get-AzureRmLog -ResourceId /subscriptions/#######-####-####-####-############/resourcegroups/<resource-group-name>
     /providers/<provider-namespace>/<resource-name>

##Alterações no cmdlet Get para recursos e grupos de recursos

Incorporamos alterações nos cmdlets Get-AzureRmResource e Get-AzureRmResourceGroup para que esses cmdlets agora consultem diretamente o provedor de recursos. Separamos a funcionalidade de consulta de cache em novos cmdlets chamados Find-AzureRmResource*. Se você desejar localizar um grupo de recursos que tem uma determinada marca, você pode usar o novo cmdlet Find-AzureRmResourceGroup. Com essa alteração, os parâmetros de consulta em marcas foram removidos dos cmdlets Get-AzureRmResource e Get-AzureRmResourceGroup.

Na versão 0.9.8, localize todos os grupos de recursos que contenham uma marca específica e execute:

    Get-AzureResourceGroup -Tag <tag-object>

Na versão 1.0 preview, você executará o cmdlet abaixo para obter o cenário acima:

    Find-AzureRmResourceGroup -Tag <tag-object>
    
##Test-AzureResource and Test-AzureResourceGroup removidos

Esses cmdlets não funcionavam de forma confiável para todos os tipos de cenário e recurso. Estamos trabalhando para encontrar uma solução melhor para essa funcionalidade. Enquanto isso, não queríamos continuar usando os cmdlets que não eram confiáveis. Removemos esses cmdlets nesta versão e vamos adicionar uma solução confiável em uma versão futura.

##Aprimoramentos de Get-AzureRmResourceProvider

Agora, você pode usar o cmdlet Get-AzureRmResourceProvider para obter informações sobre o local de provedores de recursos. Ele informará quais provedores e tipos estão disponíveis em uma determinada região. Além disso, você pode usar esse cmdlet para obter a lista de locais disponíveis para um provedor específico. Removemos o cmdlet Get-AzureLocation, pois você pode obter todas as informações de local por meio do cmdlet Get-AzureRmResourceProvider.

Na versão 0.9.8, para obter uma lista de todos os locais com suporte, você executará:

     Get-AzureLocation

E para obter o estado de registro dos provedores, executará:

     Get-AzureProvider -ListAvailable

Na versão 1.0 preview, você pode fazer o que foi explicado acima usando somente um cmdlet, como mostrado abaixo:

     Get-AzureRmResourceProvider -Location <location>

O que foi mostrado acima filtrará o resultado para mostrar somente os provedores e os tipos disponíveis no local especificado.

Ou você pode filtrar o resultado para um provedor específico, como mostrado abaixo:

     Get-AzureRmResourceProvider -ProviderNamespace <provider-namespace>

O que foi mostrado acima filtrará o resultado para mostrar os locais com suporte somente para o provedor especificado.

##Cmdlets de política

Adicionamos suporte a políticas ao Gerenciador de Recursos do Azure. Cmdlets do PowerShell para gerenciar políticas foram adicionados nesta versão. Para obter mais detalhes sobre políticas, consulte [Usar a política para gerenciar recursos e controlar o acesso](resource-manager-policy.md).

<!---HONumber=AcomDC_0128_2016-->