<properties
	pageTitle="Nós Linux em pools do Lote do Azure | Microsoft Azure"
	description="Saiba como processar suas cargas de trabalho de computação paralelas em pools de máquinas virtuais do Linux no Lote do Azure."
	services="batch"
	documentationCenter="python"
	authors="mmacy"
	manager="timlt"
	editor="" />

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="na"
	ms.date="06/03/2016"
	ms.author="marsma" />

# Provisionar nós de computação Linux em pools do Lote do Azure

O Lote do Azure permite executar cargas de trabalho de computação paralelas em máquinas virtuais do Linux e do Windows. Este artigo fornece detalhes sobre como criar pools de nós de computação do Linux no serviço do Lote usando as bibliotecas de cliente [Python do Lote][py_batch_package] [.NET do Lote][api_net].

> [AZURE.NOTE] No momento, o suporte para Linux no Lote está em preview. Alguns aspectos do recurso discutidos aqui podem ser alterados antes da disponibilidade geral. Os [pacotes de aplicativos](batch-application-packages.md) **no momento não têm suporte** nos nós de computação do Linux.

## Configuração de Máquina Virtual

Ao criar um pool de nós de computação no Lote, você tem duas opções das quais pode selecionar o tamanho do nó e o sistema operacional: **Configuração de Serviços de Nuvem** e **Configuração de Máquina Virtual**.

A **Configuração de Serviços de Nuvem** fornece *apenas* os nós de computação do Windows. Os tamanhos de nó de computação disponíveis estão relacionados em [Tamanhos para Serviços de Nuvem](../cloud-services/cloud-services-sizes-specs.md) e os sistemas operacionais disponíveis estão relacionados na [Matriz de compatibilidade de versões de SOs Convidados e do SDK do Azure](../cloud-services/cloud-services-guestos-update-matrix.md). Quando você cria um pool que contém nós de Serviços de Nuvem, precisa especificar somente o tamanho do nó e sua “Família de Sistemas Operacionais”, que são encontrados nesses artigos. Quando você compra pools de nós de computação do Windows, os Serviços de Nuvem são usados com mais frequência.

A **Configuração de Máquina Virtual** fornece imagens do Linux e do Windows para os nós de computação. Os tamanhos de nó de computação disponíveis estão relacionados em [Tamanhos das máquinas virtuais no Azure](../virtual-machines/virtual-machines-linux-sizes.md) (Linux) e em [Tamanhos das máquinas virtuais no Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Windows). Ao criar um pool que contém nós de Configuração de Máquina Virtual, você deve especificar não apenas o tamanho dos nós, mas também a **referência de imagem de máquina virtual** e o **SKU do agente de nó** do Lote a ser instalado nos nós.

### Referência da imagem da máquina virtual

O serviço do Lote usa [Conjuntos de Escala de Máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) nos bastidores para fornecer nós de computação do Linux, e as imagens do sistema operacional para essas máquinas virtuais são fornecidas pelo [Marketplace de Máquinas Virtuais do Azure][vm_marketplace]. Quando você configura uma referência de imagem de máquina virtual, especifica as propriedades de uma imagem de máquina virtual do Marketplace. As propriedades a seguir são necessárias ao criar uma referência de imagem de máquina virtual:

| **Propriedades de referência de imagem** | **Exemplo** |
| ----------------- | ------------------------ |
| Publicador | Canônico |
| Oferta | UbuntuServer |
| SKU | 14\.04.4-LTS |
| Versão | mais recente |

> [AZURE.TIP] Você pode encontrar mais informações sobre essas propriedades e como relacionar as imagens do Marketplace em [Navegue e selecione imagens da máquina virtual Linux no Azure com o PowerShell ou a CLI](../virtual-machines/virtual-machines-linux-cli-ps-findimage.md). Observe que nem todas as imagens do Marketplace são compatíveis com o Lote no momento. Confira o [SKU do agente do nó](#node-agent-sku) abaixo.

### SKU do agente do nó

O agente do nó do Lote é um programa que é executado em cada nó no pool e fornece a interface de comando e controle entre o nó e o serviço do Lote. Há diferentes implementações do agente do nó, conhecido como SKUs, para diferentes sistemas operacionais. Essencialmente, ao criar uma Configuração de Máquina Virtual, você primeiro especifica a referência de imagem de máquina virtual e depois especifica qual agente do nó instalar na imagem. Normalmente, cada SKU do agente do nó é compatível com várias imagens de máquina virtual. Aqui estão alguns exemplos de SKUs do agente do nó:

* batch.node.ubuntu 14.04
* batch.node.centos 7
* batch.node.windows amd64

> [AZURE.IMPORTANT] Nem todas as imagens de máquina virtual disponíveis no Marketplace são compatíveis no momento com os agentes do nó do Lote disponíveis. Você deve usar os SDKs do Lote para listar os SKUs do agente de nó disponíveis e as imagens de máquina virtual com as quais eles são compatíveis. Confira a [Lista de imagens de máquina virtual](#list-of-virtual-machine-images) abaixo para obter mais informações.

## Criar um pool do Linux: Python do Lote

O trecho de código a seguir mostra a criação de um pool de nós de computação do Ubuntu Server usando a [Biblioteca do cliente do Lote do Microsoft Azure para Python][py_batch_package]. A documentação de referência para o módulo do Python do Lote pode ser encontrada[azure.batch package ][py_batch_docs] em Read the Docs.

Neste trecho de código, criamos um [ImageReference][py_imagereference] explicitamente, especificando cada uma de suas propriedades (editor, oferta, sku, versão). É recomendável, porém, que você use o método [list\_node\_agent\_skus][py_list_skus] no código de produção para determinar e selecionar dentre as combinações de SKU do agente do nó e imagem disponíveis no tempo de execução.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_A1"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, base_url = batch_url)
client = batch.BatchServiceClient(config)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id = pool_id, vm_size = vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes.
ir = batchmodels.ImageReference(
    publisher = "Canonical",
    offer = "UbuntuServer",
    sku = "14.04.2-LTS",
    version = "latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = "batch.node.ubuntu 14.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Conforme mencionado acima, é recomendável que, em vez de criar o [ImageReference][py_imagereference] explicitamente, você use o método [list\_node\_agent\_skus][py_list_skus] para selecionar dinamicamente entre as combinações de imagem do agente do nó/Marketplace com suporte no momento. O trecho de código Python a seguir mostra o uso desse método.

```python
# Get the list of node agents from the Batch service
nodeagents = client.account.list_node_agent_skus()

# Obtain the desired node agent
ubuntu1404agent = next(agent for agent in nodeagents if "ubuntu 14.04" in agent.id)

# Pick the first image reference from the list of verified references
ir = ubuntu1404agent.verified_image_references[0]

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference = ir,
    node_agent_sku_id = ubuntu1404agent.id)
```

## Criar um pool do Linux: .NET do Lote

O trecho de código a seguir mostra a criação de um pool de nós de computação do Ubuntu Server usando a biblioteca de cliente do [.NET do Lote][nuget_batch_net]. Você pode encontrar a [documentação de referência do .NET do Lote][api_net] no MSDN.

O trecho de código a seguir usa o método [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] para fazer a seleção na lista de combinações de SKU do agente do nó e imagem do Marketplace com suporte no momento. Essa técnica é desejável porque a lista de combinações com suporte pode ser alterada periodicamente (mais comumente, combinações com suporte são adicionadas).

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_A1";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image
// that we wish to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.SkuId.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. Note that there are one or more image
// references associated with this node agent SKU.
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: ubuntuAgentSku.Id);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicated: nodeCount);

// Commit the pool to the Batch service
pool.Commit();
```

Embora o trecho de código acima use o método [PoolOperations][net_pool_ops].[ListNodeAgentSkus][net_list_skus] para relacionar e selecionar dinamicamente dentre as combinações de SKU do agente do nó e imagem com suporte (recomendado), você também pode configurar um [ImageReference][net_imagereference] explicitamente:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    skuId: "14.04.2-LTS",
    version: "latest");
```

## Lista de imagens de máquina virtual

A tabela abaixo relaciona quais imagens de máquina virtual do Marketplace são compatíveis com os agentes do nó do Lote disponíveis **no momento que este artigo foi escrito**. É importante observar que essa lista não é definitiva, uma vez que imagens e agentes do nó podem ser adicionados ou removidos a qualquer momento. É recomendável que os aplicativos e serviços do Lote sempre usem [list\_node\_agent\_skus][py_list_skus] \(Python) e [ListNodeAgentSkus][net_list_skus] \(.NET do Lote) para determinar e selecionar entre os SKUs disponíveis no momento.

> [AZURE.WARNING] A lista a seguir pode ser alterada a qualquer momento. Sempre use os métodos do **SKU do agente do nó da lista** disponíveis nas APIs do Lote para relacionar e, então, selecionar dentre os SKUs do agente do nó e a máquina virtual compatíveis ao executar seus trabalhos do Lote.

| **Publicador** | **Oferta** | **Imagem do SKU** | **Versão** | **ID do SKU do Agente do Nó** |
| ------- | ------- | ------- | ------- | ------- |
| Canônico | UbuntuServer | 14\.04.0-LTS | mais recente | batch.node.ubuntu 14.04 |
| Canônico | UbuntuServer | 14\.04.1-LTS | mais recente | batch.node.ubuntu 14.04 |
| Canônico | UbuntuServer | 14\.04.2-LTS | mais recente | batch.node.ubuntu 14.04 |
| Canônico | UbuntuServer | 14\.04.3-LTS | mais recente | batch.node.ubuntu 14.04 |
| Canônico | UbuntuServer | 14\.04.4-LTS | mais recente | batch.node.ubuntu 14.04 |
| Canônico | UbuntuServer | 15\.10 | mais recente | batch.node.debian 8 |
| Canônico | UbuntuServer | 16\.04.0-LTS | mais recente | batch.node.ubuntu 16.04 |
| Credativ | Debian | 8 | mais recente | batch.node.debian 8 |
| OpenLogic | CentOS | 7\.0 | mais recente | batch.node.centos 7 |
| OpenLogic | CentOS | 7\.1 | mais recente | batch.node.centos 7 |
| OpenLogic | CentOS | 7,2 | mais recente | batch.node.centos 7 |
| OpenLogic | CentOS-HPC | 7\.1 | mais recente | batch.node.centos 7 |
| Oracle | Oracle-Linux | 7\.0 | mais recente | batch.node.centos 7 |
| SUSE | SLES | 12 | mais recente | batch.node.opensuse 42.1 |
| SUSE | SLES | 12-SP1 | mais recente | batch.node.opensuse 42.1 |
| SUSE | SLES-HPC | 12 | mais recente | batch.node.opensuse 42.1 |
| SUSE | openSUSE | 13\.2 | mais recente | batch.node.opensuse 13.2 |
| SUSE | openSUSE-Leap | 42\.1 | mais recente | batch.node.opensuse 42.1 |
| MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 | mais recente | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-Datacenter | mais recente | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter | mais recente | batch.node.windows amd64 |
| MicrosoftWindowsServer | WindowsServer | Windows-Server-Technical-Preview | mais recente | batch.node.windows amd64 |

## Conectar-se a nós Linux

Durante o desenvolvimento ou durante a solução de problemas, talvez seja necessário fazer logon nos nós em seu pool. Ao contrário dos nós de computação do Windows, não é possível usar o protocolo RDP para se conectar aos nós Linux. Em vez disso, o serviço do Lote habilita acesso do SSH em cada nó para a conexão remota.

O trecho de código Python a seguir cria um usuário em cada nó em um pool, necessário para a conexão remota, em seguida, imprime as informações de conexão SSH para cada nó.

```python
import getpass

# Specify the username and prompt for a password
username = "linuxuser"
password = getpass.getpass()

# Create the user that will be added to each node
# in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = client.compute_node.get_remote_login_settings(pool_id,
                                                          node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Aqui está a saída de exemplo para o código acima para um pool que contém quatro nós Linux:

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

Observe que, em vez de uma senha, você pode especificar uma chave pública SSH ao criar um usuário em um nó. No SDK do Python, isso é feito usando o parâmetro **ssh\_public\_key** em [ComputeNodeUser][py_computenodeuser] e, no .NET, isso é feito com a propriedade [ComputeNodeUser][net_computenodeuser].[SshPublicKey][net_ssh_key].

## Preços

O Lote do Azure baseia-se na tecnologia de Serviços de Nuvem do Azure e Máquinas Virtuais do Azure. O serviço de Lote em si é oferecido sem custos, o que significa que você é cobrado apenas pelos recursos de computação consumidos pelas soluções do Lote. Ao escolher a **Configuração de Serviços de Nuvem**, você será cobrado com base na estrutura de [Preços dos Serviços de Nuvem][cloud_services_pricing]. Ao escolher a **Configuração da Máquina Virtual**, você será cobrado com base na estrutura de [Preços das Máquinas Virtuais][vm_pricing].

## Próximas etapas

### Tutorial do Python do Lote

Para obter um tutorial mais detalhado sobre como trabalhar com o Lote usando o Python, confira [Get started with the Azure Batch Python client](batch-python-tutorial.md) (Introdução ao cliente Python do Lote do Azure). Seu [exemplo de código][github_samples_pyclient] complementar inclui uma função auxiliar, `get_vm_config_for_distro`, que mostra outra técnica para obter uma configuração de máquina virtual.

### Exemplos de código do Python do Lote

Confira outros [exemplos de código do Python][github_samples_py] no repositório [azure-batch-samples][github_samples] no GitHub para vários scripts que mostram como executar as operações comuns do Lote como criação de pool, trabalho e tarefa e muito mais. O [LEIAME][github_py_readme] que acompanha os exemplos do Python apresenta detalhes sobre como instalar os pacotes necessários.

### Fórum do Lote

O [Fórum do Lote do Azure][forum] no MSDN é um ótimo lugar para discutir sobre o Lote e fazer perguntas sobre o serviço. Vá diretamente ler as postagens “fixadas” úteis e poste suas dúvidas conforme elas surgirem enquanto você cria suas soluções do Lote.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[cloud_services_pricing]: https://azure.microsoft.com/pricing/details/cloud-services/
[forum]: https://social.msdn.microsoft.com/forums/azure/pt-BR/home?forum=azurebatch
[github_py_readme]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_py]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[github_samples_pyclient]: https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/article_samples/python_tutorial_client.py
[portal]: https://portal.azure.com
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_computenodeuser]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.aspx
[net_imagereference]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.imagereference.aspx
[net_list_skus]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listnodeagentskus.aspx
[net_pool_ops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.aspx
[net_ssh_key]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenodeuser.sshpublickey.aspx
[nuget_batch_net]: https://www.nuget.org/packages/Azure.Batch/
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_list_skus]: http://azure-sdk-for-python.readthedocs.org/en/dev/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
[vm_pricing]: https://azure.microsoft.com/pricing/details/virtual-machines/

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagrama de alto nível de pacotes de aplicativos"

<!---HONumber=AcomDC_0608_2016-->