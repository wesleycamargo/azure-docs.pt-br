---
title: O que é o Azure Machine Learning Studio? | Microsoft Docs
description: Visão geral do Azure ML Studio, uma ferramenta do tipo "arrastar e soltar" para criar rapidamente modelos em de uma biblioteca de algoritmos e módulos pronta para uso.
keywords: aprendizado de máquina do azure, azure ml, ml studio
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/28/2018
ms.openlocfilehash: b8a1723d95e6f13ca81cacc3d73aa55c6d8fa06b
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393937"
---
# <a name="what-is-azure-machine-learning-studio"></a>O que é o Azure Machine Learning Studio?
O Microsoft Azure Machine Learning Studio é uma ferramenta colaborativa do tipo "arrastar e soltar", que você pode usar para criar, testar e implantar soluções de análise preditiva em seus dados. O Machine Learning Studio publica modelos como serviços Web que podem ser facilmente consumidos por aplicativos personalizados ou ferramentas de BI como o Excel.

O Machine Learning Studio é onde a ciência de dados, as análises preditivas, os recursos de nuvem e seus dados se encontram!

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>O espaço de trabalho interativo do Machine Learning Studio
Para desenvolver um modelo de análise preditiva, normalmente você usa dados de uma ou mais fontes, transforma e analisa esses dados com várias funções de manipulação de dados e estatísticas e gera um conjunto de resultados. Desenvolver um modelo como este é um processo iterativo. À medida que você modifica as diversas funções e seus parâmetros, seus resultados convergem até que você esteja satisfeito, com um modelo treinado e eficiente.

**Azure Machine Learning Studio** oferece um espaço de trabalho visual e interativo para compilar, testar e iterar em um modelo de análise preditivo. Você arrasta e solta ***conjuntos de dados*** e ***módulos*** de análise em telas interativas conectando-as para formar um ***teste***, o qual você executa no Azure Machine Learning Studio. Para iterar em seu design de modelo, você edita o teste, salva uma cópia, se desejado, e executa-o novamente. Quando você estiver pronto, você poderá converter seu ***teste de treinamento*** em uma ***experiência preditiva*** e, em seguida, publicá-la como um ***serviço Web*** para que seu modelo possa ser acessado por outras pessoas.

Não há necessidade de programação, basta conectar visualmente os conjuntos de dados e módulos para construir seu modelo de análise preditivo.

> [!TIP]
> Para baixar e imprimir um diagrama que fornece uma visão geral dos recursos do Machine Learning Studio, consulte [Diagrama de visão geral dos recursos do Azure Machine Learning Studio](studio-overview-diagram.md).
> 
> 

![Diagrama do Azure ML Studio: criar testes, ler dados de várias fontes, gravar dados de pontuação, escrever modelos.][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Introdução ao Machine Learning Studio
Ao entrar pela primeira vez no [Machine Learning Studio,](https://studio.azureml.net) você verá a página **Inicial** . Aqui você pode exibir a documentação, vídeos, seminários na Web e obter outros recursos valiosos.

Clique no menu superior esquerdo ![Menu](./media/what-is-ml-studio/menu.png) e você verá várias opções.

### <a name="cortana-intelligence"></a>Cortana Intelligence
Clique em **Cortana Intelligence** e você será levado para a página inicial do [Cortana Intelligence Suite](https://www.microsoft.com/cloud-platform/cortana-intelligence-suite). O Cortana Intelligence Suite é um pacote de análise avançada e de big data totalmente gerenciado que permite transformar seus dados em ação inteligente. Consulte a página do Suite para obter a documentação completa, inclusive histórias de clientes.

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
Há duas opções aqui, **Home**, a página onde você começou, e **Studio**.

Clique em **Studio** e você será levado para o **Azure Machine Learning Studio**. Em primeiro lugar, será solicitado que você entre usando a conta da Microsoft, ou sua conta de trabalho ou escola. Depois de conectado, você verá as seguintes guias à esquerda:

* **PROJETOS** - coleções de testes, conjuntos de dados, blocos de anotações e outros recursos que representam um único projeto
* **TESTES** – Testes que você criou e executou ou salvou como rascunhos
* **SERVIÇOS WEB** -serviços Web que você implantou dos testes
* **NOTEBOOKS** - Notebooks Jupyter que você criou
* **CONJUNTOS DE DADOS** - Conjuntos de dados que você carregou no Estúdio
* **MODELOS TREINADOS** - Modelos que você treinou em testes e salvos no Estúdio
* **CONFIGURAÇÕES** – Uma coleção das configurações que você pode usar para configurar sua conta e recursos.

### <a name="gallery"></a>Galeria
Clique em **Galeria** e você será levado para a **[Galeria de IA do Azure](http://gallery.cortanaintelligence.com/)**. A Galeria é um lugar no qual uma comunidade de desenvolvedores e cientistas de dados podem compartilhar soluções criadas usando os componentes do Cortana Intelligence Suite.

Para saber mais sobre a Galeria, veja as [Soluções de compartilhamento e descoberta na Galeria de IA do Azure](gallery-how-to-use-contribute-publish.md).

## <a name="components-of-an-experiment"></a>Componentes de um teste
Um teste consiste em conjuntos de dados que fornecem dados para módulos analíticos, os quais você conecta para construir um modelo de análise preditiva. Especificamente, um teste válido possui três características:

* O teste tem no mínimo um conjunto de dados e um módulo.
* Os conjuntos de dados podem ser conectados somente aos módulos.
* Os módulos podem ser conectados a conjuntos de dados ou a outros módulos.
* Todas as portas de entrada dos módulos devem ter uma conexão com o fluxo de dados.
* Todos os parâmetros necessários para cada módulo devem estar configurados.

Você pode criar uma experiência do zero, ou você pode usar uma experiência de exemplo existente como um modelo. Para saber mais, confira [Copiar os testes de amostra para criar novos experimentos do aprendizado de máquina](sample-experiments.md).

Para obter um exemplo de criação de um teste simples, consulte [Criar um teste simples no Azure Machine Learning Studio](create-experiment.md).

Para obter uma explicação mais completa da criação de uma solução de análise preditiva, consulte [Desenvolver uma solução preditiva com o Azure Machine Learning](walkthrough-develop-predictive-solution.md).

### <a name="datasets"></a>Conjunto de dados
Um conjunto de dados inclui dados que foram atualizados no Machine Learning Studio, de forma que possam ser usados no processo de modelagem. Alguns conjuntos de dados de amostra estão incluídos no Machine Learning Studio, com os quais você pode testar, além de ser possível também carregar mais conjuntos de dados, caso necessário. Aqui estão alguns exemplos dos conjuntos de dados incluídos:

* **Dados MPG para vários automóveis** – valores MPG (milhas por galão) para automóveis identificados por número de cilindros, cavalo-vapor, etc.
* **Dados de câncer de mama** – dados de diagnósticos de câncer de mama
* **Dados de incêndios em florestas** – tamanhos dos incêndios em floresta na região nordeste de Portugal

À medida que você compila um teste, você pode escolher na lista de conjuntos de dados à esquerda das telas.

Para obter uma lista de conjuntos de dados de exemplo incluídos no Machine Learning Studio, consulte [Usar os conjuntos de dados de exemplo no Azure Machine Learning Studio](use-sample-datasets.md).

### <a name="modules"></a>Módulos
Um módulo é um algoritmo que você pode executar em seus dados. O Machine Learning Studio tem uma série de módulos, desde funções de ingresso até treinamento, pontuação e processos de validação. Aqui estão alguns exemplos dos módulos incluídos:

* [Converter em ARFF][convert-to-arff] – converte um conjunto de dados serializados .NET no formato ARFF (Arquivo de Atributo-Relação).
* [Calcular Estatísticas Elementares][elementary-statistics] – calcula as estatísticas elementares como média, desvio padrão etc.
* [Regressão Linear][linear-regression] – cria um modelo online de regressão linear com base em descendência gradiente.
* [Modelo de Pontuação][score-model] – pontua uma classificação treinada ou modelo de regressão.

À medida que você compila um teste, você pode escolher a partir da lista de módulos à esquerda das telas.  

Um módulo pode ter um conjunto de parâmetros que você pode usar para configurar os algoritmos internos do módulo. Ao selecionar um módulo nas telas, os parâmetros do módulo são exibidos no painel **Propriedades** à direita das telas. Você pode modificar os parâmetros nesse painel para ajustar seu modelo.

Para obter ajuda sobre navegação através da biblioteca grande dos algoritmos de aprendizado de máquina disponíveis, consulte [Como escolher algoritmos para o Microsoft Azure Machine Learning](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Implantando um serviço Web de análise preditiva
Quando seu modelo de análise preditiva estiver pronto, você pode implantá-lo como um serviço Web diretamente no Machine Learning Studio. Para obter mais detalhes sobre esse processo, consulte [Implantar um serviço Web do Azure Machine Learning](publish-a-machine-learning-web-service.md).

[ml-studio-overview]:./media/what-is-ml-studio/azure-ml-studio-diagram.jpg



## <a name="key-machine-learning-terms-and-concepts"></a>Principais termos e conceitos do aprendizado de máquina
Termos do aprendizado de máquina podem ser confusos. Aqui estão as definições dos principais termos para ajudá-lo. Use comentários para nos dizer sobre qualquer outro termo que gostaria de ver definido.

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>Exploração de dados, análise descritiva e análise preditiva

**Exploração de dados** é o processo de coleta de informações sobre um conjunto de dados grande e geralmente não estruturado para encontrar características para uma análise focada.

**Mineração de dados** refere-se à mineração de dados automatizada.

**Análise descritiva** é o processo de análise de um conjunto de dados para gerar um resumo do que aconteceu. A grande maioria da análise de negócios - como relatórios de vendas, métricas de Web e análises de redes sociais - são descritivas.

**Análise preditiva** é o processo de criação de modelos por meio de dados atuais ou históricos para prever resultados futuros.

### <a name="supervised-and-unsupervised-learning"></a>Aprendizado supervisionado e não supervisionado
 **aprendizado supervisionado** são treinados com dados rotulados – em outras palavras, dados compostos de exemplos das respostas desejadas. Por exemplo, um modelo que identifica o uso fraudulento do cartão de crédito seria treinado por meio de um conjunto de dados com pontos de dados rotulados de cobranças fraudulentas e válidas conhecidas. Na maioria das vezes, o aprendizado de máquina é supervisionado.

 **aprendizado não supervisionado** é usado em dados sem rótulos e o objetivo é localizar relações existentes nos dados. Por exemplo, você talvez queira localizar agrupamentos de dados demográficos de clientes com hábitos de compra semelhantes.

### <a name="model-training-and-evaluation"></a>Treinamento e avaliação de modelo
Um modelo de aprendizado de máquina é uma abstração da pergunta que você está tentando responder ou o resultado que você deseja prever. Os modelos são treinados e avaliados por meio de dados existentes.

#### <a name="training-data"></a>Dados de treinamento
Quando você treina um modelo a partir de dados, usa um conjunto de dados conhecido e faz ajustes no modelo com base nas características dos dados para obter a resposta mais precisa. No Azure Machine Learning, um modelo é criado por meio de um módulo de algoritmos que processa os dados de treinamento e módulos funcionais, por exemplo, um módulo de pontuação.

No aprendizado supervisionado, se você estiver treinando um modelo de detecção de fraudes, usará um conjunto de transações rotuladas como fraudulentas ou válidas. Você dividirá seu conjunto de dados aleatoriamente e usará uma parte para treinar o modelo e outra para testar ou avaliar o modelo.

#### <a name="evaluation-data"></a>Dados de avaliação
A partir do momento em que você tem um modelo treinado, avalie esse modelo usando os dados de teste restantes. Você utiliza os dados cujos resultados você já conhece, para que você possa determinar se o seu modelo prevê com precisão.

## <a name="other-common-machine-learning-terms"></a>Outros termos comuns de aprendizado de máquina
* **algoritmo**: Um conjunto independente de regras usadas para resolver problemas por meio do processamento de dados, cálculo ou raciocínio automatizado.
* **detecção de anomalias**: um modelo que marca os eventos ou valores incomuns e ajuda a descobrir problemas. Por exemplo, a detecção de fraudes de cartão de crédito procura compras incomuns.
* **dados categóricos**: Dados organizados em categorias e que podem ser divididos em grupos. Por exemplo, um conjunto de dados categórico para carros poderia especificar ano, marca, modelo e preço.
* **classificação**: Um modelo para organizar os pontos de dados em categorias com base em um conjunto de dados para o qual os agrupamentos de categorias já são conhecidos.
* **engenharia de recursos**: O processo de extração ou seleção de recursos relacionados a um conjunto de dados, para otimizar o conjunto de dados e aprimorar os resultados. Por exemplo, dados de passagens aéreas poderiam ser otimizados classificando-os em dias de semana e feriados. Consulte [Engenharia e seleção de recursos no Azure Machine Learning](../team-data-science-process/create-features.md).
* **módulo**: uma parte funcional em um modelo do Machine Learning Studio, como, por exemplo, o módulo Inserir Dados, que permite inserir e editar pequenos conjuntos de dados. Um algoritmo também é um tipo de módulo no Machine Learning Studio.
* **modelo**: um modelo de aprendizado supervisionado é o produto de um experimento do aprendizado de máquina composto de dados de treinamento, módulo de algoritmo e módulos funcionais, como um módulo do Modelo de Pontuação.
* **dados numéricos**: Dados que têm significado, como medidas (dados contínuos) ou contagens (dados discretos). Também conhecidos como *dados quantitativos*.
* **partição**: O método pelo qual você divide dados em exemplos. Consulte [Partição e Exemplo](https://msdn.microsoft.com/library/azure/dn905960.aspx) para obter mais informações.
* **previsão**: Uma previsão é uma estimativa de valor ou valores a partir de um modelo de aprendizado de máquina. Você também pode ver o termo "pontuação prevista." No entanto, as pontuações previstas não são a saída final de um modelo. Uma avaliação do modelo vem a seguir da pontuação.
* **regressão**: um modelo para prever um valor com base em variáveis independentes, como prever o preço de um carro com base em seu ano e marca.
* **pontuação**: um valor previsto gerado por meio de uma classificação treinada ou um modelo de regressão, usando o [módulo Modelo de Pontuação](https://msdn.microsoft.com/library/azure/dn905995.aspx) no Machine Learning Studio. Os modelos de classificação também retornam uma pontuação para a probabilidade do valor previsto. Depois de ter gerado pontuações por meio de um modelo, você pode avaliar a precisão desse modelo usando o [módulo Avaliar modelo](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **exemplo**: uma parte de um conjunto de dados deve ser representativa do todo. Exemplos podem ser selecionados aleatoriamente ou com base em recursos específicos do conjunto de dados.

## <a name="next-steps"></a>Próximas etapas
Você pode aprender os fundamentos da análise preditiva e aprendizado de máquina usando um [tutorial passo a passo](create-experiment.md) e [aproveitando os exemplos](sample-experiments.md).  


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
