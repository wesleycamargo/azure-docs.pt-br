<properties 
	pageTitle="Pré-processar e limpar dados | Azure" 
	description="Pré-processar e limpar dados" 
	metaKeywords="data cleansing" 
	services="machine-learning" 
	documentationCenter="" 
	authors="xibingaomsft,msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="xibingaomsft,msolhab" /> 


Pré-processar e limpar dados
========================================
Dados brutos costumam conter ruídos e não são confiáveis e podem ser valores ausentes.  Usar esses dados para a modelagem pode produzir resultados incorretos.  O pré-processamento e limpeza de dados são etapas importantes que devem ser realizadas antes de usar um conjunto de dados para o aprendizado de máquina.  Este artigo apresenta várias etapas de pré-processamento de dados que podem ser executadas antes da ingestão de dados no Aprendizado de Máquina do Azure. 

----------------

1. **Por que pré-processar e limpar dados?**

	Dados do mundo real são coletados de várias fontes e processos e podem conter irregularidades ou dados corrompidos, comprometendo a qualidade do conjunto de dados.  Os problemas de qualidade de dados típicos que podem surgir são:
	- Incompleto:  Os dados não têm atributos ou contém valores ausentes.
	- Ruído:  Os dados contêm registros incorretos ou exceções.
	- Inconsistentes:  Os dados contêm registros conflitantes ou discrepâncias. 
		
	Dados de qualidade são um pré-requisito para modelos de previsão de qualidade.  Para evitar a "entrada e saída de lixo" e melhorar a qualidade de dados e, portanto, a qualidade do modelo, é fundamental conduzir uma filtragem de integridade de dados para identificar problemas de dados no início e decidir sobre os as etapas correspondentes de pré-processamento e limpeza de dados.

2. **Poderia dar exemplo de filtragens de integridade de dados típicas empregadas?** 
	
	Podemos verificar a qualidade geral dos dados confirmando:
    - O número de registros.
    - O número de atributos (ou recursos).
	- Os tipos de dados do atributo (nominais, ordinais ou contínuos).
	- O número de valores ausentes.
	- Boa formação dos dados.  Se os dados estiverem em tsv ou csv, pergunta se os separadores de colunas e separadores de linha sempre separam colunas e linhas corretamente.  Se os dados estiverem em formato HTML ou XML, verifique se os dados são bem formados com base nos seus respectivos padrões.  A análise também pode ser necessária para extrair informações estruturadas de dados não estruturados ou semiestruturados.
	- Em registros de dados inconsistentes, como por exemplo, se os dados contiverem a média de nota de um aluno, verifique se a média de nota está no intervalo designado, digamos, 0 a 4. 

	Quando você encontrar problemas com os dados, etapas de processamento serão necessárias, geralmente envolvendo a limpeza de valores ausentes, normalização de dados, diferenciação, processamento de texto para remover e/ou substituir caracteres incorporados que podem afetar o alinhamento dos dados e tipos de dados mistos em campos comuns, entre outros. 
	
	O Aprendizado de Máquina do Azure consome dados tabulares bem formados.  Se os dados já estiverem em formato tabular, o pré-processamento dos dados pode ser executado diretamente com o Aprendizado de Máquina do Azure no Estúdio AM.  Se os dados não estiverem em formato tabular, como XML, por exemplo XML, a análise pode ser necessária para converter os dados em formato tabular.  

3. **Quais são as principais tarefas de pré-processamento de dados?**

	- Limpeza de dados:  Preencher ou valores ausentes, detectar e remover exceções e dados com ruídos.
	- Transformação de dados:  Normalize dados para reduzir o ruído e dimensões.
	- Redução de dados:  Registros de dados de exemplo ou atributos para fácil manipulação de dados.
	- Diferenciação de dados:  Converta atributos contínuos para atributos categóricos para maior facilidade de uso com determinados métodos de aprendizado de máquina.
	- Limpeza de texto: remover caracteres inseridos que podem causar desalinhamento de dados, como por exemplo guias incorporadas em um arquivo de dados separado por tabulações, novas linhas incorporadas que podem quebrar registros, etc.	
	
	As seções a seguir detalham algumas das etapas de pré-processamento de dados.

4. **Como lidar com valores ausentes?**
	
	Para lidar com valores ausentes, é melhor primeiro identificar o motivo dos valores ausentes para melhor tratar o problema.  Métodos de manipulação de valores ausentes típicos são:

	- Exclusão:  Remover os registros com valores ausentes
	- Substituição fictícia:  Substituir os valores ausentes por um valor fictício: por exemplo, _desconhecido_ para valores categóricos ou 0 para valores numéricos.
	- Substituição média:  Se os dados ausentes forem numéricos, substitua os valores ausentes pela média. 
	- Substituição frequente:  Se os dados ausentes forem categóricos, substitua os valores ausentes pelo item mais frequente 
	- Substituição de regressão:  Use o método de regressão para substituir valores ausentes por valores de regressão.  

5. **Como normalizar dados?**
	
	A normalização de dados escala valores numéricos para um intervalo especificado.  Métodos de normalização de dados populares incluem:
	- Normalização de Mín.-Máx.:  Transformar linearmente os dados em um intervalo de, digamos, entre 0 e 1.  E o mínimo é escalado para 0 e máximo para 1.
	- Normalização de valor Z:  Escalar dados com base na média e desvio padrão: dividir a diferença entre os dados e a média pelo desvio padrão.
	- Escala decimal:  Dimensionar os dados movendo o ponto decimal do valor do atributo.  

6. **Como diferenciar os dados?**  

	Dados podem ser diferenciados ao converter valores contínuos em atributos nominais ou intervalos.  Algumas formas de fazer isso são:
	- Compartimentalização de largura igual:  Dividir o intervalo de todos os possíveis valores de um atributo entre N grupos do mesmo tamanho e atribuir os valores correspondentes a um compartimento a um número do compartimento.
	- Compartimentação de altura de igualdade:  Dividir o intervalo de todos os possíveis valores de um atributo em N grupos que contém o mesmo número de instâncias e atribuir os valores correspondentes a um compartimento ao número do compartimento.  

7. **Como reduzir os dados?**  

	Há vários métodos para reduzir o tamanho dos dados para facilitar a manipulação de dados.  Dependendo do tamanho dos dados e do domínio, os métodos a seguir podem ser aplicados: 
	- Amostragem de registros:  Realizar a amostragem dos registros de dados de exemplo e escolha somente o subconjunto representativo dos dados. 
	- Amostragem de atributo:  Selecionar apenas os atributos importantes dos dados.  
	- Agregação:  Dividir os dados em grupos e armazenar os números em cada grupo.  Por exemplo, os números de receita diária de uma cadeia de restaurante nos últimos 20 anos podem ser agregados para receita mensal para reduzir o tamanho dos dados.  

8. **Como limpar dados de texto?**  

	Campos de texto em dados tabulares podem incluir caracteres que afetam os alinhamentos das colunas e/ou limites de registros.  Por exemplo, guias incorporadas em um arquivo separado por tabulações causa desalinhamento de coluna e caracteres de nova linha incorporados quebram linhas de registros.  A manipulação incorreta de codificação de texto durante a gravação/leitura do texto resulta em perda de informações e introdução indesejada de caracteres ilegíveis, como por exemplo valores nulos, podendo também afetar a análise de texto.  Uma análise e edição detalhada podem ser necessárias para limpar os campos de texto para alinhamento adequado e/ou extrair dados estruturados de dados de texto não estruturados ou semiestruturados.

A exploração de dados oferece uma exibição antecipada dos dados.  Alguns problemas de dados podem ser descobertos durante essa etapa e  métodos correspondentes podem ser aplicados para resolver esses problemas.  É importante fazer perguntas, tal como qual é a origem do problema e como o problema pode ter sido introduzido.  Isso também ajuda você a decidir sobre as etapas de processamento de dados que precisam ser seguidas para resolvê-los.  O tipo de informações que devem ser derivadas dos dados também pode ser usado para priorizar os esforços de processamento de dados.

**Referência**
	
>_Data Mining:  Concepts and Techniques_, Terceira Edição, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber e Jian Pei

<!--HONumber=49-->