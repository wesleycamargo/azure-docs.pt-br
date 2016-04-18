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
   ms.date="03/29/2016"
   ms.author="cawa" />

# Configurar a integração contínua para um aplicativo do Service Fabric usando o Visual Studio Team Services

Este artigo descreve as etapas para configurar a integração contínua para um aplicativo do Azure Service Fabric usando o Visual Studio Team Services (VSTS) e assim garantir que seu aplicativo seja criado, empacotado e implantado de forma automática. Observe que estas instruções sempre recriam o cluster do zero.

Este documento reflete o procedimento atual e deve ser alterado ao longo do tempo.

## Pré-requisitos

Para começar, configure seu projeto no Visual Studio Team Services:

1. Se você ainda não tiver criado uma conta de Serviços de equipe, configure-a usando a [conta da Microsoft](http://www.microsoft.com/account).

2. Crie um novo projeto no Team Services usando a conta da Microsoft.

3. Envie a fonte para o seu aplicativo do Service Fabric novo ou existente para este projeto.

Para saber mais sobre como trabalhar com projetos do Team Services, veja [Conectar-se ao Visual Studio](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

## Configurar a entidade de serviço

### Configurar a autenticação para automação

Antes de configurar o computador de compilação, você precisará criar uma [entidade de Serviço](../resource-group-create-service-principal-portal.md), que o agente de compilação usará para se autenticar no Azure. Você também precisa criar um certificado e carregá-lo em um Cofre da Chave do Azure, uma vez que o Cofre da Chave não oferece suporte à autenticação da entidade de serviço. Você pode executar essas etapas de qualquer computador. Seu computador de desenvolvimento é uma boa opção.

### Instale o Azure PowerShell e entre.

1.  Instale o PowerShellGet.

    a. Se estiver executando o Windows 10 com as atualizações mais recentes, você poderá pular esta etapa (o PowerShellGet já está instalado).

    b. Se não, instale o [Windows Management Framework 5.0](http://www.microsoft.com/download/details.aspx?id=48729), que inclui o PowerShellGet.

2.	Instale e atualize o módulo AzureRM. Se você tiver qualquer versão anterior do Azure PowerShell instalada, remova-a:

    a. Clique com o botão direito do mouse no botão Iniciar e selecione **Adicionar/Remover Programas**.

    b. Procurar por "Azure PowerShell" e desinstale-o.

    c. Abra o prompt de comando do PowerShell.

    d. Instale o módulo AzureRM usando o comando `Install-Module AzureRM`.

    e. Atualize o módulo AzureRM usando o comando `Update-AzureRM`.

3.	Desabilite ou habilite a coleta de dados do Azure.

    Os cmdlets do Azure solicitarão a ativação ou desativação da coleta de dados até que você faça uma escolha. Essas solicitações bloquearão a automação enquanto aguardam a entrada do usuário. Para suprimir esses avisos, faça uma escolha antecipadamente, executando um dos seguintes comandos:

    - Enable-AzureRmDataCollection

    - Disable-AzureRmDataCollection

4.	Entre no Azure PowerShell:

    a. Execute o comando `Login-AzureRmAccount`.

    b. Na caixa de diálogo exibida, insira suas credenciais do Azure.

    c. Execute o comando `Get-AzureRmSubscription`.

    d. Localize a assinatura que deseja usar.

    e. Execute o comando `Select-AzureRmSubscription -SubscriptionId <ID for your subscription>`.

### Criar uma entidade de serviço

1. Siga [estas instruções](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) para criar uma entidade de serviço e um ponto de extremidade de serviço para seu projeto.

2. Observe os valores que são impressos no fim da saída do script. Você precisará deles para configurar sua definição de compilação.

### Crie um certificado e carregue-o em um novo Cofre da Chave do Azure

>[AZURE.NOTE] Esse script de exemplo gera um certificado autoassinado, o que não é uma prática segura e só é aceitável para experimentação. Como alternativa, siga as diretrizes da organização para obter um certificado legítimo. Essas instruções também usam um único certificado para o servidor e o cliente. Em produção, você deve usar certificados de cliente e servidor separados.

1. Baixar e extrair [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) para uma pasta neste computador.

2. Em um prompt de administrador do PowerShell, altere para o diretório `<extracted zip>/Manual`.

3. Execute o script `CreateAndUpload-Certificate.ps1` do PowerShell com os seguintes parâmetros:

| Parâmetro | Valor |
| --- | --- |
| KeyVaultLocation | Qualquer valor Esse parâmetro deve corresponder ao local em que você planeja criar o cluster. |
| CertificateSecretName | Qualquer valor |
| CertificateDnsName | Deve corresponder ao nome DNS do cluster. Exemplo: `mycluster.westus.azure.cloudapp.net` |
| SecureCertificatePassword | Qualquer valor Esse parâmetro é usado quando você importa o certificado para o computador de compilação. |
| KeyVaultResourceGroupName | Qualquer valor No entanto, não use o nome do grupo de recursos que você planeja usar para seu cluster. |
| KeyVaultName | Qualquer valor |
| PfxFileOutputPath| Qualquer valor Este arquivo é usado para importar o certificado para o computador de compilação. |

Quando o script for concluído, ele produzirá os três valores a seguir. Anote esses valores, pois eles serão usados como variáveis de build.

 - `ServiceFabricCertificateThumbprint`
 - `ServiceFabricKeyVaultId`
 - `ServiceFabricCertificateSecretId`

## Configurar o computador de compilação

### Instalar o Visual Studio 2015

Se você já tiver provisionado um computador (ou planeja fornecer o seu próprio), instale o [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) no computador selecionado.

Se você ainda não tiver um computador, poderá provisionar rapidamente uma VM (máquina virtual do Azure) com o Visual Studio 2015 pré-instalado. Para fazer isso:

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Escolha o comando **Novo** no canto superior esquerdo da tela.

3. Selecione **Marketplace**.

4. Procure por **Visual Studio 2015**.

5. Selecione **Computação** > **Máquina Virtual** > **Da Galeria**.

6. Escolha a imagem **Visual Studio Enterprise 2015 Atualização 1 com SDK do Azure 2.8 no Windows Server 2012 R2**.

    >[AZURE.NOTE] O SDK do Azure não é um componente obrigatório, mas não há imagens disponíveis no momento apenas com o Visual Studio 2015 instalado.

7.	Siga as instruções na caixa de diálogo para criar sua VM.

### Instalar o SDK do Service Fabric

Instale o [SDK do Service Fabric](https://azure.microsoft.com/campaigns/service-fabric/) em seu computador.

### Instale o Azure PowerShell

Para instalar o Azure PowerShell, siga as etapas da seção anterior, “Instalar o Azure PowerShell e entrar”. Ignore a etapa “Entrar no Azure PowerShell”.

### Registrar os módulos do Azure PowerShell com a conta de Serviço de Rede

>[AZURE.NOTE] Faça isso *antes* de iniciar o agente de compilação. Caso contrário, ele não escolherá a nova variável de ambiente.

1. Pressione a tecla de logotipo do Windows+R, digite **regedit** e pressione Enter.

2. Clique com o botão direito do mouse no nó `HKEY_Users\.Default\Environment` e selecione **Novo** > **Valor de Cadeia de Caracteres Expansível**.

3. Insira `PSModulePath` como o nome e `%PROGRAMFILES%\WindowsPowerShell\Modules` como o valor. Substitua `%PROGRAMFILES%` pelo valor da variável de ambiente `PROGRAMFILES`.

### Importar o certificado de automação

1.	Importe o certificado em sua máquina de build. Para fazer isso:

    a. Copie o arquivo PFX criado pelo script CreateAndUpload-Certificate.ps1 à sua máquina de build.

    b. Abra um prompt de administrador do PowerShell e execute os comandos a seguir usando a senha passada para `CreateAndUpload-Certificate.ps1` anteriormente.

        ```
        $password = Read-Host -AsSecureString
        Import-PfxCertificate -FilePath <path/to/cert.pfx> -CertStoreLocation Cert:\LocalMachine\My -Password $password -Exportable
        ```

2.	Execute o gerenciador de certificados:

    a. Abra o Painel de Controle no Windows. Clique com o botão direito do mouse no botão Iniciar e escolha **Painel de Controle**.

    b. Procure por **certificado**.

    c. Escolha **Ferramentas Administrativas** > **Gerenciar certificados de computador**.

3.	Conceda à conta de Serviço de Rede permissão para usar o certificado de automação:

    a. Em **Certificados – Computador Local**, expanda **Pessoal** e escolha **Certificados**.

    b. Selecione um certificado na lista.

    c. Clique com o botão direito do mouse no certificado e escolha **Todas as Tarefas** > **Gerenciar Chaves Privadas**.

    d. Escolha o botão **Adicionar**, digite **Serviço de Rede** e escolha **Verificar Nomes**.

    e. Selecione **OK** e feche o gerenciador de certificados.

    ![Captura de tela de etapas para conceder permissão de conta de Serviço Local](media/service-fabric-set-up-continuous-integration/windows-certificate-manager.png)

4.  Copie o certificado para a pasta `Trusted People`.

    a. O certificado foi importado para **Pessoal/Certificados**, mas precisamos adicioná-lo a **Pessoas Confiáveis**. Clique com o botão direito do mouse no certificado e escolha **Copiar**. Em seguida, clique com o botão direito do mouse na pasta **Pessoas Confiáveis** e escolha **Colar**.

### Registrar o agente de build

1.	Baixe o agent.zip. Para fazer isso:

    a. Entre em seu projeto de equipe, como ****https://[your-VSTS-account-name].visualstudio.com**.

    b. Selecione o ícone de “engrenagem” no canto superior direito da tela.

    c. Selecione a guia **Pools de agentes**.

    d. Escolha **Baixar agente** para baixar o arquivo agent.zip.

    >[AZURE.NOTE] Se o download não for iniciado, verifique o bloqueador de pop-up.

    e. Copie agent.zip no computador de compilação que você criou anteriormente.

    f. Descompacte agent.zip em `C:\agent` (ou em qualquer local com um caminho curto) no computador de compilação.

    >[AZURE.NOTE] Se você planeja usar os Serviços Web do ASP.NET 5, é recomendável escolher o nome mais curto possível para essa pasta, a fim de evitar erros **PathTooLongExceptions** durante a implantação. Quando o ASP.NET Core for lançado, ele minimizará esse problema.

2.	Em um prompt de comando de administrador, execute `C:\agent\ConfigureAgent.cmd`. O script solicitará os seguintes parâmetros:

|Parâmetro|Valor|
|---|---|
|Nome do Agente|Aceite o valor padrão, `Agent-[machine name]`.|
|Url do TFS|Insira a URL de seu projeto de equipe, como `https://[your-VSTS-account-name].visualstudio.com`.|
|Pool de Agente|Insira o nome de seu pool de agente. (Se você ainda não criou um pool de agente, aceite o valor padrão).|
|Pasta de trabalho|Aceite o valor padrão. Essa é a pasta na qual o agente de build realmente criará seu aplicativo. Se você planeja usar os Serviços Web do ASP.NET 5, é recomendável que escolha o nome mais curto possível para essa pasta, a fim de evitar erros de PathTooLongExceptions durante a implantação.|
|Instalar como Serviço do Windows?|O valor padrão é N. Altere o valor para **Y**.|
|Conta de usuário para executar o serviço|Aceite o valor padrão, `NT AUTHORITY\NetworkService`.|
|Senha de `NT AUTHORITY\Network Service`|A conta de serviço da rede não tem uma senha, mas recusará senhas em branco. Digite uma cadeia de caracteres não vazia para a senha (o que você inserir será ignorado).|
|Desconfigurar um agente existente?|Aceite o valor padrão, **N**.|

3.  Quando as credenciais forem solicitadas, insira as credenciais de sua conta da Microsoft que tenha direitos para seu projeto de equipe.

4.  Verifique se o agente de compilação foi registrado e configure seus recursos. Para fazer isso:

    a. Volte para o navegador da Web (`https://[your-VSTS-account-name].visualstudio.com/_admin/_AgentPool`) e atualize a página.

    b. Selecione o pool de agente que você selecionou ao executar ConfigureAgent.ps1 anteriormente.

    c. Verifique se o agente de build aparece na lista e tem um status verde realçado. Se o realce for vermelho, o agente de build estará enfrentando problemas para se conectar ao Team Services.

    ![Captura de tela que mostra o status do agente de compilação](media/service-fabric-set-up-continuous-integration/vso-configured-agent.png)

    d. Escolha o agente de compilação e a guia **Recursos**.

    e. Adicione um recurso chamado **azureps** com qualquer valor. Isso indica para o VSTS que esse computador tem o Azure PowerShell instalado nele, que é obrigatório para usar algumas das tarefas de compilação fornecidas pelo VSTS.


## Criar sua definição de build

>[AZURE.NOTE] A definição de build que você cria dessas instruções não dará suporte para vários builds simultâneos, mesmo em computadores separados. Isso ocorre porque cada build competiria pelo mesmo grupo de recursos/cluster. Se você quiser executar vários agentes de build, será necessário modificar os seguintes scripts/instruções para evitar essa interferência.

### Adicione os scripts de integração contínua ao controle do código-fonte de seu aplicativo

1.	Extraia [ServiceFabricContinuousIntegrationScripts.zip](https://gallery.technet.microsoft.com/Set-up-continuous-f8b251f6) para qualquer pasta no computador. Copie o conteúdo de `Powershell\Automation` para qualquer pasta no controle do código-fonte.

2.	Verifique os arquivos resultantes.

### Criar a definição de build

1.	Crie uma definição de build vazia. Para fazer isso:

    a. Abra o projeto no Visual Studio Team Services.

    b. Escolha a guia **Compilar**.

    c. Escolha o sinal **+** verde para criar uma nova definição de compilação.

    d. Escolha **Vazio** e **Avançar**.

    e. Verifique se o repositório e a ramificação certos estão selecionados.

    f. Escolha a fila do agente na qual você registrou o agente de compilação e marque a caixa de seleção **Integração Contínua**.

2.	Na guia **Variáveis**, crie as seguintes variáveis com estes valores.

    |Variável|Valor|Segredo|Permitir no tempo da fila|
    |---|---|---|---|
    |BuildConfiguration|Liberar||X|
    |BuildPlatform|x64|||
    |ServicePrincipalPassword|A senha que você usou quando criou a entidade de serviço.|X||
    |ServicePrincipalId|Na saída do script que você usou para criar sua entidade de serviço.|||
    |ServicePrincipalTenantId|Na saída do script que você usou para criar sua entidade de serviço.|||
    |ServicePrincipalSubscriptionId|Na saída do script que você usou para criar sua entidade de serviço.|||
    |ServiceFabricCertificateThumbprint|Na saída de CreateAndUpload-Certificate.ps1|||
    |ServiceFabricKeyVaultId|Na saída de CreateAndUpload-Certificate.ps1|||
    |ServiceFabricCertificateSecretId|Na saída de CreateAndUpload-Certificate.ps1|||
    |ServiceFabricClusterName|Deve corresponder ao Nome DNS do seu certificado.|||
    |ServiceFabricClusterResourceGroupName|Qualquer nome desejado.|||
    |ServiceFabricClusterLocation|Deve corresponder ao local do seu cofre de chaves.|||
    |ServiceFabricClusterAdminPassword|8 a 123 caracteres, com pelo menos 3 dos seguintes tipos de caractere: letra maiúscula, letra minúscula, número, caracteres especiais.|X||
    |ServiceFabricClusterResourceGroupTemplateFilePath|`<path/to/extracted/automation/scripts/ArmTemplate-Full-3xVM-Secure.json>`|||
    |ServiceFabricPublishProfilePath|`<path/to/your/publish/profiles/MyPublishProfile.xml>` O ponto de extremidade de conexão em seu perfil de publicação será ignorado. O ponto de extremidade de conexão para o cluster temporário será usado.|||
    |ServiceFabricDeploymentScriptPath|`<path/to/Deploy-FabricApplication.ps1>`|||
    |ServiceFabricApplicationProjectPath|`<path/to/your/fabric/application/project/folder>` Deve ser a pasta que contém o arquivo .sfproj.||||

3.  Salve a definição de build e dê um nome a ela. Você pode alterar esse nome mais tarde se quiser.

### Adicionar uma etapa "Restaurar pacotes NuGet"

1. Na guia **Build**, escolha o comando **Adicionar etapa de build...**.

2. Escolha **Pacote** > **Instalador do NuGet**

3. Escolha o ícone de lápis ao lado do nome da etapa de compilação e renomeie-o para **Restaurar pacotes NuGet**.

4. Escolha o botão **...** ao lado do campo **Solução** e, em seguida, escolha o arquivo .sln.

5. Salve definição de build.

### Adicionar uma etapa de "Compilação"

1.	Na guia **Compilar**, escolha o comando **Adicionar etapa de compilação…**.

2.	Escolha **Compilar** > **MSBuild**.

3.	Escolha o ícone de lápis ao lado do nome da etapa de compilação e renomeie-o para **Compilar**.

4.	Escolha o botão **...** ao lado do campo **Solução** e escolha o arquivo .sln.

5.	Digite `$(BuildPlatform)` para **Plataforma**.

6.	Digite `$(BuildConfiguration)` para **Configuração**.

7.	Desmarque a caixa de seleção **Restaurar Pacotes NuGet**.

8.	Salve definição de build.

### Adicionar uma etapa de "Pacote"

1.	Na guia **Compilar**, escolha o comando **Adicionar etapa de compilação…**.

2.	Escolha **Compilar** > **MSBuild**.

3.	Escolha o ícone de lápis ao lado do nome da etapa de compilação e renomeie-o para **Pacote**.

4.	Escolha o botão **...** ao lado do campo **Solução** e escolha o arquivo .sfproj do projeto do aplicativo.

5.	Digite `$(BuildPlatform)` para **Plataforma**.

6.	Digite `$(BuildConfiguration)` para **Configuração**.

7.	Digite `/t:Package` para **Argumentos de MSBuild**.

8.	Desmarque a caixa de seleção **Restaurar Pacotes NuGet** (se ainda não estiver desmarcada).

9.	Salve definição de build.

### Adicionar uma etapa “Remover grupo de recursos de cluster”

Se uma compilação anterior não tiver sido limpa após sua execução (por exemplo, se a compilação tiver sido cancelada antes de poder ser limpa), poderá haver um grupo de recursos existente em conflito com o novo. Para evitar conflitos, limpe qualquer grupo de recursos restante (e seus recursos associados) antes de criar um novo.

1.	Na guia **Compilar**, escolha o comando **Adicionar etapa de compilação…**.

2.	Escolha **Utilitário** > **PowerShell**.

3.	Escolha o ícone de lápis ao lado do nome da etapa de compilação e renomeie-o para **Remover grupo de recursos do cluster**.

4.	Escolha o comando **...** ao lado de **Nome de arquivo do script**. Navegue até o local para onde você extraiu os scripts de automação e escolha **Remove-ClusterResourceGroup.ps1**.

5.	Para **Argumentos**, digite `-ServicePrincipalPassword "$(ServicePrincipalPassword)"`.

6.	Salve definição de build.

### Adicionar uma etapa para “Provisionar e implantar em cluster seguro”

1.	Na guia **Compilar**, escolha o comando **Adicionar etapa de compilação…**.

2.	Escolha **Utilitário** > **PowerShell**.

3.	Escolha o ícone de lápis ao lado do nome da etapa de compilação e renomeie-o para **Provisionar e implantar em cluster seguro**.

4.	Escolha o botão **...** ao lado de **Nome de arquivo do script**. Navegue até onde você extraiu os scripts de automação e escolha **ProvisionAndDeploy-SecureCluster.ps1**.

5.	Para **Argumentos**, digite `-ServicePrincipalPassword "$(ServicePrincipalPassword)" -ServiceFabricClusterAdminPassword "$(ServiceFabricClusterAdminPassword)"`

6.	Salve definição de build.

### Adicionar uma etapa “Remover grupo de recursos de cluster”

Agora que você terminou de usar o cluster temporário, você deve limpá-lo. Se não fizer isso, você continuará a ser cobrado pelo cluster temporário. Essa etapa remove o grupo de recursos, que remove o cluster e todos os outros recursos no grupo.

>[AZURE.NOTE] Há uma diferença entre essa etapa e a anterior, "Remover o grupo de recursos do cluster": esta deve ter a opção **Sempre Executar** selecionada.

1.	Na guia **Compilar**, escolha o comando **Adicionar etapa de compilação…**.

2.	Escolha **Utilitário** > **PowerShell**.

3.	Escolha o ícone de lápis ao lado do nome da etapa de compilação e renomeie-o para **Remover grupo de recursos do cluster**.

4.	Escolha o botão **...** ao lado de **Nome de arquivo do script**. Navegue até o local para onde você extraiu os scripts de automação e escolha **RemoveClusterResourceGroup.ps1**.

5.	Para **Argumentos**, digite `-ServicePrincipalPassword "$(ServicePrincipalPassword)`.

6.	Em **Opções de Controle**, marque a caixa de seleção **Sempre Executar**.

7.	Salve definição de build.

### Experimente

Escolha **Enfileirar Compilação** para iniciar uma compilação. As compilações também serão disparadas no envio por push ou no check-in.


## Soluções alternativas

As instruções anteriores criam um novo cluster para cada build e o remove ao final do build. Se você preferir que cada build execute uma atualização de aplicativo (para um cluster existente), use as etapas a seguir:

1.	Crie manualmente um cluster de teste por meio do Portal do Azure ou do Azure PowerShell. Você pode consultar o script `ProvisionAndDeploy-SecureCluster.ps1` como uma referência.

2.	Configure o perfil de publicação para dar suporte à atualização do aplicativo seguindo [estas instruções](service-fabric-visualstudio-configure-upgrade.md).

3.	Substitua a etapa **Provisionar e implantar em cluster seguro** por uma etapa que chame Deploy-FabricApplication.ps1 diretamente (e o transmita ao perfil de publicação).

4.	Remova as etapas de build de **Remover Grupo de Recursos de Cluster** de sua definição de build.

## Próximas etapas

Para saber mais sobre a integração contínua com aplicativos do Service Fabric, leia os artigos a seguir:

 - [Página inicial da documentação do compilação](https://msdn.microsoft.com/Library/vs/alm/Build/overview)
 - [Implantar um agente de compilação](https://msdn.microsoft.com/Library/vs/alm/Build/agents/windows)
 - [Criar e configurar uma definição de compilação](https://msdn.microsoft.com/Library/vs/alm/Build/vs/define-build)

<!---HONumber=AcomDC_0406_2016-->