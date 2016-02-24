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
   ms.date="01/27/2015"
   ms.author="cawa" />

# Configurar a integração contínua para um aplicativo do Service Fabric usando o VSTS Visual Studio Team Services

Este artigo fornece uma orientação pela configuração da CI (integração contínua) para um aplicativo de Service Fabric usando o VSTS (Visual Studio Team Services), para que seu aplicativo possa ser desenvolvido, empacotado e implantado de um modo automatizado. Observe que este documento reflete a experiência atual e deve ser alterado à medida que o desenvolvimento progride. Além disso, estas instruções recriam o cluster do zero todas as vezes.

## Pré-requisitos

Para começar, configure seu projeto no Visual Studio Team Services.

1. Se você ainda não fez isso, crie uma conta do Team Services usando sua [conta da Microsoft](http://www.microsoft.com/account).

2. Crie um novo projeto no Team Services usando a conta da Microsoft.

3. Envie a fonte para o seu aplicativo do Service Fabric novo ou existente para este projeto.

Consulte [Conectar-se ao Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online) para saber mais sobre como trabalhar com projetos do Team Services.

## Configurar a entidade de serviço

### Configurar a autenticação para automação

Antes de configurar o computador de build, você precisa criar uma [Entidade de Serviço](../resource-group-create-service-principal-portal.md) que será usada pelo agente de build para autenticar no Azure. Você também precisa criar um certificado e carregá-lo em um Cofre da Chave, uma vez que o Cofre da Chave do Azure não oferece suporte à autenticação da Entidade de Serviço. Você pode executar essas etapas de qualquer computador. Seu computador de desenvolvimento é uma boa opção.

### Instale o Azure PowerShell e entre.

1.	Instale o PowerShell do Azure.
    - Instale o PowerShellGet. Para fazer isso, instale o [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729), que inclui o PowerShellGet.
    >[AZURE.NOTE] Você pode ignorar esta etapa se estiver executando o Windows 10 com as atualizações mais recentes.

2.	Instale e atualize o módulo AzureRM.

    1.  Se você tiver qualquer versão anterior do Azure PowerShell instalada, remova-a. Clique com o botão direito do mouse no botão Iniciar e selecione **Adicionar/Remover Programas**. Procurar por "Azure PowerShell" e desinstale-o.

    2.  Inicie um prompt de comando do PowerShell.

    3.	Instale o módulo "AzureRM" usando o comando `Install-Module AzureRM`.

    4.	Atualize o módulo "AzureRM" usando o comando `Update-AzureRM`.

3.	Desabilite ou habilite a coleta de dados do Azure.

    Os cmdlets do Azure solicitarão a ativação ou desativação da coleta de dados até que você faça uma escolha. Essas solicitações bloquearão a automação enquanto aguardam a entrada do usuário. Para suprimir essas solicitações fazendo uma escolha antecipadamente, execute um dos seguintes comandos:

    - Enable-AzureRmDataCollection

    - Disable-AzureRmDataCollection

4.	Entre no Azure PowerShell.

    1. Execute o comando `Login-AzureRmAccount`.

    2. Na caixa de diálogo que aparece, insira suas credenciais do Azure.

    3. Execute o comando `Get-AzureRmSubscription`.

    4. Localize a assinatura que você deseja usar e, em seguida, execute o comando `Select-AzureRmSubscription -SubscriptionId <id for your subscription>`. "

### Criar uma entidade de serviço

1.	Baixar e extrair [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) para uma pasta neste computador.

2.	Em um prompt de comando de administrador do PowerShell, altere para o diretório `Powershell\Manual` no arquivo extraído.

3.	Escolha uma senha para a Entidade de serviço usando o comando a seguir. Guarde essa senha, pois ela será usada como uma variável de build.

    ```
    $password = Read-Host -AsSecureString
    ```
4.	Execute o script do PowerShell Create-ServicePrincipal.ps1 com os seguintes parâmetros:

    |Parâmetro|Valor|
    |---|---|
    |DisplayName|Qualquer nome.|
    |HomePage|Qualquer URI. Não precisa realmente existir.|
    |IdentifierUri|Qualquer URI exclusivo. Não precisa realmente existir.|
    |SecurePassword|$password|

    Quando o script for concluído, ele produzirá os três valores a seguir. Anote os valores, pois eles serão usados como variáveis de build.

    - `ServicePrincipalId`

    - `ServicePrincipalTenantId`

    - `ServicePrincipalSubscriptionId`

### Crie um certificado e carregue-o em um novo Cofre da Chave do Azure

>[AZURE.NOTE] Esse script de exemplo gera um certificado autoassinado, o que não é uma prática segura e só é aceitável para experimentação. Como alternativa, siga as diretrizes da organização para obter um certificado legítimo.

1.	Em um prompt de administrador do PowerShell, altere para o diretório no qual você extraiu o `Manual.zip`.

2.	Execute o script `CreateAndUpload-Certificate.ps1` do PowerShell com os seguintes parâmetros.

    |Parâmetro|Valor|
    |---|---|
    |KeyVaultLocation|Qualquer valor Deve corresponder ao local no qual você pretende criar o cluster.|
    |CertificateSecretName|Qualquer valor|
    |SecureCertificatePassword|Qualquer valor. É usado quando você importa o certificado em sua máquina de build.|
    |KeyVaultResourceGroupName|Qualquer valor No entanto, não use o nome do grupo de recursos que você planeja usar para seu cluster.|
    |KeyVaultName|Qualquer valor|
    |PfxFileOutputPath|Qualquer valor Esse arquivo é usado para importar o certificado em sua máquina de build.|

    Quando o script for concluído, ele produzirá os três valores a seguir. Anote esses valores, pois eles serão usados como variáveis de build.

    - `ServiceFabricCertificateThumbprint`

    - `ServiceFabricKeyVaultId`

    - `ServiceFabricCertificateSecretId`

## Configurar o computador de compilação

### Instalar o Visual Studio 2015

1.	Se você já tiver provisionado uma máquina (ou planeja provisionar a sua própria), instale o [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) nessa máquina.

2.	Se você ainda não tiver uma máquina, provisione rapidamente uma máquina virtual do Azure (VM) com o Visual Studio 2015 pré-instalado. Para fazer isso:

    1.	Faça logon no [Portal de Gerenciamento do Azure](https://portal.azure.com).

    2.	Escolha o comando **Novo** no canto superior esquerdo da tela.

    3.	Escolha **Marketplace**.

    4.	Procure por **Visual Studio 2015**.

    5.	Escolha **Computação** > **Máquina Virtual** > **Da Galeria**.

    6.	Escolha a imagem **Atualização 1 do Visual Studio Enterprise 2015 com o Azure SDK 2.8 no Windows Server 2012 R2**.

        >[AZURE.NOTE] O SDK do Azure não é um componente obrigatório, mas não há imagens disponíveis no momento apenas com o Visual Studio 2015 instalado.

    7.	Siga as instruções na caixa de diálogo para criar sua VM.

### Instalar o SDK do Service Fabric

Instale o [SDK do Service Fabric](https://azure.microsoft.com/campaigns/service-fabric/).

### Instale o Azure PowerShell

Para instalar o Azure PowerShell, execute as etapas da seção anterior **Instalar o Azure PowerShell e entrar**. Ignore a subseção **Entrar no Azure PowerShell**.

### Registre os módulos do Azure PowerShell com a conta de Serviço Local

>[AZURE.NOTE] Faça isso *antes* de iniciar o agente de build, caso contrário, ele não utilizará a nova variável de ambiente.

1. Pressione Win + R e digite **regedit** e pressione enter.

2. Clique com o botão direito no nó `HKEY_Users\.Default\Environment` e selecione **Novo > Valor de Cadeia de Caracteres Expansível**.

3. Digite `PSModulePath` para o nome e `%PROGRAMFILES%\WindowsPowerShell\Modules` para o valor.

	1. Substitua `%PROGRAMFILES%` pelo valor da variável de ambiente `PROGRAMFILES`.

### Importar o certificado de automação

1.	Importe o certificado em sua máquina de build. Para fazer isso:

    1. Copie o arquivo PFX criado pelo script CreateAndUpload-Certificate.ps1 à sua máquina de build.

    2. Abra um prompt de administrador do PowerShell e execute os seguintes comandos, usando a senha passada para `CreateAndUpload-Certificate.ps1` anteriormente.

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```

2.	Execute o gerenciador de certificado.

    1. Abra o Painel de Controle do Windows. Clique com o botão direito no botão Iniciar e escolha **Painel de Controle**.

    2. Procure por **certificado**.

    3. Escolha **Ferramentas Administrativas** > **Gerenciar certificados de computador**.

3.	Conceda à conta de Serviço Local permissão para usar o certificado de automação.

    1.	Em **Certificados – Computador Local**, expanda **Pessoal**, escolha **Certificados**.

    2.	Selecione um certificado na lista.

    3.	Clique com o botão direito no certificado e escolha **Todas as Tarefas** > **Gerenciar Chaves Privadas**.

    4.	Escolha o botão **Adicionar** e, em seguida, digite **Serviço Local** e clique em **Verificar Nomes**.

    5.	Escolha o botão **OK** e feche o gerenciador de certificados.

![](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

### Registrar o agente de build

1.	Baixe o agent.zip. Para fazer isso:

    1.	Faça logon em seu projeto de equipe, como **https://[your-VSTS-account-name].visualstudio.com**.

    2.	Escolha o ícone de “engrenagem” no canto superior direito da tela.

    3.	No painel de controle, escolha a guia **Pools de agente**.

    4.	Escolha **Baixar agente** para baixar o arquivo agent.zip.

    5.	Copie agent.zip na máquina de build que você criou anteriormente.

    6.	Descompacte agent.zip em `C:\agent` (ou em qualquer local com um caminho curto) em sua máquina de build.

        >[AZURE.NOTE] Se você planeja compilar Serviços Web do ASP.NET 5, é recomendável que você escolha os nome mais curto possível para essa pasta, a fim de evitar erros de **PathTooLongExceptions** durante a implantação.

2.	Em um prompt de comando de administrador, execute `C:\agent\ConfigureAgent.cmd`. O script solicitará os seguintes parâmetros:

    |Parâmetro|Valor|
    |---|---|
    |Nome do Agente|Aceite o valor padrão, `Agent-[machine name]`.|
    |Url do TFS|Insira a URL de seu projeto de equipe, por exemplo, `https://[your-VSTS-account-name].visualstudio.com`.|
    |Pool de Agente|Insira o nome de seu pool de agente. (Se você ainda não criou um pool de agente, aceite o valor padrão).|
    |Pasta de trabalho|Aceite o valor padrão. Essa é a pasta na qual o agente de build realmente criará seu aplicativo. Observação: se você planeja compilar Serviços Web do ASP.NET 5, é recomendável que você escolha os nome mais curtos possíveis para essa pasta, a fim de evitar erros de PathTooLongExceptions durante a implantação.|
    |Instalar como Serviço do Windows?|O valor padrão é N. Alterar o valor para **Y**.|
    |Conta de usuário para executar o serviço|Aceite o valor padrão, `NT AUTHORITY\LocalService`.|
    |Desconfigurar um agente existente?|Aceite o valor padrão, **N**.|

3. Você receberá uma solicitação pelas credenciais. Insira as credenciais de sua conta da Microsoft que tenha direitos para seu projeto de equipe.

4. Verifique se o agente de build foi registrado. Para fazer isso:

    1. Volte para o navegador da Web (deve estar na página `https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`) e atualize a página.

    2. Escolha o pool de agente que você selecionou ao executar ConfigureAgent.ps1 anteriormente.

    3. Verifique se o agente de build aparece na lista e tem um status verde realçado. Se o realce for vermelho, o agente de build estará enfrentando problemas para se conectar ao Team Services.

![](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)


## Criar sua definição de build

>[AZURE.NOTE] A definição de build que você cria dessas instruções não dará suporte para vários builds simultâneos, mesmo em computadores separados. Isso ocorre porque cada build competiria pelo mesmo grupo de recursos/cluster. Se você quiser executar vários agentes de build, será necessário modificar os seguintes scripts/instruções para evitar essa interferência.

### Adicione os scripts de integração contínua ao controle de origem de seu aplicativo.

1.	Extrair [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) para qualquer pasta no computador. Copiar o conteúdo de `Powershell\Automation` para qualquer pasta no controle do código-fonte.

2.	Verifique os arquivos resultantes.

### Criar a definição de build

1.	Crie uma definição de build vazia. Para fazer isso:

    1.	Abra o projeto no Visual Studio Team Services.

    2.	Escolha a guia **Build**.

    3.	Escolha o sinal **+** verde para criar uma nova definição de build.

    4.	Escolha **Vazio** e o botão **Avançar**.

    5.  Verifique se o repositório e a ramificação certos estão selecionados.

    6.  Selecione a fila do agente na qual você registrou seu agente de build, e marque a caixa de seleção **Integração Contínua**.

2.	Na guia **Variáveis**, crie as seguintes variáveis com estes valores.

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
    |ServiceFabricClusterName|Qualquer nome desejado.|||
    |ServiceFabricClusterResourceGroupName|Qualquer nome desejado.|||
    |ServiceFabricClusterLocation|Qualquer nome que corresponda ao local de sua chave do cofre.|||
    |ServiceFabricClusterAdminPassword|Qualquer nome desejado.|X||
    |ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
    |ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>` Observação: o ponto de extremidade de conexão em seu perfil de publicação será ignorado. O ponto de extremidade de conexão para o cluster temporário será usado.|||
    |ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
    |ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` Deve ser a pasta que contém o arquivo .sfproj.||||

3.	Salve a definição de build e dê um nome a ela. (Você pode alterar esse nome mais tarde se quiser.)

### Adicionar uma etapa de "Compilação"

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

### Adicionar uma etapa “Remover grupo de recursos de cluster”

Se um build anterior não for excluído após sua utilização (por exemplo, se o build foi cancelada antes de ser excluído), talvez exista um grupo de recursos que entrará em conflito com o novo. Para evitar conflitos, limpe qualquer grupo de recursos restante (e seus recursos associados) antes de criar um novo.

1.	Na guia **Build**, escolha o comando **Adicionar etapa de build...**.

2.	Escolha **Utilitário** > **PowerShell**.

3.	Escolha o ícone de lápis ao lado do nome da etapa de build e renomeie-o como **Remover grupo de recursos do cluster**.

4.	Escolha o comando **...** ao lado de **Nome de arquivo do script**. Navegue até onde você extraiu os scripts de automação e, em seguida, escolha **Remove-ClusterResourceGroup.ps1**.

5.	Para **Argumentos**, digite `-ServicePrincipalPassword "$(ServicePrincipalPassword)"`.

6.	Salve definição de build.

### Adicionar uma etapa para “Provisionar e implantar em cluster seguro”

1.	Na guia **Build**, escolha o comando **Adicionar etapa de build...**.

2.	Escolha **Utilitário** > **PowerShell**.

3.	Escolha o ícone de lápis ao lado do nome da etapa de build e renomeie-o como **Provisionar e implantar em cluster seguro**.

4.	Escolha o botão **...** ao lado de **Nome de arquivo do script**. Navegue até onde você extraiu os scripts de automação e, em seguida, escolha **ProvisionAndDeploy-SecureCluster.ps1**.

5.	Para **Argumentos**, insira `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`

6.	Salve definição de build.

### Adicionar uma etapa “Remover grupo de recursos de cluster”

Agora que você terminou de usar o cluster temporário, você deve limpá-lo. Se não fizer isso, você continuará a ser cobrado pelo cluster temporário. Essa etapa remove o grupo de recursos, que remove o cluster e todos os outros recursos no grupo.

>[AZURE.NOTE] Há uma diferença entre essa etapa e a “Remover o grupo de recursos de Cluster” anterior: esta deve ter a opção “Sempre executar” marcada.

1.	Na guia **Build**, escolha o comando **Adicionar etapa de build...**.

2.	Escolha **Utilitário** > **PowerShell**.

3.	Escolha o ícone de lápis ao lado do nome da etapa de build e renomeie-o como **Remover grupo de recursos do cluster**.

4.	Escolha o botão **...** ao lado de **Nome de arquivo do script**. Navegue até onde você extraiu os scripts de automação e, em seguida, escolha **RemoveClusterResourceGroup.ps1**.

5.	Para **Argumentos**, insira `-ServicePrincipalPassword "$(ServicePrincipalPassword)`."

6.	Em **Opções de Controle**, marque a caixa de seleção **Sempre Executar**.

7.	Salve definição de build.

### Experimente!

Clique em **Build da Fila** para iniciar um build. Os builds também serão acionados ao enviar por push/fazer check-in.


## Soluções alternativas

As instruções anteriores criam um novo cluster para cada build e o remove ao final do build. Se você preferir que cada build execute uma atualização de aplicativo (para um cluster existente), execute estas etapas.

1.	Crie manualmente um cluster de teste por meio do Portal de Gerenciamento ou do Azure PowerShell. Você pode consultar o script `ProvisionAndDeploy-SecureCluster.ps1` como uma referência.

2.	Configure o perfil de publicação para dar suporte à atualização do aplicativo seguindo [estas instruções](service-fabric-visualstudio-configure-upgrade.md).

3.	Substitua a etapa **Provisionar e implantar em cluster seguro** com uma etapa que chama Deploy-FabricApplication.ps1 diretamente (e o transmite ao perfil de publicação).

4.	Remova as etapas de build de **Remover Grupo de Recursos de Cluster** de sua definição de build.

## Próximas etapas

Para saber mais sobre a integração contínua com aplicativos do Service Fabric, leia os artigos a seguir:

- [Página inicial da documentação do compilação](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
- [Implantar um agente de compilação](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
- [Criar e configurar uma definição de compilação](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_0128_2016-->