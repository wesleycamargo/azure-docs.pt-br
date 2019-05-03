---
title: Criar e explorar experimentos no Portal
titleSuffix: Azure Machine Learning service
description: Saiba como criar e gerenciar automatizado experimentos de machine learning no portal
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: 8b6d7f791300a970e71fda4f1d56354a45d07afd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029889"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>Criar e explorar automatizado experimentos de machine learning no portal do Azure (visualização)

 Neste artigo, você aprenderá como criar, executar e explorar automatizado experimentos de machine learning no portal do Azure sem uma única linha de código. Aprendizado de máquina automatizado automatiza o processo de selecionar o melhor algoritmo para usar seus dados específicos, portanto, você pode gerar um modelo de aprendizado de máquina rapidamente. [Saiba mais sobre o aprendizado de máquina automatizado](https://docs.microsoft.com/azure/machine-learning/service/concept-automated-ml).

 Se você preferir uma experiência mais de código com base, você também pode [configurar seus experimentos em Python de aprendizado de máquina automatizada](how-to-configure-auto-train.md) com o [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

* Um workspace de serviço do Azure Machine Learning. Ver [criar um espaço de trabalho do serviço de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

## <a name="get-started"></a>Introdução

Navegue até o painel esquerdo do seu espaço de trabalho. Selecione automatizada Machine Learning na seção criação (visualização).

![Painel de navegação do portal do Azure](media/how-to-create-portal-experiments/nav-pane.png)

 Quando se trata de fazer qualquer experimentos com aprendizado de máquina automatizada pela primeira vez, você verá o seguinte:

![Página de aterrissagem do experimento de portal do Azure](media/how-to-create-portal-experiments/landing-page.png)

Caso contrário, você verá seu painel automatizado machine learning com uma visão geral de todos os testes e execuções, incluindo aqueles que são executados usando o SDK de aprendizado de máquina automatizada. Aqui você pode filtrar e explorar suas execuções por data, experimente o nome e status de execução.

![Painel de teste de portal do Azure](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>Criar uma experiência

Selecione o botão Criar experimento para preencher o formulário a seguir.

![Criar formulário de teste](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. Insira seu nome de experimento.

1. Selecione uma computação para a criação de perfil de dados e o trabalho de treinamento. Uma lista de seus serviços de computação existentes está disponível na lista suspensa. Para criar uma nova computação, siga as instruções na etapa 3.

1. Selecione o botão Criar uma nova computação para abrir o abaixo do painel e configurar o contexto de computação para esse teste.

    ![Criar nova computação para teste](media/how-to-create-portal-experiments/create-new-compute.png)

    Campo|DESCRIÇÃO
    ---|---
    Nome de computação| Insira um nome exclusivo que identifica seu contexto de computação.
    Tamanho da máquina virtual| Selecione o tamanho de máquina virtual para sua computação.
    Configurações adicionais| *Nó de Min*: Insira o número mínimo de nós para sua computação. O número mínimo de nós de computação do AML é 0. Para habilitar a criação de perfil de dados, você deve ter 1 ou mais nós. <br> *Nó de max*: Insira o número máximo de nós para sua computação. O padrão é 6 nós para uma computação do AML.

      Para iniciar a criação de sua nova computação, selecione **criar**. Isso pode levar alguns instantes.

      >[!NOTE]
      > Seu nome de computação indicará se a computação que você selecione/cria *criação de perfil habilitada*. (Consulte 7b para obter mais detalhes sobre a criação de perfil de dados).

1. Selecione uma conta de armazenamento para seus dados. Visualização pública só dá suporte a uploads de arquivos local e contas de armazenamento de BLOBs do Azure.

1. Selecione um contêiner de armazenamento.

1. Selecione um arquivo de dados de seu contêiner de armazenamento ou carregar um arquivo do seu computador local para o contêiner.

    ![Selecione o arquivo de dados de teste](media/how-to-create-portal-experiments/select-file.png)

1. Use as guias de visualização e de perfil para configurar ainda mais seus dados para esse teste.

    1. Na guia Visualização, indicar se os dados incluem cabeçalhos e selecione os recursos (colunas) para treinamento usando o **incluídos** Alternar botões em cada coluna de recurso.

        ![Visualização de dados](media/how-to-create-portal-experiments/data-preview.png)

    1. Na guia perfil, você pode exibir o [perfil de dados](#profile) por recurso, bem como a distribuição, tipo e estatísticas de resumo (média, mediana, max/min e assim por diante) de cada um.

        ![Guia de perfil de dados](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > A seguinte mensagem de erro será exibido se for de seu contexto de computação **não** habilitados de criação de perfil: *Criação de perfil de dados está disponível apenas para destinos de computação que já estão em execução*.

1. Selecione o tipo de trabalho de treinamento: classificação, regressão ou previsão.

1. Selecione a coluna de destino. A coluna que você gostaria de fazer previsões em.

1. Para fazer previsões:
    1. Selecione a coluna de hora: Esta coluna contém os dados de tempo a ser usado.
    1. Selecione o horizonte de previsão: Indica quantas unidades de tempo (minutos/horas/dias/semanas/meses/anos) o modelo será capaz de prever o futuro. Quanto mais o modelo é necessária para prever o futuro, menos precisa tornará. [Saiba mais sobre previsão e previsão horizonte](https://docs.microsoft.com/azure/machine-learning/service/how-to-auto-train-forecast#configure-experiment).

1. (Opcional) Configurações avançadas: configurações adicionais que você pode usar para controlar melhor o trabalho de treinamento.

    Configurações avançadas|DESCRIÇÃO
    ------|------
    Métrica principal| Métrica principal usada para pontuar o modelo. [Saiba mais sobre as métricas do modelo](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#explore-model-metrics).
    Critérios de saída| Quando qualquer um desses critérios são atendidos, o trabalho de treinamento termina antes da conclusão completo. <br> *O tempo (minutos) do trabalho de treinamento*: Quanto tempo para permitir a execução do trabalho de treinamento.  <br> *Número máximo de iterações*: Número máximo de pipelines (iterações) para o trabalho de treinamento de teste. O trabalho não será executado mais do que o número especificado de iterações. <br> *Métrica de limite de pontuação*:  Pontuação mínima de métrica para todos os pipelines. Isso garante que se você tiver uma métrica de destino definida que você deseja acessar, não gastar mais tempo no trabalho de treinamento que o necessário.
    Pré-processamento| Selecione para habilitar ou desabilitar o pré-processamento feito pelo automatizado de machine learning. Pré-processamento inclui limpeza automática de dados, preparação e transformação para gerar recursos sintéticos. [Saiba mais sobre o pré-processamento](#preprocess).
    Validação| Selecione uma das opções de validação cruzada para usar no trabalho de treinamento. [Saiba mais sobre validação cruzada](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#cross-validation-split-options).
    Simultaneidade| Selecione os limites de vários núcleos que você deseja usar ao usar vários núcleo computação.
    Algoritmo bloqueado| Selecione algoritmos que você deseja excluir o trabalho de treinamento.

   ![Formulário de configurações avançadas](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> Para obter mais informações sobre campos, clique a dica de ferramenta informações.

<a name="profile"></a>

### <a name="data-profiling"></a>Criação de perfil

Você pode obter uma ampla variedade de estatísticas de resumo em seu conjunto de dados para verificar se o seu conjunto de dados está pronto para ML. Para colunas não numéricas, elas incluem estatísticas apenas básicas, como min, max e contagem de erros. Para colunas numéricas, você também pode examinar seus momentos estatísticos e estimado de quantis. Especificamente, nosso perfil de dados inclui:

* **Recurso**: nome da coluna que está sendo resumida.

* **Perfil**: uma visualização em linha com base no tipo inferido. Por exemplo, cadeias de caracteres, booleanos e datas terá contagens de valor, enquanto os decimais (numéricos) têm aproximadas histogramas. Isso permite que você obtenha uma compreensão rápida da distribuição dos dados.

* **Distribuição de tipo**: uma contagem de valor na linha de tipos dentro de uma coluna. Valores nulos são seu próprio tipo, portanto essa visualização é útil para detectar valores ausentes ou ímpares.

* **Tipo**: o tipo inferido da coluna. Os valores possíveis incluem: cadeias de caracteres, booleanos, datas e casas decimais.

* **Min**: o valor mínimo da coluna. Entradas em branco são exibidas para os recursos cujo tipo não tem uma ordem inerente (por exemplo, boolianos).

* **Max**: o valor máximo da coluna. Como "min", entradas em branco aparecem para recursos com tipos irrelevantes.

* **Contagem de**: o número total de entradas ausentes e não ausentes na coluna.

* **Não ausente contagem**: o número de entradas na coluna que não estão ausentes. Observe que erros e cadeias de caracteres vazias são tratados como valores, portanto, eles não contribuirá para a "contagem não ausente".

* **Quantis** (em intervalos de 0,1, 1, 5, 25, 50, 75, 95, 99 e 99,9%): os valores aproximados em cada quantil para fornecer uma noção da distribuição dos dados. Entradas em branco são exibidos para os recursos com tipos irrelevantes.

* **Significa**: a média aritmética da coluna. Entradas em branco são exibidos para os recursos com tipos irrelevantes.

* **Desvio padrão**: o desvio padrão da coluna. Entradas em branco são exibidos para os recursos com tipos irrelevantes.

* **Variação**: a variação da coluna. Entradas em branco são exibidos para os recursos com tipos irrelevantes.

* **A distorção**: a distorção da coluna. Entradas em branco são exibidos para os recursos com tipos irrelevantes.

* **Curtose**: a curtose de coluna. Entradas em branco são exibidos para os recursos com tipos irrelevantes.

Além disso, você pode usar essas estatísticas para decidir se deseja incluir ou excluir determinadas colunas. Ativando/desativando o seletor de cada coluna, você pode controlar o escopo em que as colunas serão usadas no seu experimento de aprendizado de máquina automatizada.

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>Avançadas de pré-processamento

Ao configurar seus testes, você pode habilitar a configuração avançada `Preprocess`. Portanto, a fazer significa que as seguintes etapas de pré-processamento e a personalização de dados são executadas automaticamente.

|Pré-processamento&nbsp;etapas| DESCRIÇÃO |
| ------------- | ------------- |
|Remover alta cardinalidade ou nenhum recurso de variação|Cancele-os de conjuntos de treinamento e validação, incluindo recursos com todos os valores ausentes, o mesmo valor em todas as linhas ou com cardinalidade muito alta (por exemplo, hashes, IDs ou GUIDs).|
|Acrescentar valores ausentes|Para recursos numéricos, imputar com média de valores na coluna.<br/><br/>Para recursos categóricos, imputar com o valor mais frequente.|
|Gerar recursos adicionais|Para recursos DateTime: Ano, mês, dia, dia da semana, dia do ano, trimestre, semana do ano, hora, minuto, segundo.<br/><br/>Para recursos Text: Frequência de termo com base em caractere-tri-grams, bi-grams e unigrams.|
|Transformar e codificar |Recursos numéricos com poucos valores exclusivos são transformados em recursos categóricos.<br/><br/>Codificação One-hot é realizada para baixa cardinalidade categórica; alta cardinalidade, codificação one-hot-hash.|
|Incorporações de palavras|Recursos de texto que converte os vetores de tokens de texto em vetores de frase usando um modelo previamente treinado. Vetor de incorporação de cada palavra em um documento é agregado em conjunto para produzir um vetor de recurso do documento.|
|Codificações de destino|Para recursos categóricos, mapeia cada categoria com o valor de destino média para problemas de regressão e para a probabilidade de classe para cada classe para problemas de classificação. A ponderação baseada em frequência e k vezes a validação cruzam é aplicada para reduzir o ajuste do mapeamento e ruído causados por categorias de dados esparsos.|
|Codificação de destino do texto|Entrada de texto, um modelo linear empilhado com bolsa de palavras é usado para gerar a probabilidade de cada classe.|
|Peso de evidência (contratempos)|Calcula contratempos como uma medida de correlação de colunas categóricas para a coluna de destino. Ele é calculado como o log da taxa de probabilidades de fora da classe na classe vs. Esta etapa gera uma coluna de recurso numérico por classe e remove a necessidade de explicitamente imputar valores ausentes e tratamento de exceções.|
|Distância do cluster|Treina um modelo de clustering k-means em todas as colunas numéricas.  Saídas k novos recursos, um novo recurso numérico por cluster, que contém a distância de cada exemplo para o centroide de cada cluster.|

## <a name="run-experiment"></a>Executar o experimento

Para executar o experimento, clique no botão Iniciar.
  
O experimento Preparando processo leva alguns minutos.

## <a name="view-results"></a>Exibir resultados

Depois que a fase de preparação do teste for concluída, você verá a tela de detalhes de execução. Isso dará a você uma lista completa dos modelos criados. Por padrão, o modelo que marca a mais alta com base em seus parâmetros serão na parte superior da lista. Conforme o trabalho de treinamento tenta out mais modelos, você os verá adicionados à lista e o gráfico.
Use o gráfico para obter uma comparação rápida das métricas para os modelos produzidos até o momento.

![Painel de detalhes de execução](media/how-to-create-portal-experiments/run-details.png)

É possível fazer uma busca detalhada em qualquer um dos modelos de saída que abre os detalhes desse modelo, incluindo métricas e gráficos de desempenho e de distribuição. [Saiba mais sobre gráficos](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments#understanding-automated-ml-charts).

![Detalhes de iteração](media/how-to-create-portal-experiments/dashboard.png)

Trabalhos de treinamento pode levar algum tempo para cada pipeline concluir a execução.

## <a name="deploy-model"></a>Implantar modelo

Depois de ter o melhor modelo em mãos, é hora de implantá-lo como um serviço web para prever novos dados.

ML automatizado ajuda você com a implantação de modelo sem escrever código:

1. No painel Resumo de execução à direita, selecione "Registrar o modelo".

    ![Registrar modelo](media/how-to-create-portal-experiments/register-model.png)

1. Quando o modelo estiver registrado, você poderá baixar o script de pontuação a ser usado durante a implantação.

    ![Baixe o script de pontuação](media/how-to-create-portal-experiments/download-scoring-script.png)

1. Depois que o script de pontuação, vá para a página de "Modelos" (no painel de navegação à esquerda, sob **ativos**).

    ![Painel de navegação do modelo](media/how-to-create-portal-experiments/nav-pane-models.png)

1. Marque a caixa de seleção ao lado do modelo que você registrou e selecione "Criar a imagem".

    Você pode identificar o modelo, sua descrição, o que inclui o número da iteração e a ID de execução, no seguinte formato: **modelo _ < Iteration_number > de < Run_ID >**.

1. Insira um nome para a imagem e carregar o arquivo de pontuação que você baixou anteriormente. [Saiba mais sobre scripts de pontuação](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where.md#script).

    Você pode usar seu próprio script de pontuação e o arquivo Conda. Se você não tiver um arquivo de Conda [criar seu próprio](tutorial-deploy-models-with-aml.md#create-environment-file) e carregá-lo juntamente com quaisquer outros arquivos adicionais que talvez você queira usar.

    ![Criar um formulário de imagem](media/how-to-create-portal-experiments/create-image.png)

1. Selecione o botão "Criar" para iniciar a criação de imagem. Isso levará alguns minutos para ser concluída, uma vez concluído, você verá uma mensagem na barra superior.

1. Vá para a guia "Imagens", marque a caixa de seleção ao lado da imagem que você deseja implantar e selecione "Criar a implantação".

    ![Tela de implantação de criação de imagem](media/how-to-create-portal-experiments/images-create-deployment.png)

1. Insira um nome exclusivo da implantação.

1. (opcional) Insira uma descrição para a implantação.

1. Selecione o tipo de destino de computação usar. 

    ![Criar formulário de implantação](media/how-to-create-portal-experiments/create-deployment.png)

1. Selecione "Criar" para iniciar o processo de implantação, levará vários minutos para ser concluída.

1. É isso! Você tem um serviço web operacional para gerar previsões.

## <a name="next-steps"></a>Próximas etapas

* [Como consumir um modelo implantado](how-to-consume-web-service.md).
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md).