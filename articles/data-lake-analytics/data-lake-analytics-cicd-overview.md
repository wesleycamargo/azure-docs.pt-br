---
title: Como configurar o pipeline de CI/CD para o Azure Data Lake Analytics | Microsoft Docs
description: Saiba como configurar a integração contínua e a implantação contínua para Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: c069bc2a6147a021ea9bdf37e2926d5c8f33281c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145002"
---
# <a name="how-to-set-up-cicd-pipeline-for-azure-data-lake-analytics"></a>Como configurar o pipeline de CI/CD para o Azure Data Lake Analytics

Neste documento, você aprenderá como configurar o pipeline de CI/CD para trabalhos do U-SQL e para bancos de dados U-SQL.

## <a name="cicd-for-u-sql-job"></a>CI/CD para trabalho do U-SQL

As Ferramentas do Azure Data Lake para Visual Studio fornecem o tipo de projeto U-SQL que ajuda a organizar scripts U-SQL. O uso do projeto U-SQL para gerenciar seu código U-SQL simplifica os cenários de CI/CD.

## <a name="build-u-sql-project"></a>Compilar um projeto U-SQL

O projeto U-SQL pode ser compilado com o MSBuild passando os parâmetros correspondentes. Siga as etapas abaixo para configurar o processo de compilação para projetos U-SQL.

### <a name="project-migration"></a>Migração do projeto

Antes de configurar a tarefa de build do projeto U-SQL, certifique-se de usar a versão mais recente do projeto U-SQL. Abra o arquivo do projeto U-SQL no editor e verifique se você tem os seguintes itens de importação:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Caso contrário, há duas opções para migrar o projeto:

- Opção 1: altere a antiga opção de importação de itens pela opção descrita anteriormente.
- Opção 2: abra o projeto antigo nas Ferramentas do Azure Data Lake para Visual Studio após a versão 2.3.3000.0. O antigo modelo de projeto será atualizado automaticamente para a versão mais recente. O novo projeto criado após a versão 2.3.3000.0 usa o novo modelo diretamente.

### <a name="get-nuget-package"></a>Obter o pacote do NuGet

O MSBuild não oferece suporte interno ao tipo de projeto U-SQL. Para adicionar essa capacidade, será preciso adicionar uma referência para sua solução ao [pacote do Nuget Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) que adiciona o serviço de linguagem necessário.

Para adicionar a referência do pacote do NuGet, clique com botão direito do mouse na solução no Gerenciador de Soluções e escolha **Gerenciar Pacotes do NuGet**. Ou você pode adicionar um arquivo chamado `packages.config` na pasta da solução e adicionar o seguinte conteúdo a ele.

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Gerenciar as referências do banco de dados U-SQL

Se os scripts U-SQL no projeto U-SQL possuírem instruções de consulta para objetos de banco de dados U-SQL, por exemplo, consultar uma tabela U-SQL ou fazer referência a um assembly, será preciso referenciar o projeto de banco de dados U-SQL correspondente que inclui a definição desses objetos antes de compilar este projeto U-SQL.

[Saiba mais sobre o projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

>[!NOTE]
>O projeto de banco de dados U-SQL atualmente está em visualização pública. Se houver instrução DROP no projeto, a compilação falhará. A instrução DROP será permitida em breve.
>

### <a name="build-u-sql-project-with-msbuild-command-line"></a>Compilar o projeto U-SQL com a linha de comando MSBuild

Depois de migrar o projeto e obter o pacote do NuGet, você pode chamar a linha de comando padrão do MSBuild com os argumentos adicionais abaixo para criar seu projeto U-SQL:

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

A definição de argumentos e os valores são:

* USQLSDKPath=<pacote do Nuget do U-SQL>\build\runtime: este parâmetro faz referência ao caminho de instalação do pacote do NuGet para o serviço de linguagem U-SQL.
* USQLTargetType=Merge ou SyntaxCheck:
    * Mesclar: o modo Mesclar compila arquivos code-behind, como arquivos .cs, .py e .r, e incorpora a biblioteca de códigos definida pelo usuário resultante (como código binário, Python ou R) no script U-SQL.
    * SyntaxCheck: o modo SyntaxCheck primeiro mescla arquivos code-behind no script U-SQL e, em seguida, compila o script U-SQL para validar seu código.
* DataRoot=<DataRoot path>: o DataRoot só é necessário para o modo SyntaxCheck. Ao compilar o script com o modo SyntaxCheck, o MSBuild verifica as referências no script para objetos do banco de dados. Configure um ambiente local correspondente que contenha os objetos referenciados do banco de dados U-SQL na pasta DataRoot da máquina de compilação antes de compilar. Você também pode gerenciar essas dependências de banco de dados ao [fazer referência a um projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). Observe que o MSBuild verifica apenas a referência de objetos de banco de dados e não os arquivos.
* EnableDeployment=true ou false: EnableDeployment indica se é permitido implantar bancos de dados U-SQL referenciados durante o processo de compilação. Se você referenciar o projeto de banco de dados U-SQL e consumir os objetos de banco de dados no script U-SQL, configure esse parâmetro como true.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Integração contínua com o Visual Studio Team Service

Além da linha de comando, os clientes também podem usar o Build do Visual Studio ou a tarefa do MSBuild para compilar projetos U-SQL no Visual Studio Team Service. Para configurar a tarefa de compilação, verifique se:

1.  Adicione a tarefa de restauração do NuGet para obter a solução referenciada do pacote do NuGet incluindo `Azure.DataLake.USQL.SDK` de modo que o MSBuild possa encontrar os destinos de linguagem do U-SQL. Defina **Avançado > Diretório de destino** como `$(Build.SourcesDirectory)/packages` se quiser usar o exemplo de argumentos do MSBuild diretamente na etapa 2.

    ![Data Lake define tarefa do MSBuild de CI/CD para o projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Data Lake define tarefa do Nuget de CI/CD para projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Configure os Argumentos do MSBuild e poderá definir os argumentos no Build do Visual Studio ou na tarefa do MSBuild conforme descrito a seguir ou você pode definir variáveis para esses argumentos na definição do build do VSTS.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

    ![Data Lake define variáveis do MSBuild de CI/CD para o projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

### <a name="u-sql-project-build-output"></a>Saída da compilação do projeto U-SQL

Depois de executar a compilação, todos os scripts no projeto U-SQL serão criados e enviados a um arquivo zip chamado `USQLProjectName.usqlpack`. A estrutura de pastas do projeto será mantida na saída de compilação compactada.

>[!NOTE]
>
>O arquivo code-behind para cada script U-SQL será mesclado como uma instrução embutida na saída de compilação do script.
>

## <a name="test-u-sql-script"></a>Testar o script U-SQL

O Azure Data Lake oferece projetos de teste para o script U-SQL e para o UDO/UDAG/UDF de C#:
* [Saiba como adicionar casos de teste ao script U-SQL e ao código C# estendido](data-lake-analytics-cicd-test.md#test-u-sql-scripts)
* [Saiba como executar esses casos de teste no Visual Studio Team Service](data-lake-analytics-cicd-test.md#run-test-cases-in-visual-studio-team-service)

## <a name="u-sql-job-deployment"></a>Implantação de trabalhos do U-SQL

Depois de verificar o código através da compilação e do processo de testes, você pode enviar trabalhos do U-SQL diretamente do Visual Studio Team Service usando a **Tarefa do Azure PowerShell**. Você também pode implantar o script no Azure Data Lake Store/Armazenamento de Blob do Azure e [executar os trabalhos agendados por meio do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-visual-studio-team-service"></a>Enviar trabalhos do U-SQL por meio do Visual Studio Team Service

A compilação de saída do projeto U-SQL é um arquivo zip chamado **USQLProjectName.usqlpack** que inclui todos os scripts U-SQL do projeto. Você pode usar a [tarefa do Azure PowserShell no Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) com o script de exemplo do PowserShell abaixo para enviar trabalhos do U-SQL diretamente do build do Visual Studio Team Service ou do pipeline de lançamento.

```powershell
<#
    This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and submit all scripts one-by-one.

    Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.
          
    Example :
        USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

    return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."

    # Submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{
    Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
    Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
    Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

    Write-Output "Starting USQL script deployment..."
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Implantar trabalhos do U-SQL por meio do Azure Data Factory

Além de enviar trabalhos do U-SQL diretamente do Visual Studio Team Service, você também pode carregar os scripts compilados para o Azure Data Lake Store/Armazenamento de Blob do Azure e [executar os trabalhos agendados por meio do Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Use a [tarefa do Azure PowerShell no Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) com o script de exemplo do PowerShell abaixo para carregar os scripts U-SQL para a conta do Azure Data Lake Store.

```powershell
<#
    This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.
          
    Example :
        FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DesitinationFolder = "USQLScriptSource" # Desitination folder in ADLS
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles

    $files = @(get-childitem $usqlScripts -recurse)
    foreach($file in $files)
    {
        Write-Host "Uploading file: $($file.Name)"
        Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DesitinationFolder $file)" -Force
    }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-u-sql-database"></a>CI/CD para o banco de dados U-SQL

As Ferramentas do Azure Data Lake para Visual Studio oferecem o modelo de projeto de banco de dados U-SQL que ajuda os desenvolvedores a desenvolver, gerenciar e implantar os bancos de dados U-SQL de forma simples e rápida. [Saiba mais sobre o projeto de banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Compilar o projeto de banco de dados U-SQL

### <a name="get-nuget-package"></a>Obter o pacote do NuGet

O MSBuild não oferece suporte interno ao tipo de projeto de banco de dados U-SQL. Para adicionar essa capacidade, será preciso adicionar uma referência para sua solução ao [pacote do Nuget Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) que adiciona o serviço de linguagem necessário.

Para adicionar a referência do pacote do NuGet, clique com botão direito do mouse na solução no Gerenciador de Soluções e escolha **Gerenciar Pacotes do NuGet** para a Solução e, em seguida, pesquise e instale o pacote do NuGet. Outra opção é adicionar um arquivo chamado "packages.config" na pasta da solução e adicionar o seguinte conteúdo a ele.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-database-project-with-msbuild-command-line"></a>Compilar o projeto de banco de dados U-SQL com a linha de comando MSBuild

Você pode chamar a linha de comando do MSBuild padrão e passar a referência do Pacote do NuGet do SDK do U-SQL como argumento adicional conforme descrito abaixo para compilar seu projeto de banco de dados U-SQL:

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Os argumentos `USQLSDKPath=<U-SQL Nuget package>\build\runtime` fazem referência ao caminho de instalação do pacote do NuGet para o serviço de linguagem U-SQL.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Integração contínua com o Visual Studio Team Service

Além da linha de comando, os clientes também podem usar o **Build do Visual Studio** ou a  **tarefa do MSBuild** para compilar projetos banco de dados U-SQL no Visual Studio Team Service. Para configurar a tarefa de compilação, verifique se:

1.  Adicione a tarefa de restauração do NuGet para obter a solução referenciada do pacote do NuGet incluindo `Azure.DataLake.USQL.SDK` de modo que o MSBuild possa encontrar os destinos de linguagem do U-SQL. Defina **Avançado > Diretório de destino** como `$(Build.SourcesDirectory)/packages` se quiser usar o exemplo de argumentos do MSBuild diretamente na etapa 2.

    ![Data Lake define tarefa do MSBuild de CI/CD para o projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Data Lake define tarefa do Nuget de CI/CD para projeto U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Configure os Argumentos do MSBuild e poderá definir os argumentos no Build do Visual Studio ou na tarefa do MSBuild conforme descrito a seguir ou você pode definir variáveis para esses argumentos na definição do build do VSTS.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
    ```

    ![O Data Lake define variáveis do MSBuild de CI/CD para o projeto de banco de dados U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

### <a name="u-sql-database-project-build-output"></a>Saída de compilação do projeto de banco de dados U-SQL

A saída de build do projeto de banco de dados U-SQL é um pacote de implantação do banco de dados U-SQL nomeado com o sufixo `.usqldbpack`. O pacote `.usqldbpack` é um arquivo zip que inclui todas as instruções DDL em um único script U-SQL na pasta DDL e todos os arquivos .dll e adicionais para assemblies na pasta Temp.

## <a name="test-table-valued-function-and-stored-procedure"></a>Testar a função com valor de tabela e o procedimento armazenado

Neste momento, não há suporte direto para a adição de casos de teste para procedimentos armazenados e funções com valor de tabela. Como alternativa, você pode criar um projeto U-SQL que tem scripts U-SQL que chamam essas funções e gravar casos de teste para elas. Siga as etapas abaixo para configurar casos de teste para funções com valor de tabela e procedimentos armazenados definidos no projeto de banco de dados U-SQL:

1.  Crie um projeto U-SQL para fins de teste e grave scripts U-SQL chamando os procedimentos armazenados e as funções com valor de tabela.
2.  Adicione uma referência de banco de dados para este projeto U-SQL. Para obter a definição do procedimento armazenado e da função com valor de tabela, você precisará fazer referência ao projeto de banco de dados que contém a instrução DDL. [Saiba mais sobre a referência de bancos de dados](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Adicione casos de teste para os scripts U-SQL que chamam procedimentos armazenados e funções com valor de tabela. [Saiba como adicionar casos de teste ao script U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-visual-studio-team-service"></a>Implantar o banco de dados U-SQL por meio do Visual Studio Team Service

O `PackageDeploymentTool.exe` oferece as interfaces de programação e de linha de comando que ajudam a implantar o pacote de implantação (.usqldbpack) do banco de dados U-SQL. O SDK está incluído no [pacote do NuGet do SDK do U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) localizando em build/runtime/PackageDeploymentTool.exe. Usando o `PackageDeploymentTool.exe`, você pode implantar bancos de dados U-SQL no Azure Data Lake Analytics e a na conta local.

>[!NOTE]
>
>O suporte da linha de comando do PowerShell e o suporte da tarefa de liberação do Visual Studio Team Service para a implantação do banco de dados U-SQL estarão disponíveis em breve.
>

Siga as etapas abaixo para configurar a tarefa de implantação de banco de dados no Visual Studio Team Service:

1. Adicione uma tarefa de Script do PowerShell no build ou pipeline de lançamento e execute o seguinte script do PowerShell. Esta tarefa ajuda a obter dependências do SDK do Azure para `PackageDeploymentTool.exe` e `PackageDeploymentTool.exe`. É possível definir os parâmetros -AzureSDK e -DBDeploymentTool para carregar as dependências e a ferramenta de implantação em algumas pastas específicas. Passe o caminho -AzureSDK para `PackageDeploymentTool.exe` como o parâmetro -AzureSDKPath na etapa 2. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL dabatase deployment tool
        [string]$workingfolder = "" # Folder to execute these command lines
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.2.3-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.3.3-preview -outf Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.3.3-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.3.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. Adicione uma **Tarefa de linha de comando** no build ou no pipeline de lançamento e preencha o script chamando `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` está localizado na pasta $DBDeploymentTool definida. O script de exemplo é o seguinte: 

    * Implantar banco de dados U-SQL localmente

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Use o modo de autenticação interativa para implantar o banco de dados U-SQL na Conta do Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Use a autenticação de segredo para implantar o banco de dados U-SQL na Conta do Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Use a autenticação certFile para implantar o banco de dados U-SQL na Conta do Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

**Descrição do parâmetro PackageDeploymentTool.exe:**

**Parâmetros comuns:**

|Parâmetro|DESCRIÇÃO|Valor Padrão|Obrigatório|
|---------|-----------|-------------|--------|
|Pacote|Caminho do pacote de implantação de banco de dados U-SQL a ser implantado|nulo|verdadeiro|
|Banco de dados|Nome do banco de dados a ser implantado em/ou criado|master|falso|
|LogFile|Caminho do arquivo para registro, padrão para saída padrão (console)|nulo|falso|
|LogLevel|Nível de log: Detalhado, Normal, Aviso, Erro|LogLevel.Normal|falso|

**Parâmetro para a implantação local:**

|Parâmetro|DESCRIÇÃO|Valor Padrão|Obrigatório|
|---------|-----------|-------------|--------|
|DataRoot|Caminho da pasta raiz de dados local|nulo|verdadeiro|

**Parâmetro para a implantação do Azure Data Lake Analytics:**

|Parâmetro|DESCRIÇÃO|Valor Padrão|Obrigatório|
|---------|-----------|-------------|--------|
|Conta|Especifica a implantação em qual a conta do Azure Data Lake Analytics pelo nome da conta|nulo|verdadeiro|
|ResourceGroup|Nome do grupo de recursos do Azure para a conta do Azure Data Lake Analytics|nulo|verdadeiro|
|SubscriptionId|ID da assinatura do Azure para a conta do Azure Data Lake Analytics|nulo|verdadeiro|
|Locatário|Nome do locatário (nome de domínio do AAD, você pode encontrá-la na página de gerenciamento de assinaturas no portal do Azure)|nulo|verdadeiro|
|AzureSDKPath|Caminho para pesquisar assemblies dependentes no SDK do Azure|nulo|verdadeiro|
|Interativo|Usar o modo interativo ou não para autenticação|falso|falso|
|ClientId|ID do aplicativo AAD para nenhuma autenticação interativa, exigida para nenhuma autenticação interativa|nulo|exigida para nenhuma autenticação interativa|
|Secrete|Segredo/senha para nenhuma autenticação interativa, só deve ser usado em um ambiente seguro/confiável|nulo|exigida para nenhuma autenticação interativa ou use SecreteFile|
|SecreteFile|O arquivo salva o segredo/senha para nenhuma autenticação interativa, certifique-se de apenas o usuário atual consiga ler|nulo|exigida para nenhuma autenticação interativa ou use Secrete|
|CertFile|O arquivo salva a certificação X.509 para nenhuma autenticação interativa, o padrão é usar a autenticação de segredo do cliente|nulo|falso|
|JobPrefix|Prefixo para o trabalho DDL do U-SQL de implementação do banco de dados|Deploy_ + DateTime.Now|falso|

## <a name="next-steps"></a>Próximas etapas

- [Como testar seu código do Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Executar um script U-SQL no computador local](data-lake-analytics-data-lake-tools-local-run.md)
- [Usar o projeto de banco de dados U-SQL para desenvolver o banco de dados U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
