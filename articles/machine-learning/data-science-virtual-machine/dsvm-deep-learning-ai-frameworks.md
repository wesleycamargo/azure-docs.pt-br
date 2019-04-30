---
title: Estruturas de Aprendizado Profundo e AI – Azure | Microsoft Docs
description: Saiba mais sobre e estruturas de aprendizado profundo e ferramentas com suporte na Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: gokuma
ms.openlocfilehash: 59f88d54d3542738f1a500c8c476995eb1535ecf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130257"
---
# <a name="deep-learning-and-ai-frameworks"></a>Estruturas de Aprendizado Profundo e de AI
A DSVM ([Máquina Virtual de Ciência de Dados](https://aka.ms/dsvm)) e a [VM de Aprendizado Profundo](https://aka.ms/dsvm/deeplearning) dão suporte a várias estruturas de aprendizado profundo para ajudar a criar aplicativos de AI (Inteligência Artificial) com funcionalidades cognitivas e análise preditiva como entendimento de imagem e linguagem.

Aqui estão os detalhes sobre todas as estruturas de aprendizado profundo sobre na DSVM.

## <a name="microsoft-cognitive-toolkit"></a>Kit de Ferramentas Cognitivas da Microsoft

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Windows, Linux     |
| Como é configurado/instalado no DSVM?  | O Microsoft Cognitive Toolkit (CNTK) está instalado em Python 3.5 no [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e Python 3.6 no [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).   |
| Links para exemplos      | Exemplos de blocos de anotações do Jupyter estão incluídos.     |
| Ferramentas relacionadas ao DSVM      | Keras      |
| Como usar/executar?    | * Em um terminal: ative o ambiente correto, em seguida, execute o Python. <br/> *Em Jupyter: conecte-se ao [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), em seguida, abra o diretório CNTK para obter exemplos. |

## <a name="tensorflow"></a>TensorFlow

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Windows, Linux     |
| Como é configurado/instalado no DSVM?  | O TensorFlow está instalado em Python 3.5 no [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e Python 3.6 no [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition).  |
| Links para exemplos      | Exemplos de blocos de anotações do Jupyter estão incluídos.     |
| Ferramentas relacionadas ao DSVM      | Keras      |
| Como usar/executar?    | * Em um terminal: ative o ambiente correto, em seguida, execute o Python. <br/> *Em Jupyter: conecte-se ao [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), em seguida, abra o diretório TensorFlow para obter exemplos.  |

## <a name="horovod"></a>Horovod

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizagem profunda distribuída para TensorFlow      |
| Edições do DSVM com suporte      | Ubuntu     |
| Como é configurado/instalado no DSVM?  | Horovod está instalado em Python 3.5 no [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Links para exemplos      | [https://github.com/uber/horovod/tree/master/examples](https://github.com/uber/horovod/tree/master/examples)     |
| Ferramentas relacionadas ao DSVM      | TensorFlow      |
| Como usar/executar?    | Em um terminal: ative o ambiente correto, em seguida, execute o Python. |

## <a name="keras"></a>Keras

|    |           |
| ------------- | ------------- |
| O que é?   | API de aprendizado profundo de alto nível      |
| Edições do DSVM com suporte      | Windows, Linux     |
| Como é configurado/instalado no DSVM?  | O TensorFlow está instalado em Python 3.5 no [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e Python 3.6 no [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). |
| Links para exemplos      | https://github.com/fchollet/keras/tree/master/examples      |
| Ferramentas relacionadas ao DSVM      | Kit de Ferramentas Cognitivas Microsoft, TensorLlow, Theano      |
| Como usar/executar?    | * Em um terminal: ative o ambiente correto, em seguida, execute o Python. <br/> *Em Jupyter: baixe os exemplos da localização do GitHub, conecte-se ao [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), em seguida, abra o diretório de exemplo. |

## <a name="caffe"></a>Caffe

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Ubuntu     |
| Como é configurado/instalado no DSVM?  | O Caffe é instalado em `/opt/caffe`.    |
| Como alternar para o Python 2.7 | Execute o `source activate root` |
| Links para exemplos      | Os exemplos são incluídos no `/opt/caffe/examples`.      |
| Ferramentas relacionadas ao DSVM      | Caffe2      |

### <a name="how-to-use--run-it"></a>Como usar/executar?

Use X2Go para fazer logon em sua VM e, em seguida, inicie um novo terminal e insira

```
cd /opt/caffe/examples
source activate root
jupyter notebook
```

Uma nova janela do navegador é aberta com blocos de anotações de exemplo.

Binários são instalados em /opt/caffe/build/install/bin.

A versão instalada do Caffe requer Python 2.7 e não funcionará com o Python 3.5 ativado por padrão. Execute `source activate root` para alternar para o ambiente Anaconda.

## <a name="caffe2"></a>Caffe2

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Ubuntu     |
| Como é configurado/instalado no DSVM?  | O Caffe2 é instalado no [ambiente conda (raiz) do Python 2.7](dsvm-languages.md#python-linux-and-windows-server-2012-edition). A fonte está em `/opt/caffe2`. |
| Links para exemplos      | Exemplos de blocos de anotações estão incluídos no JupyterHub. |
| Ferramentas relacionadas ao DSVM      | Caffe      |
| Como usar/executar?    | * No terminal: ative o [ambiente de Python raiz](dsvm-languages.md#python-linux-and-windows-server-2012-edition), inicie o Python e importe Caffe2. <br/> * No JupyterHub: [conecte-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), em seguida, navegue até o diretório Caffe2 para localizar os blocos de anotações de exemplo. Alguns blocos de anotações exigem que a raiz Caffe2 seja definida no código Python; insira /opt/caffe2. |
| Compilar notas | O Caffe2 é criado a partir do código-fonte no Linux e inclui CUDA, cuDNN e Intel MKL. A confirmação atual é 0d9c0d48c6f20143d6404b99cc568efd29d5a4be, que foi escolhido para estabilidade em todas as GPUs e exemplos testados. |

## <a name="chainer"></a>Encadeador

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Windows, Linux     |
| Como é configurado/instalado no DSVM?  | O Chainer é instalado em [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL e ChainerCV também são instalados.   |
| Links para exemplos      | Exemplos de blocos de anotações estão incluídos no JupyterHub. |
| Ferramentas relacionadas ao DSVM      | Caffe      |
| Como usar/executar?  | * No terminal: ative o ambiente [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition), execute _python_ e, em seguida, importe o Chainer. <br/> *Em JupyterHub: [conecte-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), em seguida, navegue até o diretório Chainer para localizar os blocos de anotações de exemplo.


## <a name="deep-water"></a>Deep Water

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo para H2O      |
| Edições do DSVM com suporte      | Ubuntu     |
| Como é configurado/instalado no DSVM?  | O Deep Water é instalado em [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e também está disponível em `/dsvm/tools/deep_water`.   |
| Links para exemplos      | Exemplos de blocos de anotações estão incluídos no JupyterHub.      |
| Ferramentas relacionadas ao DSVM      | H2O, Sparkling Water      |

### <a name="how-to-use--run-it"></a>Como usar/executar?

O Deep Water requer CUDA 8 com cuDNN 5.1. Este não está no caminho da biblioteca por padrão, como as outras estruturas de aprendizagem profunda usa CUDA 9 e cuDNN 7. Para usar CUDA 8 + cuDNN 5.1 para o Deep Water:

```
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}
export CUDA_ROOT=/usr/local/cuda-8.0
```

Para usar o Deep Water:
* No terminal: ative o ambiente [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition), em seguida, execute _python_. <br/>
* No JupyterHub: [conecte-se ao JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), em seguida, navegue até o diretório deep_water para localizar os blocos de anotações de exemplo.

## <a name="mxnet"></a>MXNet

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Windows, Linux     |
| Como é configurado/instalado no DSVM?  | MXNet é instalado em `C:\dsvm\tools\mxnet` no Windows e `/dsvm/tools/mxnet` no Linux. As associações de Python estão instaladas em Python 3.5 no [Linux e Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) e Python 3.6 no [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Associações do R também são instaladas no Ubuntu.   |
| Links para exemplos      | Exemplos de blocos de anotações do Jupyter estão incluídos.    |
| Ferramentas relacionadas ao DSVM      | Keras      |
| Como usar/executar?    | * Em um terminal: ative o ambiente correto, em seguida, execute o Python. <br/> *Em Jupyter: conecte-se ao [Jupyter](provision-vm.md#tools-installed-on-the-microsoft-data-science-virtual-machine) ou [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), em seguida, abra o diretório mxnet para obter exemplos.  |
 | Compilar notas | O MXNet é compilado da fonte no Linux. Esta compilação inclui CUDA, cuDNN, NCCL e MKL. |

## <a name="nvidia-digits"></a>NVIDIA DIGITS

|    |           |
| ------------- | ------------- |
| O que é?   | Sistema de aprendizado profundo da NVIDIA para treinar rapidamente modelos de aprendizado profundo      |
| Edições do DSVM com suporte      | Ubuntu     |
| Como é configurado/instalado no DSVM?  | DIGITS está instalado em `/dsvm/tools/DIGITS` e disponível como um serviço, chamado _digits_.   |

### <a name="how-to-use--run-it"></a>Como usar/executar?

Faça logon na VM com X2Go. Em um terminal, inicie o serviço:

    sudo systemctl start digits

O serviço demora aproximadamente um minuto para iniciar. Abra um navegador da Web e navegue até `http://localhost:5000`. Observe que os DIGITS não fornecem um logon seguro e não devem ser expostos fora da VM.



## <a name="nvidia-smi"></a>nvidia-smi

|    |           |
| ------------- | ------------- |
| O que é?   | Ferramenta NVIDIA para consultar a atividade da GPU      |
| Edições do DSVM com suporte      | Windows, Linux     |
| Como é configurado/instalado no DSVM?  | _nvidia-smi_ está disponível no caminho do sistema.   |
| Como usar/executar? | Inicie um prompt de comando (no Windows) ou um terminal (no Linux) e, em seguida, execute _nvidia-smi_.



## <a name="theano"></a>Theano

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Ubuntu     |
| Como é configurado/instalado no DSVM?  | O Theano é instalado em Python 2.7 (_raiz_), bem como no ambiente Python 3.5 (_py35_).   |
| Ferramentas relacionadas ao DSVM      | Keras      |
| Como usar/executar?    | * Em um terminal, ative a versão do Python que deseja (raiz ou py35), execute o python e importe o theano. <br/> *Em Jupyter, selecione o kernel Python 2.7 ou 3.5 e importe o theano.  <br/>Para contornar um bug MKL recente, você precisa primeiro definir a camada de threading do MKL:<br/><br/>_export MKL_THREADING_LAYER=GNU_|



## <a name="torch"></a>Torch

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Ubuntu     |
| Como é configurado/instalado no DSVM?  | O Torch é instalado em `/dsvm/tools/torch`. O PyTorch é instalado em Python 2.7 (_raiz_), bem como no ambiente Python 3.5 (_py35_).   |
| Links para exemplos      | Exemplos de Torch estão localizados em `/dsvm/samples/torch`. Exemplos de PyTorch estão localizados em `/dsvm/samples/pytorch`.      |


## <a name="pytorch"></a>PyTorch

|    |           |
| ------------- | ------------- |
| O que é?   | Estrutura de aprendizado profundo      |
| Edições do DSVM com suporte      | Linux     |
| Como é configurado/instalado no DSVM?  | O PyTorch é instalado em [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  |
| Links para exemplos      | Exemplos de blocos de anotações do Jupyter estão incluídos e exemplos também podem ser encontrados em /dsvm/samples/pytorch.      |
| Ferramentas relacionadas ao DSVM      | Torch      |
| Como usá-lo/executá-lo? |* Em um terminal: ative o ambiente correto, em seguida, execute o Python. <br/> *Em Jupyter: conecte-se ao [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-data-science-virtual-machine-for-linux), em seguida, abra o diretório PyTorch para obter exemplos.  |

## <a name="mxnet-model-server"></a>Servidor de Modelo MXNet

|    |           |
| ------------- | ------------- |
| O que é?   | Um servidor para criar pontos de extremidade HTTP para modelos MXNet e ONNX      |
| Edições do DSVM com suporte      | Linux     |
| Como é configurado/instalado no DSVM?  | _mxnet-model-server_ está disponível no terminal.   |
| Links para exemplos      | Procurar exemplos atualizados na [página do servidor do modelo MXNet](https://github.com/awslabs/mxnet-model-server).    |
| Ferramentas relacionadas ao DSVM      | MXNet      |

## <a name="tensorflow-serving"></a>Fornecimento do TensorFlow

|    |           |
| ------------- | ------------- |
| O que é?   | Um servidor para executar inferências em um modelo TensorFlow      |
| Edições do DSVM com suporte      | Linux     |
| Como é configurado/instalado no DSVM?  | _tensorflow_model_server_ está disponível no terminal.   |
| Links para exemplos      | Exemplos estão disponíveis [online](https://www.tensorflow.org/serving/).      |
| Ferramentas relacionadas ao DSVM      | TensorFlow      |

## <a name="tensorrt"></a>TensorRT

|    |           |
| ------------- | ------------- |
| O que é?   | Um servidor inferência de aprendizagem profunda de NVIDIA. |
| Edições do DSVM com suporte      | Ubuntu     |
| Como é configurado/instalado no DSVM?  | O TensorRT é instalado como um pacote _apt_.   |
| Links para exemplos      | Exemplos estão disponíveis [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).      |
| Ferramentas relacionadas ao DSVM      | TensorFlow Serving, MXNet Model Server  |



