---
title: Início Rápido do Azure - Treinamento do CNTK com IA do Lote - Python | Microsoft Docs
description: Aprenda rapidamente a executar um trabalho de treinamento do CNTK com IA do Lote usando o SDK do Python
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: da5c1181f9c4d311bdeabe837435ae4e0eb3dc1a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Execute um trabalho de treinamento do CNTK usando o SDK do Python do Azure

Este início rápido fornece detalhes usando o SDK do Python do Azure para executar um trabalho de treinamento do Microsoft Cognitive Toolkit (CNTK) usando o serviço do IA do Lote.

Neste exemplo, você pode usar o banco de dados MNIST de imagens manuscritas para treinar uma rede neural convolucional (CNN) em um cluster GPU de nó único.

## <a name="prerequisites"></a>pré-requisitos

* Assinatura do Azure - caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* SDK do Python do Azure - Confira [instruções de instalação](/python/azure/python-sdk-azure-install)

* Conta de armazenamento do Azure - Confira [Como criar uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md)

* Credenciais da entidade de serviço do Azure Active Directory - Confira [Como criar uma entidade de serviço com a CLI](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

* Registre o provedor de recursos do IA do Lote uma vez para a sua assinatura usando o Azure Cloud Shell ou a CLI do Azure. Um registro do provedor pode levar até 15 minutos.

```azurecli
az provider register -n Microsoft.BatchAI
```

## <a name="configure-credentials"></a>Configurar credenciais
Adicione o seguinte código ao seu arquivo de script e substitua `FILL-IN-HERE` por valores apropriados:

```Python
# credentials used for authentication
aad_client_id = 'FILL-IN-HERE'
aad_secret = 'FILL-IN-HERE'
aad_tenant = 'FILL-IN-HERE'
subscription_id = 'FILL-IN-HERE'

# credentials used for storage
storage_account_name = 'FILL-IN-HERE'
storage_account_key = 'FILL-IN-HERE'

# specify the credentials used to remote login your GPU node
admin_user_name = 'FILL-IN-HERE'
admin_user_password = 'FILL-IN-HERE'
```

Observe que colocar as credenciais no código-fonte não é uma boa prática, e isso é feito aqui para simplificar o início rápido.
Em vez disso, considere o uso de variáveis de ambiente ou de um arquivo de configuração separado.

## <a name="create-batch-ai-client"></a>Criar cliente do IA do Lote

O código a seguir cria um objeto de credenciais de entidade de serviço e cliente de IA do Lote:

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=aad_client_id, secret=aad_secret, tenant=aad_tenant)

batchai_client = batchai.BatchAIManagementClient(
    credentials=creds, subscription_id=subscription_id)
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os trabalhos e os clusters da IA do Lote são recursos do Azure e devem ser colocados em um grupo de recursos do Azure. O trecho a seguir cria um grupo de recursos:

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'
resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)
resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>Preparar o compartilhamento de arquivos do Azure
Para fins de ilustração, este início rápido usa um compartilhamento de arquivos do Azure para hospedar os dados de treinamento e scripts para o trabalho de treinamento.

1. Crie um compartilhamento de arquivos chamado `batchaiquickstart`.

```Python
from azure.storage.file import FileService
azure_file_share_name = 'batchaiquickstart'
service = FileService(storage_account_name, storage_account_key)
service.create_share(azure_file_share_name, fail_on_exist=False)
```

2. Crie um diretório no compartilhamento chamado `mnistcntksample`

```Python
mnist_dataset_directory = 'mnistcntksample'
service.create_directory(azure_file_share_name, mnist_dataset_directory,
                         fail_on_exist=False)
```
3. Faça o download do [pacote de exemplo](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) e descompacte-o no diretório atual. O código a seguir carrega os arquivos necessários no compartilhamento de Arquivos do Azure:

```Python
for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt',
          'ConvNet_MNIST.py']:
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f)
```

## <a name="create-gpu-cluster"></a>Criar o cluster GPU

Crie um cluster do IA do Lote. Neste exemplo, o cluster consiste em um único nó de VM STANDARD_NC6. Esse tamanho de VM tem uma GPU NVIDIA K80. Monte o compartilhamento de arquivos em uma pasta chamada `azurefileshare`. O caminho completo dessa pasta no nó de computação GPU é `$AZ_BATCHAI_MOUNT_ROOT/azurefileshare`.

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare'

parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus',
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6',
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
        admin_user_name=admin_user_name,
        admin_user_password=admin_user_password),
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=1)
    ),
    # Configure each node in the cluster
    node_setup=models.NodeSetup(
        # Mount shared volumes to the host
        mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=storage_account_key),
                    azure_file_url='https://{0}/{1}'.format(
                        service.primary_endpoint, azure_file_share_name),
                    relative_mount_path=relative_mount_point)],
        ),
    ),
)
batchai_client.clusters.create(resource_group_name, cluster_name,
                               parameters).result()
```

## <a name="get-cluster-status"></a>Obter o status do cluster

Monitore o status do cluster usando o comando a seguir:

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; Leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count))
```

O código anterior imprime informações básicas de alocação de cluster, como o exemplo a seguir:

```
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving: 0
```

O cluster está pronto quando os nós são alocados e após concluir a preparação (confira o atributo `nodeStateCounts`). Se algo der errado, o atributo `errors` contém a descrição do erro.

## <a name="create-training-job"></a>Criar um trabalho de treinamento

Após a criação do cluster, configure e envie o trabalho de aprendizado:

```Python
job_name = 'myjob'

parameters = models.job_create_parameters.JobCreateParameters(
    # The job and cluster must be created in the same location
    location=cluster.location,
    # The cluster this job will run on
    cluster=models.ResourceId(id=cluster.id),
    # The number of VMs in the cluster to use
    node_count=1,
    # Write job's standard output and execution log to Azure File Share
    std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(
        relative_mount_point),
    # Configure location of the training script and MNIST dataset
    input_directories=[models.InputDirectory(
        id='SAMPLE',
        path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(
            relative_mount_point, mnist_dataset_directory))],
    # Specify location where generated model will be stored
    output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")],
    # Container configuration
    container_settings=models.ContainerSettings(
        image_source_registry=models.ImageSourceRegistry(
            image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')),
    # Toolkit specific settings
    cntk_settings=models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
)

# Create the job
batchai_client.jobs.create(resource_group_name, job_name, parameters).result()
```

## <a name="monitor-job"></a>Monitorar o trabalho
Você pode inspecionar o estado do trabalho usando o código a seguir:

```Python
job = batchai_client.jobs.get(resource_group_name, job_name)

print('Job state: {0} '.format(job.execution_state.name))
```

A saída é semelhante a: `Job state: running`.

O `executionState` contém o estado atual da execução do trabalho:
* `queued`: o trabalho está aguardando que os nós de cluster fiquem disponíveis
* `running`: o trabalho está em execução
* `succeeded` (ou `failed`): o trabalho está concluído e `executionInfo` contém os detalhes do resultado

## <a name="list-stdout-and-stderr-output"></a>Saída stderr e stdout de lista
Use o código a seguir para listar os arquivos stdout, stderr e de log gerados:

```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="stdouterr"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="list-generated-model-files"></a>Listar os arquivos de modelo gerados
Use o código a seguir para listar os arquivos de modelo gerados:
```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="MODEL"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="delete-resources"></a>Excluir recursos

Use o código a seguir para excluir o trabalho:
```Python
batchai_client.jobs.delete(resource_group_name, job_name)
```

Use o código a seguir para excluir o cluster:
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```

Use o código a seguir para excluir todos os recursos alocados:
```Python
resource_management_client.resource_groups.delete('myresourcegroup')
```
## <a name="next-steps"></a>Próximas etapas

Neste início rápido você aprendeu a executar um trabalho de treinamento do CNTK em um cluster da AI do Lote, usando o SDK do Python do Azure. Para saber mais sobre como usar a IA do Lote com kits de ferramentas diferentes, confira as [receitas treinamento](https://github.com/Azure/BatchAI).
