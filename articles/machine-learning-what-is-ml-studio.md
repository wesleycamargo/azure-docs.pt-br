<properties title="What Is Azure Machine Learning Studio?" pageTitle="O que é o Estúdio de Aprendizado de Máquina? | Azure" description="Overview of Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/11/2014" ms.author="garye" />

# O que é o Estúdio de Aprendizado de Máquina do Azure?

O Estúdio de Aprendizado de Máquina do Microsoft Azure é um ambiente de desenvolvimento visual colaborativo que lhe permite compilar, testar e implantar soluções analíticas preditivas que funcionam em seus dados. O serviço e ambiente de desenvolvimento de Aprendizado de Máquina é baseado em nuvem, fornece recursos de computação e flexibilidade de memória, bem como elimina as preocupações com instalação e configuração, pois você trabalha o tempo todo em seu navegador da Web. 

O Estúdio AM é onde a ciência de dados, as análises preditivas, os recursos de nuvem e seus dados se encontram!

## O espaço de trabalho interativo do Estúdio AM

Para desenvolver um modelo de análise preditiva, normalmente você usa dados de uma ou mais fontes, transforma e analisa esses dados com várias funções de manipulação de dados e estatísticas e gera um conjunto de resultados. Desenvolver um modelo como esse envolve um processo iterativo - à medida que você modifica as diversas funções e seus parâmetros, seus resultados convergem até que você esteja satisfeito, com um modelo treinado e eficiente.

O **Estúdio AM** oferece um espaço de trabalho visual, interativo para compilar, testar e iterar em um modelo de análise preditivo. Você arrasta e solta ***conjuntos de dados*** e ***módulos*** de análise em ***telas*** interativas conectando-as para formar um ***teste***, o qual você ***executa*** no Estúdio AM. Para iterar em seu design de modelo, você ***edita*** o teste, ***salva*** uma cópia se desejado e executa-o novamente. Quando estiver pronto, você pode ***publicar*** seu teste como um ***serviço Web*** para que seu modelo possa ser acessado por outras pessoas. 

Não há necessidade de programação, basta conectar visualmente os conjuntos de dados e módulos para construir seu modelo de análise preditivo.

![ML Studio Overview][ml-studio-overview]

## Introdução ao Estúdio AM

Ao entrar pela primeira vez no Estúdio AM, será possível ver as seguintes guias à esquerda:

- **Página inicial do Studio** - um conjunto de links para documentação e outros recursos
- **TESTES** - Testes criados, executados e salvos como rascunhos. 
- **SERVIÇOS WEB** - Uma lista de testes que você publicou. 
- **CONFIGURAÇÕES** - Uma coleção das configurações que você pode usar para configurar sua conta e recursos. 

>[WACOM.NOTE] Quando você estiver construindo um teste, uma lista de trabalho de módulos e conjuntos de dados disponíveis é exibida na parte esquerda da tela. Essa é a lista de componentes que você pode usar para compilar seu modelo.

## Componentes de um teste

Um teste consiste em conjuntos de dados que fornecem dados para módulos analíticos, os quais você conecta para construir um modelo de análise preditiva. Especificamente, um teste válido possui três características:

- Ele tem no mínimo um conjunto de dados e um módulo. 
- Os conjuntos de dados podem ser conectados somente aos módulos. 
- Os módulos podem se conectar aos conjuntos de dados e a outros módulos. 
- Todas as portas de entrada dos módulos devem ter uma conexão com o fluxo de dados. 
- Todos os parâmetros necessários a um módulo devem estar configurados. 

Para obter um exemplo de criação de um teste simples, consulte [Criar um teste simples no Studio de Aprendizado de Máquina do Azure](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-create-experiment/). 
Para obter uma explicação mais completa da criação de uma solução de análise preditiva, consulte [Desenvolver uma solução preditiva com o Aprendizado de Máquina do Azure](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/).

### Conjunto de dados

Um conjunto de dados inclui dados que foram atualizados no Estúdio AM de forma que possam ser usados no processo de modelagem.  Alguns conjuntos de dados de amostra estão incluídos no Estúdio AM com os quais você pode testar, além de ser possível também carregar mais conjuntos de dados, caso precise.  Aqui estão alguns exemplos dos conjuntos de dados incluídos:

- **Dados MPG para vários automóveis** - valores MPG para automóveis identificados por número de cilindros, cavalo-vapor, etc. 
- **Dados de câncer de mama** - dados de diagnósticos de câncer de mama 
- **Dados de incêndios em florestas** - tamanhos dos incêndios em floresta na região nordeste de Portugal 

À medida que você compila um teste, a lista de trabalho dos conjuntos de dados é disponibilizada à esquerda das telas. 

### Módulos

Um módulo é um algoritmo que você pode executar em seus dados.  O Estúdio AM possui uma série de módulos, desde funções de ingresso para treinamento, pontuação e processos de validação.  Aqui estão alguns exemplos dos módulos incluídos:

- **Converter em ARFF** - converte um conjunto de dados serializados .NET em formato ARFF 
- **Estatísticas elementares** - calcula as estatísticas elementares como média, desvio padrão, etc. 
- **Regressão linear** - cria um modelo online de regressão linear com base em descendência gradiente 
- **Modelo de pontuação** - pontua uma classificação treinada ou modelo de regressão 

À medida que você compila um teste, a lista de trabalho dos módulos é disponibilizada à esquerda das telas. 

Um módulo pode ter um conjunto de parâmetros que você pode usar para configurar os algoritmos internos do módulo. Ao selecionar um módulo nas telas,  os parâmetros do módulo são exibidos no painel à direita das telas. Você pode modificar os parâmetros nesse painel para ajustar seu modelo.


[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/context.jpg
