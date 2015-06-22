<properties 
	pageTitle="Entrega contínua de serviços de nuvem com TFS no Azure" 
	description="Saiba como configurar a entrega contínua para aplicativos de nuvem do Azure. Exemplos de código para instruções de linha de comando do MSBuild e scripts do PowerShell." 
	services="cloud-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="kempb"/>

# Fornecimento contínuo de serviços de nuvem no Azure

O processo descrito neste artigo mostra como configurar o fornecimento contínuo de aplicativos na nuvem do Azure. Este processo permite criar pacotes automaticamente e implantar o pacote no Azure após cada verificação de código. O processo de compilação do pacote descrito neste artigo equivale ao comando Pacote do Visual Studio, e as etapas de publicação equivalem ao comando Publicar do Visual Studio. O artigo aborda os métodos que você usaria para criar um servidor de compilação com instruções de linha de comando do MSBuild e scripts do Windows PowerShell, além de demonstrar como configurar as definições do Visual Studio Team Foundation Server - Team Build para usar os comandos do MSBuild e os scripts do PowerShell. O processo é personalizável para o ambiente de compilação e os ambientes de destino do Azure.

Você também pode usar o Visual Studio Online, uma versão do TFS hospedada no Azure, para fazer isso mais facilmente. Para obter mais informações, consulte [Fornecimento contínuo para o Azure usando o Visual Studio Online][].

Antes de começar, você deve publicar seu aplicativo do Visual Studio. Isso garantirá que todos os recursos estejam disponíveis e sejam inicializados quando você tentar automatizar o processo de publicação.

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Configurar servidor de compilação][]
-   [Etapa 2: criar um pacote usando comandos MSBuild][]
-   [Etapa 3: criar um pacote usando o TFS Team Build (opcional)][]
-   [Etapa 4: Publicar um pacote usando um Script do PowerShell][]
-   [Etapa 5: publicar um pacote usando o TFS Team Build (opcional)][]

<h2> <a name="step1"> </a>Etapa 1: Configurar servidor de compilação</h2>

Antes de criar um pacote do Azure usando o MSBuild, você deve instalar o software e as ferramentas necessárias no servidor de compilação.

O Visual Studio não precisa estar instalado no servidor de compilação. Se você quiser usar o Team Foundation Build Service para gerenciar o servidor de compilação, siga as instruções na documentação do [Team Foundation Build Service][].

1.  No servidor de compilação, instale o [.NET Framework 4][], [.NET Framework 4.5][] ou [.NET Framework 4.5.2][], que incluem o MSBuild.
2.  Instale as [Ferramentas de criação do Azure][] (procure MicrosoftAzureAuthoringTools-x86.msi ou MicrosoftAzureAuthoringTools-x64.msi, dependendo do processador do servidor de compilação). Versões mais antigas dos arquivos podem ter WindowsAzure no nome de arquivo.
3. Instale as [Bibliotecas do Azure][] (procure MicrosoftAzureLibsForNet-x86.msi ou MicrosoftAzureLibsForNet-x64.msi).
4.  Copie o arquivo Microsoft.WebApplication.targets de uma instalação do Visual Studio para o servidor de compilação. Em um computador com o Visual Studio instalado, o arquivo está localizado no diretório C:\\Program Files(x86)\\MSBuild\\Microsoft\\VisualStudio\\v11.0\\WebApplications (v12.0 for Visual Studio 2013). Você deve copiá-lo para o mesmo diretório no servidor de compilação.
5.  Instale as [Ferramentas do Azure para Visual Studio][]. Procure por MicrosoftAzureTools.VS110.exe para compilar projetos do Visual Studio 2012, por MicrosoftAzureTools.VS120.exe para compilar projetos do Visual Studio 2013 e por MicrosoftAzureTools.VS140.exe para compilar projetos do Visual Studio 2015 Preview.

<h2><a name="step2"> </a>Etapa 2: criar um pacote usando comandos MSBuild</h2>

Esta seção descreve como criar um comando do MSBuild que compila um pacote do Azure. Execute esta etapa no servidor de compilação para verificar setudo está configurado corretamente e se o comando do MSBuild faz o que você deseja fazer. Você pode adicionar essa linha de comando a scripts de construção existentes no servidor de compilação ou usar a linha de comando em uma definição de compilação do TFS, conforme descrito na próxima seção. Para obter mais informações sobre parâmetros de linha de comando e sobre o MSBuild, consulte [Referência de linha de comando do MSBuild][].

1.  Se o Visual Studio estiver instalado no servidor de compilação, clique em **Iniciar**, em **Todos os Programas** e localize e clique em **Prompt de Comando do Visual Studio** na pasta **Ferramentas do Visual Studio**.

    Se o Visual Studio não estiver instalado no servidor de compilação, abra um prompt de comando e verifique se MSBuild.exe está acessível no caminho. MSBuild está instalado com o .NET Framework no caminho %WINDIR%\\Microsoft.NET\\Framework*Versão*. Por exemplo, para adicionar MSBuild.exe à variável de ambiente PATH quando você tem o .NET Framework 4 instalado, digite o seguinte comando no prompt de comando:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"

2.  No prompt de comando, navegue até a pasta que contém o arquivo de projeto do Microsoft Azure que você deseja compilar.

3.  Execute msbuild com a opção /target:Publish, como no seguinte exemplo:

        MSBuild /target:Publish

    Essa opção pode ser abreviada como /t:Publish. A opção /t:Publish no MSBuild não deve ser confundida com os comandos Publicar disponíveis no Visual Studio quando você tem o SDK do Azure instalado. A opção /t:Publish compila apenas os pacotes do Azure. Ela não implanta os pacotes como os comandos Publicar no Visual Studio.

    Você também pode especificar o nome do projeto como um parâmetro do MSBuild. Se não for especificado, o diretório atual é usado. Para obter mais informações sobre as opções da linha de comando, consulte [Referência da linha de comando do MSBuild][1].

4.  Localize a saída. Por padrão, este comando cria um diretório com relação à pasta raiz do projeto, como *ProjectDir*\\bin*Configuration*\\app.publish. Ao criar um projeto do Azure, você gera dois arquivos, o arquivo do pacote propriamente dito e o arquivo de configuração que o acompanha:

    -   Project.cspkg
    -   ServiceConfiguration.*TargetProfile*.cscfg

    Por padrão, cada projeto do Azure inclui um arquivo de configuração de serviço (arquivo .cscfg) para compilações locais (depuração) e outro para compilações de nuvem (de preparo ou produção), mas você pode adicionar ou remover arquivos de configuração de serviço conforme necessário. Quando compilar um pacote dentro do Visual Studio, você será questionado quanto ao arquivo de configuração de serviço a ser incluído com o pacote.

5.  Especifique o arquivo de configuração do serviço. Quando você compila um pacote usando o MSBuild, o arquivo de configuração de serviço local é incluído por padrão. Para incluir um arquivo de configuração de serviço diferente, defina a propriedade TargetProfile do comando MSBuild, como no seguinte exemplo:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Especifique o local para a saída. Defina o caminho usando a opção /p:PublishDir=*Directory*\\, incluindo o separador de barra invertida à direita, como no seguinte exemplo:

        MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

    Depois de criar e testar uma linha de comando do MSBuild apropriada para compilar os projetos e integrá-los em um pacote do Azure, você poderá adicionar essa linha de comando aos scripts de compilação. Se seu servidor de compilação usar scripts personalizados, esse processo dependerá das especificidades de seu processo de compilação personalizado. Se estiver usando o TFS como um ambiente de compilação, você poderá seguir as instruções na próxima etapa para adicionar a compilação do pacote do Azure ao processo de compilação.

<h2> <a name="step3"> </a>Etapa 3: criar um pacote usando o TFS Team Build (opcional)</h2>

Se tiver o Team Foundation Server (TFS) configurado como um controlador de compilação e o servidor de compilação estiver configurado como um computador de compilação TFS, você poderá configurar uma compilação automatizada para o pacote do Azure. Para obter informações sobre como configurar e usar o Team Foundation Server como um sistema de compilação, consulte [Noções básicas sobre o Team Foundation Build System][]. Em particular, o procedimento a seguir presume que você tenha configurado seu servidor de compilação conforme descrito em [Configurar um computador de compilação][], e que você tenha criado um projeto da equipe e criado um projeto de serviço de nuvem no projeto da equipe.

Para configurar o TFS para compilar pacotes do Azure, execute as seguintes etapas:

1.  No Visual Studio, no computador de desenvolvimento, no menu Exibir , escolha **Team Explorer** ou Ctrl+\\, Ctrl+M. Na janela do Team Explorer, expanda o nó **Compilações** ou escolha a página **Compilações** e escolha **Definição de Nova Compilação**.

    ![][0]

2.  Clique na guia **Gatilho** e especifique as condições desejadas para quando deseja que o pacote seja compilado. Por exemplo, especifique **Integração Contínua** para compilar o pacote sempre que um check-in de controle do código-fonte ocorrer.

3.	Selecione a guia **Configurações da fonte**, e certifique-se de que a pasta do projeto esteja listada na coluna **Pasta do Controle do Código-Fonte** e que o status seja **Ativo**.

4.  Escolha a guia **Padrões de Compilação** e, em Controlador de compilação, verifique o nome do servidor de compilação. Além disso, escolha a opção **Copiar resultado da criação para a seguinte pasta** de recebimento e especifique o local de recebimento desejado.

5.  Clique na guia **Processo**. Na guia Processo, selecione o modelo padrão, em **Compilação**, selecione o projeto se ele ainda não estiver selecionado e expanda a seção **Avançado** na seção **Compilação** da grade.

6.  Escolha **Argumentos do MSBuild**e defina os argumentos da linha de comando do MSBuild apropriados, conforme descrito na Etapa 2 acima. Por exemplo, insira **/t:Publish /p:PublishDir=\\\\myserver\\drops** para compilar um pacote e copie os arquivos de pacote para o local \\\\myserver\\drops:

    ![][2]

    **Nota:** copiar os arquivos para um compartilhamento público torna mais fácil implantar manualmente os pacotes do seu computador de desenvolvimento.

5.  Teste o êxito da etapa de compilação fazendo check-in de uma alteração para o projeto ou coloque na fila uma nova compilação. Para colocar na fila uma nova compilação, no Team Explorer, clique com o botão direito do mouse em **Todas as Definições de Compilação** e, em seguida, escolha **Colocar Nova Compilação na Fila**.

<h2> <a name="step4"> </a>Etapa 4: Publicar um pacote usando um Script do PowerShell</h2>

Esta seção descreve como criar um script do Windows PowerShell que publicará a saída do pacote do aplicativo de nuvem para o Azure usando parâmetros opcionais. Esse script pode ser chamado após a etapa de compilação na sua automação de compilação personalizada. Ele também pode ser chamado das atividades do fluxo de trabalho do Modelo de processo no Visual Studio TFS Team Build.

1.  Instale os [cmdlets do PowerShell do Azure][] (v0.6.1 ou superior). Durante a fase de instalação do cmdlet, opte por instalar como um snap-in. Observeque essa versão suportada oficialmente substitui a versão mais antigaoferecida por meio do CodePlex, embora as versões anteriores foram numeradas 2.x.x.

2.  Inicie o PowerShell do Azure usando o menu Iniciar. Se você iniciar dessa forma, os cmdlets do PowerShell do Azure serão carregados.

3.  No prompt do PowerShell, verifique se os cmdlets do PowerShell são carregados digitando o comando parcial Get-Azure e pressionando tab para preenchimento de declaração.

    Se pressionar tab repetidamente, você deverá ver diversos comandos do PowerShell do Azure.

4.  Verifique se você consegue se conectar à assinatura do Azure importando as informações de assinatura do arquivo .publishsettings.

    Import-AzurePublishSettingsFile c:\\scripts\\WindowsAzure\\default.publishsettings

    Em seguida, dê o comando

    Get-AzureSubscription

    Isso exibirá informações sobre a sua assinatura. Verifique se tudo está correto.

4.  Salve o modelo de script fornecido ao [final deste artigo][] na sua pasta de scripts como c:\\scripts\\WindowsAzure**PublishCloudService.ps1**.

5.  Consulte a seção de parâmetros do script. Adicione ou modifique os valores padrão. Esses valores podem ser substituídos sempre passando parâmetros explícitos.

6.  Verifique se há contas de serviço de nuvem e de armazenamento válidas criadas na assinatura que possam ser direcionadas pelo script de publicação. A conta de armazenamento (armazenamento de blob) será usada para carregar e armazenar temporariamente o arquivo de configuração e o pacote de implantação, enquanto a implantação está sendo criada.

    -   Para criar um novo serviço de nuvem, você pode chamar esse script ou usar o Portal de Gerenciamento do Azure. O nome do serviço de nuvem será usado como um prefixo em um nome de domínio totalmente qualificado e, portanto, deve ser exclusivo.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Para criar uma nova conta de armazenamento, você pode chamar esse script ou usar o Portal de Gerenciamento do Azure. O nome da conta de armazenamento será usado como um prefixo em um nome de domínio totalmente qualificado e, portanto, deve ser exclusivo. Você pode tentar usar o mesmo nome que o serviço de nuvem.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

7.  Chame o script diretamente do PowerShell do Azure, ou conecte esse script à automação de compilação do host para ocorrer após a compilação do pacote.

    **AVISO:** o script vai sempre excluir ou substituir as implantações existentes por padrão, se eles forem detectados. Isso é necessário para habilitar o fornecimento contínuo de automação onde não é possível nenhum aviso ao usuário.

    **Cenário de exemplo 1:** implantação contínua ao ambiente de preparo de um serviço:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    Isso é normalmente seguido pela verificação da execução de teste e uma troca de VIP. A troca de VIP pode ser feita por meio do Portal de Gerenciamento do Azure ou usando o cmdlet Move-Deployment.

    **Cenário de exemplo 2:** implantação contínua ao ambiente de produção de um serviço de teste dedicado

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Área de trabalho remota:**

    Se a Área de Trabalho Remota estiver habilitada no projeto do Azure, você precisará realizar etapas individuais adicionais para garantir que o certificado de serviço de nuvem seja carregado em todos os serviços de nuvem segmentados por esse script.

    Localize os valores de impressão digital do certificado esperados por suas funções. Os valores da impressão digital são visíveis na seção Certificados no arquivo de configuração de nuvem (ou seja, ServiceConfiguration.Cloud.cscfg). Ela também é visível na caixa de diálogo Configuração de Área de Trabalho Remota no Visual Studio, quando você Mostrar Opções e exibir o certificado selecionado.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Carregue certificados de Área de Trabalho Remota como uma etapa única de configuração usando o seguinte script de cmdlet:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY<THUMBPRINT>)

    Por exemplo:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    Você também pode exportar o arquivo de certificado PFX com a chave privada e carregar certificados para cada serviço de nuvem de destino usando o Portal de Gerenciamento do Azure. Leia o seguinte artigo para saber mais: [http://msdn.microsoft.com/library/windowsazure/gg443832.aspx][].

    **Atualizar implantação vs. Excluir Implantação -> Nova Implantação**

    O script vai, por padrão, executar uma implantação de atualização($enableDeploymentUpgrade = 1) quando nenhum parâmetro for passado ou o valor 1 for passado explicitamente. Para instâncias únicas isso tem a vantagem de levar menos tempo do que uma implantação completa. Para instâncias que exigem alta disponibilidade, isso também tem a vantagem de deixar algumas instâncias em execução enquanto outras são atualizadas (movimentação de seu domínio de atualização), além de seu VIP não ser excluído.

    A implantação de atualização pode ser desabilitada no script ($enableDeploymentUpgrade = 0) ou passando -enableDeploymentUpgrade 0 como um parâmetro, que alterará o comportamento do script para excluir primeiro qualquer implantação existente e, em seguida, criar uma nova implantação.

    **Aviso:** o script vai sempre excluir ou substituir as implantações existentes por padrão, se eles forem detectados. Isso é necessário para habilitar o fornecimento contínuo de automação onde é possível sem nenhum aviso ao usuário/operador.

<h2><a name="step5"> </a>Etapa 5: Publicar um pacote usando o TFS Team Build (opcional)</h2>

Esta etapa conectará o TFS Team Build ao script criado na etapa 4, que lida com a publicação da compilação do pacote no Azure. Isso implica modificar o modelo de processo usado pela sua definição de compilação para que seja executada uma atividade de Publicar no final do fluxo de trabalho. A atividade de Publicar executará o comando PowerShell passando parâmetros da compilação. A saída dos destinos do MSBuild e o script de publicação serão redirecionados para a saída de compilação padrão.

1.  Edite a definição de compilação responsável pela implantação contínua.

2.  Selecione a guia **Processo**.

3.	Execute [estas instruções](http://msdn.microsoft.com/library/dd647551.aspx) para adicionar um projeto de atividade ao modelo de processo de compilação, baixe o modelo padrão, adicione-o ao projeto e faça check-in. Atribua um novo nome ao modelo de processo de compilação, como AzureBuildProcessTemplate.

3.  Volte à guia **Processo** e use **Mostrar Detalhes** para mostrar uma lista dos modelos de processos de compilação disponíveis. Escolha o botão **Novo...** e navegue para o projeto recém-adicionado e faça o check-in. Localize o modelo que você acabou de criar e escolha **OK**.

4.  Abra o modelo de processo selecionado para edição. Você pode abrir diretamente no designer de fluxo de trabalho ou no editor de XML para trabalhar como XAML.

5.  Adicione a seguinte lista de novos argumentos como itens de linha separados na guia de argumentos do designer de fluxo de trabalho. Todos os argumentos devem ter direção =In e digite =String. Esses serão usados para parâmetros de fluxo da definição de compilação para o fluxo de trabalho, que, em seguida, é usado para chamar o script de publicação.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![][3]

    O XAML correspondente tem esta aparência:

        <Activity  _ />
          <x:Members>
            <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
            <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
            <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
            <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
            <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
            <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
            <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
            <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
            <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
            <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
            <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
            <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
            <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
            <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
            <x:Property Name="GetVersion" Type="InArgument(x:String)" />
            <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
            <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
            <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
            <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
            <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
            <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
            <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
            <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
            <x:Property Name="Environment" Type="InArgument(x:String)" />
            <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
            <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
            <x:Property Name="ServiceName" Type="InArgument(x:String)" />
          </x:Members>

          <this:Process.MSBuildArguments>

6.  Adicione uma nova sequência no final do Agente de Execução:

    1.  Comece adicionando uma atividade de declaração If para verificar se há um arquivo de script válido. Defina a condição para este valor:

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  No caso Then da declaração If, adicione uma nova atividade de sequência. Defina o nome de exibição para 'Iniciar publicação'

    3.  Com a sequência de publicação inicial ainda selecionada, adicione a lista a seguir de novas variáveis como itens de linha separadas na guia de variáveis do designer de fluxo de trabalho. Todas as variáveis devem ter um tipo de Variável =String e Escopo=Início da publicação. Esses serão usados para parâmetros de fluxo da definição de compilação para o fluxo de trabalho, que, em seguida, é usado para chamar o script de publicação.

        -   SubscriptionDataFilePath, do tipo String

        -   PublishScriptFilePath, do tipo String

            ![][4]

    4.  Se estiver usando o TFS 2012 ou anterior, adicione uma atividade ConvertWorkspaceItem ao início da nova Sequência. Se estiver usando o TFS 2013 ou posterior, adicione uma atividade GetLocalPath ao início da nova sequência. Para um ConvertWorkspaceItem, defina as propriedades da seguinte maneira: Direction=ServerToLocal, DisplayName='Convert publish script filename', Input=' PublishScriptLocation', Result='PublishScriptFilePath', Workspace='Workspace'. Para uma atividade GetLocalPath, defina a propriedade IncomingPath como 'PublishScriptLocation’ e o Resultado como 'PublishScriptFilePath'. Esta atividade converte o caminho para o script de publicação dos locais do servidor TFS (se aplicável) para um caminho de disco local padrão.

    5.  Se estiver usando o TFS 2012 ou anterior, adicione outra atividade ConvertWorkspaceItem ao final da nova Sequência. Direction=ServerToLocal, DisplayName='Convert subscription filename', Input=' SubscriptionDataFileLocation', Result= 'SubscriptionDataFilePath', Workspace='Workspace'. Se estiver usando o TFS 2013 ou posterior, adicione outra atividade GetLocalPath. IncomingPath='SubscriptionDataFileLocation' e Result='SubscriptionDataFilePath.'

    6.  Adicione uma atividade InvokeProcess ao final da nova sequência. Essa atividade chama PowerShell.exe com os argumentos passados pela definição de compilação.

        1.  Argumentos = String.Format(" -File ""{0}"" -serviceName {1} -storageAccountName {2} -packageLocation ""{3}"" -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}"" -selectedSubscription {6} -environment ""{7}""", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName = Execute publish script

        3.  FileName = "PowerShell" (incluindo as aspas)

        4.  OutputEncoding= System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)

    7.  Na caixa de texto da seção **Gerenciar Saída Padrão** de InvokeProcess, defina o valor da caixa de texto como “ data”. Essa é uma variável para armazenar os dados de saída padrão.

    8.  Adicione uma atividade WriteBuildMessage logo abaixo da seção de **Gerenciar Saída Padrão**. Defina a importância ='Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High'e a mensagem = 'dados'. Isso garante que a saída padrão do script será gravada na saída de compilação.

    9.  Na caixa de texto da seção **Gerenciar Erro de Saída** de InvokeProcess, defina o valor da caixa de texto como “data”. Essa é uma variável para armazenar os dados de erro padrão.

    10. Adicione uma atividade WriteBuildError pouco abaixo da seção **Gerenciar Erro de Saída**. Defina a mensagem = 'dados'. Isso garante que os erros padrões do script serão gravados na saída do erro de compilação.

	11. Corrija quaisquer erros, indicados por sinais de exclamação azuis. Passe o mouse sobre os pontos de exclamação para ver informações sobre o erro. Salve o fluxo de trabalho para limpar os erros.

    O resultado final das atividades de fluxo de trabalho de publicação terá a seguinte aparência no designer:

    ![][5]

    O resultado final das atividades de fluxo de trabalho de publicação terá a seguinte aparência neste XAML:

		<If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
	        <If.Then>
	          <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
	            <Sequence.Variables>
	              <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
	              <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
	            </Sequence.Variables>
	            <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
	            <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
	            <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
	              <mtbwa:InvokeProcess.ErrorDataReceived>
	                <ActivityAction x:TypeArguments="x:String">
	                  <ActivityAction.Argument>
	                    <DelegateInArgument x:TypeArguments="x:String" Name="data" />
	                  </ActivityAction.Argument>
	                  <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
	                </ActivityAction>
	              </mtbwa:InvokeProcess.ErrorDataReceived>
	              <mtbwa:InvokeProcess.OutputDataReceived>
	                <ActivityAction x:TypeArguments="x:String">
	                  <ActivityAction.Argument>
	                    <DelegateInArgument x:TypeArguments="x:String" Name="data" />
	                  </ActivityAction.Argument>
	                  <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
	                </ActivityAction>
	              </mtbwa:InvokeProcess.OutputDataReceived>
	            </mtbwa:InvokeProcess>
	          </Sequence>
	        </If.Then>
	      </If>
	    </Sequence>


7.  Salve o fluxo de trabalho do modelo de processo de compilação e o faça check-in deste arquivo.

8.  Edite a definição de compilação (feche-a se já estiver aberta), e selecione o botão **Novo** se não quiser ver o novo modelo na lista de Modelos de Processo.

9.  Defina os valores da propriedade do parâmetro na seção Vários como segue:

    1.  CloudConfigLocation ='c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg' *Esse valor é derivado de: ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = 'c:\\drops\\app.publish\\ContactManager.Azure.cspkg' *Esse valor é derivado de: ($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation = 'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename' *Use o nome de serviço de nuvem apropriado aqui*

    5.  Ambiente = 'Teste'

    6.  StorageAccountName = 'mystorageaccountname' *Use o nome da conta de armazenamento adequado aqui*

    7.  SubscriptionDataFileLocation = 'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'padrão'

    ![][6]

10. Salve as alterações para a Definição de Compilação.

11. Coloque uma compilação em fila para executar a compilação do pacote e publicar. Se você tiver um gatilho definido para Integração Contínua, você vai executar esse comportamento em cada check-in.

### <a name="script"> </a>Modelo de script PublishCloudService.ps1

<pre>
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = “Teste",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = “padrão",
        $subscriptionDataFile = ""
     )
      

function Publish()
{
	$deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue 
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - Nenhuma implantação é detectada. Criando uma nova implantação. "
    }
    #verifique por implantação existente e, em seguida, atualize, exclua + implante ou cancele, de acordo com as variáveis booleanas $alwaysDeleteExistingDeployments e $enableDeploymentUpgrade
	se ($deployment.Name -ne $null)
	{
		switch ($alwaysDeleteExistingDeployments)
	    {
	        1 
			{
                switch ($enableDeploymentUpgrade)
                {
                    1  #Atualize a implantação em vigor (normalmente mais rápido, mais econômico e não destrói VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - A implantação existe em $servicename.  Atualizando implantação.”
				        UpgradeDeployment
                    }
                    0 #Exclua e então crie a nova implantação
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - A implantação existe em $servicename.  Excluindo implantação."
				        DeleteDeployment
                        CreateNewDeployment
                        
                    }
                } # switch ($enableDeploymentUpgrade)
			}
	        0
			{
				Write-Output "$(Get-Date -f $timeStampFormat) - ERRO: A implantação existe em $servicename.  Execução do script cancelada."
				exit
			}
	    } #switch ($alwaysDeleteExistingDeployments)
	} else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
	write-progress -id 3 -atividade “Criando nova Implantação" -Status “Em andamento"
	Write-Output "$(Get-Date -f $timeStampFormat) - Criando nova implantação: em andamento"

	$opstat = New-AzureDeployment -Slot $slot -Pacote $packageLocation -Configuração $cloudConfigLocation -etiqueta $deploymentLabel -ServiceName $serviceName
	    
    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity “Criando nova Implantação" -concluído -Status “Concluído"
	Write-Output "$(Get-Date -f $timeStampFormat) - Criando nova implantação: Concluído, ID da implantação: $completeDeploymentID"
    
	StartInstances
}

function UpgradeDeployment()
{
	write-progress -id 3 -atividade “Atualizando Implantação" -Status “Em andamento"
	Write-Output "$(Get-Date -f $timeStampFormat) - Atualização implantação: em andamento"

    # perform Update-Deployment
	$setdeployment = Set-AzureDeployment -Atualização-Slot $slot -Pacote $packageLocation -Configuração $cloudConfigLocation -etiqueta $deploymentLabel -ServiceName $serviceName -Force
    
    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid
    
    write-progress -id 3 -activity “Atualizando Implantação" -completed -Status “Concluído"
	Write-Output "$(Get-Date -f $timeStampFormat) - Atualização implantação: Concluído, ID da implantação: $completeDeploymentID"
}

function DeleteDeployment()
{

	write-progress -id 2 -activity “Excluindo Implantação" -Status “Em Andamento"
	Write-Output "$(Get-Date -f $timeStampFormat) - Excluindo implantação: em andamento"

    #WARNING - sempre exclua com force
	$removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

	write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
	Write-Output "$(Get-Date -f $timeStampFormat) - Excluindo implantação: concluído"
	
}

function StartInstances()
{
	write-progress -id 4 -activity “Iniciando Instâncias" -status “Em Andamento"
	Write-Output "$(Get-Date -f $timeStampFormat) - Iniciando instâncias: em andamento"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running') 
    {
	    $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
	$deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
	$oldStatusStr = @("") * $deployment.RoleInstanceList.Count
	
	while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
	{
		$i = 1
		foreach ($roleInstance in $deployment.RoleInstanceList)
		{
			$instanceName = $roleInstance.InstanceName
			$instanceStatus = $roleInstance.InstanceStatus

			if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
			{
				$oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
				Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
			}

			write-progress -id (4 + $i) -activity “Iniciando Instância '$instanceName'" -status "$instanceStatus"
			$i = $i + 1
		}

		sleep -Seconds 1

		$deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
	}

	$i = 1
	foreach ($roleInstance in $deployment.RoleInstanceList)
	{
		$instanceName = $roleInstance.InstanceName
		$instanceStatus = $roleInstance.InstanceStatus

		if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
		{
			$oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
			Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
		}

		$i = $i + 1
	}
	
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
	$opstat = $deployment.Status 
	
	write-progress -id 4 -activity “Iniciando instâncias" -completed -status $opstat
	Write-Output "$(Get-Date -f $timeStampFormat) - Iniciando instâncias: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
	foreach ($roleInstance in $roleInstanceList)
	{
		if ($roleInstance.InstanceStatus -ne "ReadyRole")
		{
			return $false
		}
	}
	
	return $true
}

#configure powershell com módulos do Azure 1.7
Import-Module do Azure

#configure powershell com publishsettings para sua assinatura
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#defina as variáveis do ambiente restante para cmdlets do Azure
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publicar e gravar o progresso para o log de atividade
Write-Output "$(Get-Date -f $timeStampFormat) - Script de implantação do Serviço de Nuvem do Azure iniciado."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparando a implantação do $deploymentLabel para $subscriptionname com ID de assinatura $subscriptionid."

Publicar

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Serviço de Nuvem criado com URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Script de implantação do Serviço de Nuvem do Azure concluído."
</pre>

## Próximas etapas

Para habilitar a depuração remota ao usar entrega contínua, consulte [estas instruções](http://go.microsoft.com/fwlink/p/?LinkID=402354).

[Fornecimento contínuo para o Azure usando o Visual Studio Online]: cloud-services-continuous-delivery-use-vso.md
[Etapa 1: Configurar servidor de compilação]: #step1
[Etapa 2: criar um pacote usando comandos MSBuild]: #step2
[Etapa 3: criar um pacote usando o TFS Team Build (opcional)]: #step3
[Etapa 4: Publicar um pacote usando um Script do PowerShell]: #step4
[Etapa 5: publicar um pacote usando o TFS Team Build (opcional)]: #step5
[Team Foundation Build Service]: http://go.microsoft.com/fwlink/p/?LinkId=239963
[.NET Framework 4]: http://go.microsoft.com/fwlink/?LinkId=239538
[.NET Framework 4.5]: http://go.microsoft.com/fwlink/?LinkId=245484
[.NET Framework 4.5.2]: http://go.microsoft.com/fwlink/?LinkId=521668
[Ferramentas de criação do Azure]: http://go.microsoft.com/fwlink/?LinkId=239600
[Bibliotecas do Azure]: http://go.microsoft.com/fwlink/?LinkId=257862
[Ferramentas do Azure para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=257862
[Referência de linha de comando do MSBuild]: http://msdn.microsoft.com/library/ms164311(v=VS.90).aspx
[1]: http://go.microsoft.com/fwlink/p/?LinkId=239966
[Noções básicas sobre o Team Foundation Build System]: http://go.microsoft.com/fwlink/?LinkId=238798
[Configurar um computador de compilação]: http://go.microsoft.com/fwlink/?LinkId=238799
[0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01.png
[2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
[cmdlets do PowerShell do Azure]: http://go.microsoft.com/fwlink/?LinkId=256262
[the .publishsettings file]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
[final deste artigo]: #script
[3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
[4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
[5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
[6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png

<!--HONumber=54--> 