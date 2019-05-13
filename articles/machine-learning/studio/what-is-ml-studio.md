---
title: O que é
titleSuffix: Azure Machine Learning Studio
description: O Azure Machine Learning Studio é uma ferramenta do tipo "arrastar e soltar" para criação rápida de modelos com base em uma biblioteca pronta para uso de algoritmos e módulos.
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/20/2019
ms.openlocfilehash: dd1eaa95a23deed0bf2098995be43402c605defc
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024213"
---
# <a name="what-is-azure-machine-learning-studio"></a>O que é o Azure Machine Learning Studio?
O Microsoft Azure Machine Learning Studio é uma ferramenta colaborativa do tipo "arrastar e soltar", que você pode usar para criar, testar e implantar soluções de análise preditiva em seus dados. O Machine Learning Studio publica modelos como serviços Web que podem ser facilmente consumidos por aplicativos personalizados ou ferramentas de BI como o Excel.

O Machine Learning Studio é onde a ciência de dados, as análises preditivas, os recursos de nuvem e seus dados se encontram!


## <a name="the-machine-learning-studio-interactive-workspace"></a>O workspace interativo do Machine Learning Studio
Para desenvolver um modelo de análise preditiva, normalmente você usa dados de uma ou mais fontes, transforma e os analisa por meio de várias funções estatísticas e de manipulação de dados, além de gerar um conjunto de resultados. Desenvolver um modelo como este é um processo iterativo. À medida que você modifica as diversas funções e seus parâmetros, seus resultados convergem até que você esteja satisfeito, com um modelo treinado e eficiente.

**Azure Machine Learning Studio** oferece um workspace visual e interativo para compilar, testar e iterar em um modelo de análise preditivo. Você arrasta e solta ***conjuntos de dados*** e ***módulos*** de análise em telas interativas conectando-as para formar um ***teste***, o qual você executa no Azure Machine Learning Studio. Para iterar em seu design de modelo, você edita o teste, salva uma cópia, se desejado, e executa-o novamente. Quando você estiver pronto, você poderá converter seu ***teste de treinamento*** em uma ***experiência preditiva*** e, em seguida, publicá-la como um ***serviço Web*** para que seu modelo possa ser acessado por outras pessoas.

Não há necessidade de programação, basta conectar visualmente os conjuntos de dados e módulos para construir seu modelo de análise preditivo.

![Diagrama do Azure Machine Learning Studio: crie experimentos, leia dados de várias fontes, grave dados de pontuação, grave modelos.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-machine-learning-studio-overview-diagram"></a>Baixe o diagrama de visão geral do Machine Learning Studio
Baixe o diagrama de **Visão geral de recursos do Microsoft Azure Machine Learning Studio** e obtenha uma exibição de alto nível dos recursos do Machine Learning Studio. Para mantê-lo próximo, imprima o diagrama em tamanho tabloide (11 x 17 polegadas).

**Baixe aqui o diagrama: [Visão geral dos recursos do Microsoft Azure Machine Learning Studio](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)**
![Visão geral dos recursos do Microsoft Azure Machine Learning Studio](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)

## <a name="get-started-with-machine-learning-studio"></a>Introdução ao Machine Learning Studio
Quando você insere o Machine Learning Studio, pela primeira vez,](https://studio.azureml.net) vê a **Página inicial**. Aqui você pode exibir a documentação, vídeos, seminários na Web e obter outros recursos valiosos.

Clique no menu superior esquerdo ![Menu](./media/what-is-ml-studio/menu.png) e você verá várias opções.
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
Clique em **Galeria** e você será levado para a **[Galeria de IA do Azure](https://gallery.azure.ai/)**. A Galeria é um lugar no qual uma comunidade de desenvolvedores e cientistas de dados podem compartilhar soluções criadas usando os componentes do Cortana Intelligence Suite.

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

Para obter uma explicação mais completa da criação de uma solução de análise preditiva, consulte [Desenvolver uma solução preditiva com o Azure Machine Learning Studio](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Conjunto de dados
Um conjunto de dados inclui dados que foram atualizados no Machine Learning Studio, de forma que possam ser usados no processo de modelagem. Alguns conjuntos de dados de amostra estão incluídos no Machine Learning Studio, com os quais você pode testar, além de ser possível também carregar mais conjuntos de dados, caso necessário. Aqui estão alguns exemplos dos conjuntos de dados incluídos:

* **Dados MPG para vários automóveis** – valores MPG (milhas por galão) para automóveis identificados por número de cilindros, cavalo-vapor, etc.
* **Dados de câncer de mama** – dados de diagnósticos de câncer de mama
* **Dados de incêndios em florestas** – tamanhos dos incêndios em floresta na região nordeste de Portugal

Conforme você cria um teste, pode escolher na lista de conjunto de dados disponíveis à esquerda da tela.

Para obter uma lista de conjuntos de dados de exemplo incluídos no Machine Learning Studio, consulte [Usar os conjuntos de dados de exemplo no Azure Machine Learning Studio](use-sample-datasets.md).

### <a name="modules"></a>Módulos
Um módulo é um algoritmo que você pode executar em seus dados. O Machine Learning Studio tem uma série de módulos, desde funções de ingresso até treinamento, pontuação e processos de validação. Aqui estão alguns exemplos dos módulos incluídos:

* [Converter em ARFF][convert-to-arff] – converte um conjunto de dados serializados .NET no formato ARFF (Arquivo de Atributo-Relação).
* [Calcular Estatísticas Elementares][elementary-statistics] – calcula as estatísticas elementares como média, desvio padrão etc.
* [Regressão Linear][linear-regression] – cria um modelo online de regressão linear com base em descendência gradiente.
* [Modelo de Pontuação][score-model] – pontua uma classificação treinada ou modelo de regressão.

À medida que você compila um teste, você pode escolher a partir da lista de módulos à esquerda das telas.

Um módulo pode ter um conjunto de parâmetros que você pode usar para configurar os algoritmos internos do módulo. Ao selecionar um módulo nas telas, os parâmetros do módulo são exibidos no painel **Propriedades** à direita das telas. Você pode modificar os parâmetros nesse painel para ajustar seu modelo.

Para obter ajuda sobre navegação através da grande biblioteca de algoritmos de aprendizado de máquina disponíveis, consulte [Como escolher algoritmos para o Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Implantando um serviço Web de análise preditiva
Quando seu modelo de análise preditiva estiver pronto, você pode implantá-lo como um serviço Web diretamente no Machine Learning Studio. Para obter mais detalhes sobre esse processo, consulte [Implantar um serviço Web do Azure Machine Learning](publish-a-machine-learning-web-service.md).

<a name="compare"></a>
## <a name="how-is-machine-learning-studio-different-from-azure-machine-learning-service"></a>Qual é a diferença entre o Machine Learning Studio e o Serviço do Azure Machine Learning?

[O serviço do Azure Machine Learning](../service/overview-what-is-azure-ml.md) oferece SDKs **-e-** uma interface visual (versão prévia) para preparar dados e treinar e implantar modelos de machine learning rapidamente. Esta interface visual (versão prévia) oferece uma experiência do tipo "arrastar e soltar" semelhante para o Studio. No entanto, diferentemente da plataforma de computação proprietária do Studio, a interface visual usa seus próprios recursos de computação e é totalmente integrada ao serviço do Azure Machine Learning.

Veja uma comparação rápida.

|| Machine Learning Studio | Serviço do Azure Machine Learning:<br/>Interface visual|
|---| --- | --- |
|| GA (em disponibilidade geral) | Em versão prévia|
|Módulos de interface| Muitos | Conjunto inicial de módulos populares|
|Destinos de computação de treinamento| Destino de computação proprietário, apenas suporte à CPU| Dá suporte à computação do Azure Machine Learning, GPU ou CPU.<br/>(Outros serviços de computação com suporte no SDK)|
|Destinos de computação de implantação| Formato do serviço Web proprietário, não personalizável | Opções de segurança empresariais e Serviço de Kubernetes do Azure. <br/>([Outros serviços de computação](../service/how-to-deploy-and-where.md) com suporte no SDK) |
|Treinamento de modelo automatizado e ajuste de hiperparâmetro | Não  | Ainda não está na interface visual. <br/> (Suporte no SDK e no portal do Azure). | 

Experimente a interface visual (versão prévia) com o [Início rápido: Preparar e visualizar dados sem escrever código](../service/ui-quickstart-run-experiment.md)

> [!NOTE]
> Os modelos criados no Studio não podem ser implantados nem gerenciados pelo serviço do Azure Machine Learning. No entanto, os modelos criados e implantados na interface visual do serviço podem ser gerenciados por meio do workspace do serviço do Azure Machine Learning.

## <a name="free-trial"></a>Avaliação gratuita

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="next-steps"></a>Próximas etapas
Você pode aprender os fundamentos da análise preditiva e aprendizado de máquina usando um [guia de início rápido passo a passo](create-experiment.md) e [aproveitando os exemplos](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
