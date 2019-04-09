---
title: Introdução à Máquina Virtual de Ciência de Dados do Azure para Linux e Windows | Microsoft Docs
description: Principais cenários de análise e componentes para Máquinas Virtuais de Ciência de Dados do Windows e Linux.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/27/2017
ms.author: gokuma
ms.openlocfilehash: 74fe2345af5d1249d6704c7560b42323561bc623
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918239"
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>Introdução à Máquina Virtual de Ciência de Dados do Azure para Linux e Windows

A DSVM (Máquina Virtual de Ciência de Dados) é uma imagem de VM personalizada na nuvem do Microsoft Azure especificamente criada para ciência de dados. Ela tem muitas ferramentas conhecidas de ciência de dados, entre outras, pré-instaladas e pré-configuradas que ajudam a começar a criar rapidamente aplicativos inteligentes para análise avançada. Ela está disponível no Windows Server e no Linux. Oferecemos a edição do Windows do DSVM no Server 2016 e no Server 2012. Oferecemos edições de Linux do DSVM no Ubuntu 16.04 LTS e no CentOS 7.4.

Este tópico explica o que você pode fazer com a VM de Ciência de Dados, descreve alguns dos principais cenários de uso da VM, lista os principais recursos nas versões Windows e Linux, além de fornecer instruções sobre como começar a usá-los.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>O que é possível fazer com a Máquina Virtual de Ciência de Dados?
O objetivo da DSVM (Máquina Virtual de Ciência de Dados) é fornecer a profissionais de dados com todos os níveis de habilidade em todos os setores um ambiente de ciência de dados descomplicado, pré-configurado e totalmente integrado. Em vez de distribuir um workspace equiparável por conta própria, você pode provisionar um DSVM, poupando dias ou até mesmo _semanas_ com processos de instalação, configuração e de gerenciamento de pacotes. Depois de alocar o DSVM, você pode começar a trabalhar imediatamente no seu projeto de ciência de dados.

A VM de Ciência de Dados foi desenvolvida e configurada para trabalhar com amplos cenários de uso. Você pode escalar ou reduzir seu ambiente verticalmente, conforme as necessidades de seu projeto mudarem, usar sua linguagem preferida para programar tarefas de ciência de dados e instalar outras ferramentas para personalizar o sistema exatamente de acordo com suas necessidades.

## <a name="key-scenarios"></a>Principais cenários
Esta seção sugere alguns cenários importantes para os quais a VM de Ciência de Dados pode ser implantada.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Área de trabalho de análise pré-configurada na nuvem
A VM de Ciência de Dados fornece uma configuração de linha de base para equipes de ciência de dados que buscam substituir as respectivas áreas de trabalho locais por uma área de trabalho de nuvem gerenciada. Essa linha de base garante que todos os cientistas de dados em uma equipe tenham uma configuração consistente com a qual podem verificar experiências e promover colaboração. Ela também diminui os custos reduzindo a carga de administração de sistema e poupando o tempo necessário para avaliar, instalar e manter os vários pacotes de software necessários à análise avançada.

### <a name="data-science-training-and-education"></a>Educação e treinamento de ciência de dados
Os treinadores e educadores corporativos que ensinam sobre ciência de dados geralmente fornecem uma imagem de máquina virtual para garantir que seus alunos tenham uma configuração consistente e que as amostras funcionem de maneira previsível. A VM de Ciência de Dados cria um ambiente sob demanda com uma configuração consistente que facilita o suporte e os desafios de incompatibilidade. Nos casos em que esses ambientes precisam ser criados com frequência, especialmente para aulas rápidas de treinamento, os alunos são consideravelmente beneficiados.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacidade elástica sob demanda para projetos em larga escala
As maratonas/competições de ciência de dados ou modelagem e exploração de dados em larga escala exigem que a capacidade de hardware seja escalada horizontalmente, geralmente por um curto período. A VM de Ciência de Dados pode ajudar a replicar o ambiente de ciência de dados rapidamente em servidores escalados horizontalmente sob demanda, o que permite a realização de testes que exigem a execução de recursos de computação altamente potentes.

### <a name="short-term-experimentation-and-evaluation"></a>Avaliação e experimento de curto prazo
A VM de Ciência de Dados pode ser usada para avaliar ferramentas, ou aprender sobre elas, como o Microsoft ML Server, SQL Server, Visual Studio, Jupyter, kits de ferramentas de machine learning/aprendizado profundo, além de novas ferramentas conhecidas na comunidade com mínimo esforço de configuração. Uma vez que a VM de Ciência de Dados pode ser configurada rapidamente, ela pode ser aplicada em outros cenários de uso de curto prazo, como na replicação de testes publicados, na execução de demonstrações, depois de passo a passos em sessões online ou em tutoriais de conferência.

### <a name="deep-learning"></a>Aprendizado
A VM de ciência de dados pode ser usada para modelos de treinamento usando algoritmos de aprendizagem profunda em hardware baseado em GPU (unidades de processamento gráfico). Utilizando as funcionalidades de colocação em escala de VM da nuvem do Azure, a DSVM ajuda você a usar hardware baseado em GPU na nuvem de acordo com a necessidade. É possível mudar para uma VM baseada em GPU durante o treinamento de modelos grandes ou quando houver necessidade de cálculos em alta velocidade mantendo o mesmo disco do SO.  A edição do Windows Server 2016 do DSVM vem pré-instalada com drivers de GPU, estruturas e versões de GPU de estruturas de aprendizagem profunda. Na edição para Linux, aprendizagem profunda em GPU está habilitada nos DSVMs CentOS e Ubuntu. Você pode implantar a edição do Ubuntu, CentOS ou Windows 2016 da VM de Ciência de Dados em máquinas virtuais do Azure não baseadas em GPU, quando, então, todas as estruturas de aprendizagem profunda usarão o modo de CPU.

## <a name="whats-included-in-the-data-science-vm"></a>O que está incluído na VM de Ciência de Dados?
A Máquina Virtual de Ciência de Dados tem muitas ferramentas conhecidas de ciência de dados e de aprendizado já instaladas e configuradas. Ela também inclui ferramentas que facilitam trabalhar com vários produtos de análise e dados do Azure, como o Microsoft ML Server (R, Python) para a criação de modelos preditivos ou o SQL Server 2017 para a exploração do conjuntos de dados de grande escala. Também está incluído um conjunto de outras ferramentas da comunidade de software livre e da Microsoft, bem como código de exemplo e notebooks. A tabela a seguir relaciona e compara os principais componentes incluídos nas edições do Windows e Linux da Máquina Virtual de Ciência de Dados.


| **Ferramenta**                                                           | **Edição do Windows** | **Edição do Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) com pacotes populares pré-instalados   |S                      | S             |
| O [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition inclui a <br />  estrutura de alto desempenho paralela e distribuída &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) (R e Python)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) – Novos algoritmos de AM de última geração da Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [Operacionalização do R e Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |S                      | S |
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) Pro Plus com ativação compartilhada – Excel, Word e PowerPoint   |S                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 com pacotes populares pré-instalados    |S                      |S              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) com pacotes populares para linguagem Julia pré-instalados                         |S                      |S              |
| Bancos de dados relacionais                                                            | [Microsoft SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS),<br/>[Microsoft SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Ferramentas de Banco de Dados                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> Drivers * ODBC/JDBC| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (ferramenta de consultas), <br /> * bcp, sqlcmd <br /> Drivers * ODBC/JDBC|
| Análise no banco de dados escalonável com os serviços do SQL Server ML (R, Python) | S     |N              |
| **[Servidor do Jupyter Notebook](https://jupyter.org/) com os kernels a seguir,**                                  | S     | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (somente Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | S |
| JupyterHub (Servidor de notebooks com vários usuários)| N | S |
| JupyterLab (Servidor de notebooks com vários usuários) | N | Y (somente Ubuntu) |
| **Ferramentas de desenvolvimento, IDEs e editores de código**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) com Git Plugin, Azure HDInsight (Hadoop), Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs) e [RTVS (Ferramentas do R para Visual Studio)](https://microsoft.github.io/RTVS-docs/) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (IDE de Julia)](https://junolab.org/)| S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim e Emacs | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git e GitBash | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* .NET Framework | S | N |
| Power BI Desktop | S | N |
| SDKs para acessar o Azure e o pacote de serviços Cortana Intelligence | S | S |
| **Ferramentas de movimentação de dados e gerenciamento** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Gerenciador de Armazenamento do Azure | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CLI do Azure](https://docs.microsoft.com/cli/azure) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure PowerShell | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Driver FUSE do blob](https://github.com/Azure/azure-storage-fuse) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy(Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Ferramenta de Migração de Dados do DocDB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Gateway de Gerenciamento de Dados da Microsoft](https://msdn.microsoft.com/library/dn879362.aspx): Mover dados entre OnPrem e Nuvem | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Utilitários de linha de comando Unix/Linux | S | S |
| [Apache Drill](https://drill.apache.org) para Exploração de dados | S | S |
| **Ferramentas de Machine Learning** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integração com [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](https://togaware.com/rattle/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (somente Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CatBoost](https://tech.yandex.com/catboost/) | N | Y (somente Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) | N | Y (somente Ubuntu) |
| **Ferramentas de aprendizado profundo** <br>Todas as ferramentas funcionarão em uma CPU ou GPU |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | S (Windows 2016) | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | N | S (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](https://mxnet.io/) | S (Windows 2016) | S|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe e Caffe2](https://github.com/caffe2/caffe2) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](https://pytorch.org/)| N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Servidor Modelo MXNet](https://github.com/awslabs/mxnet-model-server) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Fornecimento do TensorFlow](https://www.tensorflow.org/serving/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA, cuDNN, NVIDIA Driver](https://developer.nvidia.com/cuda-toolkit) | S | S |
| **Plataforma Big Data (somente Devtest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* [Spark](https://spark.apache.org/) autônomo local | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Hadoop](https://hadoop.apache.org/) local (HDFS, YARN) | N | S |

## <a name="get-started"></a>Introdução

### <a name="windows-data-science-vm"></a>VM de Ciência de Dados do Windows
* Para saber mais sobre como criar e usar uma DSVM do Windows, consulte [Provisionar a máquina virtual de ciência de dados do Windows](provision-vm.md). Para saber mais sobre como executar várias tarefas necessárias para o seu projeto de DSVM do Windows, confira [Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados](vm-do-ten-things.md).

### <a name="linux-data-science-vm"></a>VM de Ciência de Dados do Linux
* Para saber mais sobre como criar e usar uma DSVM do Ubuntu, consulte [Provisionar a Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](dsvm-ubuntu-intro.md). Para saber mais sobre como criar e usar uma DSVM do CentOS, consulte [Provisionar a Máquina Virtual de Ciência de Dados do Linux CentOS no Azure](linux-dsvm-intro.md).
* Para obter um passo a passo que mostre como executar várias tarefas comuns de ciência de dados com o VM Linux, CentOS e Ubuntu, confira [Ciência de dados na Máquina Virtual da Ciência de Dados do Linux](linux-dsvm-walkthrough.md).

## <a name="next-steps"></a>Próximas etapas
[Guia do desenvolvedor de R para Azure](/azure/architecture/data-guide/technology-choices/r-developers-guide)
