<properties
   pageTitle="Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Descreve o Gerenciador de Recursos do Azure e como ele pode ser usado para exibir e atualizar implantações por meio do Azure Resource Manager"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="stuartleeks"
   manager="ankodu"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="stuartle;tomfitz"/>

# Usar o Gerenciador de Recursos do Azure para exibir e modificar recursos
O [Gerenciador de Recursos do Azure](https://resources.azure.com) é uma excelente ferramenta para procurar nos recursos criados em sua assinatura. Com essa ferramenta, é possível entender como os recursos são estruturados e ver as propriedades atribuídas a cada um deles. É possível saber mais sobre as operações API REST e os cmdlets do PowerShell disponíveis para um tipo de recurso, e também emitir comandos por meio da interface. O Gerenciador de Recursos pode ser particularmente útil quando estiver criando modelos do Gerenciador de Recursos, já que ele permite exibir as propriedades dos recursos existentes.

A fonte da ferramenta do Gerenciador de Recursos está disponível no [GitHub](https://github.com/projectkudu/ARMExplorer), que fornece uma valiosa referência caso precise implementar um comportamento semelhante em seus próprios aplicativos.

## Exibir recursos
Navegue até [https://resources.azure.com](https://resources.azure.com) e entre com as mesmas credenciais usadas para o [Portal do Azure](https://portal.azure.com).

Depois de carregado, o modo de exibição de árvore à esquerda permite fazer uma busca detalhada em suas assinaturas e seus grupos de recursos:

![modo de exibição de árvore](./media/resource-manager-resource-explorer/are-01-treeview.png)

À medida que faz essa busca detalhada em um grupo de recursos, você verá os provedores para os quais há recursos nesse grupo:

![providers](./media/resource-manager-resource-explorer/are-02-treeview-providers.png)

Nesse local, é possível fazer uma busca detalhada nas instâncias de recurso. Na captura de tela abaixo, é possível ver a instância do `sltest` SQL Server no modo de exibição de árvore. No lado direito, é possível ver informações sobre as solicitações API REST que podem ser usadas com esse recurso. Ao navegar até o nó de um recurso, o Gerenciador de Recursos, automaticamente, faz com que a solicitação GET recupere informações sobre o recurso. Na área de texto grande abaixo da URL, você verá a resposta da API.

À medida que se familiariza com os modelos do Resource Manager, você vai se acostumando com o conteúdo do corpo! A seção **propriedades** da resposta corresponde aos valores que podem ser fornecidos na seção **propriedades** do modelo.

![sql server](./media/resource-manager-resource-explorer/are-03-sqlserver-with-response.png)

O Gerenciador de Recursos permite que você continue fazendo uma busca detalhada para explorar recursos filho; no caso do servidor do Banco de Dados SQL, há recursos filho para itens como bancos de dados e regras de firewall.

Ao explorar um banco de dados, são mostradas suas propriedades. Na captura de tela abaixo, podemos ver que o banco de dados `edition` é `Standard` e que o `serviceLevelObjective` (ou a camada de banco de dados) é `S1`.

![banco de dados sql](./media/resource-manager-resource-explorer/are-04-database-get.png)

## Alterar os recursos

Depois de navegar até um recurso, é possível selecionar o botão Editar para tornar o conteúdo JSON editável. Em seguida, é possível usar o Gerenciador de Recursos para editar o JSON e enviar uma solicitação PUT para alterar o recurso. Por exemplo, a imagem abaixo mostra a camada de banco de dados alterada para `S0`:

![banco de dados – solicitação PUT](./media/resource-manager-resource-explorer/are-05-database-put.png)

Ao selecionar **PUT**, a solicitação é enviada.

Depois que a solicitação tiver sido enviada, o Gerenciador de Recursos emitirá novamente a solicitação GET para atualizar o status. Nesse caso, podemos ver que a `requestedServiceObjectiveId` foi atualizada e que é diferente da `currentServiceObjectiveId`, indicando que uma operação de escala está em andamento. É possível clicar no botão GET para atualizar o status manualmente.

![banco de dados – solicitação GET 2](./media/resource-manager-resource-explorer/are-06-database-get2.png)

## Executando Ações em recursos

A guia **Ações** permite ver e executar outras operações REST. Por exemplo, após a seleção de um recurso de site, a guia Ações apresentará uma longa lista de operações disponíveis, algumas das quais são mostradas abaixo.

![web – solicitação POST](./media/resource-manager-resource-explorer/are-web-post.png)

## Invocando a API por meio do PowerShell
A guia PowerShell no Gerenciador de Recursos mostra os cmdlets a ser usados para interagir com o recurso que está sendo explorado no momento. Dependendo do tipo de recurso selecionado, o script do PowerShell exibido poderá variar de cmdlets simples (como `Get-AzureRmResource` e `Set-AzureRmResource`) até cmdlets mais complicados (como alternância de slots em um site).

![PowerShell](./media/resource-manager-resource-explorer/are-07-powershell.png)

Para obter mais informações sobre os cmdlets do Azure PowerShell, veja [Usando o Azure PowerShell com o Azure Resource Manager](powershell-azure-resource-manager.md)

## Resumo
Ao trabalhar com o Resource Manager, o Gerenciador de Recursos pode ser uma ferramenta muito útil. É uma ótima maneira de encontrar formas de usar o PowerShell para consultar e fazer alterações. Se estiver trabalhando com a API REST, será uma ótima maneira de começar e testar rapidamente as chamadas à API antes de começar a escrever código. E caso esteja escrevendo modelos do Resource Manager, essa pode ser uma ótima maneira de entender a hierarquia de recursos e encontrar o local em que a configuração deve ser colocada – é possível fazer uma alteração no Portal e encontrar as entradas correspondentes no Gerenciador de Recursos!

Para obter mais informações, assista ao [vídeo do Channel 9 com Scott Hanselman e David Ebbo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo)

<!---HONumber=AcomDC_0803_2016-->