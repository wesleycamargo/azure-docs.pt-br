<properties urlDisplayName="Continuous Delivery" pageTitle="Entrega cont&iacute;nua de servi&ccedil;os de nuvem com TFS no Azure" metaKeywords="Azure continuous delivery, continuous delivery sample code, continuous delivery PowerShell" description="Saiba como configurar a entrega cont&iacute;nua para aplicativos de nuvem do Azure. Exemplos de c&oacute;digo para instru&ccedil;&otilde;es de linha de comando do MSBuild e scripts do PowerShell." metaCanonical="" services="" documentationCenter="" title="Fornecimento cont&iacute;nuo de servi&ccedil;os de nuvem no Azure" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ghogen" />

# Fornecimento contínuo de serviços de nuvem no Azure

O processo descrito neste artigo mostra como configurar o fornecimento
contínuo de aplicativos na nuvem do Azure. Esse processo permite que você
crie pacotes automaticamente e implante o pacote no Azure
após o check-in de cada código. O processo de compilação do pacote descrito neste
artigo é equivalente ao comando de Pacote no Visual Studio, e as
etapas de publicação são equivalentes ao comando Publicar no Visual Studio.
O artigo aborda os métodos que você usaria para criar um servidor de compilação
com instruções de linha de comando do MSBuild e scripts do Windows PowerShell, além
de demonstrar como configurar as definições do Visual Studio TeamFoundation Server Team Build para usar os comandos do MSBuild
e os scripts do PowerShell. O processo é personalizável para o ambiente
de compilação e os ambientes de destino do Azure.

Você também pode usar o Visual Studio Online, uma versão do TFS hospedada no Azure, para fazer isso mais facilmente. Para obter mais informações, consulte [Fornecimento contínuo para o Azure usando o Visual Studio Online][Fornecimento contínuo para o Azure usando o Visual Studio Online].

Antes de começar, você deve publicar seu aplicativo do Visual Studio.
Isso garantirá que todos os recursos estejam disponíveis e sejam inicializados quando você
tentar automatizar o processo de publicação.

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Configurar o servidor de compilação][Etapa 1: Configurar o servidor de compilação]
-   [Etapa 2: Criar um pacote usando comandos MSBuild][Etapa 2: Criar um pacote usando comandos MSBuild]
-   [Etapa 3: Criar um pacote usando o TFS Team Build (opcional)][Etapa 3: Criar um pacote usando o TFS Team Build (opcional)]
-   [Etapa 4: Publicar um pacote usando um Script do PowerShell][Etapa 4: Publicar um pacote usando um Script do PowerShell]
-   [Etapa 5: Publicar um pacote usando o TFS Team Build (opcional)][Etapa 5: Publicar um pacote usando o TFS Team Build (opcional)]

## <a name="step1"> </a><span class="short-header">Configurar o servidor de compilação</span>Etapa 1: Configurar o servidor de compilação

Antes de criar um pacote do Azure usando o MSBuild, você deve
instalar o software e as ferramentas obrigatórios no servidor de compilação.

O Visual Studio não precisa estar instalado no servidor de compilação. Se
você quiser usar o Team Foundation Build Service para gerenciar o servidor
de compilação, siga as instruções no [Team Foundation Build Service][Team Foundation Build Service]
.

1.  No servidor de compilação, instale o [.NET Framework 4][.NET Framework 4] ou o [.NET Framework 4.5][.NET Framework 4.5], que
    inclui o MSBuild.
2.  Instale as [Ferramentas de criação do Azure][Ferramentas de criação do Azure] (procure
    WindowsAzureAuthoringTools-x86.msi ou WindowsAzureAuthoringTools-x64.msi,
    dependendo do processador do servidor de compilação).
3.  Instale as [Bibliotecas do Windows Azure][Bibliotecas do Windows Azure] (procure WindowsAzureLibsForNet-x86.msi ou WindowsAzureLibsForNet-x64.msi).
4.  Copie o arquivo Microsoft.WebApplication.targets de uma instalação do Visual Studio
    para o servidor de compilação. Em um computador com o Visual Studio
    instalado, o arquivo está localizado no diretório C:\\Program Files
    (x86)\\MSBuild\\Microsoft\\VisualStudio\\v11.0\\WebApplications (v12.0
    para o Visual Studio 2013). Você
    deve copiá-lo para o mesmo diretório no servidor de compilação.
5.  Instale as [Ferramentas do Azure para Visual Studio][Bibliotecas do Windows Azure].
    Procure WindowsAzureTools.VS120.exe para compilar projetos do Visual Studio 2012 e WindowsAzureTools.VS120.exe para compilar projetos do Visual Studio 2013.

## <a name="step2"> </a><span class="short-header">Criar um pacote usando MSBuild</span>Etapa 2: Criar um pacote usando comandos MSBuild

Esta seção descreve como criar um comando do MSBuild que compila um
pacote do Azure. Execute esta etapa no servidor de compilação para verificar se
tudo está configurado corretamente e se o comando do MSBuild faz o que você deseja fazer. Você pode adicionar essa linha de comando para scripts
de construção existentes no servidor de compilação, ou você pode usar a linha de comando
em uma definição de compilação do
TFS, conforme descrito na próxima seção. Para obter
mais informações sobre parâmetros de linha de comando e MSBuild, consulte [Referência de linha de comando do MSBuild][Referência de linha de comando do MSBuild].

1.  Se o Visual Studio está instalado no servidor de compilação, clique em
    **Iniciar**, em **Todos os Programas** e, em seguida, localize e clique em
    **Prompt de Comando do Visual Studio** na pasta **Ferramentas do Visual Studio**
     .

    Se o Visual Studio não estiver instalado no servidor de compilação, abra um
    prompt de comando e verifique se MSBuild.exe está acessível no
    caminho. MSBuild está instalado com o .NET Framework no caminho
    %WINDIR%\\Microsoft.NET\\Framework\\*Version*. Por exemplo, para
    adicionar MSBuild.exe à variável de ambiente PATH quando você tem o.NET
    Framework 4 instalado, digite o seguinte comando no prompt de
    comando:

        set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework.0"

2.  No prompt de comando, navegue até a pasta que contém o arquivo de projeto
    do Azure que você deseja compilar.

3.  Execute msbuild com a opção /target:Publish como no seguinte
    exemplo:

        MSBuild /target:Publish

    Essa opção pode ser abreviada como /t:Publish. A opção /t:Publish
    no MSBuild não deve ser confundida com os comandos
    Publicar disponíveis no Visual Studio quando você tem o SDK do Azure
    instalado. A opção /t:Publish compila apenas os pacotes do
    Azure. Ela não implanta os pacotes assim como os comandos Publish no
    Visual Studio.

    Você também pode especificar o nome do projeto como um
    parâmetro do MSBuild. Se não for especificado, o diretório atual é usado. Para obter
    mais informações sobre as opções da linha de comando, consulte [Referência
    da linha de comando do MSBuild][Referência
    da linha de comando do MSBuild].

4.  Localize a saída. Por padrão, este comando cria um diretório na
    relação para a pasta raiz do projeto, como
    *ProjectDir*\\bin\\*Configuration*\\app.publish\\. Ao
    criar um projeto do Azure, você gera dois arquivos, o arquivo
    de pacote propriamente dito e o arquivo de configuração de acompanhamento:

    -   Project.cspkg
    -   ServiceConfiguration.*TargetProfile*.cscfg

    Por padrão, cada projeto do Azure inclui um
    arquivo de configuração de serviço (arquivo .cscfg) para compilações (depuração)
    locais e outro para compilações de nuvem (de preparo ou produção), mas você
    pode adicionar ou remover arquivos de configuração do serviço conforme necessário. Quando você
    cria um pacote dentro do Visual Studio, você será solicitado o
    arquivo de configuração do serviço para incluir junto com o pacote.

5.  Especifique o arquivo de configuração do serviço. Quando você compila um pacote
    usando o MSBuild, o arquivo de configuração do serviço local é incluído
    por padrão. Para incluir um arquivo de configuração de serviço diferente, defina a
    propriedade TargetProfile do comando MSBuild, como no seguinte
    exemplo:

        MSBuild /t:Publish /p:TargetProfile=Cloud

6.  Especifique o local para a saída. Defina o caminho usando a opção
    /p:PublishDir=*Directory*\\, incluindo o separador de barra invertida
    à direita, como no seguinte exemplo:

        MSBuild /target:Publish /p:PublishDir=\myserver\drops\

    Depois de criar e testar uma linha de comando do MSBuild apropriada
    para compilar os projetos e integrá-los em um pacote do Azure,
    você poderá adicionar essa linha de comando aos scripts de compilação. Se seu servidor
    de decompilação usa scripts personalizados, esse processo dependerá
    das especificidades de seu processo de compilação personalizada. Se estiver usando o TFS como um
    ambiente de compilação, você poderá seguir as instruções na próxima
    etapa para adicionar a compilação do pacote do Azure ao processo de compilação.

## <a name="step3"> </a><span class="short-header">Criar um pacote usando TFS </span>Etapa 3: Criar um pacote usando o TFS Team Build (opcional)

Se tiver o Team Foundation Server (TFS) configurado como um controlador de compilação
e o servidor de compilação estiver configurado como uma máquina de compilação TFS, você poderá configurar
uma compilação automatizada para o pacote do Azure. Para obter informações
sobre como configurar e usar o Team Foundation server como um sistema de compilação, consulte
[Noções básicas sobre o Team Foundation Build System][Noções básicas sobre o Team Foundation Build System]. Em particular, o
procedimento a seguir supõe que você tenha configurado seu servidor de compilação,
conforme descrito em [Configurar uma máquina de compilação][Configurar uma máquina de compilação].

Para configurar o TFS para compilar pacotes do Azure, execute as seguintes
etapas:

1.  No Visual Studio, no computador de desenvolvimento, no menu Exibir,
    escolha **Team Explorer** ou Ctrl+\\\\, Ctrl+M.
    Najanela Team Explorer, expanda o nó **Compilação**, clique com o botão direito em **Todas as
    Definições de Compilação** e, em seguida, clique em **Nova Definição de Compilação**:

    ![][0]

2.  Clique na guia **Processo**. Na guia Processo, escolha o modelo
    padrão, em Itens a Compilar, escolha o projeto
    e expanda a seção **Avançado** na grade.

3.  Escolha **Argumentos do MSBuild** e defina os argumentos da
    linha de comando do MSBuild apropriados, conforme descrito na Etapa 2 acima. Por exemplo,
    digite **/t:Publish /p:PublishDir=\\\\myserver\\drops\\** para compilar um
    pacote e copiar os arquivos do pacote para o local
    \\\\myserver\\drops\\:

    ![][1]

    **Observação:**Copiar os arquivos para um compartilhamento público torna mais fácil
    implantar manualmente os pacotes do seu computador de desenvolvimento.

4.  Clique na guia **Gatilho** e especifique as condições desejadas
    para quando deseja que o pacote seja compilado. Por exemplo, especifique
    **Integração Contínua** para compilar o pacote sempre que um check-in
    de controle do código-fonte ocorrer.

5.  Escolha a guia **Padrões de Compilação** e, em Compilar controlador, verifique
    o nome do servidor de compilação. Além disso, escolha a opção **Copiar resultado da criação
    para a seguinte pasta de recebimento** e especifique o local
    de recebimento desejado.

6.  Teste o êxito da etapa de compilação fazendo check-in de uma alteração para o
    projeto ou coloque em fila uma nova compilação. Para colocar em fila uma nova compilação, no
    Team Explorer, clique com o botão direito do mouse em **Todas as Definições de Compilação** e, em seguida
    escolha **Criar Nova Fila**.

## <a name="step4"> </a><span class="short-header">Publicar usando o Powershell</span>Etapa 4: Publicar um pacote usando um Script do PowerShell

Esta seção descreve como criar um script do Windows PowerShell que
publicará a saída do pacote do aplicativo de nuvem para o Azure usando
parâmetros opcionais. Esse script pode ser chamado após a etapa de compilação na
sua automação de compilação personalizada. Ele também pode ser chamado de atividades
do fluxo de trabalho do modelo de processo no Visual Studio TFS Team Build.

1.  Instale os [cmdlets do PowerShell do Azure][cmdlets do PowerShell do Azure] (v0.6.1 ou superior).
    Durante a fase de instalação do cmdlet, opte por instalar como um snap-in. Observe
    que essa versão suportada oficialmente substitui a versão mais antiga
    oferecida por meio do CodePlex, embora as versões anteriores foram numeradas 2.x.x.

2.  Inicie o Azure PowerShell usando o menu Iniciar. Se você iniciar dessa forma,
    os cmdlets do PowerShell do Azure serão carregados.

3.  No prompt do PowerShell, verifique se os cmdlets do PowerShell são carregados
    digitando o comando parcial Get-Azure e pressionando tab para conclusão
    da instrução.

    Você deve ver diversos comandos do PowerShell do Azure.

4.  Verifique se você consegue se conectar à assinatura do Azure
    importando as informações de assinatura do arquivo .publishsettings.

    Import-AzurePublishSettingsFile c:\\scripts\\WindowsAzure\\default.publishsettings

    Em seguida, dê o comando

    Get-AzureSubscription

    Isso exibirá informações sobre a sua assinatura. Verifique se tudo está correto.

5.  Salve o modeço do script fornecido no [final deste artigo][final deste artigo] para
    sua pasta de scripts como
    c:\\scripts\\WindowsAzure\\**PublishCloudService.ps1**.

6.  Consulte a seção de parâmetros do script. Adicionar ou modificar
    os valores padrão. Esses valores podem ser substituídos sempre passando
    parâmetros explícitos.

7.  Verifique se há contas de serviço de nuvem e de armazenamento válidas criadas
    na assinatura que possam ser direcionadas pelo script de publicação. A
    conta de armazenamento (armazenamento de blob) será usada para carregar e
    armazenar temporariamente o arquivo de configuração e o pacote de implantação, enquanto a
    implantação está sendo criada.

    -   Para criar um novo serviço de nuvem, você pode chamar esse script ou usar
        o Portal de Gerenciamento do Azure. O nome do serviço de nuvem
        será usado como um prefixo em um nome de domínio totalmente qualificado e
        ,portanto, deve ser exclusivo.

            New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

    -   Para criar uma nova conta de armazenamento, você pode chamar esse script ou usar
        o Portal de Gerenciamento do Azure. O nome da conta de armazenamento
        será usado como um prefixo em um nome de domínio totalmente qualificado e
        ,portanto, deve ser exclusivo. Você pode tentar usar o mesmo nome que o
        serviço de nuvem.

            New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"

8.  Chame o script diretamente do PowerShell do Azure, ou conecte esse
    script à automação de compilação do host para ocorrer após a compilação do
    pacote.

    **AVISO:** O script irá sempre excluir ou substituir as implantações
    existentes por padrão, se eles forem detectados. Isso é necessário para
    habilitar o fornecimento contínuo de automação onde é possível sem nenhum
    aviso ao usuário.

    **Exemplo do cenário 1:**implantação contínua ao ambiente
    de preparo de um serviço:

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    Isso é normalmente seguido pela verificação da execução do teste e uma troca
    de VIP. A troca de VIP pode ser feita por meio do Portal de Gerenciamento
    do Azure ou usando o cmdlet Move-Deployment.

    **Exemplo do cenário 2:**implantação contínua ao ambiente de produção
    de um serviço de teste exclusivo

        PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

    **Área de trabalho remota:**

    Se a Área de Trabalho Remota estiver habilitada no projeto do Azure, você
    precisará realizar etapas individuais adicionais para garantir que o
    certificado de serviço de nuvem seja carregado em todos os serviços de nuvem
    segmentados por esse script.

    Localize os valores de impressão digital do certificado esperados por suas funções. Os
    valores da impressão digital são visíveis na seção Certificados
    no arquivo de configuração de nuvem (ou seja, ServiceConfiguration.Cloud.cscfg). Ela
    também é visível na caixa de diálogo Configuração de área de trabalho remota
    no Visual Studio quando você Mostrar opções e exibir o certificado selecionado.

        <Certificates>
              <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
        </Certificates>

    Carregar certificados de área de trabalho remota como uma etapa única de configuração
    usando o seguinte script de cmdlet:

        Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MYAdd-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)lt;THUMBPRINT>)

    Por exemplo:

        Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

    Você também pode exportar o arquivo de certificado PFX com a chave
    privada e carregar certificados para cada serviço de nuvem de destino usando o
    Portal de Gerenciamento do Azure. Leia o seguinte artigo para saber
    mais:
    [http://msdn.microsoft.com/pt-br/library/windowsazure/gg443832.aspx][http://msdn.microsoft.com/pt-br/library/windowsazure/gg443832.aspx].

    **Atualizar implantação vs. Excluir implantação -\> Nova implantação**

    O script irá por padrão executar uma implantação de atualização
    ($enableDeploymentUpgrade = 1) quando nenhum parâmetro é passado ou o
    valor 1 é passado explicitamente. Para instâncias únicas isso tem a
    vantagem de levar menos tempo do que uma implantação completa. Para instâncias
    que exigem alta disponibilidade, que isso também tem a vantagem de
    deixar algumas instâncias em execução enquanto outras são atualizadas (movimentação
    de seu domínio de atualização), além de seu VIP não será excluído.

    A implantação de atualização pode ser desabilitada no script
    ($enableDeploymentUpgrade = 0) ou passando
    -enableDeploymentUpgrade 0 como um parâmetro, que irá alterar o
    comportamento do script para excluir primeiro qualquer implantação existente e
    ,em seguida, criar uma nova implantação.

    **Aviso:** O script irá sempre excluir ou substituir as implantações
    existentes por padrão, se eles forem detectados. Isso é necessário para
    habilitar o fornecimento contínuo de automação onde é possível sem nenhum
    aviso ao usuário/operador.

## <a name="step5"> </a><span class="short-header">Publicar usando TFS </span>Etapa 5: Publicar um pacote usando o TFS Team Build (opcional)

Esta etapa conectará o TFS Team Build ao script criado na etapa 4,
que identifica a publicação da compilação do pacote no Azure. Isso
implica modificar o modelo de processo usado pela sua definição de compilação para
que seja executada uma atividade publicar no final do fluxo de trabalho. A atividade
de publicação executará o comando PowerShell passando parâmetros
da compilação. A saída dos destinos do MSBuild e o script de publicação serão
redirecionados para a saída de compilação padrão.

1.  Edite a definição de compilação responsável pela implantação contínua.

2.  Selecione a guia **Processo**.

3.  Crie um novo modelo de processo clicando no botão Novo... na
    área Seleção do Modelo de Processo da guia. A caixa de diálogo
    Novo Modelo de Processo de Compilação será carregada. Nesta caixa de diálogo, certifique-se
    de que Copiar um arquivo XAML existente está selecionado, e navegue até ProcessTemplate
    para copiar se você deseja alterar a base do padrão. Forneça
    o novo modelo com um nome como DefaultTemplateAzure.

4.  Abra o modelo de processo selecionado para edição. Você pode abrir
    diretamente no designer de fluxo de trabalho ou no editor de XML para trabalhar com
    o XAML.

5.  Adicione a seguinte lista de novos argumentos como itens de linha separados na
    guia de argumentos do designer de fluxo de trabalho. Todos os argumentos devem
    ter direção =In e digite =String. Esses serão
    usados para parâmetros de fluxo da definição de compilação para o
    fluxo de trabalho, que, em seguida, é usado para chamar o script de publicação.

        SubscriptionName
        StorageAccountName
        CloudConfigLocation
        PackageLocation
        Environment
        SubscriptionDataFileLocation
        PublishScriptLocation
        ServiceName

    ![][2]

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

    1.  Comece adicionando uma atividade de declaração If para verificar se há um
        arquivo de script válido. Defina a condição para este valor:

            Not String.IsNullOrEmpty(PublishScriptLocation)

    2.  No caso Em da declaração If, adicione uma nova atividade de
        sequência. Defina o nome de exibição para 'Iniciar publicação'

    3.  Com a sequência de publicação inicial ainda selecionada, adicione a
        lista a seguir de novas variáveis como itens de linha separadas na
        guia de variáveis do designer de fluxo de trabalho. Todas as variáveis devem
        ter um tipo de Variável =String e Escopo=Início da publicação. Esses serã
        ousados para parâmetros de fluxo da definição de compilação para o
        fluxo de trabalho, que, em seguida, é usado para chamar o script de publicação.

        -   SubscriptionDataFilePath, do tipo String

        -   PublishScriptFilePath, do tipo String

            ![][3]

    4.  Adicione uma atividade ConvertWorkspaceItem no início da nova
        sequência. Direction=ServerToLocal, DisplayName='Convert publish
        script filename', Input=' PublishScriptLocation',
        Result='PublishScriptFilePath', Workspace='Workspace'. Esta
        atividade converte o caminho para o script de publicação dos locais do servidor TFS
        (se aplicável) para um caminho de disco local padrão.

    5.  Adicione outra atividade ConvertWorkspaceItem ao final da nova
        sequência. Direction=ServerToLocal, DisplayName='Convert
        subscription filename', Input=' SubscriptionDataFileLocation',
        Result= 'SubscriptionDataFilePath', Workspace='Workspace'.

    6.  Adicione uma atividade InvokeProcess ao final da nova sequência.
        Essa atividade chama PowerShell.exe com os argumentos passados pela
        definição de compilação.

        1.  Argumentos = String.Format(" -File ""{0}"" -serviceName {1}
            -storageAccountName {2} -packageLocation ""{3}""
            -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}""
            -selectedSubscription {6} -environment ""{7}""",
            PublishScriptFilePath, ServiceName, StorageAccountName,
            PackageLocation, CloudConfigLocation,
            SubscriptionDataFilePath, SubscriptionName, Environment)

        2.  DisplayName ='Execute publish script'

        3.  FileName = "PowerShell"

        4.  OutputEncoding=
            'System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)'

    7.  Na caixa de texto da seção Identificar Saída Padrão de
        InvokeProcess, defina o valor da caixa de texto como 'data'. Essa é uma
        variável para armazenar os dados de saída padrão.

    8.  Adicione uma atividade WriteBuildMessage logo abaixo da seção de saída
        padrão. Defina a importância =
        'Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High'
        e a mensagem = 'dados'. Isso garante que a saída padrão do
        script será gravada na saída de compilação.

    9.  Na caixa de texto da seção Identificar Erro Padrão de
        InvokeProcess, defina o valor da caixa de texto como 'data'. Essa é uma
        variável para armazenar os dados de erro padrão.

    10. Adicione uma atividade WriteBuildError pouco abaixo da seção de saída
        padrão. Defina a mensagem = 'dados'. Isso garante que os erros
        padrões do script será gravada na saída do erro de compilação.

    O resultado final das atividades de fluxo de trabalho de publicação terá
    a seguinte aparência no designer:

    ![][4]

    O resultado final das atividades de fluxo de trabalho de publicação terá
    a seguinte aparência neste XAML:

            </TryCatch>
              <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap:VirtualizedContainerService.HintSize="1539,552">
                <If.Then>
                  <Sequence DisplayName="Start publish" sap:VirtualizedContainerService.HintSize="297,446">
                    <Sequence.Variables>
                      <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
                      <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                    </Sequence.Variables>
                    <sap:WorkflowViewStateService.ViewState>
                      <scg:Dictionary x:TypeArguments="x:String, x:Object">
                        <x:Boolean x:Key="IsExpanded">True</x:Boolean>
                      </scg:Dictionary>
                    </sap:WorkflowViewStateService.ViewState>
                    <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
                    <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription data file filename" sap:VirtualizedContainerService.HintSize="234,22" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
                    <mtbwa:InvokeProcess Arguments="[String.Format(" -File ""{0}"" -serviceName {1} -storageAccountName {2} -packageLocation ""{3}"" -cloudConfigLocation ""{4}"" -subscriptionDataFile ""{5}"" -selectedSubscription {6} -environment ""{7}""", PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="Execute publish script" FileName="PowerShell" sap:VirtualizedContainerService.HintSize="234,198">
                      <mtbwa:InvokeProcess.ErrorDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction.Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa:WriteBuildError sap:VirtualizedContainerService.HintSize="200,22" Message="[data]" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.ErrorDataReceived>
                      <mtbwa:InvokeProcess.OutputDataReceived>
                        <ActivityAction x:TypeArguments="x:String">
                          <ActivityAction.Argument>
                            <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                          </ActivityAction.Argument>
                          <mtbwa:WriteBuildMessage sap:VirtualizedContainerService.HintSize="200,22" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                        </ActivityAction>
                      </mtbwa:InvokeProcess.OutputDataReceived>
                    </mtbwa:InvokeProcess>
                  </Sequence>
                </If.Then>
              </If>
            </mtbwa:AgentScope>
            <mtbwa:InvokeForReason DisplayName="Check In Gated Changes for CheckInShelveset Builds" sap:VirtualizedContainerService.HintSize="1370,146" Reason="CheckInShelveset">
              <mtbwa:CheckInGatedChanges DisplayName="Check In Gated Changes" sap:VirtualizedContainerService.HintSize="200,22" />
            </mtbwa:InvokeForReason>
          </Sequence>
        </Activity>

7.  Salve o fluxo de trabalho DefaultTemplateAzure e o faça o check-in deste arquivo.

8.  Selecione o modelo de processo DefaultTemplateAzure na definição
    de compilação. Clique em Atualizar ou selecione o botão Novo se você ainda não vires
    se arquivo na lista de Modelos de Processo.

9.  Defina os valores da propriedade do parâmetro na seção Vários como segue:

    1.  CloudConfigLocation =
        'c:\\drops\\app.publish\\ServiceConfiguration.Cloud.cscfg'
        *Este valor é derivado de:
        ($PublishDir)ServiceConfiguration.Cloud.cscfg*

    2.  PackageLocation =
        'c:\\drops\\app.publish\\ContactManager.Azure.cspkg'
        *Este valor é derivado de: ($PublishDir)($ProjectName).cspkg*

    3.  PublishScriptLocation =
        'c:\\scripts\\WindowsAzure\\PublishCloudService.ps1'

    4.  ServiceName = 'mycloudservicename'
        *Use o nome de serviço de nuvem apropriado aqui*

    5.  Ambiente = 'Teste'

    6.  StorageAccountName = 'mystorageaccountname'
        *Use o nome da conta de armazenamento adequado aqui*

    7.  SubscriptionDataFileLocation =
        'c:\\scripts\\WindowsAzure\\Subscription.xml'

    8.  SubscriptionName = 'padrão'

    ![][5]

10. Salve as alterações para a Definição de Compilação.

11. Coloque uma compilação em fila para executar a compilação do pacote e publicar. Se você
    tiver um disparador definido para Implantação Contínua, você vai executar esse
    comportamento em cada check-in.

### <a name="script"> </a>Modelo de script PublishCloudService.ps1

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
                        1  #Atualização de implantação local (geralmente mais rápida, mais barata e não destroi o VIP)
                        {
                            Write-Output "$(Get-Date -f $timeStampFormat) - A implantação existe em $servicename.  Atualizando implantação.”
                            UpgradeDeployment
                        }
                        0  #Excluir e, em seguida, criar nova implantação
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
        Write-Output "$(Get-Date -f $timeStampFormat) - Criando nova Implantação: Em andamento”

        $opstat = New-AzureDeployment -Slot $slot -Pacote $packageLocation -Configuração $cloudConfigLocation -etiqueta $deploymentLabel -ServiceName $serviceName
            
        $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $completeDeploymentID = $completeDeployment.deploymentid

        write-progress -id 3 -activity “Criando nova Implantação" -concluído -Status “Concluído"
        Write-Output "$(Get-Date -f $timeStampFormat) - Criando nova Implantação: Concçuído, ID de Implantação: $completeDeploymentID"
        
        StartInstances
    }

    function UpgradeDeployment()
    {
        write-progress -id 3 -atividade “Atualizando Implantação" -Status “Em andamento"
        Write-Output "$(Get-Date -f $timeStampFormat) - Atualizando Implantação: Em andamento”

        # perform Update-Deployment
        $setdeployment = Set-AzureDeployment -Atualização-Slot $slot -Pacote $packageLocation -Configuração $cloudConfigLocation -etiqueta $deploymentLabel -ServiceName $serviceName -Force
        
        $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $completeDeploymentID = $completeDeployment.deploymentid
        
        write-progress -id 3 -activity “Atualizando Implantação" -completed -Status “Concluído"
        Write-Output "$(Get-Date -f $timeStampFormat) - Atualizando Implantação: Concçuído, ID de Implantação: $completeDeploymentID"
    }

    function DeleteDeployment()
    {

        write-progress -id 2 -activity “Excluindo Implantação" -Status “Em Andamento"
        Write-Output "$(Get-Date -f $timeStampFormat) - Excluindo Implantação: Em andamento”

        #WARNING - sempre exclua com force
        $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

        write-progress -id 2 -activity “Excluindo Implantação: Concluído" -completed -Status $removeDeployment
        Write-Output "$(Get-Date -f $timeStampFormat) - Excluindo Implantação: Conclua”
        
    }

    function StartInstances()
    {
        write-progress -id 4 -activity “Iniciando Instâncias" -status “Em Andamento"
        Write-Output "$(Get-Date -f $timeStampFormat) - Iniciando Instâncias: Em andamento”

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
                    Write-Output "$(Get-Date -f $timeStampFormat) - Iniciando Instância '$instanceName': $instanceStatus"
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
                Write-Output "$(Get-Date -f $timeStampFormat) - Iniciando Instância '$instanceName': $instanceStatus"
            }

            $i = $i + 1
        }
        
        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
        $opstat = $deployment.Status 
        
        write-progress -id 4 -activity “Iniciando instâncias" -completed -status $opstat
        Write-Output "$(Get-Date -f $timeStampFormat) - Iniciando Instâncias: $opstat"
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
    Set-AzureSubscription -CurrentStorageAccount $storageAccountName -SubscriptionName $selectedsubscription

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

## Próximas etapas

Para habilitar a depuração remota ao usar entrega contínua, consulte [estas instruções][estas instruções].

  [Fornecimento contínuo para o Azure usando o Visual Studio Online]: ../cloud-services-continuous-delivery-use-vso/
  [Etapa 1: Configurar o servidor de compilação]: #step1
  [Etapa 2: Criar um pacote usando comandos MSBuild]: #step2
  [Etapa 3: Criar um pacote usando o TFS Team Build (opcional)]: #step3
  [Etapa 4: Publicar um pacote usando um Script do PowerShell]: #step4
  [Etapa 5: Publicar um pacote usando o TFS Team Build (opcional)]: #step5
  [Team Foundation Build Service]: http://go.microsoft.com/fwlink/p/?LinkId=239963
  [.NET Framework 4]: http://go.microsoft.com/fwlink/?LinkId=239538
  [.NET Framework 4.5]: http://go.microsoft.com/fwlink/?LinkId=245484
  [Ferramentas de criação do Azure]: http://go.microsoft.com/fwlink/?LinkId=239600
  [Bibliotecas do Windows Azure]: http://go.microsoft.com/fwlink/?LinkId=257862
  [Referência de linha de comando do MSBuild]: http://msdn.microsoft.com/pt-br/library/ms164311(v=VS.90).aspx
  [Noções básicas sobre o Team Foundation Build System]: http://go.microsoft.com/fwlink/?LinkId=238798
  [Configurar uma máquina de compilação]: http://go.microsoft.com/fwlink/?LinkId=238799
  [0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01.png
  [1]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
  [cmdlets do PowerShell do Azure]: http://go.microsoft.com/fwlink/?LinkId=256262
  [final deste artigo]: #script
  [2]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
  [3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
  [4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
  [5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
  [estas instruções]: http://go.microsoft.com/fwlink/p/?LinkID=402354
