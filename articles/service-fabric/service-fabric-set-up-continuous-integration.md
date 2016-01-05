<properties
   pageTitle="Integração contínua para o Service Fabric | Microsoft Azure"
   description="Obtenha uma visão geral de como configurar a integração contínua para um aplicativo do Service Fabric usando o VSTS (Visual Studio Team Services)."
   services="service-fabric"
   documentationCenter="na"
   authors="cawams"
   manager="timlt"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="10/16/2015"
   ms.author="cawa" />

# Configurar a integração contínua para um aplicativo do Service Fabric usando o VSTS Visual Studio Team Services

Este artigo fornece uma orientação pela configuração da CI (integração contínua) para um aplicativo de Service Fabric usando o VSTS (Visual Studio Team Services), para que seu aplicativo possa ser desenvolvido, empacotado e implantado de um modo automatizado. Observe que este documento reflete a experiência atual e deve ser alterado à medida que o desenvolvimento progride. Além disso, estas instruções recriam o cluster do zero todas as vezes.

## Pré-requisitos

Para começar, configure seu projeto no Visual Studio Team Services.

1. Se você ainda não fez isso, crie uma conta do Team Services usando sua [conta da Microsoft](http://www.microsoft.com/account).
2. Crie um novo projeto no Team Services usando a conta da Microsoft.
3. Envie a fonte para o seu aplicativo do Service Fabric novo ou existente para este projeto.

Consulte [Conectar-se ao Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online) para saber mais sobre como trabalhar com projetos do Team Services.

## Etapas de configuração

### Configurar a autenticação para automação

Antes de configurar a máquina de build, será necessário criar uma "Entidade de serviço" que será usada pela automação para autenticar no Azure. Também será necessário criar um certificado e carregá-lo em um Cofre da Chave, pois o Cofre da Chave do Azure não oferece suporte à autenticação da Entidade de serviço. Você pode executar essas etapas de qualquer computador. Seu computador de desenvolvimento é uma boa opção.

### Instale o Azure PowerShell e entre.

1.	Instale o PowerShell do Azure.
    - Instale o PowerShellGet. Para fazer isso, instale o [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729), que inclui o PowerShellGet.
    >[AZURE.NOTE]Você pode ignorar esta etapa se estiver executando o Windows 10 com as atualizações mais recentes.

1.	Instale e atualize o módulo AzureRM.
    1.  Se você tiver qualquer versão anterior do Azure PowerShell instalada, remova-a. Clique com o botão direito no botão Iniciar e selecione "Adicionar/Remover Programas". Procurar por "Azure PowerShell" e desinstale-o.
    1.  Inicie um prompt de comando do PowerShell.
    1.	Instale o módulo "AzureRM" usando o comando `Install-Module AzureRM`.
    1.	Atualize o módulo "AzureRM" usando o comando `Update-AzureRM`.
1.	Desabilite ou habilite a coleta de dados do Azure.

    Os cmdlets do Azure solicitarão a ativação ou desativação da coleta de dados até que você faça uma escolha. Essas solicitações bloquearão a automação enquanto aguardam a entrada do usuário. Para suprimir essas solicitações fazendo uma escolha antecipadamente, execute um dos seguintes comandos:

    - Enable-AzureRmDataCollection
    - Disable-AzureRmDataCollection

1.	Entre no Azure PowerShell.
    1. Inicie um prompt de administrador do PowerShell e execute o comando `Login-AzureRMAccount`.
    1. Na caixa de diálogo que aparece, insira suas credenciais do Azure.
    1. Execute o comando `Get-AzureRmSubscription`.
    1. Localize a assinatura que você deseja usar e, em seguida, execute o comando `Select-AzureRmSubscription -SubscriptionId <id for your subscription>`. "

### Criar uma Entidade de serviço

1.	Extraia [ServiceFabricCIManualScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703773) para uma pasta neste computador.
1.	Em um prompt de comando de administração do PowerShell, altere para o diretório para o qual você extraiu ServiceFabricCIManualScripts.zip.
1.	Escolha uma senha para a Entidade de serviço usando o comando a seguir. Guarde essa senha, pois ela será usada como uma variável de build.

    ```
    $password = Read-Host -AsSecureString
    ```
1.	Execute o script do PowerShell Create-ServicePrincipal.ps1 com os seguintes parâmetros:

    |Parâmetro|Valor|
    |---|---|
    |ServicePrincipalDisplayName|Qualquer nome.|
    |ServicePrincipalHomePage|Qualquer URI. Não precisa realmente existir.|
    |ServicePrincipalIdentifierUri|Qualquer URI exclusivo. Não precisa realmente existir.|
    |ServicePrincipalSecurePassword|$password|

    Quando o script for concluído, ele produzirá os três valores a seguir. Anote os valores, pois eles serão usados como variáveis de build.

    - ServicePrincipalId
    - ServicePrincipalTenantId
    - ServicePrincipalSubscriptionId

### Crie um certificado e carregue-o em um novo Cofre da Chave do Azure

1.	Em um prompt de administração do PowerShell, altere para o diretório para o qual você extraiu ServiceFabricCIManualScripts.zip.
1.	Execute o script do PowerShell CreateAndUpload-Certificate.ps1 com os seguintes parâmetros:

    |Parâmetro|Valor|
    |---|---|
    |ServiceFabricKeyVaultLocation|Qualquer valor Deve corresponder ao local no qual você pretende criar o cluster.|
    |ServiceFabricCertificateSecretName|Qualquer valor|
    |ServiceFabricSecureCertificatePassword|Qualquer valor. É usado quando você importa o certificado em sua máquina de build.|
    |ServiceFabricKeyVaultResourceGroupName|Qualquer valor No entanto, não use o nome do grupo de recursos que você planeja usar para seu cluster.|
    |ServiceFabricKeyVaultName|Qualquer valor|
    |ServiceFabricPfxFileOutputPath|Qualquer valor Esse arquivo é usado para importar o certificado em sua máquina de build.|

    Quando o script for concluído, ele produzirá os três valores a seguir. Anote esses valores, pois eles serão usados como variáveis de build.

    - ServiceFabricCertificateThumbprint
    - ServiceFabricKeyVaultId
    - ServiceFabricCertificateSecretId


## Configurar seu computador de build

Observe que a definição de build que você cria a partir dessas instruções não suporta builds paralelos, mesmo em computadores separados. (Isso ocorre porque cada build competiria pelo mesmo grupo de recursos/cluster.) Se você quiser executar vários agentes de build, será necessário modificar os seguintes scripts/instruções para evitar essa interferência.

### Instalar o Visual Studio 2015

1.	Se você já tiver provisionado uma máquina (ou planeja provisionar a sua própria), instale o [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) nessa máquina.
2.	Se você ainda não tiver uma máquina, provisione rapidamente uma máquina virtual do Azure (VM) com o Visual Studio 2015 pré-instalado. Para fazer isso:
    1.	Faça logon no [Portal de Gerenciamento do Azure](http://portal.azure.com).
    1.	Escolha o comando **Novo** no canto superior esquerdo da tela.
    1.	Escolha **Marketplace**.
    1.	Procure por **Visual Studio 2015**.
    1.	Escolha **Computação** > **Máquina Virtual** > **Da Galeria**.
    1.	Escolha a imagem **Visual Studio Enterprise 2015 com Azure SDK 2.7 no Windows Server 2012 R2**.

        >[AZURE.NOTE]O SDK do Azure não é um componente obrigatório, mas não há imagens disponíveis apenas com o Visual Studio 2015 instalado.

    1.	Siga as instruções na caixa de diálogo para criar sua VM. (É recomendável que você escolha uma VM série D para obter o melhor desempenho de disco e de CPU.)

### Instalar o SDK do Service Fabric

Instale o [SDK do Service Fabric](https://azure.microsoft.com/campaigns/service-fabric/).

### Registrar o repositório NuGet do SDK do Service Fabric

1.	Crie o diretório `%SYSTEMDRIVE%\Windows\ServiceProfiles\LocalService\AppData\Roaming\NuGet` na máquina de build (se ainda não existir).
2.	Se um arquivo chamado NuGet.config já existir nesse diretório, abra-o e adicione o seguinte elemento como um filho de `<packageSources>`.

    ```
    <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />`
    ```

3.	Se NuGet.config ainda não existir, crie-o com o seguinte conteúdo. Substitua `<path to service fabric SDK>` pelo caminho para o SDK do Service Fabric na máquina de build.
>[AZURE.NOTE]Por padrão, `<path to service fabric SDK>` é `%ProgramFiles%\Microsoft SDKs\Service Fabric`.

    NuGet.config:

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <packageSources>
        <!-- Add this repository to the list of available repositories -->
        <add key="Service Fabric SDK" value="<path to service fabric SDK>\packages" />
      </packageSources>
    </configuration>
    ```

### Instale o Azure PowerShell

Para instalar o Azure PowerShell, execute as etapas da seção anterior **Instalar o Azure PowerShell e entrar**. Ignore a subseção **Logon no Azure PowerShell**.

### Registre os módulos do Azure PowerShell com a conta de Serviço Local

1. Pressione Win + R e digite **regedit** e pressione enter.
2. Clique com o botão direito no nó `HKEY_Users\.Default\Environment` e selecione **Novo > Valor de Cadeia de Caracteres Expansível**.
3. Digite `PSModulePath` para o nome e `%PROGRAMFILES%\WindowsPowerShell\Modules` para o valor.

>[AZURE.NOTE]Faça isso *antes* de iniciar o agente de build, caso contrário, ele não utilizará a nova variável de ambiente.

### Importar o certificado de automação

1.	Importe o certificado em sua máquina de build. Para fazer isso:
    1. Copie o arquivo PFX criado pelo script CreateAndUpload-Certificate.ps1 à sua máquina de build.
    1. Abra a conta de administração do PowerShell e execute os seguintes comandos, usando a senha que você passou para GenerateCertificate.ps1 anteriormente.

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```  

1.	Execute o gerenciador de certificado.
    1. Abra o Painel de Controle do Windows. Clique com o botão direito no botão Iniciar e escolha **Painel de Controle**.
    1. Procure por **certificado**.
    1. Escolha **Ferramentas Administrativas** > **Gerenciar certificados de computador**.


1.	Conceda à conta de Serviço Local permissão para usar o certificado de automação.
    1.	Em **Certificados – Computador Local**, expanda **Pessoal**, escolha **Certificados**.
    1.	Selecione um certificado na lista.
    1.	Clique com o botão direito no certificado e escolha **Todas as Tarefas** > **Gerenciar Chaves Privadas**.
    1.	Escolha o botão **Adicionar** e, em seguida, digite **Serviço Local** e escolha **Verificar Nomes**.
    1.	Escolha o botão **OK** e feche o gerenciador de certificados.

![](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### Registrar o agente de build

1.	Baixe o agent.zip. Para fazer isso:
    1.	Faça logon em seu projeto de equipe, como ****https://[your-VSTS-account-name].visualstudio.com**.
1.	Escolha o ícone de “engrenagem” no canto superior direito da tela.
    1.	No painel de controle, escolha a guia **Pools de agente**.
    1.	Escolha **Baixar agente** para baixar o arquivo agent.zip.
    1.	Copie agent.zip na máquina de build que você criou anteriormente.
    1.	Descompacte agent.zip em `C:\agent` (ou em qualquer local com um caminho curto) em sua máquina de build.

        >[AZURE.NOTE]Se você planeja compilar Serviços Web do ASP.NET 5, é recomendável que você escolha os nome mais curto possível para essa pasta, a fim de evitar erros de **PathTooLongExceptions** durante a implantação.

1.	Em um prompt de comando de administração do PowerShell, execute `C:\agent\ConfigureAgent.ps1`. O script solicitará os seguintes parâmetros:

    |Parâmetro|Valor|
    |---|---|
    |Nome do Agente|Aceite o valor padrão, `Agent-[machine name]`.)
    |Url do TFS|Insira a URL de seu projeto de equipe, por exemplo, `https://[your-VSTS-account-name].visualstudio.com`.
    |Pool de Agente|Insira o nome de seu pool de agente. (Se você ainda não criou um pool de agente, aceite o valor padrão).|
    |Pasta de trabalho|Aceite o valor padrão. Essa é a pasta na qual o agente de build realmente criará seu aplicativo. Observação: se você planeja compilar Serviços Web do ASP.NET 5, é recomendável que você escolha os nome mais curtos possíveis para essa pasta, a fim de evitar erros de PathTooLongExceptions durante a implantação.|
    |Instalar como Serviço do Windows?|O valor padrão é N. Alterar o valor para Y.|
    |Conta de usuário para executar o serviço|Aceite o valor padrão, `NT AUTHORITY\LocalService)`.|
    |Desconfigurar um agente existente?|Aceite o valor padrão, **N**.|

1. Você receberá uma solicitação pelas credenciais. Insira as credenciais de sua conta da Microsoft que tenha direitos para seu projeto de equipe.
1. Verifique se o agente de build foi registrado. Para fazer isso:

    1. Volte para o navegador da Web (deve estar na página `https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`) e atualize a página.
    1. Escolha o pool de agente que você selecionou ao executar ConfigureAgent.ps1 anteriormente.
    1. Verifique se o agente de build aparece na lista e tem um status verde realçado. Se o realce for vermelho, o agente de build estará enfrentando problemas para se conectar ao Team Services.

![](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## Configurar sua definição de build

### Adicione os scripts de integração contínua ao controle de origem de seu aplicativo.

1.	Extraia [ServiceFabricCIAutomationScripts.zip](http://go.microsoft.com/fwlink/?LinkId=703775) para qualquer pasta no controle de origem.
1.	Verifique os arquivos resultantes.

### Criar a definição de build

1.	Crie uma definição de build vazia. Para fazer isso:
    1.	Abra o projeto no Visual Studio Team Services.
    1.	Escolha a guia **Build**.
    1.	Escolha o sinal **+** verde para criar uma nova definição de build.
    1.	Escolha **Vazio** e o botão **Avançar**.
    1.  Verifique se o repositório e a ramificação certos estão selecionados.
    1.  Selecione a fila do agente na qual você registrou seu agente de build, e marque a caixa de seleção **Integração Contínua**.
1.	Na guia **Variáveis**, crie as seguintes variáveis com estes valores.

    |Variável|Valor|Segredo|Permitir no momento da fila|
    |---|---|---|---|
    |BuildConfiguration|Liberar||X|
    |BuildPlatform|x64|||
    |ServicePrincipalPassword|A senha que você passou para CreateServicePrincipal.ps1|X||
    |ServicePrincipalId|Da saída de CreateServicePrincipal.ps1|||
    |ServicePrincipalTenantId|Da saída de CreateServicePrincipal.ps1|||
    |ServicePrincipalSubscriptionId|Da saída de CreateServicePrincipal.ps1|||
    |ServiceFabricCertificateThumbprint|Da saída de GenerateCertificate.ps1|||
    |ServiceFabricKeyVaultId|Da saída de GenerateCertificate.ps1|||
    |ServiceFabricCertificateSecretId|Da saída de GenerateCertificate.ps1|||
    |ServiceFabricClusterResourceGroupName|Qualquer nome desejado.|||
    |ServiceFabricClusterName|Qualquer nome desejado.|||
    |ServiceFabricClusterLocation|Qualquer nome que corresponda ao local de sua chave do cofre.|||
    |ServiceFabricClusterAdminPassword|Qualquer nome desejado.|X||
    |ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
    |ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>` Observação: o ponto de extremidade de conexão em seu perfil de publicação será ignorado. O ponto de extremidade de conexão para o cluster temporário será usado.|||
    |ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
    |ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` Deve ser a pasta que contém o arquivo .sfproj.||||

1.	Na guia **Gatilhos**, selecione as opções **Integração Contínua** e **Processar alterações em lotes**.
1.	Na guia **Geral**, escolha a Fila na qual você registrou seus agentes de build.
1.	Salve a definição de build e dê um nome a ela. (Você pode alterar esse nome mais tarde se quiser.)

### Adicionar uma etapa de "Build"

1.	Na guia **Build**, escolha o comando **Adicionar etapa de build...**. "
2.	Escolha **Build** > **MSBuild**.
3.	Escolha o ícone de lápis ao lado do nome da etapa de build e renomeie-a como **Build**.
4.	Escolha o botão **...** ao lado do campo **Solução** e, em seguida, escolha o arquivo .sln.
5.	Digite `$(BuildPlatform)` para **Plataforma**.
6.	Digite `$(BuildConfiguration)` para **Configuração**.
7.	Marque a caixa de seleção **Restaurar Pacotes NuGet** (se ainda não estiver selecionada).
8.	Salve definição de build.

### Adicionar uma etapa de "Pacote"

1.	Na guia **Build**, escolha o comando **Adicionar etapa de build...**.
2.	Escolha **Build** > **MSBuild**.
3.	Escolha o ícone de lápis ao lado do nome da etapa de build e renomeie-a como **Pacote**.
4.	Escolha o botão **...** próximo ao campo **Solução** e selecione o arquivo .sfproj de seu projeto de aplicativo.
5.	Digite `$(BuildPlatform)` para **Plataforma**.
6.	Digite `$(BuildConfiguration)` para **Configuração**.
7.	Digite `/t:Package` para **Argumentos de MSBuild**.
8.	Desmarque a caixa de seleção **Restaurar Pacotes NuGet** (se ainda não estiver desmarcada).
9.	Salve definição de build.

### Adicionar uma etapa "Remover grupo de recursos de cluster"

Se um build anterior não for excluído após sua utilização (por exemplo, se o build foi cancelada antes de ser excluído), talvez exista um grupo de recursos que entrará em conflito com o novo. Para evitar conflitos, limpe qualquer grupo de recursos restante (e seus recursos associados) antes de criar um novo.

1.	Na guia **Build**, escolha o comando **Adicionar etapa de build...**.
2.	Escolha **Utilitário** > **PowerShell**.
3.	Escolha o ícone de lápis ao lado do nome da etapa de build e renomeie-a como **Remover Grupo de Recursos do Cluster**.
4.	Escolha o comando **...** ao lado de **Nome de arquivo do script**. Navegue até onde você extraiu os scripts de automação e, em seguida, escolha **Remove-ClusterResourceGroup.ps1**.
5.	Para **Argumentos**, digite `-ServicePrincipalPassword "$(ServicePrincipalPassword)"`.
6.	Salve definição de build.

### Adicionar uma etapa para "Provisionar e Implantar em Cluster Seguror"

1.	Na guia **Build**, escolha o comando **Adicionar etapa de build...**.
2.	Escolha **Utilitário** > **PowerShell**.
3.	Escolha o ícone de lápis ao lado do nome da etapa de build e renomeie-a como **Provisionar e Implantar em Cluster Seguro**.
4.	Escolha o botão **...** ao lado de **Nome de arquivo do script**. Navegue até onde você extraiu os scripts de automação e, em seguida, escolha **ProvisionAndDeploy-SecureCluster.ps1**.
5.	Para "Argumentos", insira `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`
6.	Salve definição de build.

### Adicionar uma etapa "Remover grupo de recursos de cluster"

Agora que você terminou de usar o cluster temporário, você deve limpá-lo. Se não fizer isso, você continuará a ser cobrado pelo cluster temporário. Essa etapa remove o grupo de recursos, que remove o cluster e todos os outros recursos no grupo.

1.	Na guia **Build**, escolha o comando **Adicionar etapa de build...**.
1.	Escolha **Utilitário** > **PowerShell**.
1.	Escolha o ícone de lápis ao lado do nome da etapa de build e renomeie-a como **Remover Grupo de Recursos do Cluster**.
1.	Escolha o botão **...** ao lado de **Nome de arquivo do script**. Navegue até onde você extraiu os scripts de automação e, em seguida, escolha **RemoveClusterResourceGroup.ps1**.
1.	Para "Argumentos", insira `-ServicePrincipalPassword "$(ServicePrincipalPassword)`.”
1.	Em **Opções de Controle**, marque a caixa de seleção **Sempre Executar**.
1.	Salve definição de build.

### Experimente!

Escolha o comando **Enfileirar Build** para iniciar um build. Builds também serão acionados no check-in.


## Soluções alternativas

As instruções anteriores criam um novo cluster para cada build e o remove ao final do build. Se você preferir que cada build execute uma atualização de aplicativo (para um cluster existente), execute estas etapas.

1.	Crie manualmente um cluster de teste por meio do Portal de Gerenciamento ou do Azure PowerShell. Você pode consultar o script "ProvisionAndDeploy-SecureCluster.ps1" como uma referência.
1.	Configure o perfil de publicação para dar suporte à atualização do aplicativo seguindo [estas instruções](service-fabric-visualstudio-configure-upgrade.md).

1.	Substitua a etapa **Provisionar e implantar em cluster seguro** com uma etapa que chama Deploy-FabricApplication.ps1 diretamente (e o transmite ao perfil de publicação).
1.	Remova as etapas de build de **Remover Grupo de Recursos de Cluster** de sua definição de build.

## Próximas etapas

Para saber mais sobre a integração contínua com aplicativos do Service Fabric, leia os seguintes artigos. [Documentação base sobre build](https://msdn.microsoft.com/Library/vs/alm/Build/overview) - [Implantar um agente de build](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows) - [Criar e configurar uma definição de build](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_1217_2015-->