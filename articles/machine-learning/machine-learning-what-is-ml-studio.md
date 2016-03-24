<properties 
	pageTitle="O que é o Estúdio de Aprendizado de Máquina do Azure? | Microsoft Azure"
	description="Visão geral do Estúdio AM do Azure, uma ferramenta do tipo ";arrastar e soltar"; para criar rapidamente modelos em de uma biblioteca de algoritmos e módulos pronta para uso."
	keywords="aprendizado de máquina do azure, am do azure, estúdio am"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/09/2016"
	ms.author="garye"/>

# O que é o Estúdio de Aprendizado de Máquina do Azure?

O Estúdio de Aprendizado de Máquina do Microsoft Azure é uma ferramenta colaborativa do tipo "arrastar e soltar", que você pode usar para criar, testar e implantar soluções de análise preditiva em seus dados. O Estúdio de Aprendizado de Máquina publica modelos como serviços Web que podem ser facilmente consumidos por aplicativos personalizados ou ferramentas de BI como o Excel.

O Estúdio de Aprendizado de Máquina é onde a ciência de dados, as análises preditivas, os recursos de nuvem e seus dados se encontram!

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## O espaço de trabalho interativo do Estúdio de Aprendizado de Máquina

Para desenvolver um modelo de análise preditiva, normalmente você usa dados de uma ou mais fontes, transforma e analisa esses dados com várias funções de manipulação de dados e estatísticas e gera um conjunto de resultados. Desenvolver um modelo como este é um processo iterativo. À medida que você modifica as diversas funções e seus parâmetros, seus resultados convergem até que você esteja satisfeito, com um modelo treinado e eficiente.

O **Estúdio de Aprendizado de Máquina do Azure** oferece um espaço de trabalho visual e interativo para compilar, testar e iterar em um modelo de análise preditivo. Você arrasta e solta ***conjuntos de dados*** e ***módulos*** de análise em ***telas*** interativas conectando-as para formar um ***teste***, o qual você ***executa*** no Estúdio de Aprendizado de Máquina. Para iterar em seu design de modelo, você ***edita*** o teste, ***salva*** uma cópia, se desejado, e executa-o novamente. Quando você estiver pronto, você poderá converter sua ***teste de treinamento*** em uma ***experiência preditiva***, e, em seguida, ***publicar*** -la como um ***serviço Web*** para que seu modelo possa ser acessado por outros.

>[AZURE.TIP] Para baixar e imprimir um diagrama que fornece uma visão geral dos recursos do Estúdio de Aprendizado de Máquina, consulte [Diagrama de visão geral dos recursos do Estúdio de Aprendizado de Máquina do Azure](machine-learning-studio-overview-diagram.md).

Não há necessidade de programação, basta conectar visualmente os conjuntos de dados e módulos para construir seu modelo de análise preditivo.

![Diagrama do Estúdio AM do Azure: criar testes, ler dados de várias fontes, gravar dados de pontuação, escrever modelos.][ml-studio-overview]

## Introdução ao Estúdio de Aprendizado de Máquina

Ao entrar pela primeira vez no [Estúdio de Aprendizado de Máquina,](https://studio.azureml.net) você verá a página **Inicial**. Aqui você pode exibir a documentação, vídeos, seminários na Web e obter outros recursos valiosos.

Existem três guias na parte superior: **Página inicial** (onde iniciar), **Estúdio**, e **Galeria**.

### Estúdio

Clique na guia **Estúdio** e será solicitado que você entre usando a conta da Microsoft, ou sua conta de trabalho ou escola. Depois de conectado, você verá as seguintes guias à esquerda:

- **TESTES** – Testes criados, executados e salvos como rascunhos
- **SERVIÇOS WEB** -serviços Web que você implantou dos testes
- **NOTEBOOKS** - Notebooks Jupyter que você criou
- **CONJUNTOS DE DADOS** - Conjuntos de dados que você carregou no Estúdio
- **MODELOS TREINADOS** - Modelos que você treinou em testes e salvos no Estúdio
- **CONFIGURAÇÕES** – Uma coleção das configurações que você pode usar para configurar sua conta e recursos.

### Galeria

Clique na guia **Galeria** guia e você será levado para a Galeria de Análise da Cortana. A Galeria é um lugar no qual uma comunidade de desenvolvedores e cientistas de dados pode compartilhar soluções criadas usando os componentes do Pacote de Análise do Cortana.

Para saber mais sobre a Galeria, consulte as [Soluções de compartilhamento e descoberta na Galeria de Analise da Cortana](machine-learning-gallery-how-to-use-contribute-publish.md).

## Componentes de um teste

Um teste consiste em conjuntos de dados que fornecem dados para módulos analíticos, os quais você conecta para construir um modelo de análise preditiva. Especificamente, um teste válido possui três características:

- O teste tem no mínimo um conjunto de dados e um módulo.
- Os conjuntos de dados podem ser conectados somente aos módulos.
- Os módulos podem ser conectados a conjuntos de dados ou a outros módulos.
- Todas as portas de entrada dos módulos devem ter uma conexão com o fluxo de dados.
- Todos os parâmetros necessários para cada módulo devem estar configurados.

Você pode criar uma experiência do zero, ou você pode usar uma experiência de exemplo existente como um modelo. Para saber mais, consulte [Use experimentos de exemplo para criar novos experimentos](machine-learning-sample-experiments.md).

Para obter um exemplo de criação de um teste simples, consulte [Criar um teste simples no Estúdio de Aprendizado de Máquina do Azure](machine-learning-create-experiment.md).

Para obter uma explicação mais completa da criação de uma solução de análise preditiva, consulte [Desenvolver uma solução preditiva com o Aprendizado de Máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md).

### Conjunto de dados

Um conjunto de dados inclui dados que foram atualizados no Estúdio de Aprendizado de Máquina, de forma que possam ser usados no processo de modelagem. Alguns conjuntos de dados de amostra estão incluídos no Estúdio de Aprendizado de Máquina, com os quais você pode testar, além de ser possível também carregar mais conjuntos de dados, caso necessário. Aqui estão alguns exemplos dos conjuntos de dados incluídos:

- **Dados MPG para vários automóveis** – valores MPG (milhas por galão) para automóveis identificados por número de cilindros, cavalo-vapor, etc.
- **Dados de câncer de mama** – dados de diagnósticos de câncer de mama
- **Dados de incêndios em florestas** – tamanhos dos incêndios em floresta na região nordeste de Portugal

À medida que você compila um teste, você pode escolher na lista de conjuntos de dados à esquerda das telas.

Para obter uma lista de conjuntos de dados de exemplo incluídos no Estúdio de Aprendizado da Máquina, consulte [Usar os conjuntos de dados de exemplo no Estúdio de Aprendizado de Máquina do Azure](machine-learning-use-sample-datasets.md).

### Módulos

Um módulo é um algoritmo que você pode executar em seus dados. O Estúdio de Aprendizado de Máquina tem uma série de módulos, desde funções de ingresso até treinamento, pontuação e processos de validação. Aqui estão alguns exemplos dos módulos incluídos:

- [Converter em ARFF][convert-to-arff] – converte um conjunto de dados serializados .NET no formato ARFF (Arquivo de Atributo-Relação).
- [Calcular Estatísticas Elementares][elementary-statistics] – calcula as estatísticas elementares como média, desvio padrão etc.
- [Regressão Linear][linear-regression] – cria um modelo online de regressão linear com base em descendência gradiente
- [Modelo de Pontuação][score-model] – pontua uma classificação treinada ou modelo de regressão

À medida que você compila um teste, você pode escolher a partir da lista de módulos à esquerda das telas.

Um módulo pode ter um conjunto de parâmetros que você pode usar para configurar os algoritmos internos do módulo. Ao selecionar um módulo nas telas, os parâmetros do módulo são exibidos no painel **Propriedades** à direita das telas. Você pode modificar os parâmetros nesse painel para ajustar seu modelo.

Para obter ajuda sobre navegação através da biblioteca grande dos algoritmos de aprendizado de máquina disponíveis, consulte [Como escolher algoritmos para o Aprendizado de Máquina do Microsoft Azure](machine-learning-algorithm-choice.md).

## Implantando um serviço Web de análise preditiva

Quando seu modelo de análise preditiva estiver pronto, você pode implantá-lo como um serviço Web diretamente do Estúdio de aprendizado de máquina. Para obter mais detalhes sobre esse processo, consulte [Implantar um serviço Web do Aprendizado de Máquina do Azure](machine-learning-publish-a-machine-learning-web-service.md).

[ml-studio-overview]: ./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/

<!---HONumber=AcomDC_0316_2016-->