---
title: Cargas de trabalho de contêiner no Lote do Azure | Microsoft Docs
description: Saiba como executar aplicativos de imagens de contêiner no Lote do Azure.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 05/07/2018
ms.author: danlep
ms.openlocfilehash: 8c9f772c9d3908e450961239797f6ce2bd4982e4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885869"
---
# <a name="run-container-applications-on-azure-batch"></a>Executar aplicativos de contêiner no Lote do Azure

O Lote do Azure permite executar e dimensionar um grande número trabalhos de computação em lote no Azure. Até agora, as tarefas do Lote foram executadas diretamente em VMs (máquinas virtuais) em um pool do Lote, mas agora você pode configurar um pool do Lote para executar tarefas em contêineres do Docker. Este artigo mostra como usar o SDK do .NET para o Lote para criar um pool de nós de computação que dão suporte às tarefas do contêiner em execução e como executar as tarefas do contêiner no pool.

O uso de contêineres fornece uma maneira fácil para executar tarefas do Lote sem a necessidade de gerenciar um ambiente e as dependências para executar aplicativos. Os contêineres implantam aplicativos como unidades leves, portáteis, autossuficientes que podem ser executadas em vários ambientes. Por exemplo, você pode criar e testar um contêiner localmente e carregar a imagem do contêiner em um registro no Azure ou em outro lugar. O modelo de implantação do contêiner faz com que o ambiente de tempo de execução do seu aplicativo esteja sempre corretamente instalado e configurado, independentemente de onde você hospeda o aplicativo. As tarefas baseadas em contêiner no Lote também podem aproveitar os recursos de tarefas que não são de contêiner, incluindo pacotes de aplicativos e o gerenciamento de arquivos de recurso e arquivos de saída. 

Este artigo supõe familiaridade com conceitos de contêiner do Docker e como criar um pool e um trabalho do Lote usando o SDK do .NET. Os trechos de código destinam-se para uso em um aplicativo cliente, como o [exemplo DotNetTutorial](batch-dotnet-get-started.md), e são exemplos de código em que você precisa dar suporte a aplicativos de contêiner no Lote.


## <a name="prerequisites"></a>pré-requisitos

* **Versões do SDK**: os SDKs do Lote começam a dar suporte a imagens de contêiner nas seguintes versões:
    * API REST do Lote versão 2017-09-01.6.0
    * SDK do .NET para o Lote versão 8.0.0
    * SDK do Python para o Lote versão 4.0
    * SDK Java para o Lote versão 3.0
    * SDK Node. js para o Lote versão 3.0

* **Contas**: em sua assinatura do Azure, você precisa criar uma conta do Lote e, opcionalmente, uma conta do Armazenamento do Azure.

* **Uma imagem de VM compatível**: há suporte para contêineres apenas nos pools criados com a Configuração de Máquina Virtual com base nas imagens detalhadas na seção a seguir, "Imagens de máquina virtual compatíveis". Caso você forneça uma imagem personalizada, o aplicativo precisará usar a [autenticação do Azure AD](batch-aad-auth.md) (Azure Active Directory) para executar cargas de trabalho baseadas em contêiner. 


## <a name="supported-virtual-machine-images"></a>Imagens de máquina virtual com suporte

Você precisa usar uma imagem do Windows ou Linux compatível para criar um pool de nós de computação da VM para cargas de trabalho de contêiner.

### <a name="windows-images"></a>Imagens do Windows

Para cargas de trabalho de contêiner do Windows, o Lote atualmente dá suporte a imagens personalizadas que você cria nas máquinas virtuais que executam o Docker no Windows, ou você pode usar o Windows Server 2016 Datacenter com a imagem de contêineres do Azure Marketplace. Esta imagem é compatível com a ID de SKU do agente do nó `batch.node.windows amd64`. O tipo de contêiner com suporte é atualmente limitado ao Docker.

### <a name="linux-images"></a>Imagens do Linux

Para cargas de trabalho de contêiner do Linux, o Lote atualmente dá suporte somente a imagens personalizadas que você cria nas VMs que executam o Docker nas seguintes versões do Linux: Ubuntu 16.04 LTS ou CentOS 7.3. Se você optar por fornecer sua própria imagem personalizada do Linux, confira as instruções em [Usar uma imagem personalizada gerenciada para criar um pool de máquinas virtuais](batch-custom-images.md).

Para o suporte ao Docker, instale o [Docker CE (Community Edition)](https://www.docker.com/community-edition) ou o [Docker EE (Enterprise Edition)](https://www.docker.com/enterprise-edition).

Se você quiser tirar proveito do desempenho de GPU dos tamanhos de VM NC ou NV do Azure, precisará instalar drivers NVIDIA na imagem. Além disso, você precisa instalar e executar o utilitário de mecanismo do Docker para GPUs NVIDIA, o [Docker NVIDIA](https://github.com/NVIDIA/nvidia-docker).

Para acessar a rede RDMA do Azure, use os tamanhos de VM compatíveis com RDMA, como A8, A9, H16r, H16mr ou NC24r. Os drivers RDMA necessários estão instalados nas imagens CentOS 7.3 HPC e Ubuntu 16.04 LTS do Azure Marketplace. Outras configurações podem ser necessárias para executar cargas de trabalho de MPI. Confira [Usar instâncias compatíveis com RDMA ou habilitadas para GPU em pool do Lote](batch-pool-compute-intensive-sizes.md).


## <a name="limitations"></a>Limitações

* O Lote dá suporte a RDMA apenas para contêineres em execução em pools Linux.


## <a name="authenticate-using-azure-active-directory"></a>Autenticar usando o Azure Active Directory

Se você usar uma imagem de VM personalizada para criar o pool do Lote, o aplicativo cliente deverá fazer a autenticação usando a autenticação integrada do Azure AD (a autenticação de chave compartilhada não funciona). Antes de executar o aplicativo, registre-o no Azure AD para estabelecer uma identidade para ele e especificar suas permissões para outros aplicativos.

Além disso, quando você usar uma imagem de VM personalizada, precisará conceder controle de acesso IAM ao aplicativo para acessar a imagem de VM. No portal do Azure, clique em **Todos os recursos**, selecione a imagem de contêiner e, na seção **IAM (controle de acesso)** da página da imagem, clique em **Adicionar**. Na página **Adicionar permissões**, especifique uma **Função**, em **Atribuir acesso a**, selecione **Usuário, grupo ou aplicativo do Azure AD** e, em **Selecionar**, insira o nome do aplicativo.

No aplicativo, passe um token de autenticação do Azure AD ao criar o cliente do Lote. Caso esteja desenvolvendo com o SDK do .NET para o Lote, use [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_), conforme descrito em [Autenticar soluções do serviço do Lote no Active Directory](batch-aad-auth.md).


## <a name="reference-a-vm-image-for-pool-creation"></a>Fazer referência a uma imagem de VM para a criação de pool

No código do aplicativo, forneça uma referência para a imagem de VM para ser usada na criação dos nós de computação do pool. Você pode fazer isso criando um objeto [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference). Você pode especificar a imagem a ser usada em uma das seguintes maneiras:

* Se você estiver usando uma imagem personalizada, forneça um identificador de recurso do Azure Resource Manager para a imagem de máquina virtual. O identificador da imagem tem um formato de caminho, conforme mostrado no exemplo abaixo:

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    Para obter essa ID da imagem no portal do Azure, abra **Todos os recursos**, selecione a imagem personalizada e, na seção **Visão geral** da página da imagem, copie o caminho na **ID de recurso**.

* Se estiver usando uma imagem do [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based), forneça um grupo de parâmetros que descrevem a imagem: editor, tipo de oferta, SKU e a versão da imagem, conforme listado em [Lista de imagens de máquina virtual](batch-linux-nodes.md#list-of-virtual-machine-images):

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>Configuração do contêiner para o pool do Lote

Para permitir que um pool do Lote execute cargas de trabalho de contêiner, é necessário especificar as configurações [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) no objeto [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) do pool.

Crie um pool habilitado para contêiner com ou sem imagens de contêiner pré-buscadas, conforme mostrado nos exemplos a seguir. O processo de pull (ou pré-busca) permite que você carregue previamente imagens de contêiner do Hub do Docker ou de outro registro de contêiner na Internet. A vantagem da pré-busca de imagens de contêiner é que quando as tarefas começam a ser executadas, elas não precisam esperar que a imagem de contêiner seja baixada. A configuração do contêiner efetua pull de imagens de contêiner para as VMs quando o pool é criado. As tarefas executadas no pool podem fazer referência à lista de imagens de contêiner e a opções de execução do contêiner.



### <a name="pool-without-prefetched-container-images"></a>Pool sem imagens de contêiner de pré-busca

Para configurar um pool habilitado para contêiner sem imagens de contêiner pré-buscadas, defina os objetos `ContainerConfiguration` e `VirtualMachineConfiguration`, conforme mostrado no exemplo a seguir. Isso e os exemplos a seguir presumem que você está usando uma imagem personalizada do Ubuntu 16.04 LTS com o Mecanismo do Docker instalado.

```csharp
// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-for-container-configuration"></a>Imagens de pré-busca para configuração do contêiner

Para executar a pré-busca de imagens de contêiner no pool, adicione a lista de imagens de contêiner (`containerImageNames`) à `ContainerConfiguration` e nomeie a lista de imagens. O exemplo a seguir pressupõe que você está usando uma imagem personalizada do Ubuntu 16.04 LTS, e fez a pré-busca de uma imagem de TensorFlow do [Hub do Docker](https://hub.docker.com). Este exemplo inclui uma tarefa inicial que é executada no host de VM em nós do pool. Você pode executar uma tarefa inicial no host, por exemplo, para montar um servidor de arquivos que pode ser acessado por meio dos contêineres.

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native host command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>Imagens de pré-busca de um registro de contêiner privado

Você também pode executar a pré-busca de imagens de contêiner ao fazer a autenticação em um servidor de registro de contêiner privado. No exemplo a seguir, os objetos `ContainerConfiguration` e `VirtualMachineConfiguration` usam uma imagem personalizada do Ubuntu 16.04 LTS e realizam a pré-busca de uma imagem privada do TensorFlow de um Registro de Contêiner do Azure privado.

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>Configurações de contêiner para a tarefa

Para executar as tarefas de contêiner nos nós de computação, é necessário definir configurações específicas ao contêiner, como opções de execução de tarefa, imagens a serem usadas e registro.

Use a propriedade `ContainerSettings` das classes de tarefa para definir configurações específicas ao contêiner. Essas configurações são definidas pela classe [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings).

Se você executar tarefas em imagens de contêiner, a [tarefa nuvem](/dotnet/api/microsoft.azure.batch.cloudtask) e a [tarefa do gerenciador de trabalho](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) exigirão configurações de contêiner. No entanto, [iniciar tarefa](/dotnet/api/microsoft.azure.batch.starttask), [tarefa de preparação de trabalho](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) e [tarefa de liberação de trabalho](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) não exigem configurações de contêiner (ou seja, podem ser executados em um contexto de contêiner ou diretamente no nó).

Quando você define as configurações do contêiner, todos os diretórios recursivamente abaixo de `AZ_BATCH_NODE_ROOT_DIR` (a raiz dos diretórios do Lote do Azure no nó) são mapeadas para o contêiner, todas as variáveis de ambiente são mapeadas para o contêiner e a linha de comando da tarefa é executada no contêiner.

O exemplo de código em [Imagens para a configuração do contêiner de pré-busca](#prefetch-images-for-container-configuration) mostrou como você pode especificar uma configuração de contêiner para uma tarefa inicial. O exemplo de código abaixo mostra como especificar a configuração do contêiner para uma tarefa de nuvem:

```csharp
// Simple container task command

string cmdLine = "<my-command-line>";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>Próximas etapas

* Consulte também o kit de ferramentas [Batch Shipyard](https://github.com/Azure/batch-shipyard) para facilitar a implantação de cargas de trabalho do contêiner no Lote do Azure através de [receitas Shipyard](https://github.com/Azure/batch-shipyard/tree/master/recipes).

* Para saber mais sobre como instalar e usar o Docker CE no Linux, confira a documentação do [Docker](https://docs.docker.com/engine/installation/).

* Para saber mais sobre como usar imagens personalizadas, confira [Usar uma imagem personalizada gerenciada para criar um pool de máquinas virtuais](batch-custom-images.md).
