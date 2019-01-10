---
title: Destinos de computação para treinamento de modelo
titleSuffix: Azure Machine Learning service
description: Configure os ambientes de treinamento (destinos de computação) para treinamento de modelo de machine learning. É possível alternar facilmente os ambientes de treinamento. Inicie o treinamento localmente. Se precisar expandir, passe para um destino de computação em nuvem.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 9037f6d7602f186bc30e55acbc050280bca134ee
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794457"
---
# <a name="set-up-compute-targets-for-model-training"></a>Configurar destinos de computação para treinamento de modelo

Com o serviço do Azure Machine Learning, é possível treinar o modelo em diferentes recursos de computação. Esses recursos de computação, chamados de __destinos de computação__, podem ser locais ou na nuvem. Neste artigo, você aprende sobre os alvos de computação suportados e como usá-los.

Um destino de computação é um recurso em que o script de treinamento é executado, ou o modelo é hospedado, quando implantado como um serviço Web. É possível criar e gerenciar um destino de computação usando o SDK do Azure Machine Learning, o portal do Azure ou a CLI do Azure. Se você tiver destinos de computação criados por meio de outro serviço (por exemplo, um cluster Azure HDInsight), poderá usá-los anexando-os ao workspace de Serviço do Azure Machine Learning.

Há três categorias de destinos de computação principais que o Azure Machine Learning dá suporte:

* __Local__: Seu computador local, ou uma máquina virtual (VM) baseada em nuvem que você usa como um ambiente de experimentação e desenvolvimento. 
* __Computação gerenciada__: a Computação do Machine Learning do Azure é uma oferta de computação gerenciada pelo Serviço do Azure Machine Learning. Ela permite criar facilmente uma computação de nó único, ou de vários nós, para treinamento, teste e inferência em lotes.
* __Computação anexada__: Você também pode trazer sua própria computação em nuvem do Azure e anexá-la ao Azure Machine Learning. Leia mais sobre suportes para tipos de computação e como usá-los nas seções a seguir.


## <a name="supported-compute-targets"></a>Destinos de computação com suporte

O serviço do Azure Machine Learning tem suporte variado nos diversos destinos de computação. Um ciclo de vida de desenvolvimento de modelo típico começa com experimentação e desenvolvimento em uma pequena quantidade de dados. Neste estágio, é recomendável que você use um ambiente local como seu computador local ou uma máquina virtual com base em nuvem. Na medida em que você aumenta o treinamento em conjuntos de dados maiores ou faz treinamento distribuído, use o ambiente de computação do Azure Machine Learning para criar um cluster de vários nós, ou de nó único, que dimensionará automaticamente sempre que você enviar uma execução. Também é possível anexar seu próprio recurso de computação, embora o suporte para vários cenários possa variar conforme detalhado na tabela abaixo:

|Destino de computação| Aceleração de GPU | Automatizado<br/> gráfico de ajuste de hiperparâmetro | Automatizado</br> aprendizado de máquina | Pipeline amigável|
|----|:----:|:----:|:----:|:----:|
|[Computador local](#local)| Talvez | &nbsp; | ✓ | &nbsp; |
|[Computação do Azure Machine Learning](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[VM remota](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ _O Azure Databricks e Azure Data Lake Analytics somente podem ser usados em um pipeline._<br/>
> Para obter mais informações sobre pipelines, consulte [Pipelines in Azure Machine Learning](concept-ml-pipelines.md).
>
> O ambiente de computação do Azure Machine Learning deve ser criado em um workspace. Não é possível anexar instâncias existentes a um workspace.
>
> Outros destinos de computação devem ser criados fora do Azure Machine Learning e, em seguida, anexados ao seu workspace.
>
> Alguns destinos de computação dependem de imagens de contêineres do Docker durante o treinamento de um modelo. A imagem base da GPU deve ser usada apenas nos Serviços do Microsoft Azure. Para treinamento de modelo, esses serviços são:
> * Computação do Azure Machine Learning
> * Serviço de Kubernetes do Azure
> * Máquina Virtual de Ciência de Dados (DSVM)

## <a name="workflow"></a>Fluxo de trabalho

O fluxo de trabalho para desenvolver e implantar um modelo com o Azure Machine Learning segue estas etapas:

1. Desenvolver os scripts de treinamento de aprendizado de máquina em Python.
1. Criar e configurar ou anexar um destino de computação existente.
1. Envie os scripts de treinamento para o destino de computação.
1. Inspecione os resultados para localizar o melhor modelo.
1. Registre o modelo no registro do modelo.
1. Implantar o modelo.

> [!NOTE]
> O script de treinamento não está vinculado a um destino de computação específico. Você pode treinar inicialmente em seu computador local e alterne os destinos de computação sem reescrever o script de treinamento.
> 
> Sempre que você associar um destino de computação ao seu workspace, criando uma computação gerenciada ou anexando uma computação existente, será necessário fornecer um nome ao computador. O nome deve ter entre 16 e 63 caracteres.

Para alternar a computação de um destino para outro, é necessário [executar a configuração](concept-azure-machine-learning-architecture.md#run-configuration). A configuração de execução define como executar o script no destino de computação.

## <a name="training-scripts"></a>Scripts de treinamento

Ao iniciar uma execução de treinamento, um instantâneo do diretório contendo os scripts de treinamento é criado e enviado ao destino de computação. Para obter mais informações, veja [cópias de sombra](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Computador local

Durante o treinamento local, você usa o SDK para enviar a operação de treinamento. Você pode fazer um treinamento usando um ambiente gerenciado pelo usuário ou gerenciado pelo sistema.

### <a name="user-managed-environment"></a>Ambiente gerenciado por usuário

Em um ambiente gerenciado pelo usuário, verifique se todos os pacotes necessários estão disponíveis no ambiente de Python em que executar o script. O trecho de código a seguir é um exemplo de como configurar o treinamento para um ambiente gerenciado pelo usuário:

```python
from azureml.core.runconfig import RunConfiguration

# Edit a run configuration property on the fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# Choose a specific Python environment by pointing to a Python path. For example:
# run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>Ambiente gerenciado pelo sistema

Ambientes gerenciados pelo sistema dependem de conda para gerenciar as dependências. Conda cria um arquivo chamado **conda_dependencies.yml** que contém uma lista de dependências. Em seguida, você pode pedir o sistema para criar um novo ambiente do conda e executar os scripts contidos nela. Ambientes gerenciados pelo sistema podem ser reutilizados mais tarde, contanto que o arquivo conda_dependencies.yml permaneçam inalterados. 

A configuração inicial do backup de um novo ambiente pode levar vários minutos para ser concluída, dependendo do tamanho das dependências necessárias. O trecho de código a seguir demonstra como criar um ambiente gerenciado pelo sistema que depende do scikit-learn:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify the conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Computação do Azure Machine Learning

A Computação do Machine Learning do Azure é uma infraestrutura de computação gerenciada que permite ao usuário criar facilmente computação de único nó a vários nós. Ela é criada na sua região do workspace e é um recurso que pode ser compartilhado com outros usuários no workspace. A computação escala verticalmente e automaticamente quando um trabalho é enviado e pode ser colocada em uma rede virtual do Azure. Ela executa em um ambiente em contêineres, empacotando as dependências do modelo em um contêiner do Docker.

É possível usar a Computação do Azure Machine Learning para distribuir o processo de treinamento em um cluster de nós de computação de CPU ou GPU na nuvem. Para obter mais informações sobre os tamanhos de máquina virtual que incluem GPUs, consulte os [Tamanhos de máquinas virtuais com GPU otimizadas](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

> [!NOTE]
> A Computação do Machine Learning do Azure tem limites padrão como o número de núcleos que podem ser alocados. Para obter mais informações, consulte [Gerenciar e solicitar cotas para recursos do Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

É possível criar o ambiente de computação do Azure Machine Learning sob demanda ao programar uma execução, ou como um recurso persistente.

### <a name="run-based-creation"></a>Criação baseada em execução

É possível criar o ambiente de computação do Azure Machine Learning como um destino de computação em tempo de execução. A computação é criada automaticamente para a execução e é dimensionada para o número de **max_nodes** especificados na configuração de execução. A computação é excluída automaticamente após a sua conclusão.

> [!IMPORTANT]
> A criação baseada em execução do ambiente de Computação do Machine Learning do Azure atualmente está em versão prévia. Não use a criação baseada em execução se você estiver usando o ajuste de hiperparâmetro ou o aprendizado de máquina automatizado. Se for necessário usar o aprendizado de máquina automatizado, crie o ambiente de computação do Azure Machine Learning antes de enviar uma execução.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

# First, list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# Create a new runconfig object
run_config = RunConfiguration()

# Signal that you want to use AmlCompute to execute the script
run_config.target = "amlcompute"

# AmlCompute is created in the same region as your workspace
# Set the VM size for AmlCompute from the list of supported_vmsizes
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Computação persistente: Basic

Um ambiente de computação do Azure Machine Learning pode ser reutilizado em vários trabalhos. Ele pode ser compartilhado com outros usuários no workspace e é mantido entre trabalhos.

Para criar um recurso persistente do ambiente de computação do Azure Machine Learning, especifique os parâmetros **vm_size** e **max_nodes**. O Azure Machine Learning usará padrões inteligentes para o restante das propriedades. A computação é definida para dimensionamento automático até zero nós quando não utilizada e para criar máquinas virtuais dedicadas para executar os trabalhos conforme necessário. 

* **vm_size**: A família de VMs dos nós criados pela Computação do Machine Learning do Azure.
* **max_nodes**: O número máximo de nós a serem dimensionados automaticamente durante a execução de um trabalho na Computação do Azure Machine Learning.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Computação persistente: Avançado

Também é possível configurar várias propriedades avançadas ao criar o ambiente de computação do Azure Machine Learning. Essas propriedades permitem que criar um cluster persistente de tamanho fixo ou dentro de uma rede virtual do Azure existente na assinatura.

Juntamente com as propriedades **vm_size** e **max_nodes**, também se pode usar as propriedades a seguir:

* **min_nodes**: O número mínimo de nós a serem dimensionados automaticamente durante a execução de um trabalho no ambiete de computação do Azure Machine Learning. O padrão mínimo é zero (0) nós.
* **vm_priority**: O tipo de máquina virtual a ser usado ao criar um recurso de ambiente de computação do Azure Machine Learning. Escolha entre **dedicada** (padrão) e **lowpriority**. As máquinas virtuais de baixa prioridade usam a capacidade excedente no Azure. Essas máquinas virtuais são mais baratas, mas as execuções podem ser evitadas quando essas máquinas virtuais são usadas.
* **idle_seconds_before_scaledown**: A quantidade de tempo ocioso a aguardar após uma execução é concluída e antes do dimensionamento automático até o número **min_nodes**. O tempo padrão é 120 segundos.
* **vnet_resourcegroup_name**: O grupo de recursos da rede virtual __existente__. O ambiente de computação do Azure Machine Learning é criado dentro dessa rede virtual.
* **vnet_name**: O nome da rede virtual. A rede virtual deve estar na mesma região do seu workspace do Azure Machine Learning.
* **subnet_name**: O nome da sub-rede dentro da rede virtual. Os recursos do ambiente de computação do Azure Machine Learning receberão endereços IP desse intervalo de sub-rede.

> [!TIP]
> Ao criar um recurso persistente de ambiente de computação do Azure Machine Learning, você pode atualizar as propriedades como o número de **min_nodes** ou **max_nodes**. Para atualizar uma propriedade, chame a função `update()` para a propriedade.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist 
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           vm_priority='lowpriority',
                                                           min_nodes=2,
                                                           max_nodes=4,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```


## <a id="vm"></a>VM remota

O Azure Machine Learning também dá suporte para que você traga seu próprio recurso de computação e anexe-o ao workspace. Um desses tipos de recursos é uma VM remota arbitrária, desde que seja acessível pelo Serviço do Azure Machine Learning. O recurso pode ser uma VM do Azure ou um servidor remoto na organização ou no local. Especificamente, fornecido o endereço IP e as credenciais (nome de usuário e senha ou chave SSH), é possível usar qualquer VM acessível para execuções remotas.
Você pode usar um ambiente do Conda interno do sistema, um ambiente do Python já existente ou um contêiner do Docker. Quando executar usando um contêiner do Docker, você precisa ter o mecanismo do Docker em execução na VM. Essa funcionalidade é especialmente útil quando você quer um ambiente de experimentação/desenvolvimento baseado em nuvem mais flexível que seu computador local.

> [!TIP]
> Use a DSVM como a VM do Azure de escolha para esse cenário. Essa VM é um ambiente de desenvolvimento de IA e ciência de dados pré-configurado no Azure que oferece uma escolha organizada de ferramentas e estruturas para o ciclo de vida completo do desenvolvimento do aprendizado de máquina. Para saber mais sobre como usar a DSVM com o Azure Machine Learning, consulte [Configurar um ambiente de desenvolvimentopara o Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

> [!WARNING]
> O Azure Machine Learning dá suporte apenas a máquinas virtuais que executam o Ubuntu. Para criar uma VM ou escolher uma VM existente, selecione uma VM que usa o Ubuntu.

As etapas a seguir usam o SDK para configurar uma Máquina Virtual de Ciência de Dados (DSVM) como um destino de treinamento:

1. Para anexar uma máquina virtual existente como um destino de computação, você precisa fornecer o nome de domínio totalmente qualificado (FQDN), o nome de logon e a senha da máquina virtual. No exemplo, substitua \<fqdn> com o FQDN público do VM ou o endereço IP público. Substitua \<nome de usuário> e \<senha > por um novo nome de usuário do SSH e senha.

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create the compute config
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")

    # If you use SSH instead of a password, use this code:
    #                                                  ssh_port=22,
    #                                                  username='<username>',
    #                                                  password=None,
    #                                                  private_key_file="path-to-file",
    #                                                  private_key_passphrase="passphrase")

    # Attach the compute target
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. Crie uma configuração para o destino de computação da DSVM. O Docker e o conda são usados para criar e configurar o ambiente de treinamento na DSVM.

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load into memory the cpu-dsvm.runconfig file created in the previous attach operation
    run_config = RunConfiguration(framework = "python")

    # Set the compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use the CPU base image
    # To use GPU in DSVM, you must also use the GPU base Docker image "azureml.core.runconfig.DEFAULT_GPU_IMAGE"
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask the system to provision a new conda environment based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when they're used for the first time
    run_config.prepare_environment = True

    # Specify the CondaDependencies object
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Banco de Dados do Azure

As Azure Databricks são um ambiente baseado em Apache Spark na nuvem do Azure. Ele pode ser usado como um destino de computação ao fazer o treinamento de modelos com um pipeline do Azure Machine Learning.

> [!IMPORTANT]
> Um destino de computação do Azure Databricks só pode ser usado em um pipeline de Aprendizado de Máquina.
>
> Você deve criar um workspace do Azure Databricks antes de usá-lo para fazer o treinamento do seu modelo. Para criar esses recursos, consulte [Executar um trabalho do Spark no Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Para anexar as Bills de Dados do Azure como um destino de computação, você deve usar o SDK de Aprendizado de Máquina do Azure e fornecer as seguintes informações:

* __Nome de computação__: O nome a ser atribuído a esse recurso de computação.
* __Nome do Workspace do Databricks__: O nome do workspace do Azure Databricks.
* __Token de acesso__: O token de acesso usado para autenticar no Azure Databricks. Para gerar um token de acesso, consulte [Autenticação](https://docs.azuredatabricks.net/api/latest/authentication.html).

O código a seguir demonstra como anexar o Azure Databricks como um destino de computação:

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create the attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Azure Data Lake Analytics

O Azure Data Lake Analytics é uma plataforma de análise de big data na nuvem do Azure. A plataforma pode ser usada como um destino de computação ao fazer o treinamento de modelos com um pipeline do Azure Machine Learning.

> [!IMPORTANT]
> Um destino de computação do Azure Data Lake Analytics só pode ser usado em um pipeline de Aprendizado de Máquina.
>
> Você deve criar uma conta do Azure Data Lake Analytics antes de usá-la para fazer o treinamento do seu modelo. Para criar este recurso, consulte [Introdução ao Azure Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Para anexar o Data Lake Analytics como um destino de computação, você deve usar o SDK do Aprendizado de Máquina do Azure e fornecer as seguintes informações:

* __Nome de computação__: O nome a ser atribuído a esse recurso de computação.
* __Grupo de recursos__: O grupo de recursos que contém a conta do Data Lake Analytics.
* __Nome da conta__: O nome de conta do Data Lake Analytics.

O código a seguir demonstra como anexar o Data Lake Analytics como um destino de computação:

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    
    # Create the attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach the ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Os pipelines do Azure Machine Learning só podem funcionar com dados armazenados no armazenamento de dados padrão da conta do Data Lake Analytics. Se os dados necessários estão em um armazenamento não padrão, pode-se usar uma operação [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py)para copiar os dados antes de fazer o treinamento do modelo.

## <a id="hdinsight"></a>Azure HDInsight 

O Azure HDInsight é uma plataforma popular para análise de dados. A plataforma fornece o Apache Spark, que pode ser usado para o treinamento do seu modelo.

> [!IMPORTANT]
> Você deve criar o cluster HDInsight antes de usá-lo para o treinamento do seu modelo. Para criar um Spark no cluster HDInsight, consulte [Criar um Cluster Spark no HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> Ao criar o cluster, especifique um nome de usuário SSH e senha. Observe esses valores, conforme necessário, ao usar o HDInsight como um destino de computação.
>
> Depois que o cluster é criado, ele tem o FQDN \<clustername >. azurehdinsight, onde \<clustername > é o nome atribuído ao cluster. O endereço FQDN (ou o endereço IP público do cluster) é necessário para usar o cluster como um destino de computação.

Para configurar um HDInsight como um destino de computação, você deve fornecer o FQDN, o nome de logon eAzure HDInsight a senha para o cluster HDinsight. O exemplo a seguir usa o SDK para anexar um cluster ao seu workspace. No exemplo, substitua \<fqdn> com o FQDN público do cluster ou o endereço IP público. Substitua \<nome de usuário> e \<senha > por um novo nome de usuário do SSH e senha do cluster.

> [!NOTE]
> Para localizar o FQDN para o cluster, visite o portal do Azure e selecione seu cluster HDInsight. Sob __visão geral__, visualize o FQDN na __URL__ de entrada. Para obter o FQDN, remova o https:\// prefixo desde o início da entrada.

![Obtenha o FQDN para um cluster HDInsight no portal do Azure](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attach an HDInsight cluster as a compute target
    attach_config = HDInsightCompute.attach_configuration(address = "fqdn-or-ipaddress",
                                                          ssh_port = 22,
                                                          username = "username",
                                                          password = None, #if using ssh key
                                                          private_key_file = "path-to-key-file",
                                                          private_key_phrase = "key-phrase")
    compute = ComputeTarget.attach(ws, "myhdi", attach_config)
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure the HDInsight run
# Load the runconfig object from the myhdi.runconfig file generated in the previous attach operation
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# Ask the system to prepare the conda environment automatically when it's used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-runs"></a>Enviar execução de treinamento

Existem duas maneiras de enviar uma execução de treinamento:

* Enviar uma execução de treinamento usando um objeto `ScriptRunConfig`.
* Enviar uma execução de treinamento usando um objeto `Pipeline`.

> [!IMPORTANT]
> The Azure Databricks and Azure Datalake Analytics compute targets can only be used in a pipeline.
>
> O destino de computação local não pode ser usado em um pipeline.

### <a name="scriptrunconfig-object"></a>objeto ScriptRunConfig

O padrão de código para enviar uma execução de treinamento com o objeto `ScriptRunConfig` é o mesmo para todos os tipos de destinos de computação:

1. Criar um objeto `ScriptRunConfig` usando a configuração de execução para o destino de computação do objeto.
1. Enviar a execução.
1. Aguarde a conclusão da execução.

O exemplo a seguir usa a configuração para o destino de computação local gerenciado pelo sistema criado anteriormente:

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="pipeline-object"></a>Objeto de pipeline

O padrão de código para enviar uma execução de treinamento com um objeto `Pipeline` é o mesmo para todos os tipos de destinos de computação:

1. Adicione um objeto `Pipeline` ao pipeline para o recurso de computação.
1. Envie uma execução usando o pipeline.
1. Aguarde a conclusão da execução.

O exemplo a seguir usa o destino de computação do Azure Databricks criado anteriormente:

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)

# List of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Para obter mais informações sobre pipelines de aprendizado de máquina, consulte [Pipelines e Azure Machine Learning](concept-ml-pipelines.md).

Por exemplo, para os Jupyter Notebooks que demonstram como é feito o treinamento de um modelo para usar um pipeline, veja [https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="access-computes-in-the-azure-portal"></a>Acessar a computação no portal do Azure

Você pode acessar quais destinos de computação são associados com seu workspace no portal do Azure. 

### <a name="view-compute-targets"></a>Exibir destinos de computação

Para ver os destinos de computação do seu workspace, use as seguintes etapas:

1. Visite o [portal do Azure](https://portal.azure.com) e navegue até o seu workspace.
1. Em __Aplicativos__, selecione __Computação__.

    ![Exibir destinos de computação](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Criar um destino de computação

Siga as etapas anteriores para exibir a lista de destinos de computação. Em seguida, use estas etapas para criar um destino de computação:

1. Clique no sinal Plus (+) para adicionar um destino de computação.

    ![Adicionar um destino de computação](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Insira um nome para o destino de computação.
1. Selecione **Computação do Machine Learning** como o tipo da computação a ser usada para __Treinamento__.

    > [!IMPORTANT]
    > Somente é possível criar um ambiente de computação do Azure Machine Learning como recurso de computação gerenciada para treinamento.

1. Preencha o formulário. Forneça valores para as propriedades necessárias, especialmente **Família de VMs**e o **máximo de nós** utilizado para acelerar a computação. 
1. Selecione __Criar__.
1. Exiba o status da operação de criação, selecionando o destino de computação na lista:

    ![Selecione um destino de computação para exibir o status da operação criar](./media/how-to-set-up-training-targets/View_list.png)

1. Em seguida, visualize os detalhes do destino de computação:

    ![Exibir os detalhes do destino de computação](./media/how-to-set-up-training-targets/compute-target-details.png)

Agora você pode enviar uma execução sobre os destinos de computação, conforme descrito anteriormente.


### <a name="reuse-existing-compute-targets"></a>Reutilizar os destinos de computação existentes

Siga as etapas anteriores para exibir a lista de destinos de computação. Em seguida, use estas etapas para criar um destino de computação:

1. Clique no sinal Plus (+) para adicionar um destino de computação.
1. Insira um nome para o destino de computação.
1. Selecione o tipo de computação a ser anexado para __Treinamento__:

    > [!IMPORTANT]
    > Nem todos os tipos de computação podem ser anexados usando o portal do Azure.
    > Os tipos de computação que podem ser anexados para treinamento são:
    >
    > * VM remota
    > * Azure Databricks
    > * Análise Azure Data Lake
    > * Azure HDInsight

1. Preencha o formulário e forneça valores para as propriedades necessárias.

    > [!NOTE]
    > A Microsoft recomenda o uso de chaves SSH, por serem mais seguras do que as senhas. As senhas são vulneráveis a ataques de força bruta. As chaves SSH contam com as assinaturas de criptografia. Para obter informações sobre como criar chaves SSH para uso com Máquinas Virtuais do Azure, consulte os seguintes documentos:
    >
    > * [Criar e usar chaves SSH no Linux ou macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Criar e usar chaves SSH no Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Selecionar __Anexar__.
1. Exiba o status da operação de anexação, selecionando o destino de computação na lista.

Agora você pode enviar uma execução sobre esses destinos de computação, conforme descrito anteriormente.

## <a name="notebook-examples"></a>Exemplos de notebook

Por exemplo, consulte os notebooks nos seguintes locais:

* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* [Referência do Azure Machine Learning](https://aka.ms/aml-sdk)
* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md)
* [Onde implantar modelos](how-to-deploy-and-where.md)
* [Compilar pipelines de machine learning com o serviço de Azure Machine Learning](concept-ml-pipelines.md)
