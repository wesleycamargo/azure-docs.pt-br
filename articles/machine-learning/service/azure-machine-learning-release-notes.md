---
title: Novidades na versão
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre as atualizações mais recentes para o Serviço do Azure Machine Learning e o aprendizado de máquina e SDKs de Python de preparação de dados.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: reference
author: hning86
ms.author: haining
ms.reviewer: j-martens
ms.date: 12/20/2018
ms.custom: seodec18
ms.openlocfilehash: 8a67a20beff306cd23b08e1d651ab5dc4c779fd2
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742754"
---
# <a name="azure-machine-learning-service-release-notes"></a>Notas de versão do serviço de aprendizado de máquina do Azure

Neste artigo, conheça os lançamentos de serviços do Aprendizado de Máquina do Azure. 

## <a name="2018-12-20"></a>20-12-2018: 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>SDK do Azure Machine Learning para Python v1.0.6

+ **Documentos de Referência do SDK**: https://aka.ms/aml-sdk

+ **Correções de bug**: Esta versão contém principalmente correções de bugs secundários

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>SDK de preparação de dados do Azure Machine Learning v1.0.4

+ **Documentos de referência do SDK**: https://aka.ms/data-prep-sdk

+ **Novos recursos**
  + A função `to_bool` agora permite que os valores incompatíveis sejam convertidos em valores de erro. Esse é o novo comportamento de incompatibilidade de padrão para `to_bool` e `set_column_types`, enquanto o comportamento padrão anterior era para converter valores incompatíveis como False.
  + Ao chamar `to_pandas_dataframe`, há uma nova opção para interpretar valores ausente/nulos em colunas numéricas como NaN.
  + Capacidade adicional para verificar o tipo de retorno de algumas expressões para garantir a consistência de tipo e falha antecipada.
  + Agora você pode chamar `parse_json` para analisar valores em uma coluna como objetos JSON e expandi-las em várias colunas.

+ **Correções de bug**
  + Corrigido um bug que travou `set_column_types` no Python 3.5.2.
  + Corrigido um bug que falhou ao se conectar ao armazenamento de dados usando uma imagem do AML.

+ **Atualizações**
  * [Notebooks de exemplo](https://aka.ms/aml-data-prep-notebooks) para obter tutoriais de Introdução, estudos de caso e guias de instruções.

## <a name="2018-12-04-general-availability"></a>04/12/2018: Disponibilidade geral

O Serviço do Azure Machine Learning já está disponível ao público em geral.

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
Com esta versão, estamos anunciando uma nova experiência de computação gerenciada por meio da [Computação do Machine Learning](how-to-set-up-training-targets.md#amlcompute). Esse destino de computação substitui a computação do IA do Lote do Azure para o Azure Machine Learning. 

O destino de computação:
+ É usado para treinamento de modelo e inferência de lote
+ É único - para computação de vários nós
+ Realiza o gerenciamento de cluster e o plano de trabalho para o usuário
+ Dimensionado automaticamente por padrão
+ Suporte para recursos de CPU e GPU 
+ Permite o uso de VMs de baixa prioridade para redução de custos

A Computação do Azure Machine Learning pode ser criada no Python, pelo portal do Azure ou pela CLI. Precisa ser criada na região do seu workspace e não pode ser anexada a outro workspace. Esse destino de computação usa um contêiner do Docker para execução e empacota as dependências para replicar o mesmo ambiente em todos os nós.

> [!Warning]
> É recomendável criar um novo workspace para usar a Computação do Azure Machine Learning. Há uma possibilidade remota de que os usuários que tentarem criar a Computação do Azure Machine Learning em um workspace existente recebam um erro. A computação existente no workspace deve continuar a funcionar sem problemas.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>SDK do Azure Machine Learning para Python v1.0.2
+ **Alterações da falha**
  + Nesta versão, removemos o suporte para a criação de uma VM do Azure Machine Learning. Ainda é possível anexar uma VM em nuvem existente ou um servidor local remoto. 
  + Também removemos o suporte para o IA do Lote, agora, o suporte para todos ocorrerá por meio da Computação do Azure Machine Learning.

+ **Novo**
  + Para pipelines do aprendizado de máquina:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Updated**
  + Para pipelines do aprendizado de máquina:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) aceita runconfig agora
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) agora copia de e para uma fonte de dados SQL
    + Agendar a funcionalidade no SDK para criar e atualizar agendas e executar pipelines publicados

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>SDK de preparação de dados do Azure Machine Learning v0.5.2
+ **Alterações da falha** 
  * `SummaryFunction.N` foi renomeado para `SummaryFunction.Count`.
  
+ **Correções de bug**
  * Use o Token de Execução do AML mais recente ao ler e gravar em armazenamentos de dados ou execuções remotas. Anteriormente, se o Token de Execução do AML fosse atualizado no Python, o tempo de execução de preparação de dados não seria atualizado com o novo Token de Execução do AML.
  * Mensagens de erro mais claras adicionais
  * to_spark_dataframe() não falhará mais quando o Spark usar a serialização `Kryo`
  * Agora, o Inspetor de Contagem de Valor pode mostrar mais de mil valores únicos
  * Não haverá mais falha na Divisão Aleatória se o Fluxo de Dados original não tiver um nome  

+ **Mais informações**
  * [SDK de preparação de dados do Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Documentos e notebooks
+ Pipelines de ML
  + Notebooks novos e atualizados para começar a ver exemplos de pipelines, escopo de lote e transferência de estilo: https://aka.ms/aml-pipeline-notebooks
  + Saiba como [criar seu primeiro pipeline](how-to-create-your-first-pipeline.md)
  + Saiba como [executar previsões em lotes usando pipelines](how-to-run-batch-predictions.md)
+ Computação de destino do Azure Machine Learning
  + [Notebooks de exemplo] (https://aka.ms/aml-notebooks) agora são atualizados para usar essa nova computação gerenciada.
  + [Saiba mais sobre essa computação](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Portal do Azure: novos recursos
+ Crie e gerencie tipos de [Computação do Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) no portal.
+ Monitore o uso de cota e a [cota de solicitações](how-to-manage-quotas.md) da Computação do Azure Machine Learning.
+ Veja o status do cluster da Computação do Machine Learning em tempo real.
+ Foi adicionado suporte à rede virtual para a criação de Computação do Azure Machine Learning e do Serviço de Kubernetes do Azure.
+ Execute novamente os pipelines publicados com parâmetros existentes.
+ Novos [gráficos de aprendizado de máquina automatizado](how-to-track-experiments.md#auto) para modelos de classificação (gráfico de importância de recursos de comparação de precisão e calibragem com explicabilidade de modelo) e modelos de regressão (resíduos e gráfico de importância de recursos com explicabilidade de modelo). 
+ Os pipelines podem ser exibidos no portal do Azure




## <a name="2018-11-20"></a>20-11-2018

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>SDK do Azure Machine Learning para Python v0.1.80

+ **Alterações da falha** 
  * Namespace *azureml.Train.widgets* foi movido para *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* substitui as seguintes classes - *azureml.core.compute.BatchAICompute* e *azureml.core.compute.DSVMCompute*. A última classe será removida nas versões subsequentes. A classe AmlCompute tem agora uma definição mais fácil e simplesmente precisa de um vm_size e do max_nodes e dimensionará automaticamente a seu cluster de 0 para o max_nodes quando um trabalho for enviado. Nossos [notebooks de amostra] (https://github.com/Azure/MachineLearningNotebooks/tree/master/training) foram atualizados com essas informações e devem fornecer exemplos de uso. Esperamos que você goste dessa simplificação e dos muitos dos recursos mais interessantes para ficar em uma versão posterior!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>SDK de preparação de dados do AML v0.5.1 

Saiba mais sobre o SDK de preparação de dados lendo [docs de referência](https://aka.ms/data-prep-sdk).
+ **Novos recursos**
   * Criada uma nova CLI DataPrep para executar pacotes DataPrep e exibir o perfil de dados para um conjunto de dados ou o fluxo de dados
   * API de SetColumnType reprojetado para melhorar a usabilidade
   * Smart_read_file renomeado para auto_read_file
   * Agora inclui distorção e curtose no Perfil de Dados
   * Pode criar amostra com amostragem estratificada
   * Pode ler de arquivos zip que contenham arquivos CSV
   * Pode dividir conjuntos de dados por linha com a divisão aleatória (por exemplo, nos conjuntos de teste-treinamento)
   * Pode obter os tipos de dados de coluna de um fluxo de dados ou um perfil de dados chamando `.dtypes`
   * Pode obter a contagem de linhas de um fluxo de dados ou um perfil de dados chamando `.row_count`

+ **Correções de bug**
   * Corrigidos para conversão dupla 
   * Ativo corrigido após qualquer adição de coluna 
   * Corrigido um problema com FuzzyGrouping, onde ele não detectaria grupos em alguns casos
   * Função de classificação corrigida para respeitar a ordem de classificação de várias colunas
   * Fixos e/ou expressões para ser semelhante a como `pandas` lida com isso
   * Leitura corrigida do caminho dbfs
   * Mensagens de erro tornadas mais compreensíveis 
   * Agora não há mais falhar durante a leitura no destino de computação remota usando o token do AML
   * Agora não falhará na DSVM do Linux
   * Agora não falha quando os valores de cadeia de caracteres não são em predicados de cadeia de caracteres
   * Agora manipula erros de asserção ao fluxo de dados deve falhar corretamente
   * Agora dá suporte a locais de armazenamento dbutils montados no Azure Databricks

## <a name="2018-11-05"></a>05-11-2018

### <a name="azure-portal"></a>Portal do Azure 
O portal do Azure para o serviço de AML tem as seguintes atualizações:
  * Uma nova **Pipelines** guia para pipelines publicados.
  * Adicionado suporte para anexar a um cluster HDInsight existente como um destino de computação.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>SDK de aprendizado de máquina do AML para Python v0.1.74

+ **Alterações da falha** 
  * Workspace.compute_targets, datastores, experimentos, imagens, modelos e *webservices* são propriedades em vez de métodos. Por exemplo, substitua *Workspace.compute_targets()* com *Workspace.compute_targets*.
  * *Run.get_context* pretere *Run.get_submitted_run*. O último método será removido em versões subsequentes.
  * A classe *PipelineData* agora espera um objeto de armazenamento de dados como um parâmetro em vez de datastore_name. Da mesma forma, o *Pipeline* aceita default_datastore em vez de default_datastore_name.

+ **Novos recursos**
  * O caderno de amostras de [amostra do AML](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) agora usa etapas de MPI.
  * O widget RunDetails para notebooks Jupyter é atualizado para mostrar uma visualização do pipeline.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>SDK de preparação de dados do AML v0.4.0 
 
+ **Novos recursos**
  * Tipo de contagem adicionada ao perfil de dados 
  * Contagem de valores e histograma estão agora disponíveis
  * Mais percentis no perfil de dados
  * Mediana está disponível em Summarize
  * Agora há suporte para Python 3.7
  * Quando você salva um fluxo de dados que contém datastores em um pacote DataPrep, as informações do armazenamento de dados serão mantidas como parte do pacote DataPrep
  * A gravação no armazenamento de dados agora é suportada 
        
+ **Erro corrigido**
  * Extensões de inteiro não assinado de 64 bits agora são tratadas corretamente no Linux
  * Etiqueta de texto incorreta corrigida para arquivos de texto simples em smart_read
  * O tipo de coluna de cadeia de caracteres agora aparece na visualização de métricas
  * A contagem de tipos agora é fixada para mostrar ValueKinds mapeados para um único FieldType em vez de um individual
  * Write_to_csv não falha quando o caminho é fornecido como uma cadeia de caracteres
  * Ao usar Replace, deixar "localizar" em branco não falhará mais 

## <a name="2018-10-12"></a>12-10-2018

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>SDK de aprendizado de máquina do Azure para Python v0.1.68

+ **Novos recursos**
  * Suporte a vários locatários ao criar novo espaço de trabalho.

+ **Bugs corrigidos**
  * Você não precisa fixar a versão da biblioteca pynacl ao implantar o serviço web.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>SDK de preparação de dados de aprendizado de máquina do Azure v0.3.0

+ **Novos recursos**
  * Adicionado método transform_partition_with_file (script_path), que permite aos usuários passar no caminho de um arquivo Python para executar

## <a name="2018-10-01"></a>01-10-2018

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>SDK de aprendizado de máquina do Azure para Python v0.1.65
A [versão 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) inclui novos recursos, mais documentação, correções de bugs e mais [exemplos de blocos de notas](https://aka.ms/aml-notebooks).

Veja [a lista de problemas conhecidos](resource-known-issues.md) para aprender sobre erros e soluções conhecidas.

+ **Alterações da falha**
  * Workspace.Experiments, Workspace.models, Workspace.compute_targets, Workspace.images, dicionário de retorno Workspace.web_services, retornado anteriormente de lista. Veja a documentação da API [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py).

  * Automatizados de aprendizado de máquina removido erro normalizado quadrada da média das métricas principais.

+ **HyperDrive**
  * Várias correções de bugs do HyperDrive para Bayesian, melhorias de desempenho para obter chamadas de métricas. 
  * Atualização de Tensorflow 1.10 do 1.9 
  * Otimização de imagem do Docker para inicialização a frio. 
  * O trabalho agora reporta o status correto, mesmo se ele sair com um código de erro diferente de 0. 
  * Validação de atributo RunConfig no SDK. 
  * O objeto de execução HyperDrive suporta cancelamentos semelhantes a uma execução normal: não é necessário passar nenhum parâmetro. 
  * Melhorias de widget para manter o estado dos valores suspensos para execuções distribuídas e execuções do HyperDrive. 
  * TensorBoard e outros arquivos de log suportam fixos para o servidor Parameter. 
  * Suporte ao Intel (R) MPI no lado do serviço. 
  * Bugfix ao ajuste de parâmetro para correção de execução distribuída durante a validação no BatchAI. 
  * O Context Manager agora identifica a instância primária. 

+ **Experiência do portal do Azure**
  * log_table () e log_row () são suportados em detalhes da execução. 
  * Crie automaticamente gráficos para tabelas e linhas com 1, 2 ou 3 colunas numéricas e uma coluna categórica opcional.

+ **Automatizado de Machine Learning**
  * Melhor tratamento e documentação de erros 
  * Corrigidos problemas de desempenho de recuperação de propriedades de execução. 
  * Fixo continuar a execução do problema. 
  * Correção de ensembling problemas de iteração.
  * Bug deslocada de treinamento fixo no MAC OS.
  * Curva média de solicitação de pull/ROC no cenário de validação personalizada da macro da resolução.
  * Removida a lógica extra de índice.
  * Removemos o filtro de get_output API.

+ **Pipelines**
  * Adicionado um método Pipeline.publish () para publicar um pipeline diretamente, sem precisar executar uma execução primeiro.   
  * Adicionado um método PipelineRun.get_pipeline_runs () para buscar as execuções de pipeline que foram geradas a partir de um pipeline publicado.

+ **Project Brainwave**
  * Suporte atualizado para novos modelos de IA disponíveis em FPGAs.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>SDK de preparação de dados de aprendizado de máquina do Azure v0.2.0
[Versão 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) inclui recursos e correções de bug a seguir:

+ **Novos recursos**
  * Suporte para codificação one-hot
  * Suporte para a transformação de quantil
   
+ **Bug corrigido:**
  * Funciona com qualquer versão do Tornado, sem necessidade de rebaixar sua versão do Tornado
  * O valor conta para todos os valores, não apenas os três primeiros

## <a name="2018-09-public-preview-refresh"></a>09-2018 (atualização da visualização pública)

Uma nova versão atualizada do Azure Machine Learning: Leia mais sobre essa versão: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/

## <a name="older-notes-sept-2017---jun-2018"></a>Versões mais antigas: Setembro de 2017 – junho de 2018
### <a name="2018-05-sprint-5"></a>2018-05 (Sprint 5)

Com esta versão do Azure Machine Learning, você pode:
+ Apresentar imagens com uma versão quantificada de ResNet 50, treinar um classificador com base nesses recursos e [implantar esse modelo em um FPGA no Azure](../service/how-to-deploy-fpga-web-service.md) para uma inferência de latência extremamente baixa.

+ Criar e implantar rapidamente o aprendizado de máquina altamente preciso e modelos de aprendizado avançado usando [Pacotes personalizados do Azure Machine Learning](../desktop-workbench/reference-python-package-overview.md)

### <a name="2018-03-sprint-4"></a>2018-03 (Sprint 4)
**Número de versão**: 0.1.1801.24353  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Encontre sua versão](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Muitas das atualizações a seguir foram feitas como resultado direto de seus comentários. Continue enviando-os!

**Alterações e novos recursos importantes**

- Suporte para executar os scripts em VMs remotas do Ubuntu nativamente em seu próprio ambiente, além de execução baseada em docker remoto.
- A nova experiência de ambiente no aplicativo Workbench permite que você crie destinos de computação e execute configurações além de nossa experiência com base em CLI.
![Guia Ambientes](media/azure-machine-learning-release-notes/environment-page.png)
- O Histórico de Execução Personalizável relata a ![Imagem de novos Relatórios de Histórico de Execução](media/azure-machine-learning-release-notes/new-run-history-reports.png)

**Atualizações detalhadas**

A seguir, está uma lista de atualizações detalhadas em cada área de componente do Azure Machine Learning neste sprint.

#### <a name="workbench-ui"></a>Interface do usuário do Workbench
- Relatórios de Histórico de Execução Personalizável
  - Configuração de gráfico aprimorado para relatórios de Histórico de Execução
    - Os pontos de entrada usados podem ser alterados
    - Filtros de nível superior podem ser adicionados e modificados em ![Adicionar filtros](media/azure-machine-learning-release-notes/add-filters.jpg)
    - Gráficos e estatísticas podem ser adicionados ou modificados (e reorganizados por arrastar e soltar).
    ![Criar novos Gráficos](media/azure-machine-learning-release-notes/configure-charts.png)

  - CRUD para relatórios de Histórico de Execução
  - Movidas todos os arquivos de configuração de exibição de lista do histórico de execução existente para relatórios no lado do servidor, que agem como pipelines em execuções dos pontos de entrada selecionados.

- Guia Ambientes
  - Adicionar novo destino de computação e executar arquivos de configuração com facilidade para seu projeto ![Novo destino de computação](media/azure-machine-learning-release-notes/add-new-environments.png)
  - Gerenciar e atualizar os arquivos de configuração usando um UX simples baseado em formulário
  - Novo botão para preparar seus ambientes para execução

- Melhorias de desempenho para a lista de arquivos na barra lateral

#### <a name="data-preparation"></a>Preparação dos dados 
- Azure Machine Learning Workbench agora permite que você seja capaz de pesquisar uma coluna usando o nome de uma coluna conhecida.


#### <a name="experimentation"></a>Experimentação
- O Azure Machine Learning Workbench agora oferece suporte à execução dos scripts de modo nativo em seu próprio ambiente python ou pyspark. Para essa funcionalidade, o usuário cria e gerencia seu próprio ambiente na VM remota e usa o Azure Machine Learning Workbench para executar scripts nesse destino. Consulte [Configuração do Serviço de Experimentação do Azure Machine Learning](../desktop-workbench/experimentation-service-configuration.md) 

#### <a name="model-management"></a>Gerenciamento de Modelos
- Suporte para personalizar os contêineres implantados: habilita a personalização da imagem de contêiner, permitindo a instalação de bibliotecas externas usando apt-get, etc. Não é mais limitado às bibliotecas instaláveis com pip. Para saber mais, confira a [documentação](../desktop-workbench/model-management-custom-container.md).
  - Use o sinalizador `--docker-file myDockerStepsFilename` e o nome do arquivo com o manifesto, imagem ou comandos de criação de serviço.
  - A imagem base é Ubuntu e não pode ser modificada.
  - Exemplo de comando: 
  
    ```shell
    $ az ml image create -n myimage -m mymodel.pkl -f score.py --docker-file mydockerstepsfile
    ```

### <a name="2018-01-sprint-3"></a>2018-01 (Sprint 3) 
**Número de versão**: 0.1.1712.18263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Encontre sua versão](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Veja a seguir as atualizações e melhorias neste sprint. Muitas dessas atualizações são feitas como resultado direto dos comentários do usuário. 

### <a name="2017-12-sprint-2"></a>2017-12 (Sprint 2)
**Número de versão**: 0.1.1711.15263  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Encontre sua versão](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Essa atualização é a terceira do Azure Machine Learning. Esta atualização inclui melhorias no aplicativo workbench, a CLI (Interface de Linha de Comando) e os serviços de back-end. Agradecemos imensamente por nos enviar sorrisos e rostos tristes. Muitas das atualizações a seguir foram feitas como resultado direto de seus comentários. 

### <a name="2017-11-sprint-1"></a>2017-11 (Sprint 1) 
**Número de versão**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Encontre sua versão](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Nessa versão, fizemos melhorias em segurança, estabilidade e facilidade de manutenção no aplicativo Workbench, na CLI e na camada de serviços back-end. 

### <a name="2017-10-sprint-0"></a>2017-10 (Sprint 0) 
**Número de versão**: 0.1.1710.31013  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;([Encontre sua versão](../desktop-workbench/known-issues-and-troubleshooting-guide.md#find-the-workbench-build-number))

Essa versão é a primeira atualização ao Workbench de Machine Learning do Azure seguindo nossa visualização pública inicial na conferência Microsoft Ignite 2017. As principais atualizações nesta versão são correções de confiabilidade e estabilização. 

## <a name="next-steps"></a>Próximas etapas

Leia a visão geral do [Azure Machine Learning](../service/overview-what-is-azure-ml.md).
