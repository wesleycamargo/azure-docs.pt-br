<properties 
	pageTitle="Usando marcas para organizar os recursos do Azure" 
	description="Mostra como aplicar marcas para organizar os recursos para cobrança e gerenciamento." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac"
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="AzurePortal" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/05/2015" 
	ms.author="tomfitz"/>


# Usando marcas para organizar os recursos do Azure

O Gerenciador de Recursos permite que você organize os recursos logicamente por meio da aplicação de marcas. As marcas consistem em pares de chave/valor que identificam recursos com propriedades definidas por você. Para marcar recursos como pertencentes à mesma categoria, aplique a mesma marca a esses recursos.

Quando você exibir os recursos com uma determinada marca, verá os recursos de todos os grupos de recursos. Você não está limitado aos recursos no mesmo grupo de recursos, o que permite que você organize seus recursos de forma independente das relações de implantação. As marcas podem ser particularmente úteis quando você precisar organizar os recursos de gerenciamento ou de cobrança.

> [AZURE.NOTE]Você só pode aplicar marcas em recursos com suporte a operações do Gerenciador de Recursos. Se você tiver criado uma Máquina Virtual, uma Rede Virtual ou um Armazenamento por meio do modelo de implantação clássica (como por meio do portal do Azure ou da [API de Gerenciamento de Serviço](https://msdn.microsoft.com/library/azure/dn948465.aspx)), não poderá aplicar uma marca a esse recurso. Será necessário implantar esses recursos novamente por meio do Gerenciador de Recursos para dar suporte à marcação. Todos os outros recursos oferecem suporte à marcação.

## Marcas no portal de visualização

Marcar recursos e grupos de recursos no portal de visualização é fácil. Use o hub Procurar para navegar até o recurso ou o grupo de recursos que você gostaria de marcar e clique na parte de Marcas na seção Visão geral, na parte superior da lâmina.

![Parte de marcas nas folhas de recurso e grupo de recursos](./media/resource-group-using-tags/rgblade.png)

Isso abrirá uma lâmina com a lista de marcas que já foram aplicadas. Se esta for sua primeira marca, a lista estará vazia. Para adicionar uma marca, especifique um nome e valor e pressione Enter. Depois de adicionar algumas marcas, você verá opções de preenchimento automático com base em valores e nomes de marca pré-existentes, para melhor assegurar uma taxonomia consistente entre os recursos e evitar erros comuns, como erros de ortografia.

![Marcar recursos com pares de nome/valor](./media/resource-group-using-tags/tag-resources.png)

Aqui, você pode clicar em cada marca individual para exibir uma lista de todos os recursos com a mesma marca. É claro que, se esta for sua primeira marca, essa lista não será muito interessante. Por enquanto, vamos pular para o PowerShell para marcar todos os nossos recursos rapidamente.


## Marcação com o PowerShell

A primeira coisa que deve ser feita é captar o [módulo do PowerShell do Azure](./install-configure-powershell.md) mais recente. Se esta é a primeira vez que você está usando o módulo PowerShell do Azure, [leia a documentação](./install-configure-powershell.md) para se familiarizar. Para os fins deste artigo, vamos supor que você já adicionou uma conta e selecionou uma assinatura com os recursos que deseja marcar.

A marcação está disponível somente para recursos e grupos de recursos disponíveis do [Gerenciador de Recursos](http://msdn.microsoft.com/library/azure/dn790568.aspx), portanto, a próxima coisa que precisamos fazer é passar a usar o Gerenciador de Recursos. Para obter mais informações, consulte [Usando o PowerShell do Azure com o Gerenciador de Recursos do Azure](powershell-azure-resource-manager.md).

    Switch-AzureMode AzureResourceManager

As marcas existem diretamente em recursos e grupos de recursos, portanto, para ver quais marcas já estão aplicadas, podemos simplesmente obter um recurso ou grupo de recursos com `Get-AzureResource` ou `Get-AzureResourceGroup`, respectivamente. Vamos começar com um grupo de recursos.

![Obtendo marcas com Get-AzureResourceGroup no PowerShell](./media/resource-group-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

Esse cmdlet retorna vários bits de metadados sobre o grupo de recursos, incluindo quais marcas foram aplicadas, se houver. Para marcar um grupo de recursos, simplesmente usaremos `Set-AzureResourceGroup` e especificaremos um valor e nome de marca.

![Obtendo marcas com Set-AzureResourceGroup no PowerShell](./media/resource-group-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

Lembre-se de que as marcas são atualizadas como um todo, portanto, se você estiver adicionando uma marca a um recurso que já foi marcado, você precisará usar uma matriz com todas as marcas que você deseja manter. Para remover uma marca, simplesmente salve a matriz sem aquela que deseja remover.

O processo é o mesmo para os recursos, exceto pelo fato de que você usará os cmdlets `Get-AzureResource` e `Set-AzureResource`. Para obter recursos ou grupos de recursos com uma marca específica, use o cmdlet `Get-AzureResource` ou `Get-AzureResourceGroup` com o parâmetro `-Tag`.

![Obtendo recursos e grupos de recursos marcados com Get-AzureResource e Get-AzureResourceGroup no PowerShell](./media/resource-group-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## Marcação com a API REST

O portal e o PowerShell usam a [API REST do Gerenciador de Recursos](http://msdn.microsoft.com/library/azure/dn790568.aspx) em segundo plano. Se você precisar integrar a marcação a outro ambiente, você pode obter marcas com um GET na ID do recurso e atualizar o conjunto de marcas com uma chamada de PATCH.


## Gerenciando sua taxonomia

Anteriormente, falamos sobre como o preenchimento automático ajuda você a garantir a consistência e evitar erros. O preenchimento automático é realizado com base na taxonomia da configuração de marcas disponíveis para a assinatura. Cada marca que você adiciona a um recurso ou grupo de recursos é adicionada automaticamente à taxonomia toda a assinatura, mas você também pode realizar um pré-preenchimento dessa taxonomia com nomes de marca e valores que você deseja usar como recursos e serão marcados no futuro.

Para obter uma lista de todas as marcas dentro de uma assinatura usando o PowerShell, use o cmdlet `Get-AzureTag`.

![Get-AzureTag no PowerShell](./media/resource-group-using-tags/Get-AzureTag-in-PowerShell.png)


Você pode ver as marcas que começam com "hidden-" e "link:". Elas são marcas internas, que você deve ignorar e evitar alterar.

Use o cmdlet `New-AzureTag` para adicionar novas marcas à taxonomia. Essas marcas serão incluídas no preenchimento automático, mesmo que elas ainda não tenham sido aplicadas a nenhum recurso ou grupo de recursos. Para remover um nome/valor de uma marca, primeiramente remova a marca de todos os recursos com os quais ela pode ser usada e, em seguida, use o cmdlet `Remove-AzureTag` para removê-la da taxonomia.

Para exibir sua taxonomia de marcas no portal, use o hub Procurar para exibir Tudo e, em seguida, selecionar Marcas.

![Localizar marcas pelo hub Procurar](./media/resource-group-using-tags/browse-tags.png)

Fixe as marcas mais importantes no seu quadro inicial para acesso rápido e você estará pronto para começar. Divirta-se!

![Fixar marcas no Quadro Inicial](./media/resource-group-using-tags/pin-tags.png)

## Marcação e cobrança

Para serviços com suporte, você pode usar marcas para agrupar os dados de cobrança. Por exemplo, [Máquinas Virtuais integrado ao Gerenciador de Recursos do Azure](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md) permitem definir e aplicar marcas para organizar o uso de cobrança para máquinas virtuais. Se você estiver executando várias VMs para organizações diferentes, poderá usar as marcas para o uso do grupo por centro de custo. Você também pode usar marcas para categorizar os custos pelo ambiente de tempo de execução, como por exemplo, o uso de cobrança para VMs em execução no ambiente de produção.

Você pode recuperar informações sobre marcas por meio da [api de uso](billing-usage-rate-card-overview.md) ou o arquivo CSV de uso que pode ser baixado no [Portal de Contas do Azure](https://account.windowsazure.com/) ou [portal EA](https://ea.azure.com).

Quando você baixa o CSV de uso para serviços que dão suporte a marcas de cobrança, as marcas aparecerão na coluna **Marcas**. Para obter mais detalhes, consulte [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md).

![Ver as marcas de cobranças](./media/resource-group-using-tags/billing_csv.png)

## Próximas etapas

- Para obter uma introdução ao uso do Azure PowerShell ao implantar recursos, consulte [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](./powershell-azure-resource-manager.md).
- Para obter uma introdução ao uso da CLI do Azure ao implantar recursos, consulte [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](./xplat-cli-azure-resource-manager.md).
- Para obter uma introdução ao uso do portal de visualização, consulte [Usando o portal de visualização do Azure para gerenciar os recursos do Azure](./resource-group-portal.md)  
  

  

<!---HONumber=August15_HO6-->