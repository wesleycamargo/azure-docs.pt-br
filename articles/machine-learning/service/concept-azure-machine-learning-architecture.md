---
title: 'ML na nuvem: termos e arquitetura'
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre a arquitetura, terminologia e conceitos que compõem o serviço do Azure Machine Learning. Você também aprenderá sobre o fluxo de trabalho geral de como usar o serviço e os serviços do Azure que são usados pelo serviço do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: larryfr
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 3966d4b27f0e3d42f47d84fb5c9f5c8519a27b6c
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184722"
---
# <a name="how-the-azure-machine-learning-service-works-architecture-and-concepts"></a>Como funciona o serviço do Azure Machine Learning: arquitetura e conceitos

Este documento descreve a arquitetura e conceitos para o serviço do Azure Machine Learning. O diagrama a seguir mostra os principais componentes do serviço e ilustra o fluxo de trabalho geral ao usar o serviço: 

[![Arquitetura e fluxo de trabalho dos serviços do Azure Machine Learning Service](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

O fluxo de trabalho geralmente segue estas etapas:

1. Desenvolver scripts de treinamento de aprendizado de máquina em __Python__.
1. Criar e configurar um __destino de computação__.
1. __Enviar os scripts__ para o destino de computação configurado para ser executado nesse ambiente. Durante o treinamento, o destino de computação armazena registros de execução para um __datastore__. Lá, os registros são salvos em um __experimento__.
1. __Consulte o experimento__ quanto a métricas registradas nas execuções atuais e anteriores. Se as métricas não indicarem um resultado desejado, volte para a etapa 1 e itere em seus scripts.
1. Depois que uma execução satisfatória for encontrada, registre o modelo persistente no __registro de modelo__.
1. Desenvolva um script de pontuação.
1. __Crie uma imagem__ e registre-a no __registro da imagem__. 
1. __Implante a imagem__ como um __serviço web__ no Azure.


> [!NOTE]
> Embora este documento define termos e conceitos usados pelo Azure Machine Learning, ele não define os termos e conceitos para a plataforma do Azure. Para obter mais informações sobre a terminologia da plataforma do Azure, consulte o [Glossário do Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Workspace

O workspace é o recurso de nível superior para o serviço do Azure Machine Learning. Ele fornece um local centralizado para trabalhar com todos os artefatos que você criar ao usar os serviços do Azure Machine Learning.

O workspace mantém uma lista de destinos de computação que podem ser usados para treinar seu modelo. Ele também mantém um histórico das execuções de treinamento, incluindo logs, métricas, saída e um instantâneo dos seus scripts. Essas informações são usadas para determinar quais execuções de treinamento produzem o melhor modelo.

Os modelos são registrados no workspace. Um modelo registrado e scripts de pontuação são usados para criar uma imagem. A imagem, em seguida, pode ser implantada em Instâncias de Contêiner do Azure, Serviço de Kubernetes do Azure, ou uma matriz de portas programáveis em campo (FPGA) como um ponto de extremidade HTTP baseado em REST. Ele também pode ser implantado em um dispositivo Azure IoT Edge como um módulo.

Você pode criar vários workspaces, e cada workspace pode ser compartilhado por várias pessoas. Ao compartilhar um workspace, controle o acesso ao workspace, atribuindo as funções a seguir para os usuários:

* Proprietário
* Colaborador
* Leitor

Quando você cria um novo workspace, ele automaticamente cria vários recursos do Azure que são usados pelo workspace:

* [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry/) - registra contêineres do docker que são usados durante o treinamento e ao implantar um modelo.
* [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) - usado como o repositório de dados padrão para o espaço de trabalho.
* [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) - armazena informações de monitoramento sobre seus modelos.
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) - armazena segredos usados por destinos de computação e outras informações confidenciais necessárias para o espaço de trabalho.

> [!NOTE]
> Em vez de criar novas versões, você também pode usar os serviços existentes do Azure. 

O diagrama a seguir é uma taxonomia do workspace:

[![Taxonomia de espaço de trabalho](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.svg)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

## <a name="model"></a>Modelo

Em sua forma mais simples, um modelo é um trecho de código que usa uma entrada e produz uma saída. Criar um modelo de aprendizado de máquina envolve selecionar um algoritmo, fornecer dados a ele e ajustar hiperparâmetros. O treinamento é um processo iterativo que produz um modelo treinado, que encapsula o modelo aprendido durante o processo de treinamento.

Um modelo é produzido por uma execução no Azure Machine Learning. Você também pode usar um modelo treinado fora do Azure Machine Learning. Um modelo pode ser registrado em um espaço de trabalho dos serviços do Azure Machine Learning.

Os serviços do Azure Machine Learning são independentes do framework. Você pode usar qualquer estrutura de aprendizado de máquina popular durante a criação de um modelo, como scikit-learn, xgboost, PyTorch, TensorFlow, Chainer e CNTK.

Para obter um exemplo de treinamento de um modelo, consulte o [Guia de Início Rápido: Crie um documento de espaço de trabalho](quickstart-get-started.md) de Serviço de aprendizado de máquina.

### <a name="model-registry"></a>Registro de modelo

O registro de modelo mantém o registro de todos os modelos no espaço de trabalho dos serviços do Azure Machine Learning. 

Os modelos são identificados por nome e versão. Cada vez que você registra um modelo com o mesmo nome de um já existente, o registro pressupõe que se trata de uma nova versão. A versão é incrementada e o novo modelo é registrado com o nome.

Você pode fornecer as marcas de metadados adicionais quando você registrar o modelo e, em seguida, usar essas marcas ao procurar pelos modelos.

Você não pode excluir modelos que estão sendo usados por uma imagem.

Para obter um exemplo de registro de um modelo, consulte o documento [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="image"></a>Imagem

As imagens fornecem uma maneira confiável de implantar um modelo, juntamente com todos os componentes necessários para usar o modelo. Uma imagem contém os seguintes itens:

* Um modelo.
* Um script ou aplicativo de pontuação. Esse script é usado para passar a entrada para o modelo e retornar a saída do modelo.
* As dependências exigidas pelo modelo ou o script/aplicativo de pontuação.  Por exemplo, você pode incluir um arquivo de ambiente do Conda que lista as dependências de pacote do Python.

Há dois tipos de imagens que podem ser criados pelo Azure Machine Learning:

* Imagem FPGA: Usada ao implantar em uma matriz de portas programáveis em campo na nuvem do Azure.
* Imagem do Docker: Usado ao implantar nos destinos de computação que não sejam FPGA. Por exemplo, as Instâncias de Contêiner do Azure e o Serviço de Kubernetes do Azure.

Para obter um exemplo de criação de uma imagem, consulte o documento [Implantar um modelo de classificação de imagem na Instância de Contêiner do Azure](tutorial-deploy-models-with-aml.md).

### <a name="image-registry"></a>Registro de imagem

O registro de imagem controla imagens criadas a partir de seus modelos. Você pode fornecer as marcas de metadados adicionais ao criar a imagem. As marcas de metadados são armazenadas pelo registro de imagem e podem ser consultadas para localizar a imagem.

## <a name="deployment"></a>Implantação

Uma implantação é uma instanciação de sua imagem em um serviço web que pode ser hospedado na nuvem ou um módulo do IoT para implantações de dispositivos integrados. 

### <a name="web-service"></a>Serviço Web

Um serviço web implantado pode usar Instâncias de Contêiner do Azure, Serviço de Kubernetes do Azure ou matriz de portas programáveis em campo (FGPA).
O serviço é criado a partir de uma imagem que encapsula seu modelo, scripts e arquivos associados. A imagem tem um balanceamento de carga, ponto de extremidade HTTP que recebe as solicitações de pontuação enviadas para o serviço web.

O Azure ajuda a monitorar a implantação do serviço web por meio da coleta de telemetria do Application Insight e/ou a telemetria de modelo se você tiver optado por habilitar esse recurso. Os dados de telemetria estão acessíveis apenas para você e armazenados em suas instâncias de conta de armazenamento e Application Insights.

Se você tiver habilitado o dimensionamento automático, o Azure dimensionará automaticamente sua implantação.

Para obter um exemplo de implantação de um modelo como um serviço web, consulte o documento [Implantar um modelo de classificação de imagem na Instância de Contêiner do Azure](tutorial-deploy-models-with-aml.md).

### <a name="iot-module"></a>Módulo de IoT

Um módulo de IoT implantado é um contêiner do Docker que inclui seu modelo e script ou aplicativo associado e as dependências adicionais. Esses módulos são implantados usando o Azure IoT Edge em dispositivos edge. 

Se você tiver habilitado o monitoramento, o Azure coleta dados de telemetria do modelo de dentro do módulo do Azure IoT Edge. Os dados de telemetria estão acessíveis apenas para você e armazenados em sua instância de conta de armazenamento.

O Azure IoT Edge garantirá que seu módulo esteja em execução e monitorará o dispositivo que está hospedando.

## <a name="datastore"></a>Repositório de dados

Um repositório de dados é uma abstração de armazenamento de uma Conta de Armazenamento do Azure. O repositório de dados pode usar um contêiner de blob do Azure ou um compartilhamento de arquivos do Azure como o armazenamento de back-end. Cada workspace tem um repositório de dados padrão e você poderá registrar repositórios de dados adicionais. 

Use a API do SDK do Python ou a CLI do Azure Machine Learning para armazenar e recuperar arquivos do repositório de dados. 

## <a name="run"></a>Executar

Uma execução é um registro que contém as seguintes informações:

* Metadados sobre a execução (carimbo de hora, duração, etc.)
* Métricas registradas pelo seu script
* Arquivos de saída coletados automaticamente pelo experimento ou carregados explicitamente por você
* Um instantâneo do diretório que contém seus scripts, antes da execução

Uma execução é produzida quando você envia um script para treinar um modelo. Uma execução pode ter zero ou mais execuções filho. Portanto, a execução de nível superior pode ter duas execuções filho, cada uma delas pode ter sua próprias execuções filho.

Para obter um exemplo de execuções de visualização produzido ao treinar um modelo, consulte o [Guia de Início Rápido: Introdução ao documento de Serviço do Azure Machine Learning](quickstart-get-started.md).

## <a name="experiment"></a>Experimento

Um experimento é um agrupamento de diversas execuções de um determinado script. Ele sempre pertence a um workspace. Quando você envia uma execução, você pode fornecer um nome de experimento. As informações para a execução são armazenadas nesse experimento. Se você enviar uma execução e especificar um nome de experimento que não existe, um novo experimento com esse nome é criado automaticamente.

Para obter um exemplo do uso de um experimento, consulte o [Guia de Início Rápido: Introdução ao documento de Serviço do Azure Machine Learning](quickstart-get-started.md).

## <a name="pipeline"></a>Pipeline

Os pipelines de aprendizado de máquina são usados para criar e gerenciar fluxos de trabalho que unem as fases de aprendizado da máquina. Por exemplo, um pipeline pode incluir preparação de dados, treinamento do modelo, implantação de modelo e fases de inferência. Cada fase pode incluir várias etapas, cada uma delas pode ser executada de modo autônomo em vários destinos de computação.

Para obter mais informações sobre os pipelines de aprendizado de máquina com esse serviço, consulte o artigo [Pipelines e Azure Machine Learning](concept-ml-pipelines.md).

## <a name="compute-target"></a>Destino de computação

Um destino de computação é o recurso de computação usado para executar o script de treinamento ou hospedar a implantação do serviço. Os destinos de computação com suporte são: 

| Destino de computação | Treinamento | Implantação |
| ---- |:----:|:----:|
| Seu computador local | ✓ | &nbsp; |
| Computação do Azure Machine Learning | ✓ | &nbsp; |
| Uma VM do Linux no Azure</br>(como a Máquina Virtual de Ciência de Dados) | ✓ | &nbsp; |
| Azure Databricks | ✓ | &nbsp; | &nbsp; |
| Análise Azure Data Lake | ✓ | &nbsp; |
| Apache Spark para HDInsight | ✓ | &nbsp; |
| Azure Container Instance | &nbsp; | ✓ |
| Serviço de Kubernetes do Azure | &nbsp; | ✓ |
| Azure IoT Edge | &nbsp; | ✓ |
| Project Brainwave</br>(Matriz de porta programável em campo) | &nbsp; | ✓ |

Os destinos de computação são anexados a um workspace. Os destinos de computação diferentes do computador local são compartilhados por usuários do workspace.

### <a name="managed-and-unmanaged-compute-targets"></a>Destinos de computação gerenciados e não gerenciados

Destinos de computação **gerenciados** são criados e gerenciados pelo Serviço do Azure Machine Learning. Esses destino de computação são otimizados para cargas de trabalho ML. __Computação do Azure Machine Learning__ é o único destino de computação gerenciada no momento (4 de dezembro de 2018). Destinos de computação gerenciados adicionais podem ser adicionados no futuro. As instâncias de Computação do ML podem ser criadas diretamente por meio do espaço de trabalho usando o portal do Azure, o SDK do Azure Machine Learning ou a CLI do Azure. Todos os outros destinos de computação devem ser criados fora do espaço de trabalho e, em seguida, anexados.

Destinos de computação **não gerenciados** são criados e gerenciados pelo Serviço do Azure Machine Learning. Você precisa criá-los fora do Azure Machine Learning e, em seguida, anexá-los ao seu espaço de trabalho antes do uso. Esses destinos de computação podem exigir etapas adicionais para manter ou melhorar o desempenho para cargas de trabalho do ML.

Para obter informações sobre como selecionar um destino de computação para treinamento, consulte o documento [Selecionar e usar um destino de computação para treinar seu modelo](how-to-set-up-training-targets.md).

Para obter informações sobre como selecionar um destino de computação para a implantação, consulte o documento [Implantar modelos com o serviço do Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="run-configuration"></a>Configuração de execução

Uma configuração de execução é um conjunto de instruções que define como um script deve ser executado em um destino de computação especificado. Ela inclui um amplo conjunto de definições de comportamento, como se deseja usar um ambiente de Python existente ou usar um ambiente de Conda criado a partir de especificação.

Uma configuração de execução pode ser persistida em um arquivo dentro do diretório que contém o script de treinamento, ou construídos como um objeto na memória e usado para enviar uma execução.

Para obter um exemplo das configurações de execução, consulte o documento [Selecionar e usar um destino de computação para treinar seu modelo](how-to-set-up-training-targets.md).

## <a name="training-script"></a>Script de treinamento

Para treinar um modelo, você deve especificar o diretório que contém o script de treinamento e os arquivos associados. Você também pode especificar um nome de experimento, que é usado para armazenar as informações obtidas durante o treinamento. Durante o treinamento, o diretório inteiro é copiado para o ambiente de treinamento (destino de computação) e o script especificado pela configuração de execução é iniciado. Um instantâneo do diretório também é armazenado no experimento no workspace.

Para um exemplo, consulte [Criar um workspace com o Python](quickstart-get-started.md)

## <a name="logging"></a>Registro em log

Ao desenvolver sua solução, use o SDK do Python do Azure Machine Learning em seu script de Python para registrar métricas arbitrárias. Após a execução, consulte as métricas para determinar se a execução produziu o modelo que você deseja implantar. 

## <a name="snapshot"></a>Instantâneo

Ao enviar uma execução, o Azure Machine Learning compacta o diretório que contém o script como um arquivo zip e o envia para o destino de computação. O arquivo zip é expandido e o script é executado lá. O Azure Machine Learning também armazena o arquivo zip como um instantâneo como parte do registro de execução. Qualquer pessoa com acesso ao workspace pode procurar um registro de execução e baixar o instantâneo.

## <a name="activity"></a>Atividade

Uma atividade representa uma operação de execução demorada. As operações a seguir estão exemplos de atividades:

* Criar ou excluir um destino de computação
* Executar um script em um destino de computação

As atividades podem fornecer notificações por meio do SDK ou da IU da Web, portanto, você pode facilmente monitorar o progresso dessas operações.

## <a name="next-steps"></a>Próximas etapas

Use os links a seguir para começar a usar o Azure Machine Learning:

* [O que é o serviço do Azure Machine Learning](overview-what-is-azure-ml.md)
* [Início Rápido: Criar um workspace com o Python](quickstart-get-started.md)
* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md)
