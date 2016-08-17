<properties
   pageTitle="Criar um modelo de implantação do aplicativo lógico | Microsoft Azure"
   description="Saiba como criar um modelo de implantação do aplicativo lógico e usá-lo para gerenciamento de versão"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/25/2016"
   ms.author="jehollan"/>

# Criar um modelo de implantação do aplicativo lógico

Após um aplicativo lógico ter sido criado, talvez você queira criá-lo como um modelo do Azure Resource Manager. Dessa forma, você pode facilmente implantar o aplicativo lógico em qualquer ambiente ou grupo de recursos em que você possa precisar dele. Para ver uma introdução aos modelos do Resource Manager, confira os artigos em [criando modelos do Azure Resource Manager](../resource-group-authoring-templates.md) e [implantando recursos usando modelos do Azure Resource Manager](../resource-group-template-deploy.md).

## Modelo de implantação do aplicativo lógico

Um aplicativo lógico tem três componentes básicos:

* **Recurso de aplicativo lógico**. Este recurso contém informações sobre coisas como plano de preços, localização e definição do fluxo de trabalho.
* **Definição de fluxo de trabalho**. Isso é o que é mostrado na exibição de código. Isso inclui a definição das etapas do fluxo e de como o mecanismo deve ser executado. Essa é a propriedade `definition` do recurso do aplicativo lógico.
* **Conexões**. Esses são recursos separados para armazenar com segurança os metadados sobre quaisquer conexões do conector, como uma cadeia de conexão e um token de acesso. Eles são referenciados em um aplicativo lógico na seção `parameters` do recurso do aplicativo lógico.

Você pode exibir todas essas partes dos aplicativos lógicos existentes usando ferramentas como o [Gerenciador de Recursos do Azure](http://resources.azure.com).

Para criar um modelo para um aplicativo lógico para usar com as implantações do grupo de recursos, você precisa definir os recursos e parametrizar, conforme necessário. Por exemplo, se você estiver implantando para um ambiente de desenvolvimento, teste e produção, provavelmente você desejará usar diferentes cadeias de conexão para um banco de dados SQL em cada ambiente. Ou talvez você queira fazer implantações em diferentes assinaturas ou grupos de recursos.

## Criar um modelo de implantação do aplicativo lógico

Algumas ferramentas podem ajudar você a criar um modelo de implantação de aplicativo lógico. Você pode criar manualmente, ou seja, usando os recursos já discutidos aqui para criar parâmetros conforme necessário. Você também pode usar um módulo do PowerShell do [criador de modelos de aplicativo lógico](https://github.com/jeffhollan/LogicAppTemplateCreator). Este módulo de software livre primeiro avalia o aplicativo lógico e quaisquer conexões que ele esteja usando, depois gera recursos de modelo com os parâmetros necessários para a implantação. Por exemplo, se você tivesse um aplicativo lógico que recebesse uma mensagem de uma fila do Barramento de Serviço do Azure e adicionasse dados a um banco de dados SQL do Azure, a ferramenta preservaria toda a lógica de orquestração e parametrizaria as cadeias de conexão do SQL e do Barramento de Serviço para que elas pudessem ser definidas na implantação.

>[AZURE.NOTE] As conexões devem estar dentro do mesmo grupo de recursos que o aplicativo lógico.

### Instalar o módulo do PowerShell do modelo de aplicativo lógico

A maneira mais fácil de instalar é por meio da [Galeria do PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), com o comando `Install-Module -Name LogicAppTemplate`.

Você também pode instalar o módulo do PowerShell manualmente:

1. Baixe a versão mais recente do [criador de modelos de aplicativo lógico](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).
1. Extraia a pasta para sua pasta do módulo do PowerShell (geralmente `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Para que o módulo funcione com qualquer token de acesso do locatário e da assinatura, recomenda-se usá-lo com a ferramenta de linha de comando [ARMClient](https://github.com/projectkudu/ARMClient). Essa [postagem de blog](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) discute o ARMClient mais detalhadamente.

### Gerar um modelo de aplicativo lógico usando o PowerShell

Depois que o PowerShell for instalado, você poderá gerar um modelo usando o seguinte comando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` é a ID da assinatura do Azure. Essa linha primeiro obtém um token de acesso via ARMClient, depois redireciona o script do PowerShell e, em seguida, cria o modelo em um arquivo JSON.

## Adicionar parâmetros a um modelo de aplicativo lógico

Depois de criar o modelo de aplicativo lógico, você pode continuar a adicionar ou modificar os parâmetros de que pode precisar. Por exemplo, se sua definição incluir uma ID do recurso para uma função do Azure ou um fluxo de trabalho aninhado que você planeja colocar em uma única implantação, você poderá adicionar mais recursos ao modelo e parametrizar as IDs conforme necessário. O mesmo aplica-se a quaisquer referências às APIs personalizadas ou pontos de extremidade Swagger que você espera implantar com cada grupo de recursos.

## Implantar um modelo de aplicativo lógico

Você pode implantar seu modelo usando qualquer número de ferramentas, incluindo o PowerShell, a API REST, o Visual Studio, o Release Management para Visual Studio e a Implantação de Modelo do Portal do Azure. Consulte este artigo sobre [implantação de recursos usando modelos do Azure Resource Manager](../resource-group-template-deploy.md) para obter informações adicionais. Recomenda-se também criar um [arquivo de parâmetro](../resource-group-template-deploy.md#parameter-file) para armazenar valores para o parâmetro.

### Autorizar Conexões OAuth

Após a implantação, o aplicativo lógico funciona de ponta a ponta com parâmetros válidos. No entanto, ainda será necessário autorizar as conexões OAuth para gerar um token de acesso válido. Você pode fazer isso abrindo o aplicativo lógico no designer e, em seguida, autorizando as conexões. Ou, se você deseja automatizar, você poderá usar um script para dar consentimento a cada conexão OAuth. Há um script de exemplo no GitHub sob o projeto [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth).

## Release Management para Visual Studio

Um cenário comum para implantar e gerenciar um ambiente é usar uma ferramenta como o Release Management para Visual Studio com um modelo de implantação de aplicativo lógico. O Visual Studio Team Services inclui uma tarefa [Implantar Grupo de Recursos do Azure](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) que pode ser adicionada a qualquer build ou pipeline da versão. Você precisa ter uma [entidade de serviço](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) da autorização a implantar e pode, então, gerar a definição da versão.

1. No Release Management, para criar uma nova definição, selecione **Vazio** para iniciar com uma definição vazia.

    ![Criar uma definição de nova e vazia][1]

1. Escolha quaisquer recursos necessários para essa finalidade. Esse provavelmente será o modelo de aplicativo lógico gerado manualmente ou como parte do processo de build.
1. Adicione uma tarefa de **Implantação do Grupo de Recursos do Azure**.
1. Configure com uma [Entidade de Serviço](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) e faça referência aos arquivos de Modelo e de Parâmetros do Modelo.
1. Continue a compilar as etapas no processo de versão para quaisquer outros ambientes, testes automatizados ou aprovadores, conforme necessário.

<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG

<!---HONumber=AcomDC_0803_2016-->