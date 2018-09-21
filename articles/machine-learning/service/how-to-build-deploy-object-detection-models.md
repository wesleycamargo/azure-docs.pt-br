---
title: Criar e implantar um modelo de detecção de objeto usando o Pacote do Azure Machine Learning para Visual Computacional.
description: Saiba como compilar, treinar, testar e implantar um modelo de detecção de objeto de visual computacional usando o Pacote do Azure Machine Learning para Visão Computacional.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 06/01/2018
ms.openlocfilehash: 65bcb92b8e97b09ca961aa02cd5891419c2cddb2
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580643"
---
# <a name="build-and-deploy-object-detection-models-with-azure-machine-learning"></a>Compilar e implantar modelos de detecção de objeto com Azure Machine Learning

Neste artigo, saiba como usar o **Pacote do Azure Machine Learning para Visão Computacional** para treinar, testar e implantar um modelo de detecção de objeto [Faster R-CNN](https://arxiv.org/abs/1506.01497). 

Um grande número de problemas no domínio do visual computacional pode ser resolvido usando a detecção de objeto. Esses problemas incluem a compilação de modelos que localizam um número variável de objetos em uma imagem. 

Ao compilar e implantar esse modelo com esse pacote, percorra as seguintes etapas:
1.  Criação do conjunto de dados
2.  Definição de modelo DNN (Rede Neural Profunda)
3.  Treinamento de modelo
4.  Avaliação e visualização
5.  Implantação do serviço Web
6.  Teste de carga do serviço Web

Neste exemplo, o TensorFlow é usado como a estrutura de aprendizado profundo, o treinamento é realizado localmente em um computador com GPU, como a [VM de ciência de dados de aprendizado profundo](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview), e a implantação usa a CLI de operacionalização do Azure ML.

Consulte a [documentação de referência do pacote](https://aka.ms/aml-packages/vision) para ver a referência detalhada de cada módulo e classe.

## <a name="prerequisites"></a>Pré-requisitos

1. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

1. As contas e os aplicativos abaixo devem ser configurados e instalados:
   - Uma conta de Experimentação do Azure Machine Learning 
   - Conta do Gerenciamento de Modelos do Azure Machine Learning
   - O Azure Machine Learning Workbench instalado

   Se esses três ainda não foram criados ou instalados, siga o artigo [Instalação do Início Rápido e do Azure Machine Learning Workbench](../service/quickstart-installation.md). 

1. O Pacote do Azure Machine Learning para Pesquisa Visual Computacional deve estar instalado. Saiba como [instalar esse pacote aqui](https://aka.ms/aml-packages/vision).

## <a name="sample-data-and-notebook"></a>Bloco de anotações e dados de exemplo

### <a name="get-the-jupyter-notebook"></a>Obter o Jupyter Notebook

Baixe do bloco de notas para executar o exemplo descrito aqui por conta própria.

> [!div class="nextstepaction"]
> [Obter o Jupyter Notebook](https://aka.ms/aml-packages/vision/notebooks/object_detection)

### <a name="load-the-sample-data"></a>Carregar os dados de exemplo

Para esta demonstração, é fornecido um conjunto de dados de itens de supermercado dentro de refrigeradores, consistindo de 30 imagens e 8 classes (eggBox, joghurt, ketchup, mushroom, mustard, orange, squash e water). Para cada imagem jpg, há um arquivo xml de anotação com nome semelhante. 

A figura a seguir mostra a estrutura de pastas recomendada. 

![estrutura de pastas](media/how-to-build-deploy-object-detection-models/data_directory.JPG)

## <a name="image-annotation"></a>Anotação de imagem

Locais de objetos anotados são necessários para treinar e avaliar um detector de objetos. [LabelImg](https://tzutalin.github.io/labelImg) é uma ferramenta de anotação de software livre que pode ser utilizada para anotar imagens. LabelImg grava um arquivo xml por imagem no formato Pascal-VOC, que pode ser lido por este pacote. 


```python
import warnings
warnings.filterwarnings("ignore")
import os, time
from cvtk.core import Context, ObjectDetectionDataset, TFFasterRCNN
from cvtk.evaluation import DetectionEvaluation
from cvtk.evaluation.evaluation_utils import graph_error_counts
from cvtk.utils import detection_utils

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

from matplotlib import pyplot as plt
# Display the images
%matplotlib inline
```

## <a name="create-a-dataset"></a>Criar um conjunto de dados

Crie um conjunto de dados CVTK que consista em um conjunto de imagens, com suas respectivas anotações de caixa delimitadora. Neste exemplo, as imagens do refrigerador fornecidas na pasta "../sample_data/foods/training" são usadas. Apenas imagens JPEG têm suporte.


```python
image_folder = "detection/sample_data/foods/train"
data_train = ObjectDetectionDataset.create_from_dir(dataset_name='training_dataset', data_dir=image_folder,
                                                    annotations_dir="Annotations", image_subdirectory='JPEGImages')

# Show some statistics of the training image, and also give one example of the ground truth rectangle annotations
data_train.print_info()
_ = data_train.images[2].visualize_bounding_boxes(image_size = (10,10))
```

    F1 2018-05-25 23:12:21,727 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-05-25 23:12:21,733 INFO azureml.vision:dataset creating dataset for scenario=detection 
    Dataset name: training_dataset
    Total classes: 8, total images: 25
    Label-wise object counts:
        Label eggBox: 20 objects
        Label joghurt: 20 objects
        Label ketchup: 20 objects
        Label mushroom: 20 objects
        Label mustard: 20 objects
        Label orange: 20 objects
        Label squash: 40 objects
        Label water: 20 objects
    Bounding box width and height distribution:
        Bounding box widths  0/5/25/50/75/95/100-percentile: 54/61/79/117/133/165/311 pixels
        Bounding box heights 0/5/25/50/75/95/100-percentile: 48/58/75/124/142/170/212 pixels
    


![png](media/how-to-build-deploy-object-detection-models/output_6_1.JPG)


## <a name="define-a-model"></a>Definir um modelo

Neste exemplo, é usado o modelo Faster R-CNN. Vários parâmetros podem ser fornecidos ao definir este modelo. O significado desses parâmetros, bem como os parâmetros usados para treinamento (consulte a próxima seção) podem ser encontrados nos documentos da API de CVTK ou no [Site de detecção de objeto Tensorflow](https://github.com/tensorflow/models/tree/master/research/object_detection). Mais obter mais informações sobre o modelo Faster R-CNN, é possível encontrá-las [neste link](https://docs.microsoft.com/cognitive-toolkit/Object-Detection-using-Faster-R-CNN#technical-details). Este modelo é baseado no Fast R-CNN e mais informações podem ser encontradas [aqui](https://docs.microsoft.com/cognitive-toolkit/Object-Detection-using-Fast-R-CNN#algorithm-details).


```python
score_threshold = 0.0       # Threshold on the detection score, use to discard lower-confidence detections.
max_total_detections = 300  # Maximum number of detections. A high value slows down training but might increase accuracy.
my_detector = TFFasterRCNN(labels=data_train.labels, 
                           score_threshold=score_threshold, 
                           max_total_detections=max_total_detections)
```

## <a name="train-the-model"></a>Treinar o modelo

O Modelo Faster R-CNN treinado em COCO com ResNet50 é usado como ponto de partida para treinamento. 

Para treinar o detector, o número de etapas de treinamento no código é definido como 350, para que o treinamento seja executado mais rapidamente (aproximadamente 5 minutos com a GPU). Na prática, defina pelo menos 10 vezes o número de imagens no conjunto de treinamento.

Neste exemplo, o número de etapas de treinamento do detector é definido como 350 para treinamento rápido. No entanto, na prática, uma boa regra é definir as etapas para 10 ou mais vezes o número de imagens no conjunto de treinamento.

Dois parâmetros de chave para treinamento são:
- Número de etapas para treinar o modelo, representado pelo argumento num_seps. Cada etapa treina o modelo com um minilote de tamanho de lote um.
- Taxa(s) de aprendizado, que pode(m) ser(em) definida(s) por initial_learning_rate

```python
print("tensorboard --logdir={}".format(my_detector.train_dir))

# to get good results, use a larger value for num_steps, e.g., 5000.
num_steps = 350
learning_rate = 0.001 # learning rate

start_train = time.time()
my_detector.train(dataset=data_train, num_steps=num_steps, 
                  initial_learning_rate=learning_rate)
end_train = time.time()
print(end_train-start_train)
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\train
    F1 2018-05-25 23:12:22,764 INFO azureml.vision:Fit starting in experiment  1125722225 
    F1 2018-05-25 23:12:22,767 INFO azureml.vision:model starting trainging for scenario=detection 
    Using existing checkpoint file that's saved at 'C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\models\detection\faster_rcnn_resnet50_coco_2018_01_28\model.ckpt.index'.
    TFRecords creation started.
    F1 2018-05-25 23:12:22,773 INFO On image 0 of 25
    TFRecords creation completed.
    Training started.
    Training progressing: step 0 ...
    Training progressing: step 100 ...
    Training progressing: step 200 ...
    Training progressing: step 300 ...
    F1 2018-05-25 23:18:02,730 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:18:10,722 INFO 2953 ops in the final graph.
    F1 2018-05-25 23:18:24,244 INFO azureml.vision:Fit finished in experiment  1125722225 
    Training completed.
    361.604615688324
    

TensorBoard pode ser usado para visualizar o andamento do treinamento. Os eventos TensorBoard estão localizados na pasta especificada pelo atributo train_dir do objeto do modelo. Para exibir o TensorBoard, siga estas etapas:
1. Copie a impressão que inicia com 'tensorboard --logdir' para uma linha de comando e execute-a. 
2. Copie a URL retornada da linha de comando para um navegador da Web para exibir o TensorBoard. 

O TensorBoard deve ser semelhante seguinte à captura de tela. Levará alguns instantes para a pasta de treinamento ser preenchida. Portanto, se o TensorBoard não aparecer corretamente na primeira vez, tente repetir as etapas de 1 a 2.  

![tensorboard](media/how-to-build-deploy-object-detection-models/tensorboard.JPG)

## <a name="evaluate-the-model"></a>Avalie o modelo

O método 'evaluate' é usado para avaliar o modelo. Essa função requer um objeto ObjectDetectionDataset como uma entrada. O conjunto de dados de avaliação pode ser criado usando a mesma função usada para o conjunto de dados de treinamento. A métrica com suporte é Precisão Média, conforme definido para o [Desafio PASCAL VOC](http://host.robots.ox.ac.uk/pascal/VOC/pubs/everingham10.pdf).  


```python
image_folder = "detection/sample_data/foods/test"
data_val = ObjectDetectionDataset.create_from_dir(dataset_name='val_dataset', data_dir=image_folder)
eval_result = my_detector.evaluate(dataset=data_val)
```

    F1 2018-05-25 23:18:24,253 INFO azureml.vision:dataset creating dataset for scenario=detection 
    F1 2018-05-25 23:18:24,286 INFO On image 0 of 5
    F1 2018-05-25 23:18:29,300 INFO Starting evaluation at 2018-05-26-03:18:29
    F1 2018-05-25 23:18:32,403 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:33,158 INFO Detection visualizations written to summary with tag image-0.
    F1 2018-05-25 23:18:33,518 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:34,342 INFO Detection visualizations written to summary with tag image-1.
    F1 2018-05-25 23:18:34,714 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:35,470 INFO Detection visualizations written to summary with tag image-2.
    F1 2018-05-25 23:18:35,835 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:36,654 INFO Detection visualizations written to summary with tag image-3.
    F1 2018-05-25 23:18:37,010 INFO Creating detection visualizations.
    F1 2018-05-25 23:18:37,798 INFO Detection visualizations written to summary with tag image-4.
    F1 2018-05-25 23:18:37,804 INFO Running eval batches done.
    F1 2018-05-25 23:18:37,805 INFO # success: 5
    F1 2018-05-25 23:18:37,806 INFO # skipped: 0
    F1 2018-05-25 23:18:38,119 INFO Writing metrics to tf summary.
    F1 2018-05-25 23:18:38,121 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/eggBox: 1.000000
    F1 2018-05-25 23:18:38,205 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/joghurt: 0.942857
    F1 2018-05-25 23:18:38,206 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/ketchup: 1.000000
    F1 2018-05-25 23:18:38,207 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mushroom: 1.000000
    F1 2018-05-25 23:18:38,208 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/mustard: 1.000000
    F1 2018-05-25 23:18:38,209 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/orange: 1.000000
    F1 2018-05-25 23:18:38,210 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/squash: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/PerformanceByCategory/AP@0.5IOU/water: 1.000000
    F1 2018-05-25 23:18:38,211 INFO PASCAL/Precision/mAP@0.5IOU: 0.992857
    F1 2018-05-25 23:18:38,253 INFO Metrics written to tf summary.
    F1 2018-05-25 23:18:38,254 INFO Finished evaluation!
    

Os resultados da avaliação podem ser impressos em um formato limpo.


```python
# print out the performance metric values
for label_obj in data_train.labels:
    label = label_obj.name
    key = 'PASCAL/PerformanceByCategory/AP@0.5IOU/' + label
    print('{0: <15}: {1: <3}'.format(label, round(eval_result[key], 2)))
print('{0: <15}: {1: <3}'.format("overall:", round(eval_result['PASCAL/Precision/mAP@0.5IOU'], 2))) 
```

    joghurt        : 0.94
    squash         : 1.0
    mushroom       : 1.0
    eggBox         : 1.0
    ketchup        : 1.0
    mustard        : 1.0
    water          : 1.0
    orange         : 1.0
    overall:       : 0.99
    

Da mesma forma, é possível calcular a precisão do modelo no conjunto de treinamento. Isso ajuda a garantir que o treinamento converta-se em uma boa solução. A precisão no conjunto de treinamento após o treinamento com êxito é próximo a 100%.

Os resultados da avaliação também podem ser exibidos no TensorBoard, incluindo valores de mAP e imagens com caixas delimitadoras previstas. Copie a impressão do código a seguir em uma janela de linha de comando para iniciar o cliente TensorBoard. Neste exemplo, um valor de porta 8008 é usado para evitar conflito com o valor padrão de 6006, que estava sendo usado para exibir o status do treinamento.


```python
print("tensorboard --logdir={} --port=8008".format(my_detector.eval_dir))
```

    tensorboard --logdir=C:\Users\lixun\Desktop\AutoDL\CVTK\Src\API\cvtk_output\temp_faster_rcnn_resnet50\models\eval --port=8008
    

## <a name="score-an-image"></a>Pontuar uma imagem

Quando estiver satisfeito com o desempenho do modelo treinado, a função "score" do objeto do modelo pode ser usada para pontuar novas imagens. As pontuações retornadas podem ser visualizadas com a função 'visualize'. 


```python
image_path = data_val.images[1].storage_path
detections_dict = my_detector.score(image_path)
path_save = "./scored_images/scored_image_preloaded.jpg"
ax = detection_utils.visualize(image_path, detections_dict, image_size=(8, 12))
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

![png](media/how-to-build-deploy-object-detection-models/output_20_0.JPG)

##  <a name="save-the-model"></a>Salvar o modelo

O modelo treinado pode ser salvo no disco e carregado de volta na memória, conforme mostrado nos exemplos de código a seguir.


```python
save_model_path = "./frozen_model/faster_rcnn.model"
my_detector.save(save_model_path)
```

    F1 2018-05-25 23:18:55,166 INFO Graph Rewriter optimizations enabled
    Converted 275 variables to const ops.
    F1 2018-05-25 23:19:03,867 INFO 2953 ops in the final graph.
    

## <a name="load-the-saved-model-for-scoring"></a>Carregar o modelo salvo para pontuação

Para usar o modelo salvo, carregue o modelo na memória com a função 'load'. É necessário carregar somente uma vez. 

```python
my_detector_loaded = TFFasterRCNN.load(save_model_path)
```

Depois que o modelo for carregado, ele poderá ser usado para pontuar uma imagem ou uma lista de imagens. Para uma única imagem, um dicionário é retornado com chaves como 'detection_boxes', 'detection_scores' e 'num_detections'. Se a entrada for uma lista de imagens, será retornada uma lista de dicionários, com um dicionário correspondente a uma imagem. 


```python
detections_dict = my_detector_loaded.score(image_path)
```

Os objetos detectados com pontuações acima de 0,5, incluindo rótulos, pontuações e coordenadas, podem ser impressos.


```python
look_up = dict((v,k) for k,v in my_detector.class_map.items())
n_obj = 0
for i in range(detections_dict['num_detections']):
    if detections_dict['detection_scores'][i] > 0.5:
        n_obj += 1
        print("Object {}: label={:11}, score={:.2f}, location=(top: {:.2f}, left: {:.2f}, bottom: {:.2f}, right: {:.2f})".format(
            i, look_up[detections_dict['detection_classes'][i]], 
            detections_dict['detection_scores'][i], 
            detections_dict['detection_boxes'][i][0],
            detections_dict['detection_boxes'][i][1], 
            detections_dict['detection_boxes'][i][2],
            detections_dict['detection_boxes'][i][3]))    
        
print("\nFound {} objects in image {}.".format(n_obj, image_path))           
```

    Object 0: label=squash     , score=0.99, location=(top: 0.74, left: 0.30, bottom: 0.84, right: 0.42)
    Object 1: label=squash     , score=0.98, location=(top: 0.27, left: 0.21, bottom: 0.37, right: 0.33)
    Object 2: label=orange     , score=0.98, location=(top: 0.31, left: 0.39, bottom: 0.37, right: 0.48)
    Object 3: label=joghurt    , score=0.98, location=(top: 0.57, left: 0.29, bottom: 0.67, right: 0.39)
    Object 4: label=eggBox     , score=0.97, location=(top: 0.41, left: 0.53, bottom: 0.49, right: 0.69)
    Object 5: label=water      , score=0.95, location=(top: 0.23, left: 0.51, bottom: 0.37, right: 0.57)
    Object 6: label=mustard    , score=0.88, location=(top: 0.61, left: 0.47, bottom: 0.66, right: 0.57)
    Object 7: label=ketchup    , score=0.80, location=(top: 0.62, left: 0.62, bottom: 0.68, right: 0.72)
    
    Found 8 objects in image ../sample_data/foods/test\JPEGImages\10.jpg.
    

Visualize as pontuações exatamente como antes.


```python
path_save = "./scored_images/scored_image_frozen_graph.jpg"
ax = detection_utils.visualize(image_path, detections_dict, path_save=path_save, image_size=(8, 12))
# ax.get_figure() # use this code extract the returned image
```

![png](media/how-to-build-deploy-object-detection-models/output_30_0.JPG)

## <a name="operationalization-deploy-and-consume"></a>Operacionalização: implantar e consumir

Operacionalização é o processo de publicação de modelos e código como serviços Web e o consumo desses serviços para produzir resultados comerciais. 

Depois que o modelo é treinado, você pode implantar o modelo como um serviço Web para consumo usando a [CLI do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Os modelos podem ser implantados em seu computador local ou de um cluster do ACS (Serviço de Contêiner do Azure). Usando o ACS, você pode dimensionar seu serviço Web manualmente ou usar a funcionalidade de dimensionamento automático.

**Entrar com a CLI do Azure**

Usando uma conta do [Azure](https://azure.microsoft.com/) com uma assinatura válida, faça logon usando o seguinte comando da CLI:
<br>`az login`

+ Para alternar para outra assinatura do Azure, use o comando:
<br>`az account set --subscription [your subscription name]`

+ Para ver a conta de gerenciamento de modelos atual, use o comando:
  <br>`az ml account modelmanagement show`

**Criar e configurar o ambiente de implantação de cluster**

Você só precisa definir o ambiente de implantação uma vez. Se você ainda não tem um, configure o ambiente de implantação agora usando [estas instruções](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

Para ver seu ambiente de implantação ativo, use o seguinte comando da CLI:
<br>`az ml env show`
   
Comando da CLI do Azure de exemplo para criar e configurar o ambiente de implantação

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>Gerenciar implantações e serviços Web

As APIs a seguir podem ser usadas para implantar modelos como serviços Web, gerenciar os serviços Web e gerenciar implantações.

|Tarefa|API|
|----|----|
|Criar objeto de implantação|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|Implantar serviço Web|`deploy_obj.deploy()`|
|Imagem de pontuação|`deploy_obj.score_image(local_image_path_or_image_url)`|
|Excluir serviço Web|`deploy_obj.delete()`|
|Criar imagem do Docker sem serviço Web|`deploy_obj.build_docker_image()`|
|Listar implantação existente|`AMLDeployment.list_deployment()`|
|Exclua se o serviço já existir com o nome da implantação|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**Documentação da API:** consulte a [documentação de referência do pacote](https://aka.ms/aml-packages/vision) para ver a referência detalhada de cada módulo e classe.

**Referência da CLI:** para operações mais avançadas relativas à implantação, consulte a [referência da CLI do gerenciamento de modelos](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference).

**Gerenciamento de implantação no portal do Azure**: você pode controlar e gerenciar suas implantações no [portal do Azure](https://ms.portal.azure.com/). No portal do Azure, localize a página da conta do Gerenciamento de Modelos do Machine Learning usando seu nome. Em seguida, vá para a página da conta do Gerenciamento de Modelos > Gerenciamento de Modelos > Serviços.

```python
# ##### OPTIONAL - Interactive CLI setup helper ###### 
# # Interactive CLI setup helper, including model management account and deployment environment.
# # If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
# # UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT

# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Create deployment object
# It will use the current deployment environment (you can check it with CLI command "az ml env show").
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=my_detector, replicas=1)

# Alternatively, you can provide azure machine learning deployment cluster name (environment name) and resource group name
# to deploy your model. It will use the provided cluster to deploy. To do that, please uncomment the following lines to create 
# the deployment object.

# azureml_rscgroup = "<resource group>"
# cluster_name = "<cluster name>"
# deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=my_detector,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# create the webservice
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Consumir o serviço Web

Após criar o serviço Web, você poderá pontuar imagens com o serviço Web implantado. Você tem várias opções:

   - É possível pontuar diretamente o serviço Web com o objeto de implantação com: deploy_obj.score_image(image_path_or_url) 
   - Ou, você pode usar a URL do ponto de extremidade do Serviço e a Chave de Serviço (Nenhuma para implantação local) com: AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
   - Forme as solicitações http diretamente para pontuar o ponto de extremidade do serviço Web (para usuários avançados).

### <a name="score-with-existing-deployment-object"></a>Pontuação de objeto de implantação existente
```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = data_train.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])

```


```python
# Time image scoring
import timeit

num_images = 3
for img_index, img_obj in enumerate(data_train.images[:num_images]):
    print("Calling API for image {} of {}: {}...".format(img_index, num_images, img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Pontuação com URL de ponto de extremidade de serviço e chave do serviço
```
    AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)
```


```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "http://xxx" # please replace with your own service url
service_key = "xxx" # please replace with your own service key

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json[:50])
```

### <a name="score-endpoint-with-http-request-directly"></a>Pontuar ponto de extremidade diretamente com solicitação HTTP
A seguir, é apresentado um código de exemplo para formar a solicitação http diretamente no Python. Você pode fazer isso em outras linguagens de programação.


```python
def score_image_with_http(image, service_endpoint_url, service_key=None, parameters={}):
    """Score local image with http request

    Args:
        image (str): Image file path
        service_endpoint_url(str): web service endpoint url
        service_key(str): Service key. None for local deployment.
        parameters (dict): Additional request paramters in dictionary. Default is {}.


    Returns:
        str: serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    import json

    if service_key is None:
        headers = {'Content-Type': 'application/json'}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key)}
    payload = []
    encoded = None
    
    # Read image
    with open(image,'rb') as f:
        image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        
    # Convert your image to base64 string
    # image_in_base64 : "b'{base64}'"
    encoded = base64.b64encode(image_buffer.getvalue())
    image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
    payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
        json.loads(result[0])
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result[0]

```

### <a name="parse-serialized-result-from-webservice"></a>Analisar o resultado serializado do serviço Web
O resultado do serviço Web está na cadeia de caracteres json que pode ser analisada.


```python
image_path_or_url = image_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json[:50])
```


```python
# Parse result from json string
import numpy as np
parsed_result = TFFasterRCNN.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
```


```python
ax = detection_utils.visualize(image_path, parsed_result)
path_save = "./scored_images/scored_image_web.jpg"
path_save_dir = os.path.dirname(os.path.abspath(path_save))
os.makedirs(path_save_dir, exist_ok=True)
ax.get_figure().savefig(path_save)
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Pacote do Azure Machine Learning para Pesquisa Visual Computacional nestes artigos:

+ Leia a [visão geral do pacote e aprenda a instalá-lo](https://aka.ms/aml-packages/vision).

+ Explore a [documentação de referência](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) do pacote.

+ Saiba mais sobre [outros pacotes do Python para o Azure Machine Learning](reference-python-package-overview.md).
