---
title: Destinos de computação para treinamento de modelo
titleSuffix: Azure Machine Learning service
description: Configure os ambientes de treinamento (destinos de computação) para treinamento de modelo de machine learning. É possível alternar facilmente os ambientes de treinamento. Inicie o treinamento localmente e, caso precise expandir, passe para um destino de computação em nuvem. Databricks
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
ms.openlocfilehash: 664d56daf3e70e2e5699d0c07331c466c60e06c5
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338850"
---
# <a name="set-up-compute-targets-for-model-training"></a>Configurar destinos de computação para treinamento de modelo

Com o serviço do Azure Machine Learning, é possível treinar o modelo em diferentes recursos de computação. Esses recursos de computação, chamados de __destinos de computação__, podem ser locais ou na nuvem. Neste documento, você aprenderá sobre os destinos de computação com suporte e como usá-los.

Um destino de computação é um recurso em que o script de treinamento é executado, ou o modelo é hospedado, quando implantado como um serviço Web. É possível criar e gerenciar um destino de computação usando o SDK do Azure Machine Learning, o portal do Azure ou a CLI do Azure. Se você tiver destinos de computação criados por meio de outro serviço (por exemplo, um cluster HDInsight), poderá usá-los anexando-os ao workspace de serviço do Azure Machine Learning.

Há três categorias de destinos de computação principais que o Azure Machine Learning dá suporte:

* __Local__: Seu computador local, ou uma VM baseada em nuvem que você usa como um ambiente de experimentação/desenvolvimento. 

* __Computação gerenciada__: A Computação do Azure Machine Learning é uma oferta de computação gerenciada pelo serviço do Azure Machine Learning. Ela permite criar facilmente uma computação de nó único, ou de vários nós, para treinamento, teste e inferência em lotes.

* __Computação anexada__: Você também pode trazer sua própria computação em nuvem do Azure e anexá-la ao Azure Machine Learning. A seguir, leia mais sobre os tipos de computação com suporte e como usá-los.


## <a name="supported-compute-targets"></a>Destinos de computação com suporte

O serviço do Azure Machine Learning tem suporte variado nos diversos destinos de computação. Um ciclo de vida de desenvolvimento de modelo típico começa com experimentação/desenvolvimento em uma pequena quantidade de dados. Nessa fase, é recomendável usar um ambiente local. Por exemplo, o computador local ou uma VM baseada em nuvem. Na medida em que você aumenta o treinamento em conjuntos de dados maiores ou faz treinamento distribuído, é recomendável usar a Computação do Azure Machine Learning para criar um cluster de vários nós, ou de nó único, que dimensionará automaticamente sempre que você enviar uma execução. Também é possível anexar seu próprio recurso de computação, embora o suporte para vários cenários possa variar conforme detalhado abaixo:

|Destino de computação| Aceleração de GPU | Ajuste de hiperparâmetro automatizado | Machine Learning automatizado | Pipeline amigável|
|----|:----:|:----:|:----:|:----:|
|[Computador local](#local)| Talvez | &nbsp; | ✓ | &nbsp; |
|[Computação do Azure Machine Learning](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[VM remota](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Azure Data Lake Analytics](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ O Azure Databricks e Azure Data Lake Analytics __somente__ podem ser usados em um pipeline. Para obter mais informações sobre pipelines, consulte o documento [Pipelines in Azure Machine Learning](concept-ml-pipelines.md).

> [!IMPORTANT]
> A Computação do Azure Machine Learning deve ser criada em um workspace. Não é possível anexar instâncias existentes a um workspace.
>
> Outros destinos de computação devem ser criados fora do Azure Machine Learning e, em seguida, anexados ao seu workspace.

> [!NOTE]
> Alguns destinos de computação dependem de imagens de contêineres do Docker durante o treinamento de um modelo. A imagem base da GPU deve ser usada apenas nos Serviços do Microsoft Azure. Para treinamento de modelo, esses serviços são:
>
> * Computação do Azure Machine Learning
> * Serviço de Kubernetes do Azure
> * Máquina Virtual de Ciência de Dados.

## <a name="workflow"></a>Fluxo de trabalho

O fluxo de trabalho para desenvolver e implantar um modelo com o Azure Machine Learning segue estas etapas:

1. Desenvolva scripts de Python de treinamento de aprendizado de máquina.
1. Criar e configurar ou anexar um destino de computação existente.
1. Envie os scripts de treinamento para o destino de computação.
1. Inspecione os resultados para localizar o melhor modelo.
1. Registre o modelo no registro do modelo.
1. Implantar o modelo.

> [!IMPORTANT]
> O script de treinamento não está vinculado a um destino de computação específico. Você pode treinar inicialmente em seu computador local e alterne os destinos de computação sem reescrever o script de treinamento.

> [!TIP]
> Sempre que você associar um destino de computação ao seu workspace, seja criando computação gerenciada ou anexando computação existente, será necessário fornecer um nome ao computador. Esse nome deverá ter entre 2 e 16 caracteres de tamanho.

Alternar de computação como um destino para outro envolve a criação de um [executar configuração](concept-azure-machine-learning-architecture.md#run-configuration). A configuração de execução define como executar o script no destino de computação.

## <a name="training-scripts"></a>Scripts de treinamento

Ao iniciar uma execução de treinamento, um instantâneo do diretório contendo os scripts de treinamento é criado e enviado ao destino de computação. Para obter mais informações, veja [instântaneo](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Computador local

Durante o treinamento localmente, você usa o SDK para enviar a operação de treinamento. Você pode treinar usando um ambiente gerenciado pelo usuário ou gerenciado pelo sistema.

### <a name="user-managed-environment"></a>Ambiente gerenciado por usuário

Em um ambiente gerenciado pelo usuário, você é responsável por garantir que todos os pacotes necessários estejam disponíveis no ambiente Python escolhido para execução do script. O trecho de código a seguir está um exemplo de configuração de treinamento para um ambiente gerenciado pelo usuário:

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>Ambiente gerenciado pelo sistema

Ambientes gerenciados pelo sistema dependem de conda para gerenciar as dependências. Conda cria um arquivo chamado `conda_dependencies.yml` que contém uma lista de dependências. Em seguida, você pode pedir o sistema para criar um novo ambiente do conda e executar os scripts contidos nela. Ambientes gerenciados pelo sistema podem ser reutilizados mais tarde, contanto que os arquivos `conda_dependencies.yml` permaneçam inalterados. 

A configuração inicial do backup de um novo ambiente pode levar vários minutos para ser concluída, dependendo do tamanho das dependências necessárias. O trecho de código a seguir demonstra a criação de um ambiente gerenciado pelo sistema que depende do scikit-learn:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Computação do Azure Machine Learning

A Computação do Azure Machine Learning é uma infraestrutura de computação gerenciada que permite ao usuário criar facilmente computação de único nó a vários nós. Ela é criada __na sua região do workspace__ e é um recurso que pode ser compartilhado com outros usuários no workspace. Escala verticalmente e automaticamente quando um trabalho é enviado e pode ser colocada em uma rede virtual do Azure. Executa em um __ambiente em contêineres__, empacotando as dependências do modelo em um contêiner do Docker.

É possível usar a Computação do Azure Machine Learning para distribuir o processo de treinamento em um cluster de nós de computação de CPU ou GPU na nuvem. Para obter mais informações sobre os tamanhos de VM que incluem GPUs, consulte a documentação [Tamanhos de máquinas virtuais com GPU otimizadas](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

> [!NOTE]
> A Computação do Azure Machine Learning tem limites padrão em alguns aspectos como, por exemplo, o número de núcleos que podem ser alocados. Para obter mais informações, consulte o documento [Gerenciar e solicitar cotas para recursos do Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

É possível criar a Computação do Azure Machine Learning sob demanda ao programar uma execução, ou como um recurso persistente.

### <a name="run-based-creation"></a>Criação baseada em execução

É possível criar a Computação do Azure Machine Learning como um destino de computação em tempo de execução. Nesse caso, a computação é criada automaticamente para a execução, escalada verticalmente até o max_nodes que você especificar na configuração de execução e, em seguida, será __automaticamente excluída__ após concluir a execução.

> [!IMPORTANT]
> A criação baseada em execução da computação do Azure Machine Learning atualmente está em estado de versão prévia. Não use a criação baseada em execução se você estiver usando Ajuste de Hiperparâmetro ou Machine Learning Automatizado. Se for necessário usar Ajuste de Hiperparâmetro ou Machine Learning Automatizado, crie a Computação do Azure Machine Learning antes de enviar uma execução.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

#Let us first list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use AmlCompute to execute script.
run_config.target = "amlcompute"

# AmlCompute will be created in the same region as workspace. Set vm size for AmlCompute from the list returned above
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Computação persistente (Básico)

Uma Computação do Azure Machine Learning pode ser reutilizada em vários trabalhos. Ela pode ser compartilhada com outros usuários no workspace, e é mantida entre trabalhos.

Para criar um recurso persistente da Computação do Azure Machine Learning, especifique os parâmetros `vm_size` e `max_nodes`. Em seguida, o Azure Machine Learning usará padrões inteligentes para o restante dos parâmetros.  Por exemplo, a computação é definida para dimensionamento automático até zero nós quando não utilizada e para criar VMs dedicadas para executar os trabalhos conforme necessário. 

* **vm_size**: Família de VMs dos nós criados pela Computação do Azure Machine Learning.
* **max_nodes**: Nós máximos a serem dimensionados automaticamente durante a execução de um trabalho na Computação do Azure Machine Learning.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Computação persistente (Avançado)

Também é possível configurar várias propriedades avançadas ao criar a Computação do Azure Machine Learning.  Essas propriedades permitem que você crie um cluster persistente de tamanho fixo ou dentro de uma Rede Virtual do Azure existente na assinatura.

Além de `vm_size` e `max_nodes`, você pode usar as seguintes propriedades:

* **min_nodes**: Nós mínimos (padrão 0 nós) para diminuir o tamanho durante a execução de um trabalho na Computação do Azure Machine Learning.
* **vm_priority**: Escolha entre VMs dedicated' (padrão) e 'lowpriority' ao criar a Computação do Azure Machine Learning. As VMs de baixa prioridade usam o excesso de capacidade do Azure e, portanto, são mais baratas, mas correm o risco de sua execução ser antecipada.
* **idle_seconds_before_scaledown**: Tempo ocioso (padrão 120 segundos) para aguardar após a conclusão da execução antes do dimensionamento automático para min_nodes.
* **vnet_resourcegroup_name**: Grupo de recursos da rede virtual __existente__. A Computação do Azure Machine Learning é criada dentro dessa rede virtual.
* **vnet_name**: Nome da rede virtual. A rede virtual deve estar na mesma região do seu workspace do Azure Machine Learning.
* **subnet_name**: Nome da sub-rede dentro da rede virtual. Os recursos da Computação do Azure Machine Learning receberão endereços IP desse intervalo de sub-rede.

> [!TIP]
> Quando você cria um recurso persistente da Computação do Azure Machine Learning, também pode atualizar as propriedades como min_nodes ou max_nodes. Para fazer isso, simplesmente chame a função `update()`.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
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

O Azure Machine Learning também dá suporte para que você traga seu próprio recurso de computação e anexe-o ao workspace. Um desses tipos de recursos é uma VM remota arbitrária, desde que seja acessível pelo serviço do Azure Machine Learning. Pode ser uma VM do Azure ou um servidor remoto na organização ou no local. Especificamente, fornecido o endereço IP e as credenciais (nome de usuário senha ou chave SSH), é possível usar qualquer VM acessível para execuções remotas.
Você pode usar um ambiente do Conda interno do sistema, um ambiente do Python já existente ou um contêiner do Docker. Executar usando o contêiner do Docker exige que você tenha o Mecanismo do Docker executando na VM. Essa funcionalidade é especialmente útil quando você quer um ambiente de experimentação/desenvolvimento baseado em nuvem mais flexível que seu computador local.

> [!TIP]
> É recomendável usar a Máquina Virtual de Ciência de Dados como a VM do Azure preferível para esse cenário. É um ambiente de desenvolvimento de IA e ciência de dados pré-configurado no Azure com uma escolha organizada de ferramentas e estruturas para o ciclo de vida completo do desenvolvimento do ML. Para obter mais informações sobre como usar a Máquina Virtual de Ciência de Dados com Azure Machine Learning, consulte o documento [Configurar um ambiente de desenvolvimento](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

> [!WARNING]
> O Azure Machine Learning dá suporte apenas a máquinas virtuais executando o Ubuntu. Quando criar uma máquina virtual ou selecionar uma existente, você deve selecionar uma que usa o Ubuntu.

As etapas a seguir usam o SDK para configurar uma Máquina Virtual de Ciência de Dados (DSVM) como um destino de treinamento:

1. Para anexar uma máquina virtual existente como um destino de computação, você precisa fornecer o nome de domínio totalmente qualificado, o nome de logon e a senha da máquina virtual.  No exemplo, substitua ```<fqdn>``` com o nome de domínio totalmente qualificado público da VM ou o endereço IP público. Substitua ```<username>``` e ```<password>``` pelo usuário SSH e senha para a VM:

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create compute config.
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")
    # If using SSH instead of a password, use this:
    #                                                  ssh_port=22,
    #                                                   username='<username>',
    #                                                   password=None,
    #                                                   private_key_file="path-to-file",
    #                                                   private_key_passphrase="passphrase")

    # Attach the compute
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. Crie uma configuração para o destino de computação da DSVM. O Docker e o conda são usados para criar e configurar o ambiente de treinamento na DSVM:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image
    # If you want to use GPU in DSVM, you must also use GPU base Docker image azureml.core.runconfig.DEFAULT_GPU_IMAGE
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Banco de Dados do Azure

As Azure Databricks são um ambiente baseado em Apache Spark na nuvem do Azure. Ele pode ser usado como um destino de computação ao treinar modelos com um pipeline do Azure Machine Learning.

> [!IMPORTANT]
> Um destino de computação do Azure Databricks só pode ser usado em um pipeline de Aprendizado de Máquina.
>
> Você deve criar um espaço de trabalho do Azure Databricks antes de usá-lo para treinar seu modelo. Para criar esses recursos, consulte o documento [Executar um trabalho do Spark no Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Para anexar as Bills de Dados do Azure como um destino de computação, você deve usar o SDK de Aprendizado de Máquina do Azure e fornecer as seguintes informações:

* __Nome de computação__: O nome que você quer atribuir a esse recurso de computação.
* __Nome do Workspace do Databricks__: O nome do workspace do Azure Databricks.
* __Token de acesso__: O token de acesso usado para autenticar no Azure Databricks. Para gerar um token de acesso, consulte o documento [Autenticação](https://docs.azuredatabricks.net/api/latest/authentication.html).

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

    # Create attach config
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

O Azure Data Lake Analytics é uma plataforma de análise de big data na nuvem do Azure. Ele pode ser usado como um destino de computação ao treinar modelos com um pipeline do Azure Machine Learning.

> [!IMPORTANT]
> Um destino de computação do Azure Data Lake Analytics só pode ser usado em um pipeline de Aprendizado de Máquina.
>
> Você deve criar uma conta do Azure Data Lake Analytics antes de usá-la para treinar seu modelo. Para criar este recurso, consulte o documento [Introdução ao Google Analytics do Azure Data Lake](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Para anexar o Data Lake Analytics como um destino de computação, você deve usar o SDK do Aprendizado de Máquina do Azure e fornecer as seguintes informações:

* __Nome de computação__: O nome que você quer atribuir a esse recurso de computação.
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
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Os pipelines do Azure Machine Learning só podem funcionar com dados armazenados no armazenamento de dados padrão da conta do Data Lake Analytics. Se os dados que você precisa para trabalhar com está em um armazenamento não padrão, você pode usar um [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) para copiar os dados antes do treinamento.

## <a id="hdinsight"></a>Azure HDInsight 

O HDInsight é uma plataforma popular para análise de dados. Ele fornece o Apache Spark, que pode ser usado para treinar seu modelo.

> [!IMPORTANT]
> Você deve criar o cluster HDInsight antes de usá-lo para treinar seu modelo. Para criar um Spark no cluster HDInsight, consulte o documento [criar um Cluster Spark no HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> Ao criar o cluster, você deve especificar um nome de usuário SSH e senha. Observe esses valores, conforme necessário ao usar o HDInsight como um destino de computação.
>
> Quando o cluster tiver sido criado, ele tem um nome de domínio totalmente qualificado (FQDN) do `<clustername>.azurehdinsight.net`, onde `<clustername>` é o nome fornecido para o cluster. Você precisa desse endereço (ou o endereço IP público do cluster) para usá-lo como um destino de computação

Para configurar um HDInsight como um destino de computação, você deve fornecer o nome de domínio totalmente qualificado, o nome de logon de cluster e a senha para o cluster HDinsight. O exemplo a seguir usa o SDK para anexar um cluster ao seu workspace. No exemplo, substitua `<fqdn>` com o nome de domínio totalmente qualificado público do cluster ou o endereço IP público. Substitua `<username>` e `<password>` com o usuário SSH e senha para o cluster:

> [!NOTE]
> Para localizar o FQDN para o cluster, visite o portal do Azure e selecione seu cluster HDInsight. Da seção __Visão geral__, o FQDN é parte da entrada __URL__. Basta remover o `https://` desde o início do valor.
>
> ![Captura de tela de visão geral sobre o cluster HDInsight com a entrada de URL realçada](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attaches a HDInsight cluster as a compute target.
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

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>Enviar execução de treinamento

Existem duas maneiras de enviar uma execução de treinamento:

* Enviando um `ScriptRunConfig` objeto.
* Enviando um `Pipeline` objeto.

> [!IMPORTANT]
> The Azure Databricks and Azure Datalake Analytics compute targets can only be used in a pipeline.
> O destino de computação local não pode ser usado em um Pipeline.

### <a name="submit-using-scriptrunconfig"></a>Enviar usando `ScriptRunConfig`

O padrão de código para enviar uma execução de treinamento usando `ScriptRunConfig` é o mesmo, independentemente da meta de computação:

* Criar um `ScriptRunConfig` usando a configuração de execução para o destino de computação do objeto.
* Enviar a execução.
* Aguarde a conclusão da execução.

O exemplo a seguir usa a configuração para o destino de computação local gerenciado pelo sistema criado anteriormente neste documento:

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="submit-using-a-pipeline"></a>Enviar usando um pipeline

O padrão de código para enviar um treinamento usando um pipeline é o mesmo, independentemente do destino de computação:

* Adicione uma etapa ao pipeline para o recurso de computação.
* Envie uma execução usando o pipeline.
* Aguarde a conclusão da execução.

O exemplo a seguir usa o destino de cálculo do Azure Databricks criado anteriormente neste documento:

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
# list of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Para obter mais informações sobre pipelines de aprendizado de máquina, consulte o documento [Pipelines e aprendizado de máquina do Azure](concept-ml-pipelines.md).

Por exemplo, os Jupyter Notebooks que demonstram treinamento usando um pipeline, veja [https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Exibir e configurar a computação usando o portal do Azure

Você pode exibir quais destinos de computação são associados com seu workspace do portal do Azure. Para obter essa lista, use as seguintes etapas:

1. Visite o [portal do Azure](https://portal.azure.com) e navegue até seu workspace.
2. Clique no link __Computação__ sob a seção __Aplicativos__.

    ![Guia Exibir computação](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Criar um destino de computação

Siga as etapas acima para exibir a lista de destinos de computação e, em seguida, use as seguintes etapas para criar um destino de computação:

1. Clique no sinal __+__ para adicionar um destino de computação.

    ![Adicionar computação ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Insira um nome para o destino de computação
1. Selecione **Computação do Machine Learning** como o tipo da computação a ser usada para __Treinamento__

    > [!IMPORTANT]
    > Somente é possível criar a Computação do Azure Machine Learning como a computação gerenciada para treinamento

1. Preencha o formulário necessário, especialmente a Família de VM e os nós máximos a serem usados para acelerar a computação 
1. Escolha __Criar__
1. É possível exibir o status da operação de criação, selecionando o destino de computação na lista

    ![Exibir lista de computação](./media/how-to-set-up-training-targets/View_list.png)

1. Em seguida, você visualizará os detalhes do destino de computação.

    ![Exibir detalhes](./media/how-to-set-up-training-targets/compute-target-details.png)

1. Agora, é possível enviar uma execução nesses destinos conforme detalhado acima


### <a name="reuse-existing-compute-in-your-workspace"></a>Reutilização de computação existente no workspace

Siga as etapas acima para exibir a lista de destinos de computação e, em seguida, use as seguintes etapas para usar um destino de computação:

1. Clique no sinal **+** para adicionar um destino de computação
2. Insira um nome para o destino de computação
3. Selecione o tipo de computação a ser anexado para __Treinamento__

    > [!IMPORTANT]
    > Nem todos os tipos de computação podem ser anexados usando o portal.
    > Atualmente, os tipos que podem ser anexados para treinamento são:
    > 
    > * VM remota
    > * Databricks
    > * Data Lake Analytics
    > * HDInsight

1. Preencher o formulário requerido

    > [!NOTE]
    > A Microsoft recomenda o uso de chaves SSH, por serem mais seguras do que senhas. As senhas são vulneráveis a ataques de força bruta, enquanto as chaves SSH se baseiam em assinaturas de criptografia. Para obter informações sobre como criar chaves SSH para uso com Máquinas Virtuais do Azure, consulte os seguintes documentos:
    >
    > * [Criar e usar chaves SSH no Linux ou macOS]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Criar e usar chaves SSH no Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Selecionar Anexar
1. Você pode exibir o status da operação de anexação, selecionando o destino de computação na lista
1. Agora, é possível enviar uma execução nesses destinos conforme detalhado acima

## <a name="examples"></a>Exemplos
Consulte os notebooks nos seguintes locais:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)

* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* [Referência do Azure Machine Learning](https://aka.ms/aml-sdk)
* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md)
* [Onde implantar modelos](how-to-deploy-and-where.md)
* [Compilar pipelines de machine learning com o serviço do Azure Machine Learning](concept-ml-pipelines.md)
