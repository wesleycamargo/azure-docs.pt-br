---
title: Migrar da IA do Lote para o Serviço do Azure Machine Learning
description: Saiba como migrar e para o Serviço do Azure Machine Learning para AMLcompute o como seu código mapeia para o código no serviço de Azure Machine Learning.
ms.service: batch-ai
services: batch-ai
ms.topic: overview
ms.author: jmartens
author: j-martens
ms.date: 2/28/2019
ms.openlocfilehash: e495ed06c640601c0500d14b42070a264fd687a9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57862107"
---
# <a name="migrating-from-batch-ai-to-azure-machine-learning-service"></a>Migrando da IA do Lote para o Serviço do Azure Machine Learning

**O serviço de IA do Lote do Azure será desativado em março.** O treinamento em escala e as funcionalidades de pontuação do IA do Lote agora estão disponíveis no [serviço do Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), que foi disponibilizado em 4 de dezembro de 2018.

Junto com muitas outras funcionalidades de aprendizado de máquina, o serviço do Azure Machine Learning inclui um destino de computação gerenciada baseado em nuvem para treinamento, implantação e modelos de aprendizado de máquina de pontuação. Esse destino de computação é chamado [Computação do Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). Comece a migrá-lo e usá-lo hoje. Você pode interagir com o serviço do Azure Machine Learning por meio de seus [SDKs do Python](../machine-learning/service/quickstart-create-workspace-with-python.md), interface de linha de comando e [portal do Azure](../machine-learning/service/quickstart-get-started.md).

A atualização do serviço de versão prévia da IA do Lote para o Serviço do Azure Machine Learning com GA oferece uma melhor experiência por meio de conceitos que são mais fáceis de usar, como Avaliadores e Armazenamentos de Dados. Ela também garante SLAs do serviço do Azure no nível de GA e atendimento ao cliente.

O Serviço do Azure Machine Learning também traz novas funcionalidades, como o aprendizado de máquina automatizado, ajuste de hiperparâmetro e pipelines de ML, que são úteis na maioria das cargas de trabalho de IA em grande escala. A capacidade de operar um modelo treinado sem alternar para um serviço separado ajuda a concluir o loop de ciência de dados da preparação de dados (usando o SDK de Preparação de Dados) à operacionalização e ao monitoramento do modelo.

## <a name="start-migrating"></a>Iniciar a migração
Para evitar interrupções aos seus aplicativos e beneficie-se dos recursos mais recentes, execute as seguintes etapas antes de 31 de março de 2019:

1. Crie um workspace de serviço do Azure Machine Learning e comece:
    + [Início rápido baseado em Python](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Início rápido baseado no portal do Azure](../machine-learning/service/quickstart-get-started.md)

1. Instale o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) e o [SDK de Preparação de Dados](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install). 

1. Configurar uma [Computação do Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) para treinamento de modelo.

1. Atualize seus scripts para usar a Computação do Azure Machine Learning. As seções a seguir mostram como o código comum que você usa para mapas da IA do lote é mapeado para o código para Azure Machine Learning. 


## <a name="create-workspaces"></a>Criar workspaces
O conceito de inicializar um workspace usando um configuration.json na IA do Lote do Azure é mapeado de forma semelhante ao uso de um arquivo de configuração no Serviço do Azure Machine Learning.

Na **IA do Lote**, você fazia desta maneira:

```python
sys.path.append('../../..')
import utilities as utils

cfg = utils.config.Configuration('../../configuration.json')
client = utils.config.create_batchai_client(cfg)

utils.config.create_resource_group(cfg)
_ = client.workspaces.create(cfg.resource_group, cfg.workspace, cfg.location).result()
```

No **Serviço do Azure Machine Learning**, tente fazer isto:

```python
from azureml.core.workspace import Workspace

ws = Workspace.from_config()
print('Workspace name: ' + ws.name, 
      'Azure region: ' + ws.location, 
      'Subscription id: ' + ws.subscription_id, 
      'Resource group: ' + ws.resource_group, sep = '\n')
```

Além disso, você pode criar um workspace diretamente especificando os parâmetros de configuração como

```python
from azureml.core import Workspace
# Create the workspace using the specified parameters
ws = Workspace.create(name = workspace_name,
                      subscription_id = subscription_id,
                      resource_group = resource_group, 
                      location = workspace_region,
                      create_resource_group = True,
                      exist_ok = True)
ws.get_details()

# write the details of the workspace to a configuration file to the notebook library
ws.write_config()
```

Saiba mais sobre a classe Workspace do Azure Machine Learning na [documentação de referência do SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).


## <a name="create-compute-clusters"></a>Criar clusters de cálculo
O Azure Machine Learning dá suporte a vários destinos de computação, alguns dos quais são gerenciados pelo serviço e outros que podem ser anexados ao workspace (por exemplo, Um cluster HDInsight ou uma VM remota). Leia mais sobre os vários [destinos de computação](../machine-learning/service/how-to-set-up-training-targets.md). O conceito de criar um cluster de cálculo da IA do Lote do Azure é mapeado para a criação de um cluster de AmlCompute no Serviço do Azure Machine Learning. A criação de Amlcompute usa uma configuração de computação semelhante ao modo como os parâmetros são passados na IA do Lote do Azure. Um aspecto a ser observado é que o dimensionamento automático está ativado por padrão no cluster AmlCompute, enquanto ele está desativado por padrão na IA do Lote do Azure.

Na **IA do Lote**, você fazia desta maneira:

```python
nodes_count = 2
cluster_name = 'nc6'

parameters = models.ClusterCreateParameters(
    vm_size='STANDARD_NC6',
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=nodes_count)
    ),
    user_account_settings=models.UserAccountSettings(
        admin_user_name=cfg.admin,
        admin_user_password=cfg.admin_password or None,
        admin_user_ssh_public_key=cfg.admin_ssh_key or None,
    )
)
_ = client.clusters.create(cfg.resource_group, cfg.workspace, cluster_name, parameters).result()
```

No **Serviço do Azure Machine Learning**, tente fazer isto:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
gpu_cluster_name = "nc6"

# Verify that cluster does not exist already
try:
    gpu_cluster = ComputeTarget(workspace=ws, name=gpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           vm_priority='lowpriority',
                                                           min_nodes=1,
                                                           max_nodes=2,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    gpu_cluster = ComputeTarget.create(ws, gpu_cluster_name, compute_config)

gpu_cluster.wait_for_completion(show_output=True)
```

Saiba mais sobre a classe AmlCompute na [documentação de referência do SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py). Observe que, na configuração acima, apenas vm_size e max_nodes são obrigatórios e o restante das propriedades, como VNets, destina-se somente à instalação avançada de cluster.

## <a name="monitor-status-of-your-cluster"></a>Monitorar o status do cluster
Isso é mais simples no Serviço do Azure Machine Learning, como você verá abaixo.

Na **IA do Lote**, você fazia desta maneira:

```python
cluster = client.clusters.get(cfg.resource_group, cfg.workspace, cluster_name)
utils.cluster.print_cluster_status(cluster)
```

No **Serviço do Azure Machine Learning**, tente fazer isto:

```python
gpu_cluster.get_status().serialize()
```

## <a name="get-reference-to-a-storage-account"></a>Obtenha uma referência para uma conta de armazenamento
O conceito de um armazenamento de dados, como um blob, é simplificado no Serviço do Azure Machine Learning com o uso do objeto DataStore. Por padrão, o workspace do Serviço do Azure Machine Learning cria uma conta de armazenamento, mas você pode anexar seu próprio armazenamento também como parte da criação do workspace. 

Na **IA do Lote**, você fazia desta maneira:

```python
azure_blob_container_name = 'batchaisample'
blob_service = BlockBlobService(cfg.storage_account_name, cfg.storage_account_key)
blob_service.create_container(azure_blob_container_name, fail_on_exist=False)
```

No **Serviço do Azure Machine Learning**, tente fazer isto:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)
```

Saiba mais sobre como registrar contas de armazenamento adicionais ou como obter uma referência a outro armazenamento de dados registrado na [Documentação de Serviço do Azure Machine Learning](../machine-learning/service/how-to-access-data.md#access).


## <a name="download-and-upload-data"></a>Baixar e carregar dados 
Com um desses serviços, você pode carregar os dados na conta de armazenamento com facilidade usando a referência de armazenamento de dados acima. Para a IA do Lote do Azure, também implantamos o script de treinamento como parte do compartilhamento de arquivo, embora você verá como pode especificá-lo como parte da configuração de trabalho no caso do Serviço do Azure Machine Learning.

Na **IA do Lote**, você fazia desta maneira:

```python
mnist_dataset_directory = 'mnist_dataset'
utils.dataset.download_and_upload_mnist_dataset_to_blob(
    blob_service, azure_blob_container_name, mnist_dataset_directory)

script_directory = 'tensorflow_samples'
script_to_deploy = 'mnist_replica.py'

blob_service.create_blob_from_path(azure_blob_container_name,
                                   script_directory + '/' + script_to_deploy, 
                                   script_to_deploy)
```


No **Serviço do Azure Machine Learning**, tente fazer isto:

```python
import os
import urllib
os.makedirs('./data', exist_ok=True)
download_url = 'https://s3.amazonaws.com/img-datasets/mnist.npz'
urllib.request.urlretrieve(download_url, filename='data/mnist.npz')

ds.upload(src_dir='data', target_path='mnist_dataset', overwrite=True, show_progress=True)

path_on_datastore = ' mnist_dataset/mnist.npz' ds_data = ds.path(path_on_datastore) print(ds_data)
```

## <a name="create-experiments"></a>Criar experimentos
Conforme mencionado acima, o Serviço do Azure Machine Learning tem um conceito de experimento semelhante à IA do Lote do Azure. Cada experimento pode ter execuções individuais, semelhante aos trabalhos que temos na IA do Lote do Azure. O Serviço do Azure Machine Learning também permite que você tenha a hierarquia em cada execução pai, para execuções filho individuais.

Na **IA do Lote**, você fazia desta maneira:

```python
experiment_name = 'tensorflow_experiment'
experiment = client.experiments.create(cfg.resource_group, cfg.workspace, experiment_name).result()
```

No **Serviço do Azure Machine Learning**, tente fazer isto:

```python
from azureml.core import Experiment

experiment_name = 'tensorflow_experiment'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="submit-jobs"></a>Enviar trabalhos
Depois de criar um experimento, você terá algumas maneiras diferentes de enviar uma execução. Neste exemplo, estamos tentando criar um modelo de aprendizado profundo usando o TensorFlow e usaremos um Avaliador do Serviço do Azure Machine Learning para fazer isso. Um [Avaliador](../machine-learning/service/how-to-train-ml-models.md) é apenas uma função de wrapper na configuração de execução subjacente, que facilita o envio de execuções e que atualmente só é compatível com o PyTorch e o TensorFlow. Por meio do conceito de armazenamentos de dados, você também verá como é fácil especificar os caminhos de montagem 

Na **IA do Lote**, você fazia desta maneira:

```python
azure_file_share = 'afs'
azure_blob = 'bfs'
args_fmt = '--job_name={0} --num_gpus=1 --train_steps 10000 --checkpoint_dir=$AZ_BATCHAI_OUTPUT_MODEL --log_dir=$AZ_BATCHAI_OUTPUT_TENSORBOARD --data_dir=$AZ_BATCHAI_INPUT_DATASET --ps_hosts=$AZ_BATCHAI_PS_HOSTS --worker_hosts=$AZ_BATCHAI_WORKER_HOSTS --task_index=$AZ_BATCHAI_TASK_INDEX'

parameters = models.JobCreateParameters(
     cluster=models.ResourceId(id=cluster.id),
     node_count=2,
     input_directories=[
        models.InputDirectory(
            id='SCRIPT',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, script_directory)),
        models.InputDirectory(
            id='DATASET',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, mnist_dataset_directory))],
     std_out_err_path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
     output_directories=[
        models.OutputDirectory(
            id='MODEL',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Models'),
        models.OutputDirectory(
            id='TENSORBOARD',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Logs')
     ],
     mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
                        cfg.storage_account_name, azure_file_share_name),
                    relative_mount_path=azure_file_share)
            ],
            azure_blob_file_systems=[
                models.AzureBlobFileSystemReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    container_name=azure_blob_container_name,
                    relative_mount_path=azure_blob)
            ]
        ),
     container_settings=models.ContainerSettings(
         image_source_registry=models.ImageSourceRegistry(image='tensorflow/tensorflow:1.8.0-gpu')),
     tensor_flow_settings=models.TensorFlowSettings(
         parameter_server_count=1,
         worker_count=nodes_count,
         python_script_file_path='$AZ_BATCHAI_INPUT_SCRIPT/'+ script_to_deploy,
         master_command_line_args=args_fmt.format('worker'),
         worker_command_line_args=args_fmt.format('worker'),
         parameter_server_command_line_args=args_fmt.format('ps'),
     )
)
```

O envio do trabalho propriamente dito na IA do Lote do Azure é feito por meio da função de criação.

```python
job_name = datetime.utcnow().strftime('tf_%m_%d_%Y_%H%M%S')
job = client.jobs.create(cfg.resource_group, cfg.workspace, experiment_name, job_name, parameters).result()
print('Created Job {0} in Experiment {1}'.format(job.name, experiment.name))
```

Encontre as informações completas sobre este snippet de código de treinamento (incluindo o arquivo mnist_replica.py que carregamos no compartilhamento de arquivo acima) no [repositório GitHub de notebooks de exemplo da IA do Lote do Azure](https://github.com/Azure/BatchAI/tree/2238607d5a028a0c5e037168aefca7d7bb165d5c/recipes/TensorFlow/TensorFlow-GPU-Distributed).

No **Serviço do Azure Machine Learning**, tente fazer isto:

```python
from azureml.train.dnn import TensorFlow

script_params={
    '--num_gpus': 1,
    '--train_steps': 500,
    '--input_data': ds_data.as_mount()

}

estimator = TensorFlow(source_directory=project_folder,
                       compute_target=gpu_cluster,
                       script_params=script_params,
                       entry_script='tf_mnist_replica.py',
                       node_count=2,
                       worker_count=2,
                       parameter_server_count=1,   
                       distributed_backend='ps',
                       use_gpu=True)
```

Encontre as informações completas sobre este snippet de código de treinamento (incluindo o arquivo mnist_replica.py) no [repositório GitHub de notebooks de exemplo do Serviço do Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/distributed-tensorflow-with-parameter-server). O armazenamento de dados propriamente dito pode ser montado em nós individuais ou os dados de treinamento podem ser baixados no próprio nó. Encontre mais detalhes sobre como referenciar o armazenamento de dados no avaliador na [documentação do Serviço do Azure Machine Learning](../machine-learning/service/how-to-access-data.md#access). 

O envio de uma execução no Serviço do Azure Machine Learning é feito por meio da função de envio.

```python
run = experiment.submit(estimator)
print(run)
```

Há outra maneira de especificar parâmetros para a execução usando uma configuração de execução – especialmente útil para definir um ambiente de treinamento personalizado. Encontre mais detalhes neste [notebook de exemplo de AmlCompute](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-amlcompute/train-on-amlcompute.ipynb). 

## <a name="monitor-runs"></a>Monitorar execuções
Depois de enviar uma execução, você poderá aguardar até sua conclusão ou monitorá-la no Serviço do Azure Machine Learning usando widgets excelentes do Jupyter que podem ser invocados diretamente no código. Extraia também o contexto de qualquer execução anterior fazendo loop por vários experimentos em um workspace e das execuções individuais em cada experimento.

Na **IA do Lote**, você fazia desta maneira:

```python
utils.job.wait_for_job_completion(client, cfg.resource_group, cfg.workspace, 
                                  experiment_name, job_name, cluster_name, 'stdouterr', 'stdout-wk-0.txt')

files = client.jobs.list_output_files(cfg.resource_group, cfg.workspace, experiment_name, job_name,
                                      models.JobsListOutputFilesOptions(outputdirectoryid='stdouterr')) 
for f in list(files):
    print(f.name, f.download_url or 'directory')
```


No **Serviço do Azure Machine Learning**, tente fazer isto:

```python
run.wait_for_completion(show_output=True)

from azureml.widgets import RunDetails
RunDetails(run).show()
```

Este é um instantâneo de como o widget é carregado no notebook para observação dos logs em tempo real: ![Diagrama de monitoramento do widget](./media/overview-what-happened-batch-ai/monitor.png)



## <a name="edit-clusters"></a>Editar clusters
A exclusão de um cluster é simples. Além disso, o Serviço do Azure Machine Learning também permite que você atualize um cluster no notebook, caso deseje dimensioná-lo para um número maior de nós ou aumentar o tempo de espera ocioso antes de reduzir verticalmente o cluster. Não permitimos que você altere o tamanho da VM do cluster em si, pois isso exige uma nova implantação efetiva no back-end.

Na **IA do Lote**, você fazia desta maneira:
```python
_ = client.clusters.delete(cfg.resource_group, cfg.workspace, cluster_name)
```

No **Serviço do Azure Machine Learning**, tente fazer isto:

```python
gpu_cluster.delete()

gpu_cluster.update(min_nodes=2, max_nodes=4, idle_seconds_before_scaledown=600)
```

## <a name="get-support"></a>Obtenha suporte

O IA do Lote do Azure está programada para ser desativada em 31 de março e já está bloqueando o registro de novas assinaturas no serviço, a menos que ele seja colocado na lista de permissões pela geração de uma exceção por meio do suporte.  Contate-nos na [Versão Prévia do Treinamento da IA do Lote do Azure](mailto:AzureBatchAITrainingPreview@service.microsoft.com) em caso de dúvidas ou comentários durante a migração para o Serviço do Azure Machine Learning.

O Serviço do Azure Machine Learning é um serviço com disponibilidade geral. Isso significa que ele vem com um SLA comprometido e vários planos de suporte para escolha.

Os preços pelo uso da infraestrutura do Azure por meio do serviço da IA do Lote do Azure ou do Serviço do Azure Machine Learning não devem variar, pois somente cobramos o preço pela computação subjacente em ambos os casos. Para obter mais informações, confira a [Calculadora de Preços](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Exiba a disponibilidade regional entre os dois serviços no [portal do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Próximas etapas

+ Leia [Visão geral do serviço do Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Configurar um destino de computação para treinamento de modelo](../machine-learning/service/how-to-set-up-training-targets.md) com o serviço do Azure Machine Learning.

+ Examine o [Roteiro do Azure](https://azure.microsoft.com/updates/) para saber mais sobre outras atualizações de serviço do Azure.
