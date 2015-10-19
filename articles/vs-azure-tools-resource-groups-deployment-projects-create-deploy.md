<properties
   pageTitle="Criando e implantando projetos de implantação de Grupo de Recursos do Azure | Microsoft Azure"
   description="Criando e implantando projetos de implantação de Grupo de Recursos do Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tlee" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/02/2015"
   ms.author="kempb" />

# Criando e implantando projetos de implantação de Grupo de Recursos do Azure

O modelo de projeto de implantação de **grupo de recursos do Azure** está disponível no Visual Studio quando o Azure SDK 2.6 está instalado. Os projetos do grupo de recursos do Azure permitem agrupar e publicar vários recursos do Azure relacionados em uma única operação de implantação. Os projetos do grupo de recursos do Azure usam uma tecnologia chamada **Gerenciador de Recursos do Azure** para realizar seu trabalho. O **Gerenciador de Recursos do Azure** é um serviço de API REST que permite que você defina grupos de recursos do Azure, que contêm vários recursos do Azure que normalmente são usados juntos e têm um ciclo de vida semelhante. Usando grupos de recursos, você pode operar em todos os recursos em um grupo com uma única chamada de função, em vez de chamar funções diferentes para cada recurso individual. Para saber mais sobre grupos de recursos do Azure, consulte [Usando o Portal de visualização do Azure para gerenciar os recursos do Azure](resource-group-portal.md). Para um cenário de implantação de Grupo de Recursos do Azure mais detalhado, de ponta a ponta, consulte [Grupo de Recursos do Azure para Visual Studio](https://azure.microsoft.com/pt-BR/blog/azure-resource-manager-2-5-for-visual-studio/).

Os projetos de grupo de recursos contêm modelos JSON do Gerenciador de Recursos do Azure, que definem os elementos que são implantados em um grupo de recursos. Consulte [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md) para obter mais informações.

O Gerenciador de Recursos do Azure tem muitos diferentes provedores de recursos disponíveis que podem ser usados para implantar recursos como o Ubuntu Server e o Windows Server 2012 R2. Este tópico usa um recurso **Aplicativos Web**, que implanta um site básico e vazio no Azure.

## Criando projetos de Grupo de Recursos do Azure

Neste procedimento, você aprenderá a criar um projeto de Grupo de Recursos do Azure com um modelo de **Aplicativo Web**.

### Para criar um projeto de Grupo de Recursos do Azure

1. No Visual Studio, escolha **Arquivo**, **Novo Projeto**, escolha **C#** ou **Visual Basic**. Então escolha **Nuvem**, e, em seguida, escolha projeto de **Grupo de Recursos do Azure**.

    ![Projeto do Cloud Deployment](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796668.png)

1. Escolha o modelo que você deseja implantar no Gerenciador de Recursos do Azure. Neste exemplo, escolheremos o modelo **Aplicativo Web**.

    ![Selecionar um modelo](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796669.png)

    Você também pode adicionar mais recursos ao Grupo de Recursos mais tarde.

    >[AZURE.NOTE]A lista de modelos disponíveis é recuperada online e pode sofrer alterações.

    O Visual Studio cria um projeto de implantação do Grupo de Recursos do Azure para um aplicativo Web.

1. Expanda os nós no projeto de implantação para ver o que foi criado.

    Como escolhemos o modelo de aplicativo Web para este exemplo, você vê os arquivos a seguir.

    |Nome do arquivo|Descrição|
    |---|---|
    |Deploy-AzureResourceGroup.ps1|Um script do PowerShell que invoca comandos do PowerShell a implantar para o Gerenciador de Recursos do Azure.

    **Observação**: esse script do PowerShell é usado pelo Visual Studio para implantar o seu modelo. Qualquer alteração que você faça a esse script também afetará a implantação no Visual Studio, então tome cuidado. | ! WebSite.json|Um arquivo de configuração que especifica todos os detalhes que você deseja que sejam implantados no Gerenciador de Recursos do Azure.| |WebSite.param.dev.json|Um arquivo de parâmetros que contém valores específicos requeridos pelo arquivo de configuração.| |AzCopy.exe|Uma ferramenta usada pelo script do PowerShell para copiar arquivos do caminho de depósito de armazenamento local para o contêiner da conta de armazenamento. Essa ferramenta é usada somente se você configurar o projeto de implantação para implantar seu código juntamente com o modelo.|

    Todos os projetos de implantação de Grupo de Recursos do Azure contêm esses quatro arquivos básicos. Outros projetos podem conter arquivos adicionais para dar suporte a outras funcionalidades.

## Personalizando um projeto de Grupo de Recursos do Azure

Você pode personalizar um projeto de implantação modificando os arquivos de modelo JSON que descrevem os recursos do Azure que você deseja implantar. JSON significa JavaScript Object Notation e é um formato de dados serializados, com o qual é fácil de trabalhar.

Os projetos do Grupo de Recursos do Azure têm dois arquivos de modelo sob o nó **modelos** no Gerenciador de Soluções que podem ser modificados: um arquivo de modelo do Gerenciador de Recursos do Azure e um arquivo de parâmetros.

- **Arquivos de modelo do Gerenciador de Recursos do Azure** (que tem a extensão .json) especificam o(s) arquivo(s) contendo os recursos desejados, bem como os parâmetros necessários para o projeto de implantação, como o local e nome do site. Eles também especificam as dependências dos componentes do Grupo de Recursos do Azure e suas propriedades, como nomes, marcas e regras para gatilhos. Você pode modificar esse arquivo para adicionar sua própria funcionalidade. Por exemplo, você pode adicionar um banco de dados ao modelo. Consulte a documentação de cada provedor de recursos para descobrir os parâmetros que você precisa fornecer. Consulte [Provedores de Recursos](https://msdn.microsoft.com/library/azure/dn790572.aspx) para obter mais informações.

- **Arquivos de parâmetros** (que têm a extensão `.param.*.json`) contêm valores para os parâmetros especificados no arquivo de configuração que são necessários para cada provedor de recursos. Neste exemplo, o arquivo de configuração para um aplicativo Web (WebSite.json) define parâmetros para *siteName* e *siteLocation*. Durante a implantação, você é solicitado a fornecer valores para os parâmetros no arquivo de modelo e esses valores são armazenados no arquivo de parâmetros. Você também pode editar o arquivo de parâmetros diretamente.

Os arquivos JSON podem ser editados no editor do Visual Studio. Se você instalar as [Ferramentas do PowerShell para Visual Studio](https://visualstudiogallery.msdn.microsoft.com/c9eb3ba8-0c59-4944-9a62-6eee37294597), em seguida, você também obterá realce de sintaxe, correspondência de chaves e IntelliSense para facilitar a leitura e edição de scripts do PowerShell. Um link para instalar as Ferramentas do PowerShell é exibido na parte superior do editor, se elas ainda não estiverem instaladas.

![Instalar Ferramentas do PowerShell](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796671.png)

Os arquivos JSON usam um esquema que é referenciado na parte superior de cada arquivo. Você pode baixar o esquema e analisá-lo se desejar entendê-lo melhor. O esquema define quais elementos são permitidos, os tipos e formatos de campos, os valores possíveis de valores enumerados e assim por diante.

Se quiser implantar para configurações diferentes ou alterar as configurações com frequência, você pode criar cópias diferentes do arquivo *param*. Tente usar o mesmo modelo para todos os ambientes.

## Implantando um projeto do Grupo de Recursos do Azure para um grupo de recursos do Azure

Quando você implanta um projeto do Grupo de Recursos do Azure, você o implanta em um grupo de recursos do Azure, que é apenas um agrupamento lógico de recursos no Azure tais como aplicativos Web, bancos de dados e assim por diante.

1. No menu de atalho do nó do projeto de implantação, escolha **Implantar**, **Nova Implantação**.

    ![Item de menu Implantar, Nova Implantação](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796672.png)

    A caixa de diálogo **Implantar no Grupo de Recursos** é exibida.

    ![Caixa de diálogo Implantar no Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796673.png)

1. Na caixa suspensa **Grupo de Recursos**, escolha um grupo de recursos existente ou crie um novo. Para criar um grupo de recursos, abra a caixa suspensa **Grupo de Recursos** e escolha **<Create New...>**.

    A caixa de diálogo **Criar Grupo de Recursos** é exibida.

    ![Caixa de diálogo Criar Grupo de Recursos](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796674.png)

    >[AZURE.NOTE]Normalmente, quando você inicia um novo projeto de implantação, você deseja criar um novo grupo de recursos no qual implantar.

1. Insira um nome e local para o grupo de recursos e, em seguida, escolha o botão **Criar**.

    O local do grupo de recursos não precisa ser o mesmo que o local dos recursos, já que os recursos em um grupo podem abranger regiões.

1. Escolha a configuração de modelo e arquivos de parâmetros que você deseja usar para essa implantação, ou aceite os oferecidos como padrão.

    Você pode editar as propriedades de um recurso, escolhendo o botão **Editar Parâmetros**. Se estão faltando parâmetros necessários quando você realiza a implantação, a caixa de diálogo **Editar Parâmetros** é exibida para que você possa fornecê-los. **<null>** aparece na caixa **Valor** ao lado dos parâmetros que têm valores ausentes. Para este exemplo (um recurso de aplicativo Web), os parâmetros necessários incluem o nome do site, o plano de hospedagem e o local do site. (Se você se lembra, esses valores de parâmetro são definidos como nulos por padrão no arquivo de parâmetros.) Os outros parâmetros têm valores padrão.

    ![Caixa de diálogo Editar Parâmetros](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796675.png)

1. Na caixa de diálogo **Editar Parâmetros**, digite o nome do site, o local do site, nome do plano de hospedagem e verifique os valores de quaisquer eventuais outras propriedades. Quando tiver terminado, escolha o botão **Salvar**.

    - O parâmetro *siteName* é a primeira parte da URL da página da Web. Por exemplo, para a URL nomedomeusite.azurewebsites.net, o nome do site é **nomedomeusite**.

    - O parâmetro *hostingPlanName* especifica seu plano de hospedagem. Para este exemplo, você pode usar o valor "Free" (gratuito). Para obter mais informações sobre os planos de hospedagem, consulte [Visão geral dos planos de Serviço de Aplicativo do Azure](http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/)

    - O parâmetro *siteLocation* refere-se à região do Azure, onde o site está hospedado, como "West US" (Oeste dos Estados Unidos). Para obter uma lista de regiões disponíveis, consulte [Regiões do Azure](http://azure.microsoft.com/regions/).

1. Escolha o botão **Implantar** para implantar o projeto no Azure.

    Você pode ver o andamento da implantação na janela **Saída**. A implantação pode levar vários minutos para ser concluída, dependendo da configuração.

    >[AZURE.NOTE]Você pode ser solicitado a instalar os cmdlets do Microsoft Azure PowerShell. Já que esses cmdlets são necessários para implantar grupos de recursos do Azure, você precisa instalá-los.

1. Em um navegador, abra o [Portal de Visualização do Azure](https://portal.azure.com/). Como essa é uma nova alteração, deve haver uma nova mensagem de notificação disponível na guia **Notificações**. Escolha essa guia para exibir detalhes sobre o novo Grupo de Recursos do Azure. Para ver uma lista de todos os grupos de recursos disponíveis, você pode escolher a guia **Procurar** e, em seguida, escolher **Grupos de Recursos**.

    ![O Grupo de Recursos do Azure provisionado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796676.png)

1. Se você fizer alterações e desejar reimplantar seu projeto, você poderá escolher o grupo de recursos existente diretamente do menu de atalho do projeto de grupo de recursos do Azure. No menu de atalho, escolha **Implantar** e então escolha o grupo de recursos para o qual você recém-realizou a implantação.

    ![Grupo de recursos do Azure implantado](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/IC796677.png)

    Implantar um grupo de recursos do Azure implanta somente esse projeto. Se sua solução tiver um projeto de código ou quaisquer outros projetos, você precisará implantá-los separadamente.

## Usando projetos do Cloud Deployment do SDK do Azure 2.5 com o SDK do Azure 2.6

Se você estiver usando os projetos do Cloud Deployment que foram criados com o Azure SDK 2.5, será melhor que você atualize para o Azure SDK 2.6 ou posterior para poder usar os novos recursos de edição e implantação de modelos de recursos do Azure. A maneira mais fácil de reutilizar os modelos criados com o Azure SDK 2.5 é criar a versão 2.6 ou posterior do Azure SDK do projeto, mover seus modelos para esse projeto e fazer algumas modificações.

### Para usar projetos do Cloud Deployment do SDK do Azure 2.5 com o SDK do Azure 2.6 ou posterior

1. Adicione um novo projeto de Grupo de Recursos do SDK do Azure 2.6 ou posterior à sua solução.

    1. Abra a solução que contém o projeto do Cloud Deployment do SDK do Azure 2.5.

    1. No menu **Arquivo**, selecione **Novo**, **Projeto**.

    1. Na caixa de diálogo **Novo Projeto**, localize o projeto de **Grupo de Recursos do Azure **sob **Visual C#**/**Nuvem** ou **Visual Basic**/**Nuvem**.

         O nome do modelo de projeto foi alterado do **Cloud Deployment** para o Grupo de Recursos do Azure.

    1. Nomeie o projeto.

    1. Altere a caixa suspensa de solução para **Adicionar à solução**.

    1. Em seguida, você será solicitado a selecionar um modelo. Como você moverá os modelos existentes de seu projeto do Cloud Deployment do SDK do Azure 2.5, você pode escolher qualquer modelo, então vamos escolher o modelo em branco na parte inferior da lista.

    1. Escolha o botão **OK**.

        O novo projeto é adicionado à sua solução.

1. Copie o modelo e arquivos de parâmetro do projeto do Cloud Deployment do SDK do Azure 2.5 para seu projeto de Grupo de Recursos do SDK do Azure 2.6 ou posterior.

    1. No Gerenciador de Soluções, localize os arquivos de modelo e arquivos de parâmetros que você deseja copiar no projeto de implantação do Azure SDK 2.5, então selecione-os e copie-os.

    2. Cole os arquivos para a pasta **Modelos** no seu novo projeto de Grupo de Recursos do SDK do Azure 2.6 ou posterior.

1. No Gerenciador de Soluções, localize os arquivos de modelo e arquivos de parâmetros que você deseja copiar no projeto de implantação do Azure SDK 2.5, então selecione-os e copie-os.

1. Cole os arquivos para a pasta Modelos no seu novo projeto de Grupo de Recursos do SDK do Azure 2.6 ou posterior.

1. Se você também estiver implantando um aplicativo Web com o modelo, você precisará criar uma referência do novo projeto de Grupo de Recursos do SDK do Azure 2.6 ou posterior para o seu aplicativo Web.

    1. No menu de contexto do nó **Referências** do seu novo projeto de Grupo de Recursos do SDK do Azure 2.6 ou posterior no Gerenciador de Soluções, escolha **Adicionar Referência**.

    1. Marque a caixa ao lado de seu Aplicativo Web na lista de projetos e, em seguida, escolha o botão **OK**.

1. No menu de contexto do nó Referências do seu novo projeto de Grupo de Recursos do SDK do Azure 2.6 ou posterior no Gerenciador de Soluções, escolha Adicionar Referência.

1. Marque a caixa ao lado de seu Aplicativo Web na lista de projetos e, em seguida, escolha o botão OK.

1. Renomeie todas as ocorrências dos parâmetros *dropLocation* e *dropLocationSasToken* para *\_artifactsLocation* e *\_artifactsLocationSasToken*.

1. Se você não planeja usá-los, você pode excluir os arquivos de modelo e de parâmetros vazios que foram adicionados automaticamente ao projeto do SDK do Azure 2.6 ou posterior quando você o criou

    1. Exclua o arquivo DeployTemplate.json.

    1. Exclua o arquivo DeploymentTemplate.param.dev.json.

1. Se você tiver feito alterações no script de publicação AzureResourceGroup.ps1 no projeto do SDK do Azure 2.5, você precisará mover essas alterações para o script Deploy-AzureResourceGroup.ps1 no projeto do SDK do Azure 2.6 ou posterior.

    Agora você pode implantar seu modelo usando o comando de implantação no projeto de Grupo de Recursos do SDK do Azure 2.6 e tirar proveito dos novos recursos de edição de modelos da versão 2.6 ou posterior do SDK do Azure. Uma vez que o projeto na versão 2.6 ou posterior esteja funcionando de modo satisfatório, você pode remover o projeto do SDK do Azure 2.5 de sua solução.

## Por que é necessário atualizar o projeto

Foram feitas algumas alterações em modelos implantados no SDK do Azure 2.6 que tornam incompatíveis os modelos e o script de implantação do SDK do Azure 2.5. A primeira e maior alteração é que a implantação é iniciada. O Azure SDK 2.5 tinha código compilado que usava as APIs REST do Azure para carregar o modelo e iniciar a implantação. Comentários dos desenvolvedores indicaram que eles prefeririam que o Visual Studio apenas iniciasse o script do PowerShell que está incluído no projeto. Então, na versão 2.6 do SDK do Azure, o comando deploy inicia o script do PowerShell que está incluído no projeto para implantar o modelo. Isso o habilita a personalizar a implantação e faça com que essas personalizações sejam sempre executadas, esteja você implantando da linha de comando usando o Azure PowerShell ou implantando pelo Visual Studio usando o comando Deploy. Para implantar do Visual Studio, você precisará usar o script de implantação do PowerShell do SDK do Azure 2.6 (ou posterior) quando você tem o SDK do Azure 2.6 ou posterior instalado.

Também foram feitos ajustes em alguns nomes de variáveis e tarefas de compilação para se alinhar melhor às convenções de nomenclatura em compilações automatizadas do TFS e outros projetos na Microsoft. O código no Visual Studio que reúne as variáveis e valores necessários para iniciar o script do PowerShell procurará por esses novos nomes.

## Próximas etapas

Para saber como adicionar recursos ao seu Grupo de Recursos do Azure no Visual Studio, consulte [Adicionar recursos a um Grupo de Recursos do Azure](vs-azure-tools-resource-group-adding-resources.md).

<!---HONumber=Oct15_HO2-->