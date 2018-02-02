---
title: "Cargas de trabalho de contêiner no Lote do Azure | Microsoft Docs"
description: "Saiba como executar aplicativos de imagens de contêiner no Lote do Azure."
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 12/01/2017
ms.author: danlep
ms.openlocfilehash: 2fa5f9335a4d00f489f11c0db23322ab971a224f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="run-container-applications-on-azure-batch"></a>Executar aplicativos de contêiner no Lote do Azure

O Lote do Azure permite executar e dimensionar um grande número trabalhos de computação em lote no Azure. Até agora, as tarefas do Lote foram executadas diretamente em VMs (máquinas virtuais) em um pool do Lote, mas agora você pode configurar um pool do Lote para executar tarefas em contêineres do Docker.

O uso de contêineres fornece uma maneira fácil para executar tarefas do Lote sem a necessidade de gerenciar as dependências e os pacotes de aplicativos. Os contêineres implantam aplicativos como unidades leves, portáteis, autossuficientes que podem ser executadas em vários ambientes. Por exemplo, você pode criar e testar um contêiner localmente e carregar a imagem do contêiner em um registro no Azure ou em outro lugar. O modelo de implantação do contêiner faz com que o ambiente de tempo de execução do seu aplicativo esteja sempre corretamente instalado e configurado, independentemente de onde você hospeda o aplicativo. Este tutorial mostra como usar o SDK do .NET para o Lote a fim de criar um conjunto de nós de computação que dão suporte a tarefas do contêiner em execução e como executar tarefas de contêiner no pool.

Este artigo supõe familiaridade com conceitos de contêiner do Docker e como criar um pool e um trabalho do Lote usando o SDK do .NET. Os trechos de código destinam-se para uso em um aplicativo cliente, como o [exemplo DotNetTutorial](batch-dotnet-get-started.md), e são exemplos de código em que você precisa dar suporte a aplicativos de contêiner no Lote.


## <a name="prerequisites"></a>pré-requisitos

* Versões do SDK: os SDKs do Lote dão suporte a imagens de contêiner nas seguintes versões:
    * API REST do Lote versão 2017-09-01.6.0
    * SDK do .NET para o Lote versão 8.0.0
    * SDK do Python para o Lote versão 4.0
    * SDK Java para o Lote versão 3.0
    * SDK Node. js para o Lote versão 3.0

* Contas: na sua conta do Azure, você precisa criar uma conta do Lote e, opcionalmente, uma conta de Armazenamento de uso geral.

* Uma imagem de VM com suporte. Os contêineres só têm suporte em pools criados com a configuração de máquina virtual a partir de imagens detalhadas na seção a seguir, "Imagens de máquina virtual com suporte".

* Se você fornecer uma imagem personalizada, o aplicativo deverá usar a autenticação do Azure AD (Azure Active Directory) para executar cargas de trabalho de contêiner. Se você usa uma imagem do Azure Marketplace, não é necessário fazer a autenticação do Azure AD; a autenticação de chave compartilhada funcionará. O suporte ao Lote do Azure para o Azure AD está documentado em [Autenticar soluções do serviço Lote com o Active Directory](batch-aad-auth.md).


## <a name="supported-virtual-machine-images"></a>Imagens de máquina virtual com suporte

Você precisa fornecer uma imagem Windows ou Linux para criar um pool de nós de computação de VM.

### <a name="windows-images"></a>Imagens do Windows

Para cargas de trabalho de contêiner do Windows, o Lote atualmente dá suporte a imagens personalizadas que você cria nas máquinas virtuais que executam o Docker no Windows, ou você pode usar o Windows Server 2016 Datacenter com a imagem de contêineres do Azure Marketplace. Esta imagem é compatível com a ID de SKU do agente do nó `batch.node.windows amd64`. O tipo de contêiner com suporte é atualmente limitado ao Docker.

### <a name="linux-images"></a>Imagens do Linux

Para cargas de trabalho de contêiner do Linux, o Lote atualmente dá suporte somente a imagens personalizadas que você cria nas VMs que executam o Docker nas seguintes versões do Linux: Ubuntu 16.04 LTS ou CentOS 7.3. Se você optar por fornecer sua própria imagem personalizada do Linux, confira as instruções em [Usar uma imagem personalizada gerenciada para criar um pool de máquinas virtuais](batch-custom-images.md).

Você pode usar a [Docker CE (Community Edition)](https://www.docker.com/community-edition) ou a [Docker EE (Enterprise Edition)](https://www.docker.com/enterprise-edition).

Se você quiser tirar proveito do desempenho de GPU dos tamanhos de VM NC ou NV do Azure, precisará instalar drivers NVIDIA na imagem. Além disso, você precisa instalar e executar o utilitário de mecanismo do Docker para GPUs NVIDIA, o [Docker NVIDIA](https://github.com/NVIDIA/nvidia-docker).

Para acessar a rede RDMA do Azure, use VMs dos seguintes tamanhos: A8, A9, H16r, H16mr ou NC24r. Os drivers RDMA necessários estão instalados nas imagens CentOS 7.3 HPC e Ubuntu 16.04 LTS do Azure Marketplace. Outras configurações podem ser necessárias para executar cargas de trabalho de MPI. Confira [Usar instâncias compatíveis com RDMA ou habilitadas para GPU em pool do Lote](batch-pool-compute-intensive-sizes.md).


## <a name="limitations"></a>Limitações

* O Lote dá suporte a RDMA apenas para contêineres em execução em pools Linux.


## <a name="authenticate-using-azure-active-directory"></a>Autenticar usando o Azure Active Directory

Se você usar uma imagem de VM personalizada para criar o pool do Lote, o aplicativo cliente deverá fazer a autenticação usando a autenticação integrada do Azure AD (a autenticação de chave compartilhada não funciona). Antes de executar o aplicativo, registre-o no Azure AD para estabelecer uma identidade para ele e especificar suas permissões para outros aplicativos.

Além disso, quando você usar uma imagem de VM personalizada, precisará conceder controle de acesso IAM ao aplicativo para acessar a imagem de VM. No portal do Azure, abra **Todos os recursos**, selecione a imagem de contêiner e a seção **IAM (controle de acesso)** da folha da imagem e clique em **Adicionar**. Na folha **Adicionar permissões**, especifique uma **Função**, em **Atribuir acesso a**, selecione **Usuário, grupo ou aplicativo do Azure AD** e, em  **Selecionar**, insira o nome do aplicativo.

Em seu aplicativo, transmita um token de autenticação do Azure AD quando criar o cliente do Lote usando [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_), conforme descrito em [Autenticar soluções do serviço Lote com o Active Directory](batch-aad-auth.md).


## <a name="reference-a-vm-image-for-pool-creation"></a>Fazer referência a uma imagem de VM para a criação de pool

No código do aplicativo, forneça uma referência para a imagem de VM para ser usada na criação dos nós de computação do pool. Você pode fazer isso criando um objeto [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference). Você pode especificar a imagem a ser usada em uma das seguintes maneiras:

* Se você estiver usando uma imagem personalizada, forneça um identificador de recurso do Azure Resource Manager para a imagem de máquina virtual. O identificador da imagem tem um formato de caminho, conforme mostrado no exemplo abaixo:

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    Para obter essa ID da imagem do portal do Azure, abra **Todos os recursos**, selecione a imagem personalizada e a seção **Visão geral** da folha da imagem e copie o caminho na **ID de recurso**.

* Se você estiver usando uma imagem do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based), forneça um grupo de parâmetros que descrevem a imagem: o tipo de oferta, o publisher, a SKU e a versão da imagem, conforme indicado na [Lista de imagens de máquinas virtuais](batch-linux-nodes.md#list-of-virtual-machine-images):

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

O pool do Lote é a coleção de nós de computação em que o Lote executa tarefas em um trabalho. Quando você cria o pool, fornece-o com a configuração da VM para os nós de computação. O objeto [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) contém uma referência ao objeto [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration). Para habilitar as cargas de trabalho do contêiner no pool, especifique as configurações `ContainerConfiguration`. A configuração da VM também é onde você especifica a referência de imagem e a ID de SKU do agente do nó da imagem, conforme mostrado nos exemplos a seguir.

Há várias opções para a criação de pool. Você pode criar um pool com ou sem imagens de contêiner buscadas previamente. 

O processo de pull (ou pré-busca) permite que você carregue previamente imagens de contêiner do Hub do Docker ou de outro registro de contêiner na Internet. A vantagem da pré-busca de imagens de contêiner é que quando as tarefas começam a ser executadas, elas não precisam esperar que a imagem de contêiner seja baixada. A configuração do contêiner efetua pull de imagens de contêiner para as VMs quando o pool é criado. As tarefas executadas no pool podem fazer referência à lista de imagens de contêiner e a opções de execução do contêiner.



### <a name="pool-without-prefetched-container-images"></a>Pool sem imagens de contêiner de pré-busca

Para configurar o pool sem imagens de contêiner de pré-busca, defina os objetos `ContainerConfiguration` e `VirtualMachineConfiguration` conforme mostrado no exemplo a seguir. Isso e os exemplos a seguir presumem que você está usando uma imagem personalizada do Ubuntu 16.04 LTS com o Mecanismo do Docker instalado.

```csharp
// Specify container configuration
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

Para executar a pré-busca de imagens de contêiner no pool, adicione a lista de imagens de contêiner (`containerImageNames`) à `ContainerConfiguration` e nomeie a lista de imagens. O exemplo a seguir pressupõe que você está usando uma imagem personalizada do Ubuntu 16.04 LTS, fez a pré-busca de uma imagem de TensorFlow do [Hub do Docker](https://hub.docker.com) e iniciou o TensorFlow em uma tarefa inicial.

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Define container settings
TaskContainerSettings startTaskContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu");
StartTask startTaskContainer = new StartTask(
    CommandLine: "<docker-image-command-line>",
    TaskContainerSettings: startTaskContainerSettings);

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

Quando você define as tarefas a serem executadas em nós de computação, deve definir configurações específicas de contêiner, como opções de execução de tarefa, imagens a serem usadas e registro.

Use a propriedade ContainerSettings das classes de tarefa para definir configurações específicas do contêiner. Essas configurações são definidas pela classe [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings).

Se você executar tarefas em imagens de contêiner, a [tarefa nuvem](/dotnet/api/microsoft.azure.batch.cloudtask) e a [tarefa do gerenciador de trabalho](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) exigirão configurações de contêiner. No entanto, [iniciar tarefa](/dotnet/api/microsoft.azure.batch.starttask), [tarefa de preparação de trabalho](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask) e [tarefa de liberação de trabalho](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) não exigem configurações de contêiner (ou seja, podem ser executados em um contexto de contêiner ou diretamente no nó).

Quando você define as configurações do contêiner, todos os diretórios recursivamente abaixo de `AZ_BATCH_NODE_ROOT_DIR` (a raiz dos diretórios do Lote do Azure no nó) são mapeadas para o contêiner, todas as variáveis de ambiente são mapeadas para o contêiner e a linha de comando da tarefa é executada no contêiner.

O exemplo de código em [Imagens para a configuração do contêiner de pré-busca](#prefetch-images-for-container-configuration) mostrou como você pode especificar uma configuração de contêiner para uma tarefa inicial. O exemplo de código abaixo mostra como especificar a configuração do contêiner para uma tarefa de nuvem:

```csharp
// Simple task command

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

* Para uma visão geral detalhada do Lote, confira [Desenvolver soluções de computação paralela em grande escala com o Lote](batch-api-basics.md).

* Para saber mais sobre como instalar e usar o Docker CE no Linux, confira a documentação do [Docker](https://docs.docker.com/engine/installation/).

* Para saber mais sobre como usar imagens personalizadas, confira [Usar uma imagem personalizada gerenciada para criar um pool de máquinas virtuais](batch-custom-images.md).
