<properties title="What Is Azure Machine Learning Studio?" pageTitle="What Is Machine Learning Studio? | Azure" description="Overview of Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# O que é o Estúdio de Aprendizado de Máquina do Azure?

O Estúdio de Aprendizado de Máquina do Microsoft Azure é um ambiente de desenvolvimento visual colaborativo que lhe permite compilar, testar e implantar soluções analíticas preditivas que funcionam em seus dados. O serviço e ambiente de desenvolvimento de Aprendizado de Máquina é baseado em nuvem, fornece recursos de computação e flexibilidade de memória, bem como elimina as preocupações com instalação e configuração, pois você trabalha o tempo todo em seu navegador da Web.

O Estúdio AM é onde a ciência de dados, as análises preditivas, os recursos de nuvem e seus dados se encontram!

## O espaço de trabalho interativo do Estúdio AM

Para desenvolver um modelo de análise preditiva, normalmente você usa dados de uma ou mais fontes, transforma e analisa esses dados com várias funções de manipulação de dados e estatísticas e gera um conjunto de resultados. Desenvolver um modelo como esse envolve um processo iterativo – à medida que você modifica as diversas funções e seus parâmetros, seus resultados convergem até que você esteja satisfeito, com um modelo treinado e eficiente.

O **Estúdio AM** oferece um espaço de trabalho visual, interativo para compilar, testar e iterar em um modelo de análise preditivo. Você arrasta e solta ***conjuntos de dados*** e ***módulos***
de análise em ***telas*** interativas, conectando-as para formar um ***teste***, o qual você ***envia*** ao Estúdio AM para execução. Para iterar em seu design de modelo, você ***edita*** o teste, ***salva*** uma cópia se desejado e o envia novamente.

Não há necessidade de programação, basta conectar visualmente os conjuntos de dados e módulos para construir seu modelo de análise preditivo.

![Visão geral do Estúdio AM][Visão geral do Estúdio AM]

## Introdução ao Estúdio AM

Ao entrar pela primeira vez no Estúdio AM, será possível ver as seguintes guias à esquerda:

-   **TESTES** – Testes criados, executados e salvos como rascunhos.
-   **SERVIÇOS WEB** – Uma lista de testes que você publicou.
-   **MÓDULOS** – Uma lista de referência dos módulos de análise disponíveis no Estúdio AM.
-   **CONJUNTOS DE DADOS** – Uma lista de referência dos conjuntos de dados disponíveis no Estúdio AM.
-   **CONFIGURAÇÕES** – Uma coleção das configurações que você pode usar para configurar sua conta e recursos.

> [WACOM.NOTE] A lista de **módulos** e **conjuntos de dados** exibida nessas guias são apenas para referência. Essas listas incluem informações adicionais sobre os conjuntos de dados e módulos, inclusive quando foram criados e os números de versão.
> Quando você está construindo um teste, uma lista de trabalho de conjuntos de dados e módulos disponíveis é exibida à esquerda das telas. Essa é a lista de componentes que você pode usar para compilar seu modelo.

## Componentes de um teste

Um teste consiste em conjuntos de dados que fornecem dados para módulos analíticos, os quais você conecta para construir um modelo de análise preditiva. Especificamente, um teste válido possui três características:

-   Ele tem no mínimo um conjunto de dados e um módulo.
-   Os conjuntos de dados podem ser conectados somente aos módulos.
-   Os módulos podem se conectar aos conjuntos de dados e a outros módulos.
-   Todas as portas de entrada dos módulos devem ter uma conexão com o fluxo de dados.
-   Todos os parâmetros necessários a um módulo devem estar configurados.

Para obter um exemplo de como criar um teste simples, consulte **Criando um teste de amostra**.
Para obter informações passo a passo completas sobre como criar uma solução analítica preditiva, consulte **Criando e iterando um teste de Aprendizado de Máquina**.

### Conjunto de dados

Um conjunto de dados inclui dados que foram atualizados no Estúdio AM de forma que possam ser usados no processo de modelagem. Alguns conjuntos de dados de amostra estão incluídos no Estúdio AM com os quais você pode testar, além de ser possível também carregar mais conjuntos de dados, caso precise. Aqui estão alguns exemplos dos conjuntos de dados incluídos:

-   **Dados MPG para vários automóveis** - Valores MPG para automóveis identificados por número de cilindros, cavalo-vapor, etc.
-   **Dados de câncer de mama** – Dados de diagnósticos de câncer de mama
-   **Dados de incêndios em florestas** – Tamanhos dos incêndios em floresta na região nordeste de Portugal

À medida que você compila um teste, a lista de trabalho dos conjuntos de dados é disponibilizada à esquerda das telas. Você também pode exibir a lista de conjuntos de dados carregados selecionando a guia **CONJUNTOS DE DADOS** ao iniciar o Estúdio AM.

### Módulos

Um módulo é um algoritmo que você pode executar em seus dados. O Estúdio AM possui uma série de módulos, desde funções de ingresso para treinamento, pontuação e processos de validação. Aqui estão alguns exemplos dos módulos incluídos:

-   **Converter em ARFF** – Converte um conjunto de dados serializados .NET em formato ARFF
-   **Estatísticas elementares** – Calcula as estatísticas elementares como média, desvio padrão, etc.
-   **Regressão linear** – Cria um modelo online de regressão linear com base em descendência gradiente
-   **Modelo de pontuação** – Pontua uma classificação treinada ou modelo de regressão

À medida que você compila um teste, a lista de trabalho dos módulos é disponibilizada à esquerda das telas. Você também pode exibir uma lista e módulos incluídos selecionando a guia **MÓDULOS** ao iniciar o Estúdio AM.

Um módulo pode ter um conjunto de parâmetros que você pode usar para configurar os algoritmos internos do módulo. Ao selecionar um módulo nas telas, os parâmetros do módulo são exibidos no painel à direita das telas. Você pode modificar os parâmetros nesse painel para ajustar seu modelo.

  [Visão geral do Estúdio AM]: ./media/machine-learning-what-is-ml-studio/context.jpg
