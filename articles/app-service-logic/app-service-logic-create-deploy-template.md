<properties
   pageTitle="Criar Modelo de Implantação dos Aplicativos Lógicos | Microsoft Azure"
   description="Saiba como os modelos de implantação dos Aplicativos Lógicos são criados e podem ser usados no gerenciamento da versão."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/25/2016"
   ms.author="jehollan"/>
   
# Criar Modelos de Implantação dos Aplicativos Lógicos

Assim que um Aplicativo Lógico for criado, você poderá querer criá-lo como um Modelo de Recurso do Azure para implantar facilmente o Aplicativo Lógico em qualquer ambiente ou grupo de recursos necessário. Para ver uma introdução dos modelos do Resource Manager, verifique os artigos em [Criando um modelo do Azure Resource Manager](../resource-group-authoring-templates.md) e [Implantar recursos com o modelo do Azure Resource Manager](../resource-group-template-deploy.md)

## Visão Geral do Modelo de Implantação do Aplicativo Lógico

Um Aplicativo Lógico tem três componentes básicos:

* **Recurso do Aplicativo Lógico** - o recurso que contém informações sobre coisas, como plano de preços, local e definição do fluxo de trabalho.
* **Definição do Fluxo de Trabalho** - o que é visto quando você escolhe **Visualização do Código**, a definição das etapas do fluxo e como o mecanismo deve ser executado. Essa é a propriedade `definition` do Recurso do Aplicativo Lógico.
* **Conexões** - separe os recursos para armazenar com segurança os metadados ao redor de todas as conexões do conector, como as cadeias de conexão e os tokens de acesso. Elas são referenciadas em um Aplicativo Lógico em `parameters` do Recurso do Aplicativo Lógico.

Você pode exibir todas essas partes dos Aplicativos Lógicos existentes usando ferramentas como o [Azure Resource Explorer](http://resources.azure.com).

Para criar um modelo para um Aplicativo Lógico que pode trabalhar com as implantações do grupo de recursos, você precisa definir os recursos e aplicar os parâmetros, quando necessário. Por exemplo, ao implantar um ambiente de desenvolvimento, teste e produção, provavelmente você desejará usar diferentes cadeias de conexão para um banco de dados SQL em cada ambiente ou implantar em diferentes assinaturas ou grupos de recursos.

## Criando um Modelo de Implantação do Aplicativo Lógico

Existem algumas ferramentas para ajudá-lo na criação de um Modelo de Implantação do Aplicativo Lógico. Você pode criar à mão colocando os recursos acima e criando parâmetros quando necessário. Você também pode usar um módulo do PowerShell do [Criador de Modelo de Aplicativo Lógico](https://github.com/jeffhollan/LogicAppTemplateCreator). O módulo de fonte aberta funciona avaliando o Aplicativo Lógico e as conexões que ele está usando e gerando recursos do modelo com os parâmetros necessários para a implantação. Por exemplo, se você tivesse um Aplicativo Lógico que recebeu uma mensagem de uma Fila do Barramento de Serviço e adicionasse dados a um banco de dados SQL do Azure, a ferramenta preservaria toda a lógica de orquestração e aplicaria o parâmetro no SQL e nas cadeias de conexão do Barramento de Serviço para que pudessem ser definidos na implantação.

>[AZURE.NOTE] As conexões devem estar dentro do mesmo Grupo de Recursos do Aplicativo Lógico

### Instalando o Módulo do PowerShell do Modelo do Aplicativo Lógico

A maneira mais fácil de instalar é por meio da [Galeria do PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1) com o comando `Install-Module -Name LogicAppTemplate`.

Você também pode instalar manualmente:

1. Baixe a versão mais recente do [Criador de Modelo do Aplicativo Lógico](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
1. Extrair a pasta para sua pasta do módulo do PowerShell (geralmente `%UserProfile%\Documents\WindowsPowerShell\Modules`)

Para que o módulo funcione com qualquer token de acesso do locatário e da assinatura, recomenda-se usar com a ferramenta da linha de comando [armclient](https://github.com/projectkudu/ARMClient). Os detalhes sobre o `armclient` podem ser encontrados [aqui](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### Gerando um Modelo do Aplicativo Lógico por meio do PowerShell

Após a instalação, você já poderá gerar um modelo com o seguinte comando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

Em que `$SubscriptionId` é a ID da Assinatura do Azure. Primeiro, essa linha obtém um token de acesso via `armclient`, direcionando-o para o script do PowerShell e, em seguida, produzindo o modelo em um arquivo `.json`.

## Adicionando Parâmetros a um Modelo do Aplicativo Lógico

Assim que você tiver um modelo do Aplicativo Lógico, poderá continuar a adicionar ou modificar quaisquer parâmetros adicionais que possam ser necessários. Por exemplo, se sua definição incluir uma ID do recurso para uma Função do Azure ou um Fluxo de Trabalho Aninhado onde planeja colocar uma única implantação, você desejaria acrescentar recursos adicionais ao modelo e aplicar parâmetros nas IDs quando necessário. O mesmo é válido para todas as referências às APIs personalizadas ou pontos de extremidade swagger que você espera implantar com cada grupo de recursos.

## Implantando um Modelo do Aplicativo Lógico

Após obter um modelo, você poderá implantar usando várias ferramentas, incluindo o PowerShell, API REST, Visual Studio, Gerenciamento de Versão ou Implantação do Modelo do Portal do Azure. Os detalhes sobre a implantação podem ser encontrados [aqui](../resource-group-template-deploy.md). Recomenda-se também criar um [arquivo de parâmetro](../resource-group-template-deploy.md#parameter-file) para armazenar valores do parâmetro.

### Autorizando Conexões OAuth

Após a implantação, o Aplicativo Lógico funcionará totalmente com parâmetros válidos, mas as conexões OAuth ainda precisarão ser autorizadas a gerar um token de acesso válido. Para fazer isso, abra o Aplicativo Lógico no designer e autorize as conexões ou, se quiser automatizar, também poderá usar um script para consentir cada conexão OAuth. Um script de exemplo pode ser encontrado no GitHub no projeto [Conexão Auth](https://github.com/logicappsio/LogicAppConnectionAuth).

## Usando o Gerenciamento de Versão do Visual Studio

Um cenário comum para implantar e gerenciar ambientes é usar uma ferramenta como o Gerenciamento de Versão do Visual Studio com um modelo de implantação do Aplicativo Lógico. O VSTS inclui uma tarefa [Implantar Grupo de Recursos do Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) que pode ser adicionada a qualquer compilação ou pipeline da versão. Você precisa ter uma [Entidade de Serviço](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) da autorização a implantar e pode, então, gerar a definição da versão.

1. Em **Versão**, escolha iniciar uma **Nova Definição** com uma definição **Vazia**.

    ![][1]

1. Escolha qualquer artefato necessário para essa finalidade. Esse artefato provavelmente seria o Modelo do Aplicativo Lógico gerado manualmente ou como parte do processo de compilação.
1. Adicione uma tarefa **Implantação do Grupo de Recursos do Azure**.
1. Configure com uma [Entidade de Serviço](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) e faça referência aos arquivos **Modelo** e **Parâmetros do Modelo**.
1. Continue a compilar as etapas no processo de versão para quaisquer outros ambientes, testes automatizados ou aprovadores, conforme necessário.
    
<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG

<!---HONumber=AcomDC_0601_2016-->