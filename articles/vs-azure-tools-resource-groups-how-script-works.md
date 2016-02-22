<properties
	pageTitle="Visão geral do script de implantação do projeto do Grupo de Recursos do Azure | Microsoft Azure"
	description="Descreve como funciona o script do PowerShell no projeto de implantação do Grupo de Recursos do Azure."
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="02/04/2016"
	ms.author="tarcher" />

# Visão geral do script de implantação de projeto do Grupo de Recursos do Azure

Os projetos de implantação do Grupo de Recursos do Azure ajudam você a testar e a implantar arquivos e outros artefatos no Azure. Quando você cria um projeto de implantação do Gerenciador de Recursos do Azure no Visual Studio, um script do PowerShell chamado **Deploy-AzureResourceGroup.ps1** é adicionado ao projeto. Este tópico fornece detalhes sobre as ações desse script e sobre como executá-lo, dentro e fora do Visual Studio.

## O que o script faz?

O script Deploy-AzureResourceGroup.ps1 executa duas coisas importantes para o fluxo de trabalho de implantação.

- Carregar arquivos e artefatos necessários para a implantação do modelo
- Implantar o modelo

A primeira parte do script carrega os arquivos e os artefatos para a implantação e o último cmdlet no script realmente implanta o modelo. Por exemplo, se uma máquina virtual precisar ser configurada com um script, primeiro o script de implantação carregará o script de configuração com segurança em uma conta de armazenamento do Azure. Assim, o script ficará disponível para o Gerenciador de Recursos do Azure configurar a máquina virtual durante o provisionamento.

Como nem todas as implantações de modelo precisam ter artefatos adicionais que exijam carregamento, um parâmetro de opção chamado *uploadArtifacts* é avaliado. Se algum artefato precisar ser carregado, defina a opção *uploadArtifacts* ao chamar o script. Observe que o arquivo de modelo principal e o arquivo de parâmetros não precisam ser carregados. Somente outros arquivos, como os scripts de configuração, os modelos de implantação aninhados e os arquivos de aplicativo precisam ser carregados.

## Descrição detalhada do script

Veja a seguir uma descrição das ações executadas por seções específicas do script Deploy-AzureResourceGroup.ps1 do Azure PowerShell.

>[AZURE.NOTE] A versão descrita será a 1.0 do script Deploy-AzureResourceGroup.ps1.

1.	Declare os parâmetros necessários ao projeto de implantação do Gerenciador de Recursos do Azure. Alguns parâmetros têm valores padrão definidos na criação do projeto. Você pode alterar esses valores padrão no script ou adicionar valores de parâmetros diferentes antes de executar o script.

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

    |Parâmetro|Descrição|
    |---|---|
    |$ResourceGroupLocation|A região ou o local do data center do grupo de recursos, como **Oeste dos EUA** ou **Ásia Oriental**.|
    |$ResourceGroupName|O nome do grupo de recursos do Azure.|
    |$UploadArtifacts|Um valor binário que indica se os artefatos precisam ser carregados no Azure do seu sistema.|
    |$StorageAccountName|O nome de sua conta de armazenamento do Azure onde os artefatos são carregados.|
    |$StorageAccountResourceGroupName|O nome do grupo de recursos do Azure que contém a conta de armazenamento.|
    |$StorageContainerName|O nome do contêiner de armazenamento usado para carregar artefatos.|
    |$TemplateFile|O caminho até o arquivo de implantação (`<app name>.json`) em seu projeto do Grupo de Recursos do Azure.|
    |$TemplateParametersFile|O caminho até o arquivo de parâmetros (`<app name>.parameters.json`) em seu projeto do Grupo de Recursos do Azure.|
    |$ArtifactStagingDirectory|O caminho em seu sistema onde os artefatos são carregados localmente, incluindo a pasta raiz do script do PowerShell. Esse caminho pode ser absoluto ou relativo ao local de script.|
    |$AzCopyPath|O caminho onde a ferramenta AzCopy.exe copia seus arquivos .zip, incluindo a pasta raiz do script do PowerShell. Esse caminho pode ser absoluto ou relativo ao local de script.|
    |$DSCSourceFolder|O caminho até a pasta de origem do DSC (Configuração do Estado Desejado), incluindo a pasta raiz do script do PowerShell. Esse caminho pode ser absoluto ou relativo ao local de script. Confira [Introducing the Azure PowerShell DSC (Desired State Configuration) extension](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx), se for aplicável, para saber mais.|

1.	Verifique se os artefatos precisam ser carregados no Azure. Se não for necessário, pule para a etapa 11. Caso contrário, execute as seguintes etapas.

1.	Converta todas as variáveis com caminhos relativos para caminhos absolutos. Por exemplo, altere um caminho como `..\Tools\AzCopy.exe` para `C:\YourFolder\Tools\AzCopy.exe`. Além disso, inicialize as variáveis *ArtifactsLocationName* e *ArtifactsLocationSasTokenName* como null. *ArtifactsLocation* e *SaSToken* podem ser parâmetros para o modelo. Se os valores forem nulos após a leitura no arquivo de parâmetros, o script gerará valores para eles.

    As Ferramentas do Azure usam os valores de parâmetro *\_artifactsLocation* e *\_artifactsLocationSasToken* no modelo para gerenciar os artefatos. Se o script do PowerShell encontrar parâmetros com esses nomes, mas os valores de parâmetro não forem fornecidos, o script carregará os artefatos e retornará os valores apropriados para esses parâmetros. Em seguida, os passa para o cmdlet via `@OptionsParameters`.

	|Variável|Descrição|
    |---|---|
    |ArtifactsLocationName|O caminho no qual estão localizados os artefatos do Azure.|
    |ArtifactsLocationSasTokenName|O nome de token SAS (Assinatura de Acesso Compartilhado) usado pelo script para autenticar no Barramento de Serviço. Confira [Autenticação de Assinatura de Acesso Compartilhado com o Barramento de Serviço](service-bus-shared-access-signature-authentication.md) para saber mais.|

	```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.	Esta seção verifica se o arquivo <app name>.parameters.json (conhecido como o "Arquivo de parâmetros") tem um nó pai chamado **parameters** (no bloco `else`). Caso contrário, ela não terá um nó pai. Qualquer um dos formatos é aceitável.
    
	```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.	Itere na coleção de parâmetros JSON. Se um valor de parâmetro tiver sido atribuído para *\_artifactsLocation* ou *\_artifactsLocationSasToken*, defina a variável *$OptionalParameters* com esses valores. Isso impede que o script substitua inadvertidamente os valores de parâmetro fornecidos por você.

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.	Obtenha a chave da conta de Armazenamento e o contexto do recurso da conta de Armazenamento usados para armazenar os artefatos para implantação.

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.	Se você estiver usando o DSC do PowerShell para configurar uma máquina virtual, a extensão de DSC exigirá que os artefatos estejam em um único arquivo zip. Portanto, crie um arquivo .zip para a configuração do DSC. Para fazer isso, verifique se $DSCSourceFolder existe. Se houver uma configuração de DSC, remova-o e crie um novo arquivo compactado chamado dsc.zip.

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.	Se nenhum caminho para os artefatos do Azure for fornecido no arquivo de parâmetros, defina um caminho para o script do PowerShell a ser usado ao carregar os artefatos. Para fazer isso, crie um caminho usando uma combinação do caminho do ponto de extremidade da conta de Armazenamento e do nome do contêiner de Armazenamento. Em seguida, atualize o arquivo de parâmetros com esse novo caminho.

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.	Use o utilitário **AzCopy** (incluído na pasta **Ferramentas** do seu projeto de implantação do grupo de recursos do Azure) para copiar os arquivos de seu caminho de destino de armazenamento local para sua conta do Armazenamento do Azure online. Se esta etapa falhar, saia do script, pois provavelmente a implantação não será bem-sucedida sem os artefatos necessários.

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.	Se nenhum token SAS para o local dos artefatos for fornecido no arquivo de parâmetros, crie um para fornecer acesso somente leitura temporário para o contêiner do Armazenamento online. Em seguida, passe esse token SAS para o cmdline como "optionalParameter". Observe que quaisquer parâmetros passados no cmdline têm precedência sobre os valores fornecidos no arquivo de parâmetros.

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  Crie o grupo de recursos, caso ainda não exista e verifique os arquivos de modelo e de parâmetros em busca de quaisquer erros de validação que impedirão a implantação bem-sucedida.

    ```
	# Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

	Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. Por fim, implante o modelo. Esse código cria um nome exclusivo para a implantação usando um carimbo de data e hora.

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## Implantar o grupo de recursos

### Para implantar o grupo de recursos no Visual Studio

1. No menu de atalho do projeto do Grupo de Recursos do Azure, escolha **Implantar** > **Nova Implantação**.

    ![][0]

1. Na caixa de diálogo **Implantar no Grupo de Recursos**, escolha um grupo de recursos existente para implantação na caixa de listagem suspensa ou escolha **&lt;Criar Novo...&gt;** para criar um novo grupo de recursos.

    ![][1]

1. Se for solicitado, insira um nome de grupo de recursos e um local na caixa de diálogo **Criar Grupo de Recursos** e escolha o botão **Criar**.

    ![][2]

1. Escolha o botão **Editar Parâmetros** para exibir a caixa de diálogo **Editar Parâmetros** e insira quaisquer valores de parâmetro que faltem.

    ![][3]

	>[AZURE.NOTE] Se os parâmetros obrigatórios precisarem de valores, essa caixa de diálogo será exibida automaticamente na implantação.

    ![][4]

1. Quando você terminar de inserir valores de parâmetro, escolha o botão **Salvar** e escolha o botão **Implantar**.

    O script de implantação (Deploy-AzureResourceGroup.ps1) é executado e seu modelo, juntamente com os artefatos, é implantado no Azure.

### Para implantar o grupo de recursos usando o PowerShell

Se você quiser executar o script sem usar o comando Implantar e a interface de usuário do Visual Studio, no menu de atalho do script, escolha **Abrir com o ISE do PowerShell**.

![][5]


## Exemplos de implantação de comando

### Implantar usando valores padrão

Este exemplo mostra como executar o script usando os valores de parâmetro padrão. (Uma vez que o parâmetro de local não tem um valor padrão, você precisa fornecer um).

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### Implantar substituindo os valores padrão

Este exemplo mostra como executar o script para implantar arquivos de modelo e de parâmetros diferentes dos valores padrão.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### Implantar usando UploadArtifacts para preparo

Este exemplo mostra como executar o script para carregar artefatos da pasta da versão e implantar modelos não padrão.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

Este exemplo mostra como executar o script em uma tarefa do Azure PowerShell no Visual Studio Online.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## Próximas etapas
Saiba mais sobre o Gerenciador de Recursos do Azure lendo [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md).

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png

<!---HONumber=AcomDC_0211_2016-->