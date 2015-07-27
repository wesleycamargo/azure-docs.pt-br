<properties 
	pageTitle="Usar o Portal de Visualização do Azure para gerenciar os recursos do Azure" 
	description="Agrupe vários recursos como um grupo lógico que se torna o limite do ciclo de vida para recursos contidos nele." 
	services="azure-portal" 
	documentationCenter="" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="tomfitz"/>


# Usando o Portal de Visualização do Azure para gerenciar os recursos do Azure

## Introdução

Historicamente, para gerenciar um recurso (uma entidade gerenciada pelo usuário, como um servidor de banco de dados, um banco de dados ou um site) no Microsoft Azure era necessário executar operações em um recurso de cada vez. Se você tivesse um aplicativo complexo composto de vários recursos, o gerenciamento desse aplicativo tornava-se uma tarefa complexa. No Portal de Visualização do Azure, você pode criar grupos de recursos para gerenciar todos os seus recursos em um aplicativo coletivamente. O Grupo de recursos é um novo conceito no Azure que funciona como o limite do ciclo de vida de todos os recursos contidos nele.

Os grupos de recursos permitem que você gerencie todos os seus recursos em um aplicativo, coletivamente. Os grupos de recursos são habilitados pela nova funcionalidade de gerenciamento, o Gerenciador de Recursos do Azure. O Gerenciador de Recursos do Azure permite que você agrupe vários recursos como um grupo lógico que funciona como o limite do ciclo de vida de todos os recursos contidos nele. Normalmente, um grupo contém recursos relacionados a um aplicativo específico. Por exemplo, um grupo pode conter um recurso de Site que hospeda seu site público, um Banco de dados SQL que armazena dados relacionais usados pelo site e uma Conta de armazenamento que armazena ativos não relacionais.

Aqui está uma visão geral concisa de como usar grupos de recursos no Portal do Azure.

## Criando grupos de recursos

Sempre que um recurso é criado no portal, ele sempre é criado dentro de um grupo de recursos. Você pode optar por criar um novo grupo de recursos ou usar um grupo de recursos existente no fluxo de criação.<br><br />

![criar grupo de recursos](./media/resource-group-portal/1_createWebsite.png)

Quando você cria um aplicativo que consiste em alguns recursos que funcionam em conjunto (por exemplo, site + banco de dados) ele sempre é criado no seu próprio grupo de recursos, para que você possa gerenciar o ciclo de vida de todos os ativos relacionados ao grupo de recursos. Você pode adicionar ou remover mais recursos do grupo de recursos à medida que seu aplicativo evolui.

![criar grupo de recursos](./media/resource-group-portal/2_createWSandDB.png)

## Navegando por grupos de recursos

Você pode navegar por todos os grupos de recursos clicando na barra de navegação à esquerda da tela. Um grupo de recursos tem uma lâmina que oferece a você todas as informações sobre um determinado grupo de recursos. A lâmina do Grupo de recursos também fornece uma visão unificada de suas informações de cobrança e monitoramento de todos os recursos do Grupo de recursos.

A seção de resumo mostra um mapa visual de todos os recursos do grupo de recursos. Também mostra recursos em outros grupos de recursos que estão vinculados ao grupo de recursos. O mapa de recursos também mostra o status de cada recurso.![resumo do grupo de recursos](./media/resource-group-portal/3_1BrowseRGs.png)

A parte do mapa de recursos pode ser personalizada para ser exibida em um tamanho maior, que exibirá todos os recursos contidos no grupo de recursos e os recursos em outros grupos de recursos que estão vinculados. Essa parte pode ser fixada no quadro inicial, que copiará a parte para o quadro inicial.

![pin](./media/resource-group-portal/3_2BrowseRGs.png)

Um clique no mapa de recursos inicia o modo de exibição de lista de todos os recursos do mapa de recursos. Esse modo de exibição lista todos os recursos dentro de um grupo de recursos ou vinculados a ele. Clicar sobre esses recursos iniciará suas lâminas.

![consulte recursos](./media/resource-group-portal/3_3BrowseRGs.png)

## Adicionando recursos a grupos de recursos

Você pode adicionar recursos a um grupo de recursos usando o comando **Adicionar** na lâmina do grupo de recursos. Seguindo as etapas do fluxo, você pode adicionar outros recursos ao grupo de recursos.

![adicionar recurso](./media/resource-group-portal/4_AddResource.png)

Observação: não é recomendável colocar um projeto de equipe no mesmo grupo de recursos que outros recursos do Azure. Se você criar um projeto da equipe em uma nova conta e grupo e em seguida criar um site, o grupo de sites será padronizado para o último grupo usado (grupo VSO) e você acabará com recursos de tempo de execução/desenvolvedor no mesmo grupo.

## Excluindo grupos de recursos

Como os grupos de recursos permitem que você gerencie o ciclo de vida de todos os recursos contidos, a exclusão de um grupo de recursos resultará na exclusão de todos os recursos contidos no grupo. Você também pode excluir recursos individuais de um grupo de recursos. Tenha cuidado ao excluir um grupo de recursos pois pode haver outros recursos vinculados a ele. Você pode ver os recursos vinculados no mapa de recursos e tomar as medidas necessárias para evitar consequências não intencionais ao excluir grupos de recursos.

## Próximas etapas
Introdução

- [Visão Geral do Gerenciador de Recursos do Azure](../resource-group-overview.md)  
- [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md)
- [Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure](../xplat-cli-azure-resource-manager.md)  
  
Criação e implantação de aplicativos
  
- [Criação de modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md)  
- [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md)
- [Implantar um aplicativo complexo de modo previsível no Azure](../app-service-web/app-service-deploy-complex-application-predictably.md)
- [Solucionando problemas de implantações de grupos de recursos no Azure](../resource-group-deploy-debug.md)  
- [Funções de modelo do Gerenciador de Recursos do Azure](../resource-group-template-functions.md)  
- [Operações avançadas de modelo](../resource-group-advanced-template.md)  
- [Implantar recursos do Azure usando bibliotecas .NET e um modelo](../arm-template-deployment.md)
  
Organização de recursos
  
- [Usando marcas para organizar os recursos do Azure](../resource-group-using-tags.md)  
  
Gerenciar e auditar o acesso
  
- [Gerenciar e auditar o acesso a recursos](resource-group-rbac.md)  
- [Autenticação de uma entidade de serviço com o Gerenciador de Recursos do Azure](../resource-group-authenticate-service-principal.md)  
- [Criar uma nova entidade de serviço do Azure usando o portal clássico do Azure](../resource-group-create-service-principal-portal.md)  
  


 

<!---HONumber=July15_HO3-->