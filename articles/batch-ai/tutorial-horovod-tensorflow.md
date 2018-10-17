---
title: Tutorial – Treinamento distribuído com a IA do Lote do Azure e o Horovod | Microsoft Docs
description: Tutorial – Como treinar um modelo distribuído com o Horovod usando o serviço IA do Lote do Azure e a CLI do Azure.
services: batch-ai
author: johnwu10
manager: jeconnoc
ms.service: batch-ai
ms.topic: tutorial
ms.date: 09/03/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: de19b20865127fd37cd7bc1ac854288a95a68091
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44058066"
---
# <a name="tutorial-train-a-distributed-model-with-horovod"></a>Tutorial: Treinar um modelo distribuído com o Horovod

Neste tutorial, você treinará um modelo de aprendizado profundo distribuído executando-o em paralelo por vários nós em um cluster da IA do Lote. A IA do Lote é um serviço gerenciado para treinar modelos de IA e de aprendizado de máquina em grande escala em clusters de GPUs do Azure. 

Este tutorial apresenta um fluxo de trabalho comum da IA do Lote e mostra como interagir com os recursos da IA do Lote por meio da CLI do Azure. Os tópicos abordados incluem:

> [!div class="checklist"]
> * Configurar um espaço de trabalho da IA do Lote, um experimento e um cluster
> * Configurar um compartilhamento de arquivos do Azure para entrada e saída
> * Paralelizar um modelo de aprendizado profundo usando o Horovod
> * Enviar um trabalho de treinamento
> * Monitorar trabalho
> * Recuperar os resultados do treinamento

Para este tutorial, um modelo de detecção de objetos é modificado para ser executado em paralelo com o [Horovod](https://github.com/uber/horovod). O modelo treina com o [conjunto de dados de imagens CIFAR-10](https://www.cs.toronto.edu/~kriz/cifar.html). O trabalho de treinamento é executado em um cluster contendo 24 vCPUs e 4 GPUs e leva aproximadamente 60 minutos para ser concluído.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.38 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="why-use-horovod"></a>Por que usar o Horovod?

O Horovod é uma estrutura de treinamento distribuído para Tensorflow, Keras e PyTorch e é usado neste tutorial. Com o Horovod, você pode converter um script de treinamento projetado para ser executado em uma única GPU para um que seja executado com eficiência em um sistema distribuído usando apenas algumas linhas de código.

Além do Horovod, a IA do Lote dá suporte a treinamento distribuído com várias outras estruturas de software livre populares. Revise os termos de licença da estrutura que você usa para treinar modelos na produção.

## <a name="prepare-the-batch-ai-environment"></a>Preparar o ambiente da IA do Lote

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Use o comando `az group create` para criar um grupo de recursos chamado `batchai.horovod` na região `eastus`. Use o grupo de recursos para implantar recursos da IA do Lote.

```azurecli-interactive
az group create --name batchai.horovod --location eastus
```

### <a name="create-a-workspace"></a>Criar um espaço de trabalho

Crie um espaço de trabalho da IA do Lote usando o comando `az batchai workspace create`. Um espaço de trabalho é uma coleção de nível superior de outros recursos da IA do Lote. O comando a seguir cria um espaço de trabalho chamado `batchaidev` em seu grupo de recursos.

```azurecli-interactive
az batchai workspace create --resource-group batchai.horovod --workspace batchaidev 
```

### <a name="create-an-experiment"></a>Criar uma experiência

Um experimento da IA do Lote agrupa um ou mais trabalhos que você consulta e gerencia simultaneamente. O comando `az batchai experiment create` a seguir cria um experimento chamado `cifar` no grupo de recursos e no espaço de trabalho.

```azurecli-interactive
az batchai experiment create --resource-group batchai.horovod --workspace batchaidev --name cifar 
```

## <a name="set-up-a-gpu-cluster"></a>Configurar um cluster de GPU

Em seguida, configure um cluster de GPU para executar o experimento. A IA do Lote fornece uma gama flexível de opções de personalização de clusters para necessidades específicas.

O comando `az batchai cluster create` a seguir cria um cluster de quatro nós chamado `nc6cluster` em seu espaço de trabalho e grupo de recursos. Por padrão, as VMs no cluster executam uma imagem do Ubuntu Server projetada para hospedar aplicativos baseados em contêiner. Os nós de cluster neste exemplo usam o tamanho `Standard_NC6`, que contém uma GPU NVIDIA Tesla K80.

```azurecli-interactive
az batchai cluster create --resource-group batchai.horovod --workspace batchaidev --name nc6cluster --vm-priority dedicated  --vm-size Standard_NC6 --target 4 --generate-ssh-keys
```

Execute o comando `az batchai cluster show` para exibir o status do cluster. Ele geralmente leva alguns minutos para provisionar o cluster completamente.

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --output table
```

No início na criação do cluster, o cluster fica no estado `resizing`. Continue nas etapas a seguir enquanto o estado do cluster vai sendo alterado. O cluster estará pronto para executar o trabalho de treinamento quando o estado for `steady` e os nós estiverem `idle`. Por exemplo: 

```
Name        Resource Group    Workspace    VM Size       State      Idle    Running    Preparing    Leaving    Unusable
----------  ----------------  -----------  ------------  -------  ------  ---------  -----------  ---------  ----------
nc6cluster  batchai.horovod  batchaidev   STANDARD_NC6  steady        4          0            0          0           0
```

## <a name="set-up-storage"></a>Configurar o armazenamento

Use o comando `az storage account create` para criar uma conta de armazenamento a fim de armazenar o script de treinamento e a saída de treinamento.

```azurecli-interactive
az storage account create --resource-group batchai.horovod --name mystorageaccount --location eastus --sku Standard_LRS
```

Crie um compartilhamento de arquivos do Azure chamado `myshare` na conta usando o comando `az storage share create`:

```azurecli-interactive
az storage share create --name myshare --account-name mystorageaccount
```

Na prática, esse mesmo armazenamento pode ser usado em vários trabalhos e experimentos. Para manter tudo organizado, crie um diretório dentro do compartilhamento de arquivos para armazenar arquivos relacionados a esse experimento específico. O comando `az storage directory create` a seguir cria um diretório chamado `cifar`.

```azurecli-interactive
az storage directory create --name cifar --share-name myshare --account-name mystorageaccount
```

A próxima etapa é preparar o script de treinamento real, que você carregará no diretório que acabou de ser criado.

## <a name="create-the-training-script"></a>Criar o script de treinamento

Para este experimento, execute um script Python que é atualizado com algumas alterações para executar um modelo de detecção de objeto em paralelo usando o Horovod. O [modelo original](https://raw.githubusercontent.com/keras-team/keras/master/examples/cifar10_cnn.py) usa o Keras com um back-end TensorFlow. 

Em um diretório de trabalho no seu shell, use o seu editor de texto favorito para criar um arquivo chamado `cifar_cnn_distributed.py` com o conteúdo mostrado a seguir. As alterações no código-fonte original são comentadas com um prefixo `HOROVOD`.

```python
from __future__ import print_function
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D
import tensorflow as tf
import horovod.keras as hvd
import os
from keras import backend as K
import math
import argparse 

# HOROVOD: initialize Horovod.
hvd.init()

# HOROVOD: pin GPU to be used to process local rank (one GPU per process)
config = tf.ConfigProto()
config.gpu_options.allow_growth = True
config.gpu_options.visible_device_list = str(hvd.local_rank())
K.set_session(tf.Session(config=config))

batch_size = 32
num_classes = 10
# HOROVOD: adjust number of epochs based on number of GPUs.
epochs = int(math.ceil(100.0 / hvd.size()))

data_augmentation = True
num_predictions = 20
# BATCH AI: change save directory to mounted storage path
parser = argparse.ArgumentParser()
parser.add_argument("-d", "--dir", help="directory to save model to")
args = parser.parse_args()
save_dir = os.path.join(args.dir, 'saved_models')
model_name = 'keras_cifar10_trained_model.h5'

# The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# Convert class vectors to binary class matrices.
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, (3, 3), padding='same',
                 input_shape=x_train.shape[1:]))
model.add(Activation('relu'))
model.add(Conv2D(32, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Conv2D(64, (3, 3), padding='same'))
model.add(Activation('relu'))
model.add(Conv2D(64, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(512))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes))
model.add(Activation('softmax'))

# HOROVOD: adjust learning rate based on number of GPUs.
opt = keras.optimizers.rmsprop(lr=0.0001 * hvd.size(), decay=1e-6)

# HOROVOD: add Horovod Distributed Optimizer.
opt = hvd.DistributedOptimizer(opt)

# Let's train the model using RMSprop
model.compile(loss='categorical_crossentropy',
              optimizer=opt,
              metrics=['accuracy'])

callbacks = [
    # HOROVOD: broadcast initial variable states from rank 0 to all other processes.
    # This is necessary to ensure consistent initialization of all workers when
    # training is started with random weights or restored from a checkpoint.
    hvd.callbacks.BroadcastGlobalVariablesCallback(0),
]

# HOROVOD: save checkpoints only on worker 0 to prevent other workers from corrupting them.
if hvd.rank() == 0:
    callbacks.append(keras.callbacks.ModelCheckpoint('./checkpoint-{epoch}.h5'))

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255

if not data_augmentation:
    print('Not using data augmentation.')
    model.fit(x_train, y_train,
              batch_size=batch_size,
              epochs=epochs,
              validation_data=(x_test, y_test),
              shuffle=True)
else:
    print('Using real-time data augmentation.')
    # This will do preprocessing and realtime data augmentation:
    datagen = ImageDataGenerator(
        featurewise_center=False,  # set input mean to 0 over the dataset
        samplewise_center=False,  # set each sample mean to 0
        featurewise_std_normalization=False,  # divide inputs by std of the dataset
        samplewise_std_normalization=False,  # divide each input by its std
        zca_whitening=False,  # apply ZCA whitening
        zca_epsilon=1e-06,  # epsilon for ZCA whitening
        rotation_range=0,  # randomly rotate images in the range (degrees, 0 to 180)
        width_shift_range=0.1,  # randomly shift images horizontally (fraction of total width)
        height_shift_range=0.1,  # randomly shift images vertically (fraction of total height)
        shear_range=0.,  # set range for random shear
        zoom_range=0.,  # set range for random zoom
        channel_shift_range=0.,  # set range for random channel shifts
        fill_mode='nearest',  # set mode for filling points outside the input boundaries
        cval=0.,  # value used for fill_mode = "constant"
        horizontal_flip=True,  # randomly flip images
        vertical_flip=False,  # randomly flip images
        rescale=None,  # set rescaling factor (applied before any other transformation)
        preprocessing_function=None,  # set function that will be applied on each input
        data_format=None,  # image data format, either "channels_first" or "channels_last"
        validation_split=0.0)  # fraction of images reserved for validation (strictly between 0 and 1)

    # Compute quantities required for feature-wise normalization
    # (std, mean, and principal components if ZCA whitening is applied).
    datagen.fit(x_train)

    # Fit the model on the batches generated by datagen.flow().
    model.fit_generator(datagen.flow(x_train, y_train,
                                     batch_size=batch_size),
                        epochs=epochs,
                        validation_data=(x_test, y_test),
                        workers=4)

# Save model and weights
if not os.path.isdir(save_dir):
    os.makedirs(save_dir)
model_path = os.path.join(save_dir, model_name)
model.save(model_path)
print('Saved trained model at %s ' % model_path)

# Score trained model.
scores = model.evaluate(x_test, y_test, verbose=1)
print('Test loss:', scores[0])
print('Test accuracy:', scores[1])
```

Conforme mostrado neste exemplo, são necessárias apenas algumas atualizações no modelo para habilitar o treinamento distribuído usando a estrutura Horovod. 

Tenha em mente que esse script usa um modelo relativamente pequeno e um conjunto de dados para fins de demonstração e, portanto, esse modelo distribuído não necessariamente mostrará um aumento significativo de desempenho. Para realmente ver o poder do treinamento distribuído, use um modelo e um conjunto de dados muito maiores.

## <a name="upload-the-training-script"></a>Carregar o script de treinamento

Quando o script estiver pronto, a próxima etapa é fazer seu upload para o diretório de compartilhamento de arquivos que você criou anteriormente. O comando `az storage file upload` a seguir faz o upload do diretório de trabalho local para o local apropriado.

```azurecli-interactive
az storage file upload --path cifar --share-name myshare --source cifar_cnn_distributed.py --account-name mystorageaccount
```

## <a name="submit-the-training-job"></a>Enviar o trabalho de treinamento

Depois de concluir as etapas anteriores, crie um trabalho de treinamento. Na IA do Lote, você usa um arquivo `job.json` para definir os parâmetros de como executar um trabalho. Usando seu editor de texto favorito, crie um arquivo de configuração de trabalho chamado `job.json` com o conteúdo a seguir.

```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-05-01/job.json",
    "properties": {
        "nodeCount": 4,
        "horovodSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar/cifar_cnn_distributed.py",
            "commandLineArgs": "--dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare/cifar",
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/myshare",
                    "relativeMountPath": "myshare"
                }
            ]
        },
        "jobPreparation": {
            "commandLine": "apt update; apt install mpi-default-dev mpi-default-bin -y; pip install keras horovod"
        },
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "tensorflow/tensorflow:1.8.0-gpu"
            }
        }
    }
}
```

Explicação das propriedades:

| Propriedade | DESCRIÇÃO |
| --------- | --------- |
| `nodeCount` | O número de nós que serão dedicados para o trabalho. Aqui, o trabalho será executado em paralelo nos `4` nós. |
| `horovodSettings` | O campo `pythonScriptFilePath` define o caminho para o script do Horovod, localizado no diretório `cifar` criado anteriormente. O campo `commandLineArgs` contém os argumentos de linha de comando para executar o script. Para esse experimento, o diretório onde o modelo deve ser salvo é o único argumento necessário. `$AZ_BATCHAI_JOB_MOUNT_ROOT/myshare` é o caminho no qual o compartilhamento de arquivos foi montado. | 
| `stdOutErrPathPrefix` | O caminho para armazenar as saídas e os logs do trabalho, que para este exemplo é o mesmo diretório `cifar`. |
| `jobPreparation` | Todas as instruções especiais, se houver, para preparar o ambiente de execução do trabalho. Esse script requer a instalação dos pacotes MPI e Horovod indicados. |
| `containerSettings` | Configurações para o contêiner em que o trabalho é executado. Esse trabalho usa um contêiner do Docker criado com `tensorflow`.

Usando a configuração, crie o trabalho usando o comando `az batchai job create`. O comando a seguir enfileira um novo trabalho chamado `cifar_distributed` usando todos os recursos que foram configurados até este ponto. 

```azurecli-interactive
az batchai job create --cluster nc6cluster --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --experiment cifar --config-file job.json --storage-account-name mystorageaccount
```

Se os nós estiverem ocupados no momento, o trabalho poderá demorar um pouco antes de iniciar a execução. Use o comando `az batchai job show` para exibir o estado de execução do trabalho.

```azurecli-interactive
az batchai job show --experiment cifar --name cifar_distributed --resource-group batchai.horovod --workspace batchaidev --query "executionState"
```

### <a name="visualize-the-distributed-training"></a>Visualizar o treinamento distribuído

Depois que o trabalho começar a ser executado, use o comando `az batchai cluster show` novamente para consultar o status dos nós do cluster. 

```azurecli-interactive
az batchai cluster show --name nc6cluster --workspace batchaidev --resource-group batchai.horovod --query "nodeStateCounts"
```

A saída deve ser semelhante à encontrada abaixo, que mostra todos os quatro em estado de execução. Esse resultado mostra que todos os quatro nós estão sendo utilizados atualmente no treinamento distribuído.

```
{
  "idleNodeCount": 0,
  "leavingNodeCount": 0,
  "preparingNodeCount": 0,
  "runningNodeCount": 4,
  "unusableNodeCount": 0
}
```

## <a name="monitor-the-job"></a>Monitorar trabalho

### <a name="list-output-files"></a>Listar arquivos de saída

Enquanto o trabalho está em execução, use o comando `az batchai job file list` para listar os arquivos de saída que o trabalho gera.

```azurecli-interactive
az batchai job file list --experiment cifar --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev --output table
```

Para esse experimento específico, a saída deve ficar mais ou menos assim. A saída geral para o trabalho é registrada em `stdout.txt`, ao passo que `stderr.txt` gera todos os erros que ocorrem na execução principal. Os outros arquivos são logs de saída, erro e preparação de trabalho correspondentes a cada nó individualmente.

```
Name                                                    Type       Size  Modified
------------------------------------------------------  ------  -------  -------------------------
execution-tvm-676767296_1-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_2-20180718t174802z-p.log        file      15094  2018-07-18T22:41:55+00:00
execution-tvm-676767296_3-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
execution-tvm-676767296_4-20180718t174802z-p.log        file       8801  2018-07-18T22:41:28+00:00
stderr-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file        238  2018-07-18T22:41:50+00:00
stderr.txt                                              file       7653  2018-07-18T22:46:32+00:00
stdout-job_prep-tvm-676767296_1-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout-job_prep-tvm-676767296_2-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_3-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:54+00:00
stdout-job_prep-tvm-676767296_4-20180718t174802z-p.txt  file      13651  2018-07-18T22:41:55+00:00
stdout.txt                                              file    2316480  2018-07-18T22:46:32+00:00
```

### <a name="stream-an-output-file"></a>Transmitir um arquivo de saída

Use o comando `az batchai job file stream` para transmitir o conteúdo de um arquivo. O exemplo a seguir transmite o log de saída principal.

```azurecli-interactive
az batchai job file stream --experiment cifar --file-name stdout.txt --job cifar_distributed --resource-group batchai.horovod --workspace batchaidev
```

Enquanto o trabalho é executado, o comando transmite a saída padrão do trabalho de treinamento, mostrando uma saída mais ou menos assim.

```
...
50000 train samples
10000 test samples
Using real-time data augmentation.
Epoch 1/25


   1/1563 [..............................] - ETA: 2:42:25 - loss: 2.3334 - acc: 0.0312   1/1563 [..............................] - ETA: 2:30:42 - loss: 2.2973 - acc: 0.0938
   1/1563 [..............................] - ETA: 30:36 - loss: 2.3175 - acc: 0.1250
   1/1563 [..............................] - ETA: 2:32:58 - loss: 2.3489 - acc: 0.0625
   2/1563 [..............................] - ETA: 1:21:59 - loss: 2.3230 - acc: 0.0625

   2/1563 [..............................]   2/1563 [..............................] - ETA: 1:16:09 - loss: 2.2913 - acc: 0.0938 - ETA: 1:17:15 - loss: 2.3147 - acc: 0.0781
   2/1563 [..............................] - ETA: 16:07 - loss: 2.3678 - acc: 0.0938
   3/1563 [..............................] - ETA: 55:05 - loss: 2.3232 - acc: 0.0938  
   3/1563 [..............................] - ETA: 51:57 - loss: 2.3185 - acc: 0.1146  
   3/1563 [..............................] - ETA: 51:12 - loss: 2.3179 - acc: 0.1042  
   3/1563 [..............................] - ETA: 11:13 - loss: 2.3504 - acc: 0.0833
   4/1563 [..............................] - ETA: 39:43 - loss: 2.3224 - acc: 0.1094
   4/1563 [..............................] - ETA: 42:09 - loss: 2.3049 - acc: 0.1250
   4/1563 [..............................] - ETA: 39:15 - loss: 2.3089 - acc: 0.1094
   4/1563 [..............................] - ETA: 9:16 - loss: 2.3316 - acc: 0.1016 
   5/1563 [..............................] - ETA: 39:51 - loss: 2.3153 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:58 - loss: 2.3197 - acc: 0.1125
   5/1563 [..............................] - ETA: 37:35 - loss: 2.3148 - acc: 0.1062
   5/1563 [..............................] - ETA: 13:38 - loss: 2.3263 - acc: 0.1062
   6/1563 [..............................] - ETA: 35:48 - loss: 2.3168 - acc: 0.1198

   6/1563 [..............................]   6/1563 [..............................] - ETA: 34:13 - loss: 2.3142 - acc: 0.1198 - ETA: 33:51 - loss: 2.3162 - acc: 0.1042
   6/1563 [..............................] - ETA: 13:54 - loss: 2.3225 - acc: 0.1094
   7/1563 [..............................] - ETA: 30:53 - loss: 2.3181 - acc: 0.1071

   7/1563 [..............................]   7/1563 [..............................] - ETA: 29:32 - loss: 2.3149 - acc: 0.1161 - ETA: 29:13 - loss: 2.3140 - acc: 0.0938
   7/1563 [..............................] - ETA: 12:09 - loss: 2.3174 - acc: 0.1205
   8/1563 [..............................] - ETA: 26:04 - loss: 2.3113 - acc: 0.1133
   8/1563 [..............................] - ETA: 27:15 - loss: 2.3169 - acc: 0.1133
   8/1563 [..............................] - ETA: 10:51 - loss: 2.3152 - acc: 0.1172
...
```

O script treina por mais de 25 épocas ou passa pelo conjunto de dados de treinamento. Esse processo leva aproximadamente 60 minutos. 

## <a name="retrieve-the-results"></a>Recuperar os resultados

Quando o script for concluído, caso tudo tenha corrido bem, a precisão de validação deverá ser cerca de 70% a 75% e o modelo treinado será salvo no compartilhamento de arquivos em `cifar/saved_models/keras_cifar10_trained_model.h5`. 

O treinamento do modelo geralmente é parte de um fluxo de trabalho maior. Por exemplo, você pode expor o modelo treinado em outro aplicativo. Para baixar o modelo treinado localmente, use o comando `az storage file download`. 

```azurecli-interactive
az storage file download --path cifar/saved_models/keras_cifar10_trained_model.h5 --share-name myshare --account-name mystorageaccount
```
## <a name="clean-up-resources"></a>Limpar recursos

Depois que a execução dos trabalhos é concluída, uma melhor prática para economizar em custos de computação é reduzir todos os clusters a `0 nodes` para não haver cobrança por tempo ocioso. Use o comando `az batchai cluster resize` a seguir. 

```azurecli-interactive
az batchai cluster resize --name nc6cluster --resource-group batchai.horovod --target 0 --workspace batchaidev
```

Depois, redimensione para um ou mais nós a fim de executar os trabalhos. 

Se você não planeja usar a espaço de trabalho e a conta de armazenamento no futuro, exclua o grupo de recursos usando o comando `az group delete`. Excluir um grupo de recursos exclui todos os recursos que fazem parte desse grupo.

```azurecli-interactive
az group delete --name batchai.horovod
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configurar um espaço de trabalho da IA do Lote, um experimento e um cluster
> * Configurar um compartilhamento de arquivos do Azure para entrada e saída
> * Paralelizar um modelo usando o Horovod
> * Enviar um trabalho de treinamento
> * Monitorar trabalho
> * Recuperar os resultados do treinamento

Para obter exemplos de como usar a IA do Lote com estruturas diferentes, consulte as receitas no GitHub.

> [!div class="nextstepaction"]
> [Receitas da IA do Lote](https://github.com/Azure/BatchAI/tree/master/recipes)
