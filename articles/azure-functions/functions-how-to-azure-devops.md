---
title: Fornecer continuamente atualizações de código de função usando o DevOps do Azure
description: Saiba como configurar um pipeline de DevOps do Azure direcionamento do Azure Functions.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: 86f1c36bd5f972a6ebd573019a22b0c0d07dc480
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928086"
---
# <a name="continuous-delivery-using-azure-devops"></a>Entrega contínua com o DevOps do Azure

Você pode implantar automaticamente sua função para um aplicativo de função do Azure usando [Pipelines do Azure](/azure/devops/pipelines/).
Para definir seu pipeline, você pode usar:

- Arquivo YAML: Esse arquivo descreve o pipeline, ele pode ter uma seção de etapas de compilação e uma seção de versão. O arquivo YAML deve estar no mesmo repositório que o aplicativo.

- Modelos: Os modelos são feitas as tarefas que compila ou implanta seu aplicativo prontos.

## <a name="yaml-based-pipeline"></a>Pipeline com base em YAML

### <a name="build-your-app"></a>Crie seu aplicativo

Criando seu aplicativo em Pipelines do Azure depende da linguagem de programação de seu aplicativo.
Cada linguagem tem etapas de compilação específica para criar um artefato de implantação, que pode ser usado para implantar seu aplicativo de funções no Azure.

#### <a name="net"></a>.NET

Você pode usar o exemplo a seguir para criar o arquivo YAML para compilar seu aplicativo .NET.

```yaml
jobs:
  - job: Build
    pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: true
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

Você pode usar o exemplo a seguir para criar o arquivo YAML para compilar seu aplicativo JavaScript:

```yaml
jobs:
  - job: Build
    pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

Você pode usar o exemplo a seguir para criar o arquivo YAML para compilar seu aplicativo Python, o Python tem suporte somente para Linux do Azure Functions:

```yaml
jobs:
  - job: Build
    pool:
      vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

### <a name="deploy-your-app"></a>Implantar seu aplicativo

Dependendo do sistema operacional host, você precisa incluir o exemplo a seguir YAML no arquivo YAML.

#### <a name="windows-function-app"></a>Função de aplicativo do Windows

O trecho a seguir pode ser usado para implantar um aplicativo de função do Windows

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
```

#### <a name="linux-function-app"></a>Função de aplicativo do Linux

O trecho a seguir pode ser usado para implantar um aplicativo de função do Linux

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
```

## <a name="template-based-pipeline"></a>Com base no modelo de pipeline

Os modelos no DevOps do Azure, são um grupo predefinido de tarefas que compilar ou implantar um aplicativo.

### <a name="build-your-app"></a>Crie seu aplicativo

Criando seu aplicativo em Pipelines do Azure depende da linguagem de programação de seu aplicativo. Cada linguagem tem etapas de compilação específica para criar um artefato de implantação, que pode ser usado para atualizar seu aplicativo de funções no Azure.
Para usar os modelos de build internas, ao criar um novo pipeline de compilação, escolha **usar o editor clássico** para criar um pipeline usando os modelos de designer

![Editor clássico de Pipelines do Azure](media/functions-how-to-azure-devops/classic-editor.png)

Depois de configurar a origem do seu código, modelos de compilação de pesquisa para o Azure Functions e escolha o modelo que corresponda ao idioma do seu aplicativo.

![Modelos de compilação do Azure Functions](media/functions-how-to-azure-devops/build-templates.png)

#### <a name="javascript-apps"></a>Aplicativos de JavaScript

Se seu aplicativo JavaScript têm uma dependência em módulos nativos do Windows, você precisará atualizar:

- A versão do Pool de agentes para **VS2017 hospedado**

  ![Alterar agente de compilação do sistema operacional](media/functions-how-to-azure-devops/change-agent.png)

- O script a **criar extensões** etapa no modelo para `IF EXIST *.csproj dotnet build extensions.csproj --output ./bin`

  ![Script de alteração](media/functions-how-to-azure-devops/change-script.png)

### <a name="deploy-your-app"></a>Implantar seu aplicativo

Ao criar um novo pipeline de lançamento, procure o modelo de versão do Azure Functions.

![](media/functions-how-to-azure-devops/release-template.png)

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>Criando um Pipeline do Azure usando a CLI do Azure

Usando o `az functionapp devops-pipeline create` [comando](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create), será criado um pipeline do Azure para compilar e liberar quaisquer alterações de código em seu repositório. O comando gerará um novo arquivo YAML que define o pipeline de build e versão e confirmá-lo ao seu repositório.
Pré-requisitos para esse comando dependem do local do seu código:

- Se seu código está no GitHub:

    - Você precisa ter **gravar** permissão à sua assinatura.

    - Você é o administrador de projeto de DevOps do Azure.

    - Você tem permissão para criar um Token pessoal do GitHub acesso com permissões suficientes. [Requisitos de permissão de PAT do GitHub.](https://aka.ms/azure-devops-source-repos)

    - Você tem permissão para confirmar para o branch mestre em seu repositório do GitHub para confirmar o arquivo YAML gerado automaticamente.

- Se seu código é em repositórios do Azure:

    - Você precisa ter **gravar** permissão à sua assinatura.

    - Você é o administrador de projeto de DevOps do Azure.

## <a name="next-steps"></a>Próximas etapas

+ [Visão geral das Funções do Azure](functions-overview.md)
+ [Visão geral de DevOps do Azure](/azure/devops/pipelines/)
