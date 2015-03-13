<properties 
	pageTitle="Fornecimento contínuo de serviços de nuvem com TFS no Azure" 
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

O processo descrito neste artigo mostra como configurar de forma contínua
a entrega de aplicativos na nuvem do Azure. Esse processo permite que você
criar pacotes e implaner o pacote para o Azure automaticamente
após cada verificação do código. O processo de criação do pacote descrito neste
artigo é equivalente ao comando Pacote no Visual Studio e as
etapas de publicação equivalem ao comando Publicar no Visual Studio.
O artigo aborda os métodos que você usaria para criar um servidor de compilação
com instruções de linha de comando do MSBuild e scripts do Windows PowerShell, e
ele também demonstra como configurar opcionalmente a equipe do Visual Studio
Foundation Server - definições de compilação de equipe para usar os comandos do MSBuild
e scripts do PowerShell. O processo é personalizável para seu ambiente de
compilação e ambientes de destino do Azure.

Você também pode usar o Visual Studio Online, uma versão do TFS hospedada no Azure, para fazer isso mais facilmente. Para obter mais informações, consulte [Fornecimento contínuo para o Azure usando o Visual Studio Online][].

Antes de começar, você deve publicar seu aplicativo do Visual Studio.
Isso garantirá que todos os recursos estejam disponíveis e inicializados quando você 
tentar automatizar o processo de publicação.

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Configurar o servidor de compilação][]
-   [Etapa 2: Criar um pacote usando comandos MSBuild][]
-   [Etapa 3: Criar um pacote usando o TFS Team Build (opcional)][]
-   [Etapa 4: Publicar um pacote usando um Script do PowerShell][]
-   [Etapa 5: Publicar um pacote usando o TFS Team Build (opcional)][]

<h2> <a name="step1"> </a>Etapa 1: Configurar o Servidor de compilação</h2>

Antes de criar um pacote do Azure usando o MSBuild, você deve
instalar as ferramentas e o software necessários no servidor de compilação.

O Visual Studio não precisa estar instalado no servidor de compilação. Se
quiser usar o Team Foundation Build Service para gerenciar seu servidor de
compilação, siga as instruções de [Team Foundation Build Service][]
.

1.  No servidor de compilação, instale o [.NET Framework 4][], [.NET Framework 4.5][] ou [.NET Framework 4.5.2][], que incluem o MSBuild.
2.  Instale as [Ferramentas de criação do Azure][] (procure MicrosoftAzureAuthoringTools-x86.msi ou MicrosoftAzureAuthoringTools-x64.msi, dependendo do processador do servidor de compilação). Versões mais antigas dos arquivos podem ter WindowsAzure no nome de arquivo.
3. Instale as [Bibliotecas do Azure][] (procure MicrosoftAzureLibsForNet-x86.msi ou MicrosoftAzureLibsForNet-x64.msi).
4.  Copie o arquivo Microsoft.WebApplication.targets de uma instalação do Visual Studio para o servidor de compilação. Em um computador com o Visual Studio instalado, o arquivo está localizado no diretório C:\\Arquivos de Programas(x86)\\MSBuild\\Microsoft\\VisualStudio\\v11.0\\WebApplications (v12.0 para o Visual Studio 2013). Você deve copiá-lo para o mesmo diretório no servidor de compilação.
5.   Instale as [Ferramentas do Azure para Visual Studio][]. Procure por MicrosoftAzureTools.VS110.exe para compilar projetos do Visual Studio 2012, por MicrosoftAzureTools.VS120.exe para compilar projetos do Visual Studio 2013 e por MicrosoftAzureTools.VS140.exe para compilar projetos do Visual Studio 2015 Preview.

<h2><a name="step2"> </a>Etapa 2: Compilar um Pacote usando comandos MSBuild</h2>

Esta seção descreve como criar um comando do MSBuild que compila um
pacote do Azure. Execute esta etapa no servidor de compilação para verificar se
tudo está configurado corretamente e se o comando do MSBuild faz
o que você deseja fazer. Você pode adicionar essa linha de comando para scripts
de compilação existentes no servidor de compilação, ou você pode usar a linha de comando em um
TFS Build Definition, conforme descrito na próxima seção. Para obter mais
informações sobre parâmetros de linha de comando e MSBuild, consulte [Referência de linha de comando do MSBuild][].

1.  Se o Visual Studio está instalado no servidor de compilação, clique em
    **Iniciar**, clique em **Todos os programas**, localize e clique em
    **Prompt de comando do Visual Studio** em **ferramentas do Visual Studio**
    .

    Se o Visual Studio não está instalado no servidor de compilação, abra um
    prompt de comando e certifique-se de que MSBuild.exe está acessível no
    caminho. O MSBuild é instalado com o .NET Framework no caminho   
    %WINDIR%\\Microsoft.NET\\Framework\\*Version*. Por exemplo, para
    adicionar MSBuild.exe à variável de ambiente PATH quando você tiver o .NET
    Framework 4 instalado, digite o seguinte comando no prompt de
    comando:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"

2.  No prompt de comando, navegue até a pasta que contém o arquivo de projeto do Microsoft
    Azure que você deseja criar.

3.  Execute msbuild com a opção /target:Publish, como no seguinte
    exemplo:

        MSBuild /target:Publish

    Essa opção pode ser abreviada como /t:Publish. A opção /t:Publish
    no MSBuild não deve ser confundida com os comandos
    disponíveis no Visual Studio quando você tem o SDK do Azure
    instalado. A opção /t:Publish compila apenas os pacotes do
    Azure. Ela não implanta os pacotes como os comandos Publicar
    no Visual Studio fazem.

    Opcionalmente, você também pode especificar o nome do projeto como um parâmetro
    do MSBuild Se não for especificado, o diretório atual é usado. Para obter mais
    informações sobre as opções de linha de comando do MSBuild, consulte [Referência da linha de
    comando do MSBuild][1].

4.  Localize a saída. Por padrão, esse comando cria um diretório na
    relação para a pasta raiz do projeto, como
    *ProjectDir*\\bin\\*configuração*\\app.publish\\. Quando você
    cria um projeto do Azure, você gera dois arquivos, o arquivo do
    pacote em si e o arquivo de configuração que acompanha este artigo:

    -   Project.cspkg
    -   ServiceConfiguration.*TargetProfile*.cscfg

    Por padrão, cada projeto do Azure inclui um
    arquivo de configuração de serviço (arquivo. cscfg) para o as compilações locais (depuração)
    e outro para compilações de nuvem (temporário ou produção), mas você
    pode adicionar ou remover arquivos de configuração de serviço conforme necessário. Quando você
    cria um pacote no Visual Studio, você será solicitado que
    o arquivo de configuração de serviço venha incluído junto com o pacote.

5.  Especifique o arquivo de configuração de serviço. Quando você cria um pacote
    usando MSBuild, o arquivo de configuração do serviço local é incluído por
    padrão. Para incluir um arquivo de configuração de serviço diferente, defina a
    propriedade TargetProfile do comando MSBuild, como a seguir, no
    exemplo:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Especifique o local para a saída. Defina o caminho usando a opção
    /p:PublishDir=*Diretório*\\, incluindo o separador
    de barra invertida à direita, como no exemplo a seguir:

        MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

    Depois que você criar e testar uma linha de comando do MSBuild apropriado
    para compilar seus projetos e combiná-los em um pacote do Azure,
    você pode adicionar essa linha de comando aos scripts de compilação. Se seu servidor de
    compilação usa scripts personalizados, esse processo dependerá de
    especifidades do processo de compilação personalizado. Se você estiver usando o TFS como um
    ambiente de compilação, siga as instruções na próxima
    etapa para adicionar a compilação do pacote do Azure ao processo de compilação.

<h2> <a name="step3"> </a>Etapa 3: Compilar um pacote usando o TFS Team Build (opcional)</h2>

Se você tiver o Team Foundation Server (TFS) configurado como um controlador de compilação
e o servidor de compilação configurado como uma máquina de compilação do TFS, você pode configurar
uma compilação automatizada para o pacote do Azure. Para obter informações sobre
como configurar e usar o Team Foundation Server como um sistema de compilação, consulte
[Noções básicas sobre o sistema de compilação do Team Foundation][] Em particular, o
seguinte procedimento pressupõe que você tenha configurado seu servidor de compilação
conforme descrito em [Configurar uma máquina de compilação][], e que você criou
um projeto de equipe, criou um projeto de serviço de nuvem no projeto de equipe.

Para configurar o TFS para compilar pacotes do Azure, execute as seguintes:
etapas:

1.  No Visual Studio no computador de desenvolvimento, no menu Exibição,
    escolha **Team Explorer**, ou Ctrl+\\, Ctrl+M. Na
    janela do Team Explorer, expanda o nó **Compilar** ou escolha a página **Compilações**
    e escolha **Nova definição de compilação**.

    ![][0]

2.  Clique na guia **Gatilho** e, em seguida, especifique as condições desejadas para
    quando você quiser que o pacote seja criado. Por exemplo, especifique
    **Integração contínua** para compilar o pacote sempre que uma fonte de
    verificação de controle ocorrer.

3.	Escolha a guia **Configurações de origem** e verifique se a pasta do projeto está listada
	na coluna **Pasta de Controle de Origem** e o status é **Ativo**.

4.  Escolha a guia **Padrões de compilação** e em controlador de compilação, verifique
    o nome do servidor de compilação.  Além disso, escolha a opção **copiar saída de 
    compilação para a seguinte pasta-depósito** e especifique o local de depósito
    desejado.

5.  Clique na guia **Processo**. Na guia Processar, escolha o modelo
    padrão, em **Compilar**, escolha o projeto se ainda não estiver selecionado,
    e expanda a seção **Avançado** na seção **Compilar** da grade. 

6.  Escolha **Argumentos de MSBuild**e defina os argumentos da linha de comando MSBuild
    apropriados conforme descrito na etapa 2 acima. Por exemplo,
    digite **/t:Publish /p:PublishDir=\\\\myserver\\drops\\** para compilar um
    pacote e copiar os arquivos de pacote para o local
    \\\\myserver\\drops\\:

    ![][2]

    **Observação:** copiando os arquivos para um compartilhamento público torna mais fácil
    implantar manualmente os pacotes do seu computador de desenvolvimento.

5.  Teste o êxito da etapa de compilação verificando uma alteração em seu
    projeto ou fila de uma nova compilação. Para enfileirar uma nova compilação, no
    Team Explorer, clique com botão direito do mouse em **Todas as definições de compilação,** e, em seguida,
    escolha **Enfileirar nova compilação**.

<h2> <a name="step4"> </a>Etapa 4: Publicar um Pacote usando um Script do PowerShell</h2>

Esta seção descreve como criar um script do Windows PowerShell que
publicará a saída de pacote de aplicativos de nuvem no Azure usando
parâmetros opcionais. Esse script pode ser chamado após a etapa de compilação em
sua automação de compilação personalizada. Ele também pode ser chamado a partir das atividades de fluxo de trabalho
do Modelo de Processo no Visual Studio TFS Team Build.

1.  Instale os [cmdlets do PowerShell do Azure][] (v0.6.1 ou superior).
    Durante a fase de instalação do cmdlet, opte por instalar como um snap-in. Observe
    que essa versão de suporte oficial substitui a versão mais antiga
    oferecida por meio do CodePlex, embora as versões anteriores foram numeradas com 2.x.x.

2.  Inicie o PowerShell do Azure usando o menu Iniciar. Se você iniciar dessa forma,
    os cmdlets do PowerShell do Azure serão carregados.

3.  No prompt do PowerShell, verifique se os cmdlets do PowerShell são carregados
    digitando o comando parcial Get-Azure e, em seguida, pressionando tab para a conclusão da
    instrução.

    Se pressionar tab repetidamente, você deverá ver diversos comandos do PowerShell do Azure.

4.  Verifique se você pode se conectar à sua assinatura do Azure ao
    importar as informações de assinatura do arquivo .publishsettings.

    Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings

    Em seguida, dê o comando

    Get-AzureSubscription

    Isso exibirá informações sobre a sua assinatura. Verifique se tudo está correto.

4.  Salve o modelo de script fornecido no [final deste artigo][] para
    sua pasta de scripts como
    c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.

5.  Consulte a seção de parâmetros do script. Adicione ou modifique quaisquer
    valores padrão. Esses valores podem ser substituídos sempre passando nos
    parâmetros explícitos.

6.  Verifique se há contas de armazenamento e serviço de nuvem válidas criadas
    em sua assinatura que podem ser direcionadas pelo script de publicação. A
    conta de armazenamento (armazenamento de blob) será usada para carregar e
    armazenar temporariamente o arquivo de pacote de implantação e configuração enquanto a
    implantação está sendo criada.

    -   Para criar um novo serviço de nuvem, você pode chamar esse script ou usar
        o Portal de Gerenciamento do Azure. O nome do serviço de nuvem
        será usado como um prefixo em um nome de domínio totalmente qualificado e,
        portanto, deve ser exclusivo.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Para criar uma nova conta de armazenamento, você pode chamar esse script ou usar
        o Portal de Gerenciamento do Azure. O nome da conta de armazenamento
        será usado como um prefixo em um nome de domínio totalmente qualificado e,
        portanto, deve ser exclusivo. Você pode tentar usar o mesmo nome que o
        serviço de nuvem.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

7.  Chame o script diretamente do PowerShell do Azure ou conecte este
    script para a automação de compilação do host para ocorrer após a compilação do
    pacote.

    **AVISO:** o script sempre excluirá ou substituirá as implantações existentes
    por padrão, se forem detectadas. Isso é necessário para
    habilitar o fornecimento contínuo de automação onde nenhum aviso ao usuário
    é possível.

    **Cenário de exemplo 1:** implantação contínua para o teste
    de ambiente de um serviço:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    Isso é normalmente seguido pela verificação da execução de teste e uma troca de
    VIP. A troca de VIP pode ser feita por meio do Portal de Gerenciamento do Azure
    ou usando o cmdlet Move-Deployment.

    **Cenário de exemplo 2:** implantação contínua para a produção
    do ambiente de um serviço de teste dedicado

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Área de trabalho remota:**

    Se a área de trabalho remota está habilitada no projeto do Azure, você irá
    precisar executar etapas adicionais únicas para garantir que o
    Certificado de serviço de nuvem correto é carregado para todos os serviços de nuvem
    direcionados por esse script.

    Localize os valores de impressão digital do certificado esperados por suas funções. Os
    valores de impressão digital são visíveis na seção certificados do
    arquivo de configuração de nuvem (ou seja, ServiceConfiguration.Cloud.cscfg). É
    também visível na caixa de diálogo Configuração da área de trabalho remota no Visual
    Studio quando você mostrar opções e exibir o certificado selecionado.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Carregue certificados de área de trabalho remota como uma etapa de configuração única usando
    o script de cmdlet a seguir:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

    Por exemplo:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    Como alternativa, você pode exportar o arquivo PFX de certificado com a chave particular
    e carregar certificados para cada serviço de nuvem de destino usando o
    Portal de Gerenciamento do Azure. Leia o seguinte artigo para saber
    mais:
    [http://msdn.microsoft.com/library/windowsazure/gg443832.aspx][].

    **Atualizar implantação vs. Excluir implantação -\> Nova Implantação**

    O script executará, por padrão, uma implantação de atualização
    ($enableDeploymentUpgrade = 1) quando nenhum parâmetro é passado ou
    o valor 1 é passado explicitamente. Para instâncias únicas isso tem a
    vantagem de levar menos tempo que uma implantação completa. Para instâncias
    que exigem alta disponibilidade, isso também tem a vantagem de
    deixar algumas instâncias em execução enquanto outras são atualizados (movimentação
    seu domínio de atualização), além de seu VIP não ser excluído.

    Implantação de atualização pode ser desabilitada no script
    ($enableDeploymentUpgrade = 0) ou passando
    -enableDeploymentUpgrade 0 como um parâmetro, o que alterará o
    comportamento do script para excluir primeiro qualquer implantação existente e, em seguida,
    criar uma nova implantação.

    **Aviso:** o script sempre excluirá ou substituirá as implantações existentes
    por padrão, se forem detectadas. Isso é necessário para
    habilitar o fornecimento contínuo de automação onde nenhum usuário/operador
    é possível solicitar.

<h2><a name="step5"> </a>Etapa 5: Publicar um pacote usando o TFS Team Build (opcional)</h2>

Esta etapa irá conectar TFS Team Build ao script criado na etapa 4,
que identifica a publicação da compilação do pacote no Azure. Este
infere modificando o modelo de processo usado pela sua definição de compilação, então
ele executa uma atividade Publicar no final do fluxo de trabalho. A atividade Publicar
executará o comando PowerShell passando parâmetros de
compilação. Saída dos destinos do MSBuild e publicação do script serão
canalizadas para a saída de compilação padrão.

1.  Edite a definição de compilação responsável pela implantação contínua.

2.  Selecione a guia **Processo**.

3.	Siga [estas instruções](http://msdn.microsoft.com/library/dd647551.aspx) para adicionar um
    projeto de atividade para o modelo de processo de compilação, baixe o modelo padrão, adicione-o
	ao projeto e à verificação. Atribua um novo nome ao modelo de processo de compilação como
	AzureBuildProcessTemplate.

3.  Volte à guia **Processar** e use **Mostrar Detalhes** para mostrar uma lista dos
	modelos de processos de compilação disponíveis. Escolha o botão **Novo...** e navegue para o projeto
	que acabou de adicionar e verificar. Localize o modelo que você acabou de criar e escolha **OK**.

4.  Abra o modelo de processo selecionado para edição. Você pode abrir
    diretamente no designer do fluxo de trabalho ou no editor de XML para trabalhar com
    o XAML.

5.  Adicione a seguinte lista de novos argumentos como itens de linha separados na
    guia de argumentos do designer de fluxo de trabalho. Todos os argumentos devem
    ter direction=In e type=String. Esses serão usados para os parâmetros do fluxo
    de definição de compilação para o fluxo de trabalho, que então
    é usado para chamar o script de publicação.

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

    1.  Comece adicionando uma atividade de declaração If para verificar se há um arquivo
        de script válido. Defina a condição para este valor:

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  No caso Then da declaração If, adicione uma nova atividade de
        sequência. Defina o nome de exibição para 'Start publish'

    3.  Com a sequência de Iniciar publicação ainda selecionada, adicione a
        lista seguinte de novas variáveis como itens de linha separados na
        guia de variáveis do designer de fluxo de trabalho. Todas as variáveis devem
        ter a variável type =String e Scope=Start publish. Eles serão
        usados para os parâmetros de fluxo da definição de compilação para o
        fluxo de trabalho que, em seguida, é usado para chamar o script de publicação.

        -   SubscriptionDataFilePath, do tipo String

        -   PublishScriptFilePath, do tipo String

            ![][4]

    4.  Se estiver usando o TFS 2012 ou anterior, adicione uma atividade ConvertWorkspaceItem ao início da nova
        Sequência. Se estiver usando o TFS 2013 ou posterior, adicione uma atividade GetLocalPath ao início da nova sequência. Para um ConvertWorkspaceItem, defina as propriedades da seguinte forma: Direction=ServerToLocal, DisplayName='Convert publish
        script filename', Input=' PublishScriptLocation',
        Result='PublishScriptFilePath', Workspace='Workspace'. Para uma atividade de GetLocalPath, defina a propriedade IncomingPath para 'PublishScriptLocation', e o resultado para 'PublishScriptFilePath'. Esta
        atividade converte o caminho para o script de publicação dos locais do servidor TFS
        (se aplicável) para um caminho de disco local padrão.

    5.  Se estiver usando o TFS 2012 ou anterior, adicione outra atividade ConvertWorkspaceItem ao final da nova
        Sequência. Direction=ServerToLocal, DisplayName='Convert
        subscription filename', Input=' SubscriptionDataFileLocation',
        Result= 'SubscriptionDataFilePath', Workspace='Workspace'. Se estiver usando o TFS 2013 ou posterior, adicione outra atividade GetLocalPath. IncomingPath='SubscriptionDataFileLocation' e Result='SubscriptionDataFilePath.'

    6.  Adicione uma atividade InvokeProcess no final da nova sequência.
        Essa atividade chama PowerShell.exe com os argumentos passados
        pela definição de compilação.

        1.  Argumentos = String.Format(" -File ""{0}"" -serviceName {1}
            -storageAccountName {2} -packageLocation ""{3}""
            -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}""
            -selectedSubscription {6} -environment ""{7}""",
            PublishScriptFilePath, ServiceName, StorageAccountName,
            PackageLocation, CloudConfigLocation,
            SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName = Execute publish script

        3.  FileName = "PowerShell" (incluindo as aspas)

        4.  OutputEncoding=
            System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)

    7.  Na caixa de texto da seção **Gerenciar com saída padão** do
        InvokeProcess, defina o valor de caixa de texto como 'data'. Esta é uma
        variável para armazenar os dados de saída padrão.

    8.  Adicione uma atividade WriteBuildMessage logo abaixo da seção **Gerenciar Saída Padrão**.
        . Defina Importance =
        'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High'
        e o Message='data'. Isso garante que a saída padrão do
        script será gravada na saída de compilação.

    9.  Na caixa de texto da seção **Gerenciar saída de erro** do
        InvokeProcess, defina o valor de caixa de texto como 'data'. Esta é uma
        variável para armazenar os dados de erro padrão.

    10. Adicione uma atividade WriteBuildError pouco abaixo da seção **Gerenciar Erro de Saída**.
        . Defina Message='data'. Isso garante que os erros padrão
        do script serão gravados na saída de erro de compilação.

	11. Corrija quaisquer erros, indicados por sinais de exclamação azuis. Passe o ponteiro do mouse sobre
		os sinais de exclamação para ver informações sobre o erro. Salve o fluxo de trabalho para 
		limpar os erros.

    O resultado final das atividades de fluxo de trabalho de publicação parecerá como
    este no designer:

    ![][5]

    O resultado final das atividades de fluxo de trabalho de publicação parecerá como
    este no XAML:

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

8.  Edite a definição de compilação (feche-a se já estiver aberta) e selecione
	o botão **Novo** se você não ver o novo modelo na lista de modelos de processo.

9.  Defina os valores da propriedade do parâmetro na seção Vários como segue:

    1.  CloudConfigLocation ='c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg'   
        *This value is derived from:
        ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation = 'c:\\drops\\app.publish\\ContactManager.Azure.cspkg'   
        *This value is derived from: ($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation = 'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename'   
        *Use the appropriate cloud service name here*

    5.  Environment = 'Staging'

    6.  StorageAccountName = 'mystorageaccountname'   
        *Use the appropriate storage account name here*

    7.  SubscriptionDataFileLocation =
        'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'default'

    ![][6]

10. Salve as alterações para a Definição de Compilação.

11. Coloque uma compilação em fila para executar a compilação do pacote e publicar. Se você
    tiver um disparador definido para a integração contínua, você executará este
    comportamento em cada verificação.

### <a name="script"> </a>Modelo de script PublishCloudService.ps1

<pre>
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )
      

function Publish()
{
	$deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue 
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
	if ($deployment.Name -ne $null)
	{
		switch ($alwaysDeleteExistingDeployments)
	    {
	        1 
			{
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
				        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
				        DeleteDeployment
                        CreateNewDeployment
                        
                    }
                } # switch ($enableDeploymentUpgrade)
			}
	        0
			{
				Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
				exit
			}
	    } #switch ($alwaysDeleteExistingDeployments)
	} else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
	write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
	Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

	$opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName
	    
    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
	Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"
    
	StartInstances
}

function UpgradeDeployment()
{
	write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
	Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
	$setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force
    
    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid
    
    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
	Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

	write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
	Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
	$removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

	write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
	Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"
	
}

function StartInstances()
{
	write-progress -id 4 -activity "Starting Instances" -status "In progress"
	Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

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

			write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
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
	
	write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
	Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
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

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
</pre>

## Próximas etapas

Para habilitar a depuração remota ao usar entrega contínua, consulte [estas instruções](http://go.microsoft.com/fwlink/p/?LinkID=402354). 

  [Fornecimento contínuo no Azure usando o Visual Studio Online:]: ../cloud-services-continuous-delivery-use-vso/
  [Etapa 1: Configurar o Servidor de compilação]: #step1
  [Etapa 2: Criar um pacote usando comandos MSBuild]: #step2
  [Etapa 3: Criar um pacote usando o TFS Team Build (opcional)]: #step3
  [Etapa 4: Publicar um Pacote usando um Script do PowerShell]: #step4
  [Etapa 5: Publicar um pacote usando o TFS Team Build (opcional)]: #step5
  [Team Foundation Build Service]: http://go.microsoft.com/fwlink/p/?LinkId=239963
  [.NET Framework 4]: http://go.microsoft.com/fwlink/?LinkId=239538
  [.NET Framework 4.5]: http://go.microsoft.com/fwlink/?LinkId=245484
  [.NET Framework 4.5.2]: http://go.microsoft.com/fwlink/?LinkId=521668
  [Ferramentas de criação do Azure]: http://go.microsoft.com/fwlink/?LinkId=239600
  [Bibliotecas do Azure]: http://go.microsoft.com/fwlink/?LinkId=257862
  [Ferramentas do Azure para Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=257862
  [Referência de linha de comando do MSBuild]: http://msdn.microsoft.com/library/ms164311(v=VS.90).aspx
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=239966
  [Noções básicas sobre o sistema de compilação do Team Foundation]: http://go.microsoft.com/fwlink/?LinkId=238798
  [Configurar uma máquina de compilação]: http://go.microsoft.com/fwlink/?LinkId=238799
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01.png
  [2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [Cmdlets do PowerShell do Azure]: http://go.microsoft.com/fwlink/?LinkId=256262
  [o arquivo .publishsettings]: https://manage.windowsazure.com/download/publishprofile.aspx?wa=wsignin1.0
  [final deste artigo]: #script
  
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png

<!--HONumber=45--> 
