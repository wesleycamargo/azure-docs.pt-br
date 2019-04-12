---
title: Usar Pipelines do Azure para criar e implantar soluções HPC - lote do Azure | Microsoft Docs
description: Saiba como implantar um pipeline de build/versão de um aplicativo de HPC em execução em lote do Azure.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.openlocfilehash: 5b7c44d3ea3394ff728adfb9d9fd72293138fb2e
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494637"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Usar Pipelines do Azure para criar e implantar soluções HPC

Serviços de DevOps do Azure fornecem uma variedade de ferramentas usadas pelas equipes de desenvolvimento ao criar um aplicativo personalizado. As ferramentas fornecidas pelo Azure DevOps podem ser traduzidos em compilação e teste das soluções de computação de alto desempenho automatizados. Este artigo demonstra como configurar uma integração contínua (CI) e CD (implantação contínua) usando que pipelines do Azure para um alto desempenho de computação solução implantada no lote do Azure.

Pipelines do Azure fornece uma gama de processos de CI/CD modernas para compilar, implantar, testar e software de monitoramento. Esses processos aceleram o fornecimento de software, permitindo que você se concentrar em seu código em vez de dar suporte à infraestrutura e operações.

## <a name="create-an-azure-pipeline"></a>Criar um Pipeline do Azure

Neste exemplo, criaremos um build e release pipeline para implantar uma infraestrutura de lote do Azure e liberar um pacote de aplicativo. Supondo que o código é desenvolvido localmente, este é o fluxo geral de implantação:

![Diagrama mostrando o fluxo de implantação em nosso Pipeline](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Configuração

Para seguir as etapas neste artigo, você precisa de uma organização de DevOps do Azure e um projeto de equipe.

* [Criar uma organização de DevOps do Azure](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Criar um projeto no DevOps do Azure](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Controle de origem para o seu ambiente

Controle de origem permite que as equipes controlar as alterações feitas na Base de código e inspecionar as versões anteriores do código.

Normalmente, controle de origem é considerado em conjunto com o código de software. E quanto a infraestrutura subjacente? Isso nos leva à infraestrutura como código, onde usaremos modelos do Azure Resource Manager ou outras alternativas de código-fonte aberto para definir declarativamente nossa infraestrutura subjacente.

Este exemplo depende intensamente em um número de modelos do Resource Manager (documentos JSON) e os binários existentes. Você pode copiar esses exemplos em seu repositório e enviá-las ao DevOps do Azure.

A estrutura de base de código usada neste exemplo é semelhante ao seguinte:

* Uma **modelos do arm** pasta, que contém um número de modelos do Azure Resource Manager. Os modelos são explicados neste artigo.
* Um **aplicativo cliente** pasta, que é uma cópia do [Azure processamento em lote .NET arquivo com ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) exemplo. Isso não é necessário para este artigo.
* Uma **aplicativo de hpc** pasta, que é o Windows de 64 bits versão do [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* Um **pipelines** pasta. Ele contém um arquivo YAML nosso processo de build de estrutura de tópicos. Isso é discutido no artigo.

Esta seção pressupõe que você estiver familiarizado com modelos do Resource Manager Projetando e de controle de versão. Se você não estiver familiarizado com esses conceitos, consulte as páginas a seguir para obter mais informações.

* [O que é controle de origem?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Este exemplo utiliza vários modelos do Resource Manager para implantar nossa solução. Para fazer isso, usamos um número de modelos de recurso (semelhantes a unidades ou módulos) que implementam uma parte específica de funcionalidade. Também podemos usar um modelo de solução de ponta a ponta que é responsável por colocar esses subjacentes recursos juntos. Há alguns benefícios dessa abordagem:

* Os modelos de recurso subjacente podem ser individualmente o teste de unidade.
* Os modelos de recurso subjacente podem ser definidos como um padrão dentro de uma organização e ser reutilizados em várias soluções.

Neste exemplo, há um modelo de solução de ponta a ponta (Deployment) que implanta três modelos. Os modelos subjacentes são modelos de funcionalidade, responsáveis pela implantação de um aspecto específico da solução.

![Exemplo de estrutura de modelo vinculada usando modelos do Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

O primeiro modelo que veremos é para uma conta de armazenamento do Azure. Nossa solução requer uma conta de armazenamento para implantar o aplicativo em nossa conta do lote. Vale a pena esteja ciente de que o [guia de referência de modelo do Gerenciador de recursos para os tipos de recursos Microsoft. Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) ao criar modelos do Resource Manager para contas de armazenamento.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Em seguida, vamos examinar o modelo de conta do lote do Azure. A conta do lote do Azure atua como uma plataforma para executar vários aplicativos em pools (agrupamentos de computadores). Vale a pena esteja ciente de que o [guia de referência de modelo do Gerenciador de recursos para tipos de recursos do Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) ao criar modelos do Resource Manager para contas do lote.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

O próximo modelo mostra um exemplo da criação de um Pool do lote do Azure (as máquinas de back-end para processar a nossos aplicativos). Vale a pena esteja ciente de que o [guia de referência de modelo do Gerenciador de recursos para tipos de recursos do Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) ao criar modelos do Resource Manager para Pools de conta do lote.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Por fim, temos um modelo que age de forma semelhante a um orquestrador. Este modelo é responsável por implantar os modelos de recurso.

Você também pode encontrar mais informações sobre [criação de modelos vinculados do Azure Resource Manager](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) em outro artigo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>A solução HPC

A infraestrutura e software podem ser definidos como código e colocados no mesmo repositório.

Para esta solução, o ffmpeg é usado como o pacote de aplicativo. O pacote de ffmpeg pode ser baixado [aqui](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Exemplo da estrutura do repositório Git](media/batch-ci-cd/git-repository.jpg)

Há quatro seções principais para este repositório:

* O **modelos do arm** pasta que armazena a nossa infraestrutura como código
* O **aplicativo de hpc** pasta que contém os binários para ffmpeg
* O **pipelines** pasta que contém a definição para nosso pipeline de compilação.
* **Opcional**: O **aplicativo cliente** pasta que armazenará o código do aplicativo .NET. Não usaremos isso no exemplo, mas em seu próprio projeto, talvez você queira realizar execuções de aplicativo do lote de HPC por meio de um aplicativo cliente.

> [!NOTE]
> Isso é apenas um exemplo de uma estrutura para uma base de código. Essa abordagem é usada para fins de demonstração de aplicativo, infraestrutura e código do pipeline são armazenados no mesmo repositório.

Agora que o código-fonte é configurado, podemos começar a primeira compilação.

## <a name="continuous-integration"></a>Integração contínua

[Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), nos serviços de DevOps do Azure, ajuda você a implementar um pipeline de compilação, teste e implantação para seus aplicativos.

Neste estágio do pipeline, os testes são executados normalmente para validar o código e criar as partes apropriadas do software. O número e tipos de testes e as tarefas adicionais que você executar dependerá da sua compilação mais ampla e estratégia de versão.

## <a name="preparing-the-hpc-application"></a>Preparando o aplicativo de HPC

Neste exemplo, nos concentraremos na **aplicativo de hpc** pasta. O **aplicativo de hpc** pasta é o software de ffmpeg que será executado de dentro da conta do lote do Azure.

1. Navegue até a seção de compilações de Pipelines do Azure em sua organização de DevOps do Azure. Criar uma **novo pipeline**.

    ![Criar um novo Pipeline de compilação](media/batch-ci-cd/new-build-pipeline.jpg)

1. Você tem duas opções para criar um pipeline de compilação:

     a. [Usando o Designer Visual](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Para usar isso, clique em "Usar o designer visual" sobre o **novo pipeline** página.

    b. [Usando YAML compilações](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Você pode criar um novo pipeline YAML clicando no repositórios do Azure ou a opção do GitHub na nova página de pipeline. Como alternativa, você pode armazenar o exemplo a seguir em seu controle de origem e fazer referência a um arquivo YAML existente clicando em um Designer Visual e, em seguida, usando o modelo YAML.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Depois que a compilação estiver configurada conforme necessário, selecione **salvar e enfileirar**. Se você tiver habilitada a integração contínua (na **gatilhos** seção), a compilação irá disparar automaticamente quando é feita uma nova confirmação no repositório, atendem às condições definidas na compilação.

    ![Exemplo de um Pipeline de compilação existente](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Exibir atualizações em tempo real sobre o progresso de sua compilação no DevOps do Azure, navegando até a **Build** seção dos Pipelines do Azure. Selecione a compilação apropriada na sua definição de compilação.

    ![Saídas de modo de exibição ao vivo de sua compilação](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Se você usar um aplicativo cliente para executar o aplicativo do lote de HPC, você precisará criar uma definição de compilação separado para o aplicativo. Você pode encontrar um número de guias de instruções na [Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) documentação.

## <a name="continuous-deployment"></a>Implantação contínua

Pipelines do Azure também é usados para implantar seu aplicativo e a infraestrutura subjacente. [Liberar pipelines](https://docs.microsoft.com/azure/devops/pipelines/release/what-is-release-management?view=azure-devops) é o componente que permite a implantação contínua e automatiza o processo de liberação.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Implantando seu aplicativo e a infra-estrutura subjacente

Há uma série de etapas envolvidas na implantação da infra-estrutura. Como usamos [modelos vinculados](../azure-resource-manager/resource-group-linked-templates.md), esses modelos precisarão ser acessível a partir de um ponto de extremidade público (HTTP ou HTTPS). Isso poderia ser um repositório no GitHub, ou uma conta de armazenamento de BLOBs do Azure ou em outro local de armazenamento. Os artefatos de modelo carregado podem permanecer seguros, pois eles podem ser mantidos em um modo privado, mas acessado usando algum tipo de token SAS (assinatura) de acesso compartilhado. O exemplo a seguir demonstra como implantar uma infraestrutura com modelos de um blob de armazenamento do Azure.

1. Criar uma **nova definição de versão**e selecione uma definição vazia. Em seguida, precisamos renomear o ambiente criado recentemente para algo relevante para nosso pipeline.

    ![Pipeline de lançamento inicial](media/batch-ci-cd/Release-0.jpg)

1. Crie uma dependência no Pipeline de Build para obter a saída para nosso aplicativo de HPC.

    > [!NOTE]
    > Mais uma vez, observe os **Alias de origem**, pois isso será necessário quando as tarefas são criadas dentro da definição de versão.

    ![Criar um link de artefato para o HPCApplicationPackage no pipeline de compilação apropriado](media/batch-ci-cd/Release-1.jpg)

1. Crie um link para outro artefato, desta vez, um repositório do Azure. Isso é necessário para acessar os modelos do Resource Manager armazenados no repositório. Como os modelos do Resource Manager não exigem a compilação, você não precisa enviá-las por meio de um pipeline de compilação.

    > [!NOTE]
    > Mais uma vez, observe os **Alias de origem**, pois isso será necessário quando as tarefas são criadas dentro da definição de versão.

    ![Criar um link de artefato para os repositórios do Azure](media/batch-ci-cd/Release-2.jpg)

1. Navegue até a **variáveis** seção. É recomendável criar um número de variáveis em seu pipeline, portanto, você não estiver inserindo as mesmas informações em várias tarefas. Essas são as variáveis usadas neste exemplo, e como elas afetam a implantação.

    * **applicationStorageAccountName**: Nome da conta de armazenamento para armazenar os binários do aplicativo de HPC
    * **batchAccountApplicationName**: Nome do aplicativo na conta do lote do Azure
    * **batchAccountName**: Nome da conta do lote do Azure
    * **batchAccountPoolName**: Nome do pool de VMs fazendo o processamento
    * **batchApplicationId**: ID exclusiva para o aplicativo do lote do Azure
    * **batchApplicationVersion**: Versão semântica do seu aplicativo do lote (ou seja, os binários de ffmpeg)
    * **location**: Local para os recursos do Azure ser implantado
    * **resourceGroupName**: Nome do grupo de recursos a serem criados, e onde seus recursos serão implantados
    * **storageAccountName**: Nome da conta de armazenamento para manter os modelos de Gerenciador de recursos vinculados

    ![Exemplo de como as variáveis definidas para a versão de Pipelines do Azure](media/batch-ci-cd/Release-4.jpg)

1. Navegue até as tarefas para o ambiente de desenvolvimento. No instantâneo, abaixo, você pode ver as seis tarefas. Essas tarefas serão: baixar os arquivos de ffmpeg compactado, implante uma conta de armazenamento para hospedar os modelos do Gerenciador de recursos aninhados, copie os modelos do Resource Manager para a conta de armazenamento, implantar a conta do lote e as dependências necessárias, criar um aplicativo em a conta do lote do Azure e carregar o pacote de aplicativos para a conta do lote do Azure.

    ![Exemplo de tarefas usadas para lançar o aplicativo de HPC ao lote do Azure](media/batch-ci-cd/Release-3.jpg)

1. Adicione a **baixar o artefato de Pipeline (visualização)** de tarefa e defina as seguintes propriedades:
    * **Nome de exibição:** Baixar ApplicationPackage ao agente
    * **O nome do artefato para baixar:** aplicativo hpc
    * **Caminho para fazer o download para**: $(System.DefaultWorkingDirectory)

1. Crie uma conta de armazenamento para armazenar os artefatos. Uma conta de armazenamento existente da solução pode ser usada, mas para a amostra autossuficiente e o isolamento de conteúdo, estamos fazendo uma conta de armazenamento dedicado para nosso artefatos (especificamente, os modelos do Resource Manager).

    Adicione a **implantação de grupo de recursos do Azure** de tarefa e defina as seguintes propriedades:
    * **Nome de exibição:** Implantar a conta de armazenamento para modelos do Resource Manager
    * **Assinatura do Azure:** Selecione a assinatura apropriada do Azure
    * **Ação**: Criar ou atualizar o grupo de recursos
    * **Grupo de recursos**: $(resourceGroupName)
    * **Local**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Substituir parâmetros de modelo**: accountName - $(storageAccountName)

1. Carregue os artefatos do controle de origem para a conta de armazenamento. Há uma tarefa de Pipeline do Azure para executar esse procedimento. Como parte dessa tarefa, a URL de contêiner da conta de armazenamento e o Token de SAS podem ter saída a uma variável em Pipelines do Azure. Isso significa que ele possa ser reutilizado durante essa fase de agente.

    Adicione a **do Azure File Copy** de tarefa e defina as seguintes propriedades:
    * **Source:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Tipo de Conexão do Azure**: Azure Resource Manager
    * **Assinatura do Azure:** Selecione a assinatura apropriada do Azure
    * **Tipo de destino**: Blob do Azure
    * **Conta de armazenamento do RM**: $(storageAccountName)
    * **Nome do contêiner**: modelos
    * **URI do contêiner de armazenamento**: templateContainerUri
    * **Token de SAS do contêiner de armazenamento**: templateContainerSasToken

1. Implante o modelo do orchestrator. Lembre-se o modelo do orchestrator anteriores, você observará que havia parâmetros para a URL do contêiner de conta de armazenamento, além do token SAS. Você deve observar que as variáveis necessárias no modelo do Resource Manager também são mantidas na seção de variáveis da definição de versão ou que foram definidas de outra tarefa de Pipelines do Azure (por exemplo, parte da tarefa de cópia de Blob do Azure).

    Adicione a **implantação de grupo de recursos do Azure** de tarefa e defina as seguintes propriedades:
    * **Nome de exibição:** Implantar o lote do Azure
    * **Assinatura do Azure:** Selecione a assinatura apropriada do Azure
    * **Ação**: Criar ou atualizar o grupo de recursos
    * **Grupo de recursos**: $(resourceGroupName)
    * **Local**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Substituir parâmetros de modelo**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Uma prática comum é usar tarefas do Azure Key Vault. Se a entidade de serviço (conexão à sua assinatura do Azure) tem um políticas de acesso apropriado definido, ele pode fazer o download de segredos de um Azure Key Vault e ser usado como variáveis em seu pipeline. O nome do segredo será definido com o valor associado. Por exemplo, um segredo de sshPassword pode ser referenciado com $(sshPassword) na definição da versão.

1. As próximas etapas chame a CLI do Azure. O primeiro é usado para criar um aplicativo no lote do Azure. e carregue os pacotes associados.

    Adicione a **CLI do Azure** de tarefa e defina as seguintes propriedades:
    * **Nome de exibição:** Criar um aplicativo na conta do lote do Azure
    * **Assinatura do Azure:** Selecione a assinatura apropriada do Azure
    * **Localização do script**: Script embutido
    * **Script embutido**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. A segunda etapa é usada para carregar os pacotes associados ao aplicativo. Em nosso caso, os arquivos de ffmpeg.

    Adicione a **CLI do Azure** de tarefa e defina as seguintes propriedades:
    * **Nome de exibição:** Carregar pacote à conta do lote do Azure
    * **Assinatura do Azure:** Selecione a assinatura apropriada do Azure
    * **Localização do script**: Script embutido
    * **Script embutido**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > O número de versão do pacote de aplicativos é definido como uma variável. Isso é conveniente se substituir as versões anteriores do pacote funciona para você, e se você quiser controlar manualmente o número de versão do pacote enviado por push para o lote do Azure.

1. Criar uma nova versão, selecionando **versão > Crie uma nova versão**. Depois de disparado, selecione o link para a nova versão para exibir o status.

1. Você pode exibir a saída em tempo real do agente, selecionando o **Logs** botão sob seu ambiente.

    ![Exibir o status da liberação](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>O ambiente de teste

Depois que o ambiente estiver configurado, confirme se que os testes a seguir podem ser concluídos com êxito.

Conecte-se para a nova conta do lote do Azure, usando a CLI do Azure em um prompt de comando do PowerShell.

* Entrar em sua conta do Azure com `az login` e siga as instruções para se autenticar.
* Agora se autenticar a conta do lote: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Liste os aplicativos disponíveis

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Verifique o pool é válido

```azurecli
az batch pool list
```

Observe o valor de `currentDedicatedNodes` da saída desse comando. Esse valor é ajustado no próximo teste.

#### <a name="resize-the-pool"></a>Redimensionar o pool

Redimensionar o pool, então há nós disponíveis para teste de tarefa e trabalho de computação, entre em contato com o comando de lista de pool para ver o status atual até que o redimensionamento foi concluído e os nós disponíveis

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Próximas etapas

Além deste artigo, há dois tutoriais que utilizam o ffmpeg, usando o .NET e Python. Consulte estes tutoriais para obter mais informações sobre como interagir com uma conta do lote por meio de um aplicativo simples.

* [Executar uma carga de trabalho paralela com o Lote do Azure usando a API do Python](tutorial-parallel-python.md)
* [Executar uma carga de trabalho paralela com o Lote do Azure usando a API do .NET](tutorial-parallel-dotnet.md)
