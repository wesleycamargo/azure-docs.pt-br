---
title: Migrar do Workbench
titleSuffix: Azure Machine Learning service
description: Saiba como atualizar ou migrar para a versão final do serviço de aprendizado de máquina do Azure de uma versão anterior.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: haining
author: haining
ms.date: 09/24/2018
ms.openlocfilehash: 22e7e513c7ec015b070ae37c1dbdd168404ee768
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140737"
---
# <a name="migrate-from-workbench-to-the-latest-version-of-azure-machine-learning-service"></a>Migrar do Workbench para a versão mais recente do Serviço do Azure Machine Learning 

**Se você tiver instalado o aplicativo Workbench e/ou tem contas de visualização de gerenciamento de modelos e experimentação, use este artigo para migrar para a versão mais recente.**  Se você não tiver o Workbench de visualização instalado ou uma conta de gerenciamento de experimentos e / ou modelos, não será necessário migrar nada.

## <a name="what-can-i-migrate"></a>O que pode migrar?
A maioria dos artefatos criados na primeira visualização do serviço de Aprendizado de Máquina do Azure é armazenada em seu próprio armazenamento local ou em nuvem. Esses artefatos não desaparecem. Para migrar, registre os artefatos novamente com o serviço atualizado do Aprendizado do Computador do Azure. 

A tabela e o artigo a seguir explicam o que você pode fazer com seus recursos e recursos existentes antes ou depois de passar para a versão mais recente do serviço de aprendizado de máquina do Azure. Você também pode continuar a usar a versão anterior e seus recursos por algum tempo ([veja o cronograma de suporte à transição](overview-what-happened-to-workbench.md#timeline)).

|Ativo ou no recurso de uma versão antiga|Posso migrar?|Ações|
|-----------------|:-------------:|-------------|
|Modelos de aprendizado de máquina (como arquivos locais)|SIM|Nenhuma. Funciona como antes.|
|Modele as dependências e esquemas (como arquivos locais)|SIM|Nenhuma. Funciona como antes.|
|Projetos|SIM|[Anexar a pasta local para o novo workspace](#projects).|
|Histórico de execução|Não |[Que pode ser baixado](#history) por algum tempo.|
|Arquivos de preparação de dados|Não |[Prepare qualquer conjunto de dados de tamanho](#dataprep) para modelagem usando o novo SDK de preparação de dados de aprendizado de máquina do Azure ou use as Bills de dados do Azure.|
|Destinos de computação|Não |Registrá-los no novo workspace.|
|Modelos registrados|Não |Registre novamente o modelo em um novo workspace.|
|Manifestos registrados|Não |Nenhuma. Manifestos não existem mais como um conceito no novo workspace.|
|Imagens registradas|Não |Recrie a imagem do Docker de implantação em um novo workspace.|
|Serviços web implantados|Não |Nenhuma. Eles ainda funcionarão como-está <br/>ou [implantá-los novamente usando a versão mais recente](#services).|
|Experimentação e <br/>Contas de gerenciamento de modelo|Não |[Crie um workspace](#resources) em seu lugar.|
|SDK e a CLI de aprendizado de máquina|Não |Use o novo [CLI](reference-azure-machine-learning-cli.md) e [SDK](https://aka.ms/aml-sdk) para novos trabalhos.|


Saiba mais sobre [o que mudou nesta versão](overview-what-happened-to-workbench.md)?

>[!Warning]
>Este artigo não é para usuários do Azure Machine Learning Studio. É para clientes do serviço de aprendizado de máquina do Azure que instalaram o aplicativo Workbench (visualização) e / ou têm contas de visualização de gerenciamento de modelos e experimentação.

<a name="resources"></a>

## <a name="azure-resources"></a>Recursos do Azure

Recursos como suas contas de experimentação, contas de gerenciamento de modelos e ambientes de computação de aprendizado de máquina não podem ser migrados para a versão mais recente do serviço de Aprendizado de Máquina do Azure. Veja a [linha do tempo](overview-what-happened-to-workbench.md#timeline) sobre quanto tempo seus recursos continuarão funcionando.

Comece a usar a versão mais recente ao criar um workspace de serviço do Azure Machine Learning no [portal do Azure](quickstart-get-started.md). Painel de workspace do portal tem suporte nos navegadores Microsoft Edge, Chrome e Firefox apenas.

Esse novo workspace é o recurso de serviço de nível superior e permite que você use todos os recursos mais recentes do serviço do Azure Machine Learning. Saiba mais sobre esse [workspace e essa arquitetura](concept-azure-machine-learning-architecture.md).

<a name="projects"></a>

## <a name="projects"></a>Projetos

Em vez de ter seus projetos em um workspace na nuvem, os projetos agora são diretórios em sua máquina local na última versão. Veja um diagrama da [última arquitetura](concept-azure-machine-learning-architecture.md). 

Para continuar usando o diretório local que contém seus arquivos e scripts, especifique o nome do diretório no comando ['experiment.submit'](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Python ou usando o comando da CLI 'az ml project attach'.

Por exemplo: 
```python
run = exp.submit(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
```

<a name="services"></a>

## <a name="deployed-web-services"></a>Serviços web implantados

Para migrar serviços da web, reimplemente seus modelos usando o novo SDK ou CLI para os novos destinos de implementação. Não há necessidade de alterar o arquivo de pontuação original, os arquivos de dependências do arquivo de modelo, o arquivo de ambiente e os arquivos de esquema. 

Na versão mais recente, os modelos são implantados como serviços Web para clusters da ACI (Instâncias de Contêiner do Azure) ou do AKS (Serviço de Kubernetes do Azure). 

Saiba mais nestes artigos:
+ [Como e onde implantar](how-to-deploy-and-where.md)
+ [Tutorial: implantar modelos com o Serviço do Azure Machine Learning](tutorial-deploy-models-with-aml.md)

Quando o [suporte para a CLI anterior terminar](overview-what-happened-to-workbench.md#timeline), você não poderá gerenciar os serviços da Web originalmente implantados com sua conta de Gerenciamento de modelos. No entanto, esses serviços da Web continuarão a funcionar enquanto o Azure Container Service (ACS) ainda for suportado.

<a name="history"></a>

## <a name="run-history-records"></a>Registros de histórico de execução

Embora você não possa continuar adicionando a seus históricos de execução existentes no workspace antiga, é possível exportar os históricos que você usa usando a CLI anterior. Quando o suporte para a [CLI anterior terminar](overview-what-happened-to-workbench.md#timeline), você não poderá mais exportar esses históricos de execução.

Comece treinando seus modelos e acompanhando os históricos de execução usando o novo CLI e SDK. Você pode aprender como com o [Tutorial: treinar modelos com o serviço de aprendizado de máquina do Azure](tutorial-train-models-with-aml.md).

Para exportar o histórico de execuções com a CLI anterior:

```azurecli
#list all runs
az ml history list

#get details about a particular run
az ml history info

# download all artifacts of a run
az ml history download
```

<a name="dataprep"></a>

## <a name="data-preparation-files"></a>Arquivos de preparação de dados
Os arquivos de preparação de dados não são portáveis sem o Workbench. Mas você ainda pode preparar qualquer conjunto de dados de tamanho para modelagem usando o novo SDK de Preparação de Dados de Aprendizado de Máquina do Azure ou usar as Chaves de Dados do Azure para grandes conjuntos de dados. [Saiba como obter a SDK de preparação de dados](https://aka.ms/data-prep-sdk).

## <a name="next-steps"></a>Próximas etapas

Para um início rápido mostrando como criar um workspace, criar um projeto, executar um script e explorar o histórico de execução do script com a versão mais recente do serviço de aprendizado de máquina do Azure, tente [ iniciar com o serviço do Azure Machine Learning](quickstart-get-started.md).

Para uma experiência mais aprofundada desse fluxo de trabalho, siga o tutorial completo que contém etapas detalhadas para o treinamento e a implantação de modelos com o serviço de Aprendizado de Máquina do Azure. 

> [!div class="nextstepaction"]
> [Tutorial: treinar e implantar modelos](tutorial-train-models-with-aml.md)
