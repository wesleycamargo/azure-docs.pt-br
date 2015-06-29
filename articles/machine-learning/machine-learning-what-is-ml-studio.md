<properties 
	pageTitle="O que é o Estúdio de Aprendizado de Máquina do Azure? | Microsoft Azure" 
	description="Visão geral do Estúdio AM do Azure, uma ferramenta do tipo &quot;arrastar e soltar&quot; para criar rapidamente modelos em de uma biblioteca de algoritmos e módulos pronta para uso." 
	keywords="azure machine learning,azure ml, ml studio"	
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
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="garye"/>

# O que é o Estúdio de Aprendizado de Máquina do Azure?

O Estúdio de Aprendizado de Máquina do Microsoft Azure é uma ferramenta colaborativa do tipo "arrastar e soltar", que você pode usar para criar, testar e implantar soluções de análise preditiva em seus dados. O Estúdio de Aprendizado de Máquina publica modelos como serviços Web que podem ser facilmente consumidos por aplicativos personalizados ou ferramentas de BI como o Excel.

Estúdio de Aprendizado de Máquina - às vezes chamado de "Estúdio AM do Azure" - é onde se encontram a ciência de dados, a análise preditiva, os recursos de nuvem e os seus dados.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## O espaço de trabalho interativo do Estúdio de Aprendizado de Máquina

Para desenvolver um modelo de análise preditiva, normalmente você usa dados de uma ou mais fontes, transforma e analisa esses dados com várias funções de manipulação de dados e estatísticas e gera um conjunto de resultados. Desenvolver um modelo como este é um processo iterativo. À medida que você modifica as diversas funções e seus parâmetros, seus resultados convergem até que você esteja satisfeito, com um modelo treinado e eficiente.

O **Estúdio de Aprendizado de Máquina do Azure** oferece um espaço de trabalho visual e interativo para compilar, testar e iterar em um modelo de análise preditivo. Você arrasta e solta ***conjuntos de dados*** e ***módulos*** de análise em ***telas*** interativas conectando-as para formar um ***teste***, o qual você ***executa*** no Estúdio de Aprendizado de Máquina. Para iterar em seu design de modelo, você ***edita*** o teste, ***salva*** uma cópia, se desejado, e executa-o novamente. Quando estiver pronto, você pode ***publicar*** seu teste como um ***serviço Web*** para que seu modelo possa ser acessado por outras pessoas.

Não há necessidade de programação, basta conectar visualmente os conjuntos de dados e módulos para construir seu modelo de análise preditivo.

![Diagrama do Estúdio AM do Azure: criar testes, ler dados de várias fontes, gravar dados de pontuação, escrever modelos.][ml-studio-overview]

## Introdução ao Estúdio de Aprendizado de Máquina

Ao entrar pela primeira vez no Estúdio de Aprendizado de Máquina, você verá as seguintes guias à esquerda:

- **Página inicial do Estúdio** - um conjunto de links para documentação e outros recursos
- **TESTES** – Testes criados, executados e salvos como rascunhos. 
- **SERVIÇOS WEB** – Uma lista de testes que você publicou. 
- **CONFIGURAÇÕES** – Uma coleção das configurações que você pode usar para configurar sua conta e recursos. 

>[AZURE.NOTE]Quando você estiver construindo um teste, uma lista de trabalho de módulos e conjuntos de dados disponíveis será exibida na parte esquerda da tela. Essa é a lista de componentes que você pode usar para compilar seu modelo.

## Componentes de um teste

Um teste consiste em conjuntos de dados que fornecem dados para módulos analíticos, os quais você conecta para construir um modelo de análise preditiva. Especificamente, um teste válido possui três características:

- O teste tem no mínimo um conjunto de dados e um módulo. 
- Os conjuntos de dados podem ser conectados somente aos módulos. 
- Os módulos podem ser conectados a conjuntos de dados ou a outros módulos. 
- Todas as portas de entrada dos módulos devem ter uma conexão com o fluxo de dados. 
- Todos os parâmetros necessários a um módulo devem estar configurados. 

Para obter um exemplo de criação de um teste simples, consulte [Criar um teste simples no Estúdio de Aprendizado de Máquina do Azure](machine-learning-create-experiment.md). Para obter uma explicação mais completa da criação de uma solução de análise preditiva, consulte [Desenvolver uma solução preditiva com o Aprendizado de Máquina do Azure](machine-learning-walkthrough-develop-predictive-solution.md).

### Conjunto de dados

Um conjunto de dados inclui dados que foram atualizados no Estúdio de Aprendizado de Máquina, de forma que possam ser usados no processo de modelagem. Alguns conjuntos de dados de amostra estão incluídos no Estúdio de Aprendizado de Máquina, com os quais você pode testar, além de ser possível também carregar mais conjuntos de dados, caso necessário. Aqui estão alguns exemplos dos conjuntos de dados incluídos:

- **Dados MPG para vários automóveis** – valores MPG (milhas por galão) para automóveis identificados por número de cilindros, cavalo-vapor, etc. 
- **Dados de câncer de mama** – dados de diagnósticos de câncer de mama 
- **Dados de incêndios em florestas** – tamanhos dos incêndios em floresta na região nordeste de Portugal 

À medida que você compila um teste, a lista de trabalho dos conjuntos de dados é disponibilizada à esquerda das telas.

### Módulos

Um módulo é um algoritmo que você pode executar em seus dados. O Estúdio de Aprendizado de Máquina tem uma série de módulos, desde funções de ingresso até treinamento, pontuação e processos de validação. Aqui estão alguns exemplos dos módulos incluídos:

- [Converter em ARFF][convert-to-arff] – converte um conjunto de dados serializados .NET no formato ARFF (Arquivo de Atributo-Relação). 
- [Estatísticas Elementares][elementary-statistics] – calcula as estatísticas elementares como média, desvio padrão etc. 
- [Regressão Linear][linear-regression] – cria um modelo online de regressão linear com base em descendência gradiente 
- [Modelo de Pontuação][score-model] – pontua uma classificação treinada ou modelo de regressão 

À medida que você compila um teste, a lista de trabalho dos módulos é disponibilizada à esquerda das telas.

Um módulo pode ter um conjunto de parâmetros que você pode usar para configurar os algoritmos internos do módulo. Ao selecionar um módulo nas telas, os parâmetros do módulo são exibidos no painel à direita das telas. Você pode modificar os parâmetros nesse painel para ajustar seu modelo.


[ml-studio-overview]: ./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
 

<!---HONumber=58_postMigration-->