---
title: Configurar destinos de computação para treinamento de modelo com o serviço Azure Machine Learning | Microsoft Docs
description: Saiba como selecionar e configurar os ambientes de treinamento (destinos de computação) usados para treinar seu modelos de aprendizado de máquina. O serviço de Azure Machine Learning permite que você realize facilmente ambientes de treinamento do comutador. Inicia o treinamento localmente e se você precisar expandir, alterne para um destino de computação em nuvem.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 4af2e570b498e496e80b6aeee2b8aeae23c582cc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952394"
---
# <a name="select-and-use-a-compute-target-to-train-your-model"></a>Selecione e use um destino de computação para treinar seu modelo

Com o serviço de Azure Machine Learning, você pode treinar seu modelo em vários ambientes diferentes. Esses ambientes, chamados __destinos de computação__, podem ser local ou na nuvem. Neste documento, você aprenderá sobre os destinos de computação com suporte e como usá-los.

Um destino de computação é o recurso que executa o script de treinamento ou hosts de seu modelo quando ele é implantado como um serviço web. Eles podem ser criados e gerenciados usando o SDK do Azure Machine Learning ou a CLI. Se você tiver os destinos de computação que foram criados por outro processo (por exemplo, o portal do Azure ou a CLI do Azure), você pode usá-los por anexá-los para seu espaço de trabalho do serviço de Azure Machine Learning.

Você pode iniciar com execuções locais em seu computador e, em seguida, escalar verticalmente e horizontalmente para outros ambientes, como máquinas de virtuais de ciência de dados remotas com GPU ou IA do Lote do Azure. 

## <a name="supported-compute-targets"></a>Os destinos de computação com suporte

O Azure Machine Learning dá suporte aos seguintes destinos de computação:

|Destino de computação| Aceleração de GPU | Ajuste de hiperparâmetro automatizado | Seleção do modelo automatizado | Podem ser usados em pipelines|
|----|:----:|:----:|:----:|:----:|
|[Computador local](#local)| Talvez | &nbsp; | ✓ | &nbsp; |
|[DSVM (Máquina Virtual de Ciência de Dados)](#dsvm) | ✓ | ✓ | ✓ | ✓ |
|[IA do Lote do Azure](#batch)| ✓ | ✓ | ✓ | ✓ | ✓ |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

__[Instâncias de Contêiner do Azure (ACI)](#aci)__ também podem ser usadas para treinar modelos. É uma oferta de nuvem sem servidor que é barata e fácil de criar e trabalhar. ACI não oferece suporte a aceleração de GPU, hiperparâmetro automatizado de ajuste, ou seleção de modelo automatizado. Além disso, ele não pode ser usado em um pipeline.

Os principais diferenciais entre os destinos de computação são:
* __Aceleração de GPU__: GPUs estão disponíveis com a máquina de Virtual de ciência de dados e IA do Lote do Azure. Você pode ter acesso a uma GPU no computador local, dependendo do hardware, drivers e estruturas que são instaladas.
* __Ajuste de hiperparâmetro automatizado__: hiperparâmetro automatizado do Azure Machine Learning otimização ajuda você a encontrar os hiperparâmetros melhor para seu modelo.
* __Seleção de modelo automatizado__: Azure Machine Learning inteligente pode recomendar a seleção de algoritmo e hiperparâmetro ao criar um modelo. Seleção de modelo automatizado ajuda você a convergir mais rapidamente do que tentar manualmente diferentes combinações para um modelo de alta qualidade. Para mais informações, veja o documento [Tutorial: Treinar automaticamente um modelo de classificação com o Azure Automated Machine Learning](tutorial-auto-train-models.md).
* __Pipelines__: Azure Machine Learning permite que você combine diferentes tarefas, como treinamento e implantação em um pipeline. Pipelines podem ser executados em paralelo ou em sequência e fornecem um mecanismo de automação confiável. Para obter mais informações, consulte o documento [compilar pipelines de machine learning com o serviço de Azure Machine Learning](concept-ml-pipelines.md).

Você pode usar o SDK do Azure Machine Learning, a CLI do Azure ou o portal do Azure para criar destinos de computação. Você também pode usar destinos de computação existentes ao adicioná-los (anexar) no seu espaço de trabalho.

> [!IMPORTANT]
> Você não pode anexar uma Instância existente de Contêineres do Azure para seu espaço de trabalho. Em vez disso, você deve criar uma nova instância.
>
> Você não pode criar um cluster de HDInsight do Azure dentro de um espaço de trabalho. Em vez disso, você deve anexar um cluster existente.

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

Alternar de computação como um destino para outro envolve a criação de um [executar configuração](concept-azure-machine-learning-architecture.md#run-configuration). A configuração de execução define como executar o script no destino de computação.

## <a name="training-scripts"></a>Scripts de treinamento

Quando você inicia uma execução de treinamento, todo o diretório que contém seus scripts de treinamento é enviado. Um instantâneo é criado e enviado para o destino de computação. Para obter mais informações, veja [instântaneo](concept-azure-machine-learning-architecture.md#snapshot).

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
run_config_system_managed.prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```
## <a id="dsvm"></a>Máquina Virtual de Ciência de Dados

Seu computador local pode não ter a computação ou recursos GPU necessários para treinar o modelo. Nessa situação, você pode escalar verticalmente ou escalar horizontalmente o processo de treinamento adicionando outros destinos de computação, como Máquinas Virtuais de Ciência de Dados (DSVM).

> [!WARNING]
> O Azure Machine Learning dá suporte apenas a máquinas virtuais executando o Ubuntu. Quando criar uma máquina virtual ou selecionar uma existente, você deve selecionar uma que usa o Ubuntu.

As etapas a seguir usam o SDK para configurar uma Máquina Virtual de Ciência de Dados (DSVM) como um destino de treinamento:

1. Criar ou anexar uma Máquina Virtual
    
    * Para criar uma nova DSVM, primeiro verifique para ver se você tiver uma DSVM com o mesmo nome, se não crie uma nova VM:
    
        ```python
        from azureml.core.compute import DsvmCompute
        from azureml.core.compute_target import ComputeTargetException

        compute_target_name = 'mydsvm'

        try:
            dsvm_compute = DsvmCompute(workspace = ws, name = compute_target_name)
            print('found existing:', dsvm_compute.name)
        except ComputeTargetException:
            print('creating new.')
            dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
            dsvm_compute = DsvmCompute.create(ws, name = compute_target_name, provisioning_configuration = dsvm_config)
            dsvm_compute.wait_for_completion(show_output = True)
        ```
    * Para anexar uma máquina virtual existente como um destino de computação, você deve fornecer o nome de domínio totalmente qualificado, o nome de logon e a senha para a máquina virtual.  No exemplo, substitua ```<fqdn>``` com o nome de domínio totalmente qualificado público da VM ou o endereço IP público. Substitua ```<username>``` e ```<password>``` com o usuário SSH e senha para a VM:

        ```python
        from azureml.core.compute import RemoteCompute

        dsvm_compute = RemoteCompute.attach(ws,
                                        name="attach-dsvm",
                                        username='<username>',
                                        address="<fqdn>",
                                        ssh_port=22,
                                        password="<password>")

        dsvm_compute.wait_for_completion(show_output=True)
    
   It takes around 5 minutes to create the DSVM instance.

1. Create a configuration for the DSVM compute target. Docker and conda are used to create and configure the training environment on DSVM:

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image from DockerHub
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

1. Para excluir os recursos de computação quando tiver terminado, use o seguinte código:

    ```python
    dsvm_compute.delete()
    ```

## <a id="batch"></a>IA do Lote do Azure

Se demorar muito tempo para treinar seu modelo, você pode usar a IA do Lote do Azure para distribuir o treinamento em um cluster de recursos de computação na nuvem. A IA do Lote também pode ser configurada para habilitar um recurso GPU.

O exemplo a seguir procura por um cluster existente do IA do Lote por nome. Se nenhum for encontrado, ele será criado:

```python
from azureml.core.compute import BatchAiCompute
from azureml.core.compute import ComputeTarget

# choose a name for your cluster
batchai_cluster_name = ws.name + "cpu"

found = False
# see if this compute target already exists in the workspace
for ct in ws.compute_targets():
    print(ct.name, ct.type)
    if (ct.name == batchai_cluster_name and ct.type == 'BatchAI'):
        found = True
        print('found compute target. just use it.')
        compute_target = ct
        break
        
if not found:
    print('creating a new compute target...')
    provisioning_config = BatchAiCompute.provisioning_configuration(vm_size = "STANDARD_D2_V2", # for GPU, use "STANDARD_NC6"
                                                                #vm_priority = 'lowpriority', # optional
                                                                autoscale_enabled = True,
                                                                cluster_min_nodes = 1, 
                                                                cluster_max_nodes = 4)

    # create the cluster
    compute_target = ComputeTarget.create(ws,batchai_cluster_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current BatchAI cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

Para anexar um cluster de IA do lote existente como um destino de computação, você deve fornecer a id de recurso do Azure. Para obter a id de recurso do portal do Azure, você precisará:
1. Procurar por `Batch AI` serviço em **todos os Serviços**
1. Clique no nome do espaço de trabalho que pertence o cluster
1. Selecionar o cluster
1. Clicar em **Propriedades**
1. Copie o **Id**

O exemplo a seguir usa o SDK para anexar um cluster ao seu espaço de trabalho. No exemplo, substitua `<name>` com qualquer nome para a computação. Isso não precisa corresponder ao nome do cluster. Substitua `<resource-id>` com a id de recurso do Azure detalhada acima:

```python
from azureml.core.compute import BatchAiCompute
BatchAiCompute.attach(workspace=ws,
                      name=<name>,
                      resource_id=<resource-id>)
```

Você também pode verificar o status de trabalho e cluster de IA do lote usando os seguintes comandos de CLI do Azure:

- Verificar o status do cluster. Você pode ver quantos nós estão em execução usando `az batchai cluster list`.
- Verificar o status do trabalho. Você pode ver quantos trabalhos estão em execução usando `az batchai job list`.

A criação do cluster da IA do Lote demora cerca de 5 minutos

## <a name='aci'></a>Instância de Contêiner do Azure (ACI)

As Instâncias de Contêiner do Azure são contêineres isolados que têm tempos de inicialização mais rápidos e não exigem que o usuário gerenciar todas as máquinas virtuais. O serviço de Azure Machine Learning usa contêineres do Linux, que estão disponíveis nas regiões Oeste dos EUA, Leste dos EUA, Oeste europeu, Norte europeu, Oeste dos EUA 2 e Sudeste asiático. Para saber mais, veja [Disponibilidade por região](https://docs.microsoft.com/azure/container-instances/container-instances-quotas#region-availability). 

O exemplo a seguir mostra como usar o SDK para criar um destino de computação ACI e usá-lo para treinar um modelo: 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use ACI to run script.
run_config.target = "containerinstance"

# ACI container group is only supported in certain regions, which can be different than the region the Workspace is in.
run_config.container_instance.region = 'eastus'

# set the ACI CPU and Memory 
run_config.container_instance.cpu_cores = 1
run_config.container_instance.memory_gb = 2

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
#run_config.environment.docker.base_image = 'microsoft/mmlspark:plus-0.9.9'

# use conda_dependencies.yml to create a conda environment in the Docker image
run_config.environment.python.user_managed_dependencies = False

# auto-prepare the Docker image when used for the first time (if it is not already prepared)
run_config.auto_prepare_environment = True

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

Pode levar de alguns segundos a alguns minutos para criar um destino de computação do ACI.

## <a id="hdinsight"></a>Anexe um cluster HDInsight 

O HDInsight é uma plataforma popular para análise de dados. Ele fornece o Apache Spark, que pode ser usado para treinar seu modelo.

> [!IMPORTANT]
> Você deve criar o cluster HDInsight antes de usá-lo para treinar seu modelo. Para criar um Spark no cluster HDInsight, consulte o documento [criar um Cluster Spark no HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> Ao criar o cluster, você deve especificar um nome de usuário SSH e senha. Observe esses valores, conforme necessário ao usar o HDInsight como um destino de computação.
>
> Quando o cluster tiver sido criado, ele tem um nome de domínio totalmente qualificado (FQDN) do `<clustername>.azurehdinsight.net`, onde `<clustername>` é o nome fornecido para o cluster. Você precisa desse endereço (ou o endereço IP público do cluster) para usá-lo como um destino de computação

Para configurar um HDInsight como um destino de computação, você deve fornecer o nome de domínio totalmente qualificado, o nome de logon de cluster e a senha para o cluster HDinsight. O exemplo a seguir usa o SDK para anexar um cluster ao seu espaço de trabalho. No exemplo, substitua `<fqdn>` com o nome de domínio totalmente qualificado público do cluster ou o endereço IP público. Substitua `<username>` e `<password>` com o usuário SSH e senha para o cluster:

> [!NOTE]
> Para localizar o FQDN para o cluster, visite o portal do Azure e selecione seu cluster HDInsight. Da seção __Visão geral__, o FQDN é parte da entrada __URL__. Basta remover o `https://` desde o início do valor.
>
> ![Captura de tela de visão geral sobre o cluster HDInsight com a entrada de URL realçada](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute

try:
    # Attaches a HDInsight cluster as a compute target.
    HDInsightCompute.attach(ws,name = "myhdi",
                            address = "<fqdn>",
                            username = "<username>",
                            password = "<password>")
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
    
O código para enviar uma execução de treinamento é o mesmo, independentemente do destino de computação:

* Criar um `ScriptRunConfig` usando a configuração de execução para o destino de computação do objeto.
* Enviar a execução.
* Aguarde a conclusão da execução.

O exemplo a seguir usa a configuração para o destino de computação local gerenciado pelo sistema criado anteriormente neste documento:

```pyghon
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Exibir e configurar a computação usando o portal do Azure

Você pode exibir quais destinos de computação são associados com seu espaço de trabalho do portal do Azure. Para obter essa lista, use as seguintes etapas:

1. Visite o [portal do Azure](https://portal.azure.com) e navegue até seu espaço de trabalho.
2. Clique no link __Computação__ sob a seção __Aplicativos__.

    ![Guia Exibir computação](./media/how-to-set-up-training-targets/compute_tab.png)

### <a name="create-a-compute-target"></a>Criar um destino de computação

Siga as etapas acima para exibir a lista de destinos de computação e, em seguida, use as seguintes etapas para criar um destino de computação:

1. Clique no sinal __+__ para adicionar um destino de computação.

    ![Adicionar computação ](./media/how-to-set-up-training-targets/add_compute.png)

1. Insira um nome para o destino de computação.
1. Selecione o tipo de computação para anexar para __Treinamento__. 
1. Selecione __Criar Novo__ e preencha o formulário desejado. 
1. Escolha __Criar__
1. Você pode exibir o status da operação de criação, selecionando o destino de computação da lista.

    ![Exibir lista de computação](./media/how-to-set-up-training-targets/View_list.png), em seguida, você verá os detalhes para esse tipo de computação.
    ![Exibir detalhes](./media/how-to-set-up-training-targets/vm_view.PNG)
1. Agora você pode enviar uma execução em relação a esses destinos conforme detalhado acima.

### <a name="reuse-existing-compute-in-your-workspace"></a>Reutilização de computação existente no espaço de trabalho

Siga as etapas acima para exibir a lista de destinos de computação e, em seguida, use as seguintes etapas para usar um destino de computação:

1. Clique no sinal **+** para adicionar um destino de computação.
2. Insira um nome para o destino de computação.
3. Selecione o tipo de computação para anexar para Treinamento. A IA do Lote e as Máquinas Virtuais têm suporte atualmente no portal para treinamento.
4. Selecione 'Usar existente'.
    - Ao conectar a clusters de IA do Lote, selecione o destino de computação na lista suspensa, selecione o espaço de trabalho do IA do lote e o Cluster de IA do lote e, em seguida, clique em **criar**.
    - Ao anexar uma máquina Virtual, insira o endereço IP e a porta, chaves privadas/públicas e combinação de nome de usuário e senha e clique em criar.

    > [!NOTE]
    > A Microsoft recomenda que você use chaves SSH, como elas são mais seguras do que senhas. As senhas são vulneráveis a ataques de força bruta, enquanto as chaves SSH se baseiam em assinaturas de criptografia. Para obter informações sobre como criar chaves SSH para uso com Máquinas Virtuais do Azure, consulte os seguintes documentos:
    >
    > * [Criar e usar chaves SSH no Linux ou macOS]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Criar e usar chaves SSH no Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

5. Você pode exibir o status do estado do provisionamento, selecionando o destino de computação da lista de serviços de computação.
6. Agora você pode enviar uma execução em relação a esses destinos.

## <a name="examples"></a>Exemplos
Os seguintes blocos de anotações demonstram conceitos neste artigo:
* `01.getting-started/02.train-on-local/02.train-on-local.ipynb`
* `01.getting-started/04.train-on-remote-vm/04.train-on-remote-vm.ipynb`
* `01.getting-started/05.train-in-spark/05.train-in-spark.ipynb`
* `01.getting-started/07.hyperdrive-with-sklearn/07.hyperdrive-with-sklearn.ipynb`

Obtenha estes notebooks: [!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximas etapas

* [Referência do Azure Machine Learning](http://aka.ms/aml-sdk)
* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md)
* [Onde implantar modelos](how-to-deploy-and-where.md)
* [Compilar pipelines de machine learning com o serviço de Azure Machine Learning](concept-ml-pipelines.md)
