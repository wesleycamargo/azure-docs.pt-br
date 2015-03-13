<properties 
	pageTitle="Usando marcas para organizar os recursos do Azure" 
	description="" 
	services="" 
	documentationCenter="" 
	authors="flanakin" 
	writer="" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="micflan"/>


# Usando marcas para organizar os recursos do Azure

O portal de Visualização do Azure e o Gerenciador de Recursos subjacente são ferramentas para organizar seus recursos e personalizar sua experiência, tornando-a sob medida para você. 

No portal completo do Azure, as assinaturas são a única maneira de classificar e agrupar seus recursos. Com o portal de visualização, [apresentamos os grupos de recursos](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups), que permitem que você agrupe entidades relacionadas. Isso se tornou ainda mais valioso quando [introduzimos o acesso baseado em função](http://azure.microsoft.com/documentation/articles/role-based-access-control-configure). Agora, no mesmo espírito, você pode marcar seus recursos com pares de chave/valor para visualizar os recursos e categorizá-los ainda mais em grupos e, dentro do portal, em assinaturas.

Agrupe os recursos por equipe, projeto ou até mesmo ambiente para concentrar-se exatamente no que você deseja ver, quando você precisa vê-lo. 


## Marcas no portal de Visualização do Azure

Marcar recursos e grupos de recursos no portal de visualização é fácil. Use o hub Procurar para navegar até o recurso ou o grupo de recursos que você gostaria de marcar e clique na parte de Marcas na seção Visão geral, na parte superior da lâmina. 

![Tags part on resource and resource group blades](./media/azure-preview-portal-using-tags/rgblade.png)

Isso abrirá uma lâmina com a lista de marcas que já foram aplicadas. Se esta for sua primeira marca, a lista estará vazia. Para adicionar uma marca, especifique um nome e valor e pressione Enter. Depois de adicionar algumas marcas, você verá opções de preenchimento automático com base em valores e nomes de marca pré-existentes, para melhor assegurar uma taxonomia consistente entre os recursos e evitar erros comuns, como erros de ortografia.

![Tag resources with name/value pairs](./media/azure-preview-portal-using-tags/tag-resources.png)

A partir daqui, você pode clicar em cada marca individual para exibir uma lista de todos os recursos com a mesma marca. É claro que, se esta for sua primeira marca, essa lista não será muito interessante. Por enquanto, vamos pular para o PowerShell para marcar todos os nossos recursos rapidamente.


## Marcação com o PowerShell

Façamos primeiro o mais importante, pegar o mais recente [módulo do PowerShell do Azure](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Se esta for sua primeira vez usando o módulo PowerShell do Azure, [leia a documentação](http://azure.microsoft.com/documentation/articles/install-configure-powershell) para se familiarizar. Para os fins deste artigo, vamos supor que você já adicionou uma conta e selecionou uma assinatura com os recursos que deseja marcar.

A marcação só está disponível para recursos e grupos de recursos disponíveis a partir do [Gerenciador de Recursos](http://msdn.microsoft.com/library/azure/dn790568.aspx), portanto, a próxima coisa que precisamos fazer é passar a usar o Gerenciador de Recursos. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/).

  Switch-AzureMode AzureResourceManager

As marcas existem diretamente em recursos e grupos de recursos, portanto, para que ver quais marcas já estão aplicadas podemos simplesmente obter um recurso ou grupo de recursos com `Get-AzureResource` ou `Get-AzureResourceGroup`, respectivamente. Vamos começar com um grupo de recursos.

![Getting tags with Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

Esse cmdlet retorna vários bits de metadados sobre o grupo de recursos, incluindo quais marcas foram aplicadas, se houver. Para marcar um grupo de recursos, simplesmente usaremos `Set-AzureResourceGroup` e especificaremos um valor e nome de marca.

![Setting tags with Set-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

Lembre-se de que as marcas são atualizadas como um todo, portanto, se você estiver adicionando uma marca a um recurso que já foi marcado, você precisará salvar/usar uma matriz com todas as marcas que você deseja manter. Para remover uma marca, simplesmente salve a matriz sem aquela que deseja remover. 

O processo é o mesmo para os recursos, exceto pelo fato de que você usará os cmdlets `Get-AzureResource` e `Set-AzureResource`. Para obter recursos ou grupos de recursos com uma marca específica, use o cmdlet `Get-AzureResource` ou `Get-AzureResourceGroup` com o parâmetro `-Tag`.

![Getting tagged resources and resource groups with Get-AzureResource and Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## Marcação com o Gerenciador de Recursos

Ambos o portal de visualização e o PowerShell usam a [REST API do Gerenciador de Recursos](http://msdn.microsoft.com/library/azure/dn790568.aspx), nos bastidores. Se você precisar integrar a marcação a outro ambiente, você pode obter marcas com um GET na ID do recurso e atualizar o conjunto de marcas com uma chamada de PATCH.


## Gerenciando sua taxonomia

Anteriormente, falamos sobre como o preenchimento automático ajuda você a garantir a consistência e evitar erros. O preenchimento automático é realizado com base na taxonomia da configuração de marcas disponíveis para a assinatura. Cada marca que você adiciona a um recurso ou grupo de recursos é adicionada automaticamente à taxonomia toda a assinatura, mas você também pode realizar um pré-preenchimento dessa taxonomia com nomes de marca e valores que você deseja usar como recursos e serão marcados no futuro.

Para obter uma lista de todas as marcas dentro de uma assinatura usando o PowerShell, use o cmdlet `Get-AzureTag`.

![Get-AzureTag in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureTag-in-PowerShell.png)


Você pode ver as marcas que começam com "hidden-" e "link:". Elas são marcas internas, que você deve ignorar e evitar alterar. 

Use o cmdlet `New-AzureTag` para adicionar novas marcas à taxonomia. Essas marcas serão incluídas no preenchimento automático, mesmo que elas ainda não tenham sido aplicadas a nenhum recurso ou grupo de recursos. Para remover um nome/valor de uma marca, remova a marca de quaisquer recursos com os quais ela possa ser usada e, em seguida, use o cmdlet `Remove-AzureTag` para removê-la da taxonomia.

Para exibir sua taxonomia de marcas no portal, use o hub Procurar para exibir Tudo e, em seguida, selecionar Marcas.

![Find tags via the Browse hub](./media/azure-preview-portal-using-tags/browse-tags.png)

Fixe as marcas mais importantes no seu quadro inicial para acesso rápido e você estará pronto para começar. Divirta-se!

![Pin tags to the Startboard](./media/azure-preview-portal-using-tags/pin-tags.png)


<!--HONumber=46--> 
