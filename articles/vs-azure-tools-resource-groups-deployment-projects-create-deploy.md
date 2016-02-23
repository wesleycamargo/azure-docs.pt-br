<properties
   pageTitle="Criação e implantação de projetos do Visual Studio no Grupo de Recursos do Azure | Microsoft Azure"
   description="Use o Visual Studio para criar um projeto do grupo de recursos do Azure e implantar os recursos no Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor="" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="tomfitz" />

# Criação e implantação de grupos de recurso do Azure por meio do Visual Studio

Com o Visual Studio e o [SDK do Azure](https://azure.microsoft.com/downloads/), você pode criar um projeto que implementa sua infraestrutura e o código no Azure. Por exemplo, você pode definir o host da Web, o site da Web e o banco de dados para seu aplicativo, e implantar essa infraestrutura juntamente com o código. Ou pode definir uma Máquina Virtual, Rede Virtual e a Conta de Armazenamento, e implantar essa infraestrutura juntamente com um script que é executado na Máquina Virtual. O projeto de implantação **Grupo de Recursos do Azure** permite implantar todos os recursos necessários em uma única operação repetida. Para obter mais informações sobre como implantar e gerenciar seus recursos, confira [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md).

Os projetos do Grupo de Recursos do Azure contêm modelos JSON do Gerenciador de Recursos do Azure, que definem os recursos que são implantados em um Azure. Para saber mais sobre os elementos do modelo do Gerenciador de Recursos, confira [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md). O Visual Studio permite editar esses modelos e fornece ferramentas que simplificam o trabalho com eles.

Neste tópico, você implantará um aplicativo Web e o Banco de Dados SQL. No entanto, as etapas são praticamente as mesmas para qualquer tipo de recurso. Você pode implantar facilmente uma Máquina Virtual e seus recursos relacionados. O Visual Studio fornece muitos modelos iniciais diferentes para implantar cenários comuns.

## Criar um projeto do Grupo de Recursos do Azure

Neste procedimento, você criará um projeto do Grupo de Recursos do Azure com um modelo do **aplicativo Web + SQL**.

1. No Visual Studio, escolha **Arquivo**, **Novo Projeto**, escolha **C#** ou **Visual Basic**. Então escolha **Nuvem**, e, em seguida, escolha projeto de **Grupo de Recursos do Azure**.

    ![Projeto do Cloud Deployment](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796668.png)

1. Escolha o modelo que você deseja implantar no Gerenciador de Recursos do Azure. Observe que há várias opções diferentes com base no tipo de projeto que você deseja implantar. Para este tópico, escolheremos o modelo **aplicativo Web + SQL**.

    ![Selecionar um modelo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    O modelo que você escolhe é apenas um ponto de partida. Você pode adicionar e remover os recursos para atender a seu cenário.

    >[AZURE.NOTE] A lista de modelos disponíveis é recuperada online e pode sofrer alterações.

    O Visual Studio cria um projeto de implantação do grupo de recursos para o aplicativo Web e o banco de dados SQL.

1. Expanda os nós no projeto de implantação para ver o que foi criado.

    ![mostrar nós](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Como escolhemos o modelo do aplicativo Web + SQL para este exemplo, você vê os arquivos a seguir.

    |Nome do arquivo|Descrição|
    |---|---|
    |Deploy-AzureResourceGroup.ps1|Um script do PowerShell que invoca comandos do PowerShell para implantar no Gerenciador de Recursos do Azure.<br />**Observação** Esse script do PowerShell é usado pelo Visual Studio para implantar o modelo. As alterações feitas no script também afetarão a implantação no Visual Studio. Portanto, tenha cuidado.|
    |WebSiteSQLDatabase.json|O modelo do gerenciador de recursos que define a infraestrutura que você deseja implantar no Azure e os parâmetros que você pode fornecer durante a implantação. Também define as dependências entre os recursos para que eles sejam implantados na ordem correta.|
    |WebSiteSQLDatabase.parameters.json|Um arquivo de parâmetros que contém os valores necessários ao modelo. São os valores que você passa para personalizar cada implantação.|
    |AzCopy.exe|Uma ferramenta usada pelo script do PowerShell para copiar arquivos do caminho de depósito do armazenamento local para o contêiner da conta de armazenamento. Essa ferramenta será usada somente se você configurar o projeto de implantação para implantar seu código juntamente com o modelo.|

    Todos os projetos de implantação do grupo de recursos do Azure contêm esses quatro arquivos básicos. Outros projetos podem conter arquivos adicionais para dar suporte a outras funcionalidades.

## Personalizar o modelo do Gerenciador de Recursos

Você pode personalizar um projeto de implantação modificando os modelos JSON que descrevem os recursos que você deseja implantar. JSON significa JavaScript Object Notation e é um formato de dados serializados, com o qual é fácil de trabalhar. Os arquivos JSON usam um esquema que é referenciado na parte superior de cada arquivo. Você pode baixar o esquema e analisá-lo se desejar entendê-lo melhor. O esquema define quais elementos são permitidos, os tipos e formatos de campos, os valores possíveis de valores enumerados e assim por diante. Para saber mais sobre os elementos do modelo do Gerenciador de Recursos, confira [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).

Para trabalhar em seu modelo, abra o **WebSiteSQLDatabase.json**.

O editor do Visual Studio fornece ferramentas para ajudá-lo a editar o modelo do gerenciador de recursos. A janela **Estrutura de Tópicos JSON** facilita ver os elementos definidos no modelo.

![mostrar estrutura de tópicos JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

Selecionar qualquer um dos elementos na estrutura de tópicos leva você para essa parte do modelo e destaca o JSON correspondente.

![navegar o JSON](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Você pode adicionar um novo recurso ao seu modelo selecionando o botão **Adicionar Recurso** na parte superior da janela Estrutura de Tópicos JSON ou clicando com o botão direito em **recursos** e selecionando **Adicionar Novo Recurso**.

![adicionar recurso](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

Para este tutorial, selecione **Conta de Armazenamento** e forneça um nome. Um nome da conta de armazenamento deve ter apenas números, letras minúsculas e menos de 24 caracteres. O projeto adicionará uma cadeia exclusiva de 13 caracteres ao nome que você forneceu, portanto, verifique se o nome não tem mais de 11 caracteres.

![adicionar armazenamento](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Observe que não só foi adicionado o recurso, mas também um parâmetro para o tipo da conta de armazenamento e uma variável para o nome da conta de armazenamento.

![mostrar estrutura de tópicos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

O parâmetro **WebSitePicturesType** é predefinido com os tipos permitidos e um tipo padrão. Você pode deixar esses valores ou editá-los para seu cenário. Se você não quiser permitir que ninguém implante uma conta de armazenamento **Premium\_LRS** com esse modelo, simplesmente remova-o dos tipos permitidos, conforme mostrado abaixo.

    "WebSitePicturesType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS"
      ]
    }

O Visual Studio também fornece o intellisense para ajudá-lo a entender quais propriedades estão disponíveis ao editar o modelo. Por exemplo, para editar as propriedades de seu plano do Serviço de Aplicativo, navegue até o recurso **HostingPlan** e adicione um novo valor para as **propriedades**. Observe que o intellisense mostra os valores disponíveis e fornece uma descrição desse valor.

![mostrar intellisense](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Você pode definir **numberOfWorkers** para 1.

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## Implantar o projeto do Grupo de Recursos para o Azure

Agora, você está pronto para implantar seu projeto. Quando você implanta um projeto do Grupo de Recursos do Azure, você o implanta em um grupo de recursos do Azure, que é apenas um agrupamento lógico de recursos no Azure tais como aplicativos Web, bancos de dados e assim por diante.

1. No menu de atalho do nó do projeto de implantação, escolha **Implantar**, **Nova Implantação**.

    ![Item de menu Implantar, Nova Implantação](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796672.png)

    A caixa de diálogo **Implantar no Grupo de Recursos** é exibida.

    ![Caixa de diálogo Implantar no Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. Na caixa suspensa de **Grupo de recursos**, escolha um grupo de recursos existente ou crie um novo. Para criar um grupo de recursos, abra a caixa suspensa **Grupo de Recursos** e escolha **<Create New...>**.

    ![Caixa de diálogo Implantar no Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)

    A caixa de diálogo **Criar Grupo de Recursos** é exibida. Forneça ao grupo um nome e local, e selecione o botão **Criar**.

    ![Caixa de diálogo Criar Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
   
1. Você pode editar os parâmetros da implantação escolhendo o botão **Editar Parâmetros**. Forneça valores para os parâmetros e selecione o botão **Salvar**.

    ![Caixa de diálogo Editar Parâmetros](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
    
    A opção **Salvar senhas** significa que as senhas serão salvas como texto sem formatação no arquivo JSON. Esta opção não é segura.

1. Escolha o botão **Implantar** para implantar o projeto no Azure. Você pode ver o andamento da implantação na janela **Saída**. A implantação pode levar vários minutos para ser concluída, dependendo da configuração. Se solicitado, insira a senha do administrador do banco de dados.

    >[AZURE.NOTE] Você pode ser solicitado a instalar os cmdlets do Azure PowerShell. Já que esses cmdlets são necessários para implantar grupos de recursos do Azure, você precisa instalá-los.
    
1. Quando a implantação for concluída, você verá uma mensagem na janela **Saída** como:

        ...
        15:19:19 - DeploymentName     : websitesqldatabase-0212-2318
        15:19:19 - CorrelationId      : 6cb43be5-86b4-478f-9e2c-7e7ce86b26a2
        15:19:19 - ResourceGroupName  : DemoSiteGroup
        15:19:19 - ProvisioningState  : Succeeded
        ...

1. Em um navegador, abra o [Portal do Azure](https://portal.azure.com/) e entre em sua conta. Para ver o grupo de recursos, selecione **Grupos de recursos** e o grupo de recursos implantado.

    ![selecionar grupo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)

1. Você verá todos os recursos implantados.

    ![mostrar recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

1. Se você fizer alterações e desejar reimplantar seu projeto, você poderá escolher o grupo de recursos existente diretamente do menu de atalho do projeto de grupo de recursos do Azure. No menu de atalho, escolha **Implantar** e então escolha o grupo de recursos para o qual você recém-realizou a implantação.

    ![Grupo de recursos do Azure implantado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## Implantar o código com sua infraestrutura

Neste ponto, você implantou a infraestrutura de seu aplicativo, mas não há nenhum código real implantado com o projeto. Este tópico mostra como implantar um aplicativo Web e as tabelas do Banco de Dados SQL durante a implantação. Se você estiver implantando uma Máquina Virtual em vez de um aplicativo Web, desejará executar um código no computador como parte da implantação. O processo de implantação do código para um aplicativo Web ou configurar uma Máquina Virtual é quase o mesmo.

1. Em sua solução do Visual Studio, adicione um **Aplicativo Web ASP.NET**. 

    ![adicionar aplicativo Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
    
1. Selecione **MVC** e desmarque o campo **Host na nuvem** porque o projeto do grupo de recursos executará essa tarefa.

    ![selecionar MVC](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
    
1. Depois do aplicativo Web ter sido criado, adicione uma referência, no projeto do grupo de recursos, ao projeto do aplicativo Web.

    ![adicionar referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
    
    Adicionando uma referência, você vincula o projeto do aplicativo Web ao projeto do grupo de recursos e define três propriedades principais. As **Propriedades Adicionais** contêm o pacote de implantação da Web local que será enviado para o Armazenamento do Azure. **Incluir Caminho do Arquivo** contém o caminho onde o pacote será criado. **Incluir Destinos** contém o comando que a implantação executará. O valor padrão de **Build;Package** permite que a implantação compile e crie um pacote de implantação da Web (package.zip). Um perfil de publicação não é necessário porque a implantação obtém as informações necessárias das propriedades para criar o pacote.
    
      ![ver referência](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
      
1. Adicione um novo recurso ao modelo e, desta vez, selecione **Implantação da Web para Aplicativos Web**.

    ![adicionar implantação da Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
    
1. Implante novamente o projeto do grupo de recursos para o grupo de recursos. Desta vez, há alguns parâmetros novos. Você não precisa fornecer valores para **\_artifactsLocation** ou **\_artifactsLocationSasToken**, pois eles são gerados automaticamente. Defina o nome da pasta e do arquivo para o caminho que contém o pacote de implantação.

    ![adicionar implantação da Web](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
    
    Para a **Conta de armazenamento do artefato**, você pode usar a implantada com esse grupo de recursos.
    
Após a implantação terminar, você poderá navegar para o site e observar se o aplicativo ASP.NET padrão foi implantado.

![mostrar aplicativo implantado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## Próximas etapas

- Para saber mais sobre como gerenciar seus recursos com o portal, confira [Usando o Portal do Azure para gerenciar os recursos do Azure](./azure-portal/resource-group-portal.md).
- Para saber mais sobre os modelos, confira [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0218_2016-->