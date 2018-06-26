---
title: Criar e implantar um modelo de classificação de imagem usando o Pacote do Azure Machine Learning para Pesquisa Visual Computacional.
description: Saiba como criar, treinar, testar e implantar um modelo de classificação de imagem de pesquisa visual computacional usando o Pacote do Azure Machine Learning para Pesquisa Visual Computacional.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: 72f5215bac9254c9e3295b2cade7b6d44d516af6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637728"
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>Criar e implantar modelos de classificação de imagem com o Azure Machine Learning

Neste artigo, saiba como usar o **AMLPCV** (Pacote do Azure Machine Learning para Pesquisa Visual Computacional) para treinar, testar e implantar um modelo de classificação de imagem. 

Vários problemas no domínio da pesquisa visual computacional podem ser resolvidos por meio da classificação de imagem. Esses problemas incluem a criação de modelos que respondem a perguntas como:
+ _Um OBJETO está presente na imagem? Por exemplo, "cachorro", "carro", "barco" e assim por diante_
+ _Qual classe de gravidade de doença ocular é comprovada pelo exame de retina desse paciente?_

Ao compilar e implantar esse modelo com o AMLPCV, percorra as etapas abaixo:
1. Criação do conjunto de dados
2. Visualização e anotação da imagem
3. Aumento de imagem
4. Definição de modelo DNN (Rede Neural Profunda)
5. Treinamento do classificador
6. Avaliação e visualização
7. Implantação do serviço Web
8. Teste de carga do serviço Web

O [CNTK](https://www.microsoft.com/cognitive-toolkit/) é usado como a estrutura de aprendizado profundo, o treinamento é realizado localmente em um computador com GPU, como a ([VM de ciência de dados de aprendizado profundo](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)) e a implantação usa a CLI de operacionalização do Azure ML.

Consulte a [documentação de referência do pacote](https://aka.ms/aml-packages/vision) para ver a referência detalhada de cada módulo e classe.

## <a name="prerequisites"></a>pré-requisitos

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
> [Obter o Jupyter Notebook](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>Carregar os dados de exemplo

O exemplo a seguir usa um conjunto de dados que consiste em 63 imagens de utensílios de mesa. Cada imagem é rotulada como pertencente a uma de quatro classes (tigela, copo, talher, prato). O número de imagens neste exemplo é pequeno para que ele possa ser executado rapidamente. Na prática, pelo menos cem imagens por classe devem ser fornecidas. Todas as imagens estão localizadas em *"../sample_data/imgs_recycling/"*, em subdiretórios chamados"tigela","copo","talher"e"prato".

![Conjunto de dados do Azure Machine Learning](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="storage-context"></a>Contexto de armazenamento

O contexto de armazenamento é usado para determinar onde vários arquivos de saída, como imagens aumentadas ou arquivos de modelo DNN, serão armazenados. Para saber mais sobre os contextos de armazenamento, confira a [documentação StorageContext](https://review.docs.microsoft.com/en-us/python/api/cvtk.core.context.storagecontext?view=azure-python&branch=smoke-test). 

Normalmente, o conteúdo de armazenamento não precisa ser definido explicitamente. No entanto, para evitar o limite de 25 MB de tamanho de projeto imposto pelo Azure Machine Learning Workbench, defina o diretório de saídas do Pacote do Azure Machine Learning para Pesquisa Visual Computacional como um local fora do projeto do Azure Machine Learning ("../../../../cvtk_output"). Remova o diretório "cvtk_output" quando ele não for mais necessário.


```python
import warnings
warnings.filterwarnings("ignore")
import json, numpy as np, os, timeit 
from azureml.logging import get_azureml_logger
from imgaug import augmenters
from IPython.display import display
from sklearn import svm
from cvtk import ClassificationDataset, CNTKTLModel, Context, Splitter, StorageContext
from cvtk.augmentation import augment_dataset
from cvtk.core.classifier import ScikitClassifier
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix, basic_plot
import matplotlib.pyplot as plt
%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

# Set storage context.
out_root_path = "../../../cvtk_output"
Context.create(outputs_path=out_root_path, persistent_path=out_root_path, temp_path=out_root_path)
```




    {
        "storage": {
            "outputs_path": "../../../cvtk_output",
            "persistent_path": "../../../cvtk_output",
            "temp_path": "../../../cvtk_output"
        }
    }



## <a name="create-a-dataset"></a>Criar um conjunto de dados

Quando você tiver importado as dependências e definido o contexto de armazenamento, poderá criar o objeto de conjunto de dados.

Para criar o objeto com o Pacote do Azure Machine Learning para Pesquisa Visual Computacional, forneça o diretório raiz das imagens no disco local. Esse diretório deve seguir a mesma estrutura geral do conjunto de dados de utensílios de mesa, ou seja, conter subdiretórios com as imagens reais:
- root
    - rótulo 1
    - rótulo 2
    - ...
    - rótulo n
  
O treinamento de um modelo de classificação de imagem para um conjunto de dados diferente é fácil: basta alterar o caminho raiz `dataset_location` no código a seguir para apontar para imagens diferentes.


```python
# Root image directory 
dataset_location = os.path.abspath(os.path.join(os.getcwd(), "../sample_data/imgs_recycling"))

dataset_name = 'recycling'
dataset = ClassificationDataset.create_from_dir(dataset_name, dataset_location)
print("Dataset consists of {} images with {} labels.".format(len(dataset.images), len(dataset.labels)))
print("Select information for image 2: name={}, label={}, unique id={}.".format(
    dataset.images[2].name, dataset.images[2]._labels[0].name, dataset.images[2]._storage_id))
```

    F1 2018-04-23 17:12:57,593 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-04-23 17:12:57,599 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Dataset consists of 63 images with 4 labels.
    Select information for image 2: name=msft-plastic-bowl20170725152154282.jpg, label=bowl, unique id=3.

O objeto de conjunto de dados fornece funcionalidade de download de imagens usando a [API de Pesquisa de Imagem do Bing](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/). 

Há suporte para dois tipos de consulta de pesquisa: 
+ Consultas de texto normal
+ Consultas de URL de imagem

Essas consultas, junto com o rótulo de classe, devem ser fornecidas em um arquivo de texto codificado em JSON. Por exemplo: 

```json
{
    "bowl": [
                    "plastic bowl",
                    "../imgs_recycling/bowl"
    ],
    "cup": [
                    "plastic cup",
                    "../imgs_recycling/cup",
                    "http://cdnimg2.webstaurantstore.com/images/products/main/123662/268841/dart-solo-ultra-clear-conex-tp12-12-oz-pet-plastic-cold-cup-1000-case.jpg"
    ],
    "cutlery": [
                    "plastic cutlery",
                    "../imgs_recycling/cutlery",
                    "http://img4.foodservicewarehouse.com/Prd/1900SQ/Fineline_2514-BO.jpg"
    ],
    "plate": [
                    "plastic plate",
                    "../imgs_recycling/plate"
    ]
}
```

Além disso, você deve criar explicitamente um objeto de contexto para conter a chave da API de Pesquisa de Imagem do Bing. Isso requer uma assinatura da API de Pesquisa de Imagem do Bing.

## <a name="visualize-and-annotate-images"></a>Visualizar e anotar imagens

Você pode visualizar as imagens e corrigir os rótulos no objeto de conjunto de dados usando o widget a seguir. 

Se você receber o erro "Widget Javascript não detectado", execute este comando para resolvê-lo:
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
from ui_utils.ui_annotation import AnnotationUI
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Conjunto de dados do Azure Machine Learning](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>Ampliar imagens

O [`augmentation`módulo](https://docs.microsoft.com/en-us/python/api/cvtk.augmentation) fornece funcionalidade de ampliação de objeto de conjunto de dados usando todas as transformações descritas na biblioteca [imgaug](https://github.com/aleju/imgaug). As transformações de imagem podem ser agrupadas em um único pipeline; nesse caso, todas as transformações no pipeline serão aplicadas simultaneamente a cada imagem. 

Se você deseja aplicar as diferentes etapas de aumento separadamente ou de forma diferente, pode definir vários pipelines e passá-los para a função *augment_dataset*. Para saber mais e obter exemplos de aumento de imagem, confira a [documentação de imgaug](https://github.com/aleju/imgaug).

A adição de imagens aumentadas ao conjunto de treinamento é especialmente útil para pequenos conjuntos de dados. Como o processo de treinamento da DNN é mais lento devido ao maior número de imagens de treinamento, recomendamos iniciar a experimentação sem aumento.


```python
# Split the dataset into train and test  
train_set_orig, test_set = dataset.split(train_size = 0.66, stratify = "label")
print("Number of training images = {}, test images = {}.".format(train_set_orig.size(), test_set.size()))
```

    F1 2018-04-23 17:13:01,780 INFO azureml.vision:splitter splitting a dataset 
    F1 2018-04-23 17:13:01,805 INFO azureml.vision:dataset creating dataset for scenario=classification 
    F1 2018-04-23 17:13:01,809 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Number of training images = 41, test images = 22.
    


```python
augment_train_set = False

if augment_train_set:
    aug_sequence = augmenters.Sequential([
            augmenters.Fliplr(0.5),             # horizontally flip 50% of all images
            augmenters.Crop(percent=(0, 0.1)),  # crop images by 0-10% of their height/width
        ])
    train_set = augment_dataset(train_set_orig, [aug_sequence])
    print("Number of original training images = {}, with augmented images included = {}.".format(train_set_orig.size(), train_set.size()))
else:
    train_set = train_set_orig  
```

## <a name="define-dnn-models"></a>Definir modelos DNN

Os seguintes modelos de Rede Neural Profunda pré-treinados podem ser usados com esse pacote: 
+ Resnet-18
+ Resnet-34
+ Resnet-50
+ Resnet-101
+ Resnet-152

Essas DNNs podem ser usadas como classificador ou transformador de recurso. 

Encontre mais informações sobre as redes [aqui](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md), e uma introdução básica ao Aprendizado de Transferência [aqui](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0).

Os parâmetros de classificação de imagem padrão para o pacote são uma DNN Resnet-18 e resolução de pixels de 224 x 224. Esses parâmetros foram selecionados para funcionar bem com uma ampla variedade de tarefas. A precisão geralmente pode ser melhorada, por exemplo, com o aumento da resolução de imagem para 500 x 500 pixels e/ou a escolha de um modelo mais profundo (Resnet-50). No entanto, a alteração dos parâmetros gera um aumento significativo no tempo de treinamento. Consulte o artigo sobre [Como melhorar a precisão](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models).


```python
# Default parameters (224 x 224 pixels resolution, Resnet-18)
lr_per_mb = [0.05]*7 + [0.005]*7 +  [0.0005]
mb_size = 32
input_resoluton = 224
base_model_name = "ResNet18_ImageNet_CNTK"

# Suggested parameters for 500 x 500 pixels resolution, Resnet-50
# (see in the Appendix "How to improve accuracy", last row in table)
# lr_per_mb   = [0.01] * 7 + [0.001] * 7 + [0.0001]
# mb_size    = 8
# input_resoluton = 500
# base_model_name = "ResNet50_ImageNet_CNTK"

# Initialize model
dnn_model = CNTKTLModel(train_set.labels,
                       base_model_name=base_model_name,
                       image_dims = (3, input_resoluton, input_resoluton))
```

    Successfully downloaded ResNet18_ImageNet_CNTK
    

## <a name="train-the-classifier"></a>Treinar o classificador

Você pode escolher um dos seguintes métodos para a DNN previamente treinada.

  - **Refinamento de DNN**, que treina a DNN para executar a classificação diretamente. Embora o treinamento de DNN seja lento, ele normalmente gera os melhores resultados, já que todos os pesos de rede podem ser melhorados durante o treinamento para fornecer melhor precisão.

  - **Transformação de recursos de DNN**, que executa a DNN no estado em que se encontra para obter uma representação inferior dimensional de uma imagem (512, 2048 ou 4096 floats). Essa representação é usada como entrada para treinar um classificador diferente. Como a DNN é mantida inalterada, essa abordagem é muito mais rápida comparada ao ajuste de DNN; no entanto, a precisão não é tão boa. No entanto, o treinamento de um classificador externo, como um SVM linear (conforme mostrado no código a seguir) pode fornecer uma linha de base forte e ajudar a compreender a viabilidade de um problema.
  
TensorBoard pode ser usado para visualizar o andamento do treinamento. Para ativar o TensorBoard:
1. Adicione o parâmetro `tensorboard_logdir=PATH` conforme mostrado no código a seguir
1. Inicie o cliente TensorBoard usando o comando `tensorboard --logdir=PATH` em um novo console.
1. Abra um navegador conforme instruído pelo TensorBoard, que por padrão é localhost:6006. 


```python
# Train either the DNN or a SVM as classifier 
classifier_name = "dnn"

if classifier_name.lower() == "dnn":  
    dnn_model.train(train_set, lr_per_mb = lr_per_mb, mb_size = mb_size, eval_dataset=test_set) #, tensorboard_logdir=r"tensorboard"
    classifier = dnn_model
elif classifier_name.lower() == "svm":
    learner = svm.LinearSVC(C=1.0, class_weight='balanced', verbose=0)
    classifier = ScikitClassifier(dnn_model, learner = learner)
    classifier.train(train_set)
else:
    raise Exception("Classifier unknown: " + classifier)   
```

    F1 2018-04-23 17:13:28,238 INFO azureml.vision:Fit starting in experiment  1541466320 
    F1 2018-04-23 17:13:28,239 INFO azureml.vision:model starting training for scenario=classification 
    <class 'int'>
    1 worker
    Training transfer learning model for 15 epochs (epoch_size = 41).
    non-distributed mode
    Training 15741700 parameters in 53 parameter tensors.
    Training 15741700 parameters in 53 parameter tensors.
    Learning rate per minibatch: 0.05
    Momentum per minibatch: 0.9
    PROGRESS: 0.00%
    Finished Epoch[1 of 15]: [Training] loss = 2.820586 * 41, metric = 68.29% * 41 5.738s (  7.1 samples/s);
    Evaluation Set Error :: 29.27%
    Finished Epoch[2 of 15]: [Training] loss = 0.286728 * 41, metric = 9.76% * 41 0.752s ( 54.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[3 of 15]: [Training] loss = 0.206938 * 41, metric = 4.88% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 41.46%
    Finished Epoch[4 of 15]: [Training] loss = 0.098931 * 41, metric = 2.44% * 41 0.785s ( 52.2 samples/s);
    Evaluation Set Error :: 48.78%
    Finished Epoch[5 of 15]: [Training] loss = 0.046547 * 41, metric = 0.00% * 41 0.724s ( 56.6 samples/s);
    Evaluation Set Error :: 43.90%
    Finished Epoch[6 of 15]: [Training] loss = 0.059709 * 41, metric = 4.88% * 41 0.636s ( 64.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[7 of 15]: [Training] loss = 0.005817 * 41, metric = 0.00% * 41 0.710s ( 57.7 samples/s);
    Evaluation Set Error :: 14.63%
    Learning rate per minibatch: 0.005
    Finished Epoch[8 of 15]: [Training] loss = 0.014917 * 41, metric = 0.00% * 41 0.649s ( 63.2 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[9 of 15]: [Training] loss = 0.040539 * 41, metric = 2.44% * 41 0.777s ( 52.8 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[10 of 15]: [Training] loss = 0.024606 * 41, metric = 0.00% * 41 0.626s ( 65.5 samples/s);
    Evaluation Set Error :: 7.32%
    PROGRESS: 0.00%
    Finished Epoch[11 of 15]: [Training] loss = 0.004225 * 41, metric = 0.00% * 41 0.656s ( 62.5 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[12 of 15]: [Training] loss = 0.004364 * 41, metric = 0.00% * 41 0.702s ( 58.4 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[13 of 15]: [Training] loss = 0.007974 * 41, metric = 0.00% * 41 0.721s ( 56.9 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[14 of 15]: [Training] loss = 0.000655 * 41, metric = 0.00% * 41 0.711s ( 57.7 samples/s);
    Evaluation Set Error :: 4.88%
    Learning rate per minibatch: 0.0005
    Finished Epoch[15 of 15]: [Training] loss = 0.024865 * 41, metric = 0.00% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 4.88%
    Stored trained model at ../../../cvtk_output\model_trained\ImageClassification.model
    F1 2018-04-23 17:13:45,097 INFO azureml.vision:Fit finished in experiment  1541466320 
    


```python
# Plot how the training and test accuracy increases during gradient descent. 
if classifier_name == "dnn":
    [train_accs, test_accs, epoch_numbers] = classifier.train_eval_accs
    plt.xlabel("Number of training epochs") 
    plt.ylabel("Classification accuracy") 
    train_plot = plt.plot(epoch_numbers, train_accs, 'r-', label = "Training accuracy")
    test_plot = plt.plot(epoch_numbers, test_accs, 'b-.', label = "Test accuracy")
    plt.legend()
```

![png](media/how-to-build-deploy-image-classification-models/output_17_0.png)


## <a name="evaluate-and-visualize-model-performance"></a>Avaliar e visualizar o desempenho do modelo

Você pode avaliar o desempenho do modelo treinado em um conjunto de dados de teste independente usando o módulo de avaliação. Algumas das métricas de avaliação que ele computa incluem:
 
+ Precisão (por média de classe padrão)
+ Curva de PR
+ Curva ROC
+ Área em curva
+ Matriz de confusão


```python
# Run the classifier on all test set images
ce = ClassificationEvaluation(classifier, test_set, minibatch_size = mb_size)

# Compute Accuracy and the confusion matrix
acc = ce.compute_accuracy()
print("Accuracy = {:2.2f}%".format(100*acc))
cm  = ce.compute_confusion_matrix()
print("Confusion matrix = \n{}".format(cm))

# Show PR curve, ROC curve, and confusion matrix
fig, ((ax1, ax2, ax3)) = plt.subplots(1,3)
fig.set_size_inches(18, 4)
graph_roc_curve(ce, ax=ax1)
graph_pr_curve(ce, ax=ax2)
graph_confusion_matrix(ce, ax=ax3)
plt.show()
```

    F1 2018-04-23 17:14:37,449 INFO azureml.vision:evaluation doing evaluation for scenario=classification 
    F1 2018-04-23 17:14:37,450 INFO azureml.vision:model scoring dataset for scenario=classification 
    Accuracy = 95.45%
    Confusion matrix = 
    [[ 0  1  0  1]
     [ 0  7  0  0]
     [ 0  0  2  0]
     [ 0  0  0 11]]
    


![png](media/how-to-build-deploy-image-classification-models/output_20_1.png)



```python
# Results viewer UI
labels = [l.name for l in dataset.labels] 
pred_scores = ce.scores #classification scores for all images and all classes
pred_labels = [labels[i] for i in np.argmax(pred_scores, axis=1)]

from ui_utils.ui_results_viewer import ResultsUI
results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Conjunto de dados do Azure Machine Learning](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
from ui_utils.ui_precision_recall import PrecisionRecallUI
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Conjunto de dados do Azure Machine Learning](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

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
# ##### OPTIONAL###### 
# Interactive CLI setup helper, including model management account and deployment environment.
# If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
#
# UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT
#
# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
# # Optional. Persist your model on disk and reuse it later for deployment. 
# from cvtk import TFFasterRCNN, Context
# import os
# save_model_path = os.path.join(Context.get_global_context().storage.persistent_path, "saved_classifier.model")
# # Save model to disk
# dnn_model.serialize(save_model_path)
# # Load model from disk
# dnn_model = CNTKTLModel.deserialize(save_model_path)
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Optional Azure Machine Learning deployment cluster name (environment name) and resource group name
# If you don't provide here. It will use the current deployment environment (you can check with CLI command "az ml env show").
azureml_rscgroup = "<resource group>"
cluster_name = "<cluster name>"

# If you provide the cluster information, it will use the provided cluster to deploy. 
# Example: deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Create deployment object
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=dnn_model, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# Create the web service
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>Consumir o serviço Web 

Quando você implanta o modelo como um serviço Web, pode pontuar imagens com o serviço Web usando um dos seguintes métodos:

- Pontuar o serviço Web diretamente com o objeto de implantação usando `deploy_obj.score_image(image_path_or_url)`

- Usar o ponto de extremidade de URL e a chave de serviço (nenhuma para implantação local) com: `AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

- Forme suas solicitações HTTP diretamente para pontuar os pontos de extremidade do serviço Web. Essa opção é para usuários avançados.

### <a name="score-with-existing-deployment-object"></a>Pontuação de objeto de implantação existente

```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url with added paramters. Add softmax to score.
print("Score image url with added paramters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParamters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParamters.ADD_SOFTMAX:True})
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Time image scoring
import timeit

for img_index, img_obj in enumerate(test_set.images[:10]):
    print("Calling API for image {} of {}: {}...".format(img_index, len(test_set.images), img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
    print(return_json)
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>Pontuação com URL de ponto de extremidade de serviço e chave do serviço

`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "" # please replace with your own service url
service_key = "" # please replace with your own service key
# score local image with file path
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key)
print("serialized_result_in_json:", serialized_result_in_json)

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```

### <a name="score-endpoint-with-http-request-directly"></a>Pontuar ponto de extremidade diretamente com solicitação HTTP

O exemplo de código a seguir forma a solicitação HTTP diretamente no Python. No entanto, você pode fazer isso em outras linguagens de programação.


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional paramters in dictionary


    Returns:
        result (list): list of serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    routing_id = ""

    if service_key is None:
        headers = {'Content-Type': 'application/json',
                   'X-Marathon-App-Id': routing_id}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key), 'X-Marathon-App-Id': routing_id}
    payload = []
    for image in images:
        encoded = None
        # read image
        with open(image,'rb') as f:
            image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        # convert your image to base64 string
        encoded = base64.b64encode(image_buffer.getvalue())
        image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
        payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result

# Test with images
images = [test_set.images[0].storage_path, test_set.images[1].storage_path] # A list of local image files
score_image_list_with_http(images, service_endpoint_url, service_key)
```

### <a name="parse-serialized-result-from-web-service"></a>Analisar resultado serializado do serviço Web

A saída do serviço Web é uma cadeia de caracteres JSON. Você pode analisar essa cadeia de caracteres JSON com classes de modelo DNN diferentes.


```python
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Parse result from json string
import numpy as np
parsed_result = CNTKTLModel.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
# Map result to image class
class_index = np.argmax(np.array(parsed_result))
print("Class index:", class_index)
dnn_model.class_map
print("Class label:", dnn_model.class_map[class_index])
```


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Pacote do Azure Machine Learning para Pesquisa Visual Computacional nestes artigos:

+ Saiba como [melhorar a precisão desse modelo](how-to-improve-accuracy-for-computer-vision-models.md).

+ Leia a [visão geral do pacote e aprenda a instalá-lo](https://aka.ms/aml-packages/vision).

+ Explore a [documentação de referência](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision) do pacote.

+ Saiba mais sobre [outros pacotes do Python para o Azure Machine Learning](reference-python-package-overview.md).
