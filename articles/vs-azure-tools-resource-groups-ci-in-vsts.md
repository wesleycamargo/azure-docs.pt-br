<properties
	pageTitle="Integração contínua no VS Team Services usando os projetos do Grupo de recursos do Azure | Microsoft Azure"
	description="Descreve como configurar a integração contínua no Visual Studio Team Services usando os projetos de implantação do Grupo de recursos do Azure no Visual Studio."
	services="visual-studio-online"
	documentationCenter="na"
	authors="mlearned"
	manager="erickson-doug"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/01/2016"
	ms.author="mlearned" />

# Integração contínua no Visual Studio Team Services usando os projetos de implantação do Grupo de recursos do Azure

Para implantar um modelo do Azure, você precisa executar tarefas para percorrer os diversos estágios: Compilar, Testar, Copiar para o Azure (também chamado de "Preparo") e Implantar Modelo. Há duas maneiras de fazer isso no Visual Studio Team Services (VS Team Services). Os dois métodos oferecem os mesmos resultados, então escolha aquele que melhor se adapta ao seu fluxo de trabalho.

-	Adicione uma única etapa à sua definição de compilação que executa o script do PowerShell incluído no projeto de implantação do Grupo de recursos do Azure (Deploy-AzureResourceGroup.ps1). O script copia artefatos e implanta o modelo.
-	Adicione várias etapas de compilação do VS Team Services, cada uma executando uma tarefa do estágio.

Este artigo demonstra como usar a primeira opção (use uma definição de compilação para executar o script do PowerShell). Uma vantagem dessa opção é que o script usado por desenvolvedores no Visual Studio é o mesmo script usado pelo VS Team Services. Esse procedimento supõe que você já tem um projeto de implantação do Visual Studio no VS Team Services.

## Copiar artefatos para o Azure 

Independentemente do cenário, se você tiver quaisquer artefatos necessários para a implantação do modelo, será necessário conceder acesso ao Gerenciador de Recursos do Azure para esses artefatos. Esses artefatos podem incluir arquivos como:

-	Modelos aninhados
-	Scripts de configuração e scripts DSC
-	Binários do aplicativo

### Modelos aninhados e scripts de configuração
Quando você usa os modelos fornecidos pelo Visual Studio (ou compilados com trechos de código do Visual Studio), o script do PowerShell não apenas prepara os artefatos como também parametriza o URI dos recursos para implantações distintas. Em seguida, o script copia os artefatos para um contêiner seguro no Azure, cria um token SaS para esse contêiner e passa essas informações para a implantação de modelo. Confira [Criar uma implantação de modelo](https://msdn.microsoft.com/library/azure/dn790564.aspx) para saber mais sobre modelos aninhados.

## Configurar a implantação contínua no VS Team Services

Para chamar o script do PowerShell no VS Team Services, você precisa atualizar a definição de build. Resumindo, as etapas são:

1.	Editar a definição de build.
1.	Configurar a autorização do Azure no VS Team Services.
1.	Adicione uma etapa de compilação do Azure PowerShell que faz referência ao script do PowerShell no projeto de implantação do Grupo de recursos do Azure.
1.	Defina o valor do parâmetro *- ArtifactsStagingDirectory* para trabalhar com um projeto compilado no VS Team Services.

### Passo a passo detalhado

As etapas a seguir o orientarão pelas etapas necessárias para configurar a implantação contínua no VS Team Services

1.	Edite sua definição de compilação do VS Team Services e adicione uma etapa de compilação do Azure PowerShell. Escolha a definição de compilação na categoria **Definições de compilação** e escolha o link **Editar**.

    ![][0]

1.	Adicione uma nova etapa de compilação do **Azure PowerShell** para a definição de compilação e escolha o botão **Adicionar etapa de compilação...**.

    ![][1]

1.	Escolha a categoria **Tarefa de implantação**, escolha a tarefa **Azure PowerShell** e escolha o botão **Adicionar**.

    ![][2]

1.	Escolha a etapa de compilação do **Azure PowerShell** e, em seguida, preencha seus valores.

    1.	Se um ponto de extremidade de serviço do Azure já tiver sido adicionado ao VS Team Services, escolha a assinatura na caixa suspensa **Assinatura do Azure** e vá para a próxima seção.

        Se você não tiver um ponto de extremidade de serviço do Azure no VS Team Services, será necessário adicionar um. Esta subseção o guiará pelo processo. Se sua conta do Azure usa uma conta da Microsoft (como o Hotmail), será necessário executar as seguintes etapas para obter uma autenticação da Entidade de Serviço.

    1.	Escolha o link **Gerenciar** próximo à caixa de lista suspensa **Assinatura do Azure**.

        ![][3]

    1. Escolha **Azure** na caixa de lista suspensa **Novo Ponto de Extremidade de Serviço**.

        ![][4]

    1.	Na caixa de diálogo **Adicionar Assinatura do Azure**, escolha a opção **Entidade de Serviço**.

        ![][5]

    1.	Adicione as informações de assinatura do Azure à caixa de diálogo **Adicionar Assinatura do Azure**. Será necessário fornecer os seguintes itens:
        -	ID da assinatura
        -	Nome da assinatura
        -	Id da Entidade de serviço
        -	Chave da Entidade de serviço
        -	ID do locatário

    1.	Adicione um nome de sua escolha à caixa de nome da **Assinatura**. Esse valor será exibido mais tarde na lista suspensa **Assinatura do Azure** no VS Team Services.

    1.	Se você não souber sua ID de assinatura do Azure, use um dos seguintes comandos para obtê-la.
        
        Para scripts do PowerShell, use:

        `Get-AzureRmSubscription`

        Para a CLI do Azure, use:

        `azure account show`
    

    1.	Para obter uma ID da Entidade de Serviço, Chave da Entidade de Serviço e ID do Locatário, siga o procedimento em [Criar aplicativo do Active Directory e entidade de serviço usando o portal](resource-group-create-service-principal-portal.md) ou [Autenticar uma entidade de serviço com o Gerenciador de Recursos do Azure](resource-group-authenticate-service-principal.md).

    1.	Adicione os valores de ID da Entidade de Serviço, Chave da Entidade de Serviço e ID do Locatário à caixa de diálogo **Adicionar Assinatura do Azure** e escolha o botão **OK**.

        Agora você tem uma Entidade de Serviço válida para executar o script do Azure PowerShell.

1.	Edite a definição de compilação e escolha a etapa de compilação do **Azure PowerShell**. Selecione a assinatura na caixa de lista suspensa **Assinatura do Azure**. (Se a assinatura não aparecer, escolha o botão **Atualizar** ao lado do link **Gerenciar**.)

    ![][8]

1.	Forneça um caminho para o script do PowerShell Deploy-AzureResourceGroup.ps1. Para fazer isso, escolha o botão de reticências (...) ao lado da caixa **Caminho do Script**, navegue até o script Deploy-AzureResourceGroup.ps1 na pasta **Scripts** de seu projeto, selecione-o e escolha o botão **OK**.

    ![][9]

1. Depois de selecionar o script, atualize o caminho até ele para que seja executado a partir de Build.StagingDirectory (o mesmo diretório para o qual *ArtifactsLocation* está configurado). Você pode fazer isso adicionando "$(Build.StagingDirectory)/" ao início do caminho do script.

    ![][10]

1.	Na caixa **Argumentos do Script**, digite os parâmetros a seguir (em uma única linha). Ao executar o script no Visual Studio, você pode ver como o VS usa os parâmetros na janela **Saída**. Você pode usar isso como um ponto de partida para definir os valores do parâmetro na etapa de compilação.

    | Parâmetro | Descrição|
    |---|---|
    | -ResourceGroupLocation | O valor da localização geográfica na qual o grupo de recursos está localizado, por exemplo, **eastus** ou **”Leste dos EUA”**. (Adicione aspas se houver um espaço no nome). Confira [Regiões do Azure](https://azure.microsoft.com/regions/) para saber mais.| |
    | -ResourceGroupName | O nome do grupo de recursos usado para essa implantação.| |
    | -UploadArtifacts | Esse parâmetro, quando presente, especifica que os artefatos precisam ser carregados no Azure a partir do sistema local. Você só precisa definir essa opção se a implantação de seu modelo exigir artefatos adicionais que você deseja testar usando o script do PowerShell (como scripts de configuração ou modelos aninhados). |
    | -StorageAccountName | O nome da conta de armazenamento usada para artefatos de preparação para essa implantação. Esse parâmetro será necessário apenas se você estiver copiando artefatos para o Azure. Essa conta de armazenamento não será criada automaticamente pela implantação, ela já deve existir.| |
    | -StorageAccountResourceGroupName | O nome do grupo de recursos associado à conta de armazenamento. Esse parâmetro será necessário apenas se você estiver copiando artefatos para o Azure.| |
    | -TemplateFile | O caminho para o arquivo de modelo no projeto de implantação do Grupo de recursos do Azure. Para aumentar a flexibilidade, use um caminho para esse parâmetro que seja relativo ao local do script do PowerShell em vez de um caminho absoluto.|
    | -TemplateParametersFile | O caminho para o arquivo de parâmetros no projeto de implantação do Grupo de recursos do Azure. Para aumentar a flexibilidade, use um caminho para esse parâmetro que seja relativo ao local do script do PowerShell em vez de um caminho absoluto.|
    | -ArtifactStagingDirectory | Esse parâmetro permite que o script do PowerShell conheça a pasta de onde os arquivos binários do projeto devem ser copiados. Esse valor substitui o valor padrão usado pelo script do PowerShell. Para usar o VS Team Services, defina o valor como: - ArtifactStagingDirectory $(Build.StagingDirectory) |

    Veja a seguir um exemplo de argumentos de script (linha quebrada para facilitar a leitura):

    ```	
    -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
    -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
    –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'	
    ```

    Quando tiver terminado, a caixa **Argumentos de Script** deverá ser semelhante ao seguinte.

    ![][11]

1.	Depois de adicionar todos os itens necessários para a etapa de compilação do Azure PowerShell, escolha o botão de compilação **Fila** para compilar o projeto. A tela **Compilação** mostra a saída do script do PowerShell.

## Próximas etapas

Leia [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md) para saber mais sobre o Gerenciador de Recursos do Azure e os grupos de recursos do Azure.


[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png

<!---HONumber=AcomDC_0803_2016-->