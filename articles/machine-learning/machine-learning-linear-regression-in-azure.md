<properties 
	pageTitle="Usando a Regressão Linear no Aprendizado de Máquina | Microsoft Azure" 
	description="Uma comparação dos modelos de regressão linear no Excel e no Estúdio de Aprendizado de Máquina do Azure " 
	metaKeywords="" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="kbaroni;garye" />

# Usando regressão linear no Aprendizado de Máquina do Azure

> *Kate Baroni* e *Ben Boatman* são arquitetos de soluções corporativas no Data Insights Center of Excellence da Microsoft. Neste artigo, eles descrevem sua experiência de migração de um pacote existente de análise de regressão para uma solução baseada em nuvem usando o Aprendizado de Máquina do Azure.
 
&nbsp;
  
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
## Objetivo

Nosso projeto começou com dois objetivos:

1. Usar a análise preditiva para melhorar a precisão das projeções de receita mensal de nossa organização  
2. Usar o Azure ML para confirmar, otimizar, aumentar a velocidade e a escala de nossos resultados.  

Como muitas empresas, nossa organização passa por uma processo de previsão de receita mensal. Nossa pequena equipe de analistas de negócios foi encarregada de usar o Aprendizado de Máquina para dar suporte ao processo e aumentar a precisão da previsão. A equipe passou vários meses coletando dados de várias fontes e submetendo os atributos de dados à análise estatística, identificando os principais atributos relevantes à previsão de vendas de serviços. As próximas etapas eram iniciar a criação de protótipos de modelos de regressão estatística com os dados no Excel. Em poucas semanas, tínhamos um modelo de regressão do Excel que superava os processos atuais de previsão de campo e finanças. Esse se tornou o resultado de previsão de linha de base.


Em seguida, realizamos a próxima etapa para mover nossa análise preditiva para o Azure ML e descobrir como ele poderia melhorar o desempenho preditivo.


## Obtendo a paridade de desempenho preditivo

Nossa maior prioridade era obter a paridade entre os modelos de regressão do Azure ML e do Excel. Usando os mesmos dados e a mesma divisão entre dados de treinamento e de teste, queríamos obter a paridade do desempenho preditivo entre o Excel e o Azure ML. Inicialmente, falhamos. O modelo do Excel superou o modelo do Azure ML. A falha ocorreu devido à falta de compreensão da configuração da ferramenta de base no Azure ML. Após uma sincronização com a equipe de produto do Azure ML, obtivemos uma melhor compreensão da configuração de base necessária para nossos conjuntos de dados e obtivemos a paridade entre os dois modelos.

### Criar um modelo de regressão no Excel
Nossa Regressão do Excel usou o modelo de regressão linear padrão encontrado nas Ferramentas de Análise do Excel.

Calculamos o *Erro de Média Absoluta %* e o usamos como a medida de desempenho para o modelo. Levamos três meses para chegar a um modelo de trabalho usando o Excel. Aproveitamos muito o aprendizado para o experimento com o Azure ML, que, em última análise, foi útil para entender os requisitos.

### Criar um experimento comparável no Aprendizado de Máquina do Azure  
Seguimos estas etapas para criar nosso experimento no Azure ML:

1.	Carregamos o conjunto de dados como um arquivo csv para o Azure ML (arquivo muito pequeno)
2.	Criamos um novo experimento e usamos o módulo [Colunas do Projeto][project-columns] para selecionar os mesmos recursos de dados usados no Excel   
3.	Usamos o módulo [Dividir Dados][split] (com o modo *Expressão Relativa*) para dividir os dados exatamente nos mesmos conjuntos de treinamento usados no Excel  
4.	Experimentamos o módulo de [Regressão Linear][linear-regression] (somente opções padrão), documentamos e comparamos os resultados com o nosso modelo de regressão do Excel

### Examinar os resultados iniciais
Inicialmente, o modelo do Excel superou claramente o modelo do Azure ML:

| |Excel|Azure ML|
|---|:---:|:---:|
|Desempenho| | |
|<ul style="list-style-type: none;"><li>Quadrado R Ajustado</li></ul>| 0,96 |N/D|
|<ul style="list-style-type: none;"><li>Coeficiente de <br />Determinação</li></ul>|N/D|	0,78<br />(baixa precisão)|
|Erro Absoluto Médio |	US$ 9,5 milhões|	US$ 19,4 milhões|
|Erro Absoluto Médio (%)|	6,03%|	12,2%

Quando apresentamos nosso processo e os resultados aos desenvolvedores e cientistas de dados na equipe do Azure ML, eles rapidamente forneceram algumas dicas úteis.

* Quando você usa o módulo [Regressão Linear][linear-regression] no Azure ML, dois métodos são fornecidos:
	*  Gradiente Online Descendente: talvez seja mais adequado para problemas de maior escala
	*  Mínimos Quadrados Comuns: esse é o método em que muitas pessoas pensam quando ouvem falar em regressão linear. Para conjuntos de dados pequenos, Mínimos Quadrados Comuns pode ser uma opção melhor.
*  Considere a possibilidade de ajustar o parâmetro L2 de Regularização de Peso para melhorar o desempenho. Ele é definido como 0,001 por padrão e, para nosso pequeno conjunto de dados, o definimos como 0,005 para melhorar o desempenho.    

### Mistério resolvido!
Quando aplicamos as recomendações, atingimos o mesmo desempenho de linha de base no Azure ML que obtivemos no Excel:

|| Excel|ML do Azure (Inicial)|O ML do Azure com quadrados mínimos|
|---|:---:|:---:|:---:|
|Valor rotulado |Dados reais (numéricos)|mesmo|mesmo|
|Aprendiz |Excel -> Dados da Análise -> Regressão|Regressão Linear.|Regressão Linear|
|Opções de aprendiz|N/D|Padrões|quadrados mínimos comuns<br />L2 = 0,005|
|Conjunto de dados|26 linhas, 3 recursos, 1 rótulo. Todos numéricos.|mesmo|mesmo|
|Divisão: treinamento|Excel treinado nas primeiras 18 linhas e testado nas últimas 8 linhas.|mesmo|mesmo|
|Divisão: teste|Fórmula de regressão do Excel aplicada às últimas 8 linhas|mesmo|mesmo|
|**Desempenho**||||
|Quadrado R Ajustado|0,96|N/D||
|Coeficiente de Determinação|N/D|0,78|0,952049|
|Erro Absoluto Médio |US$ 9,5 milhões|US$ 19,4 milhões|US$ 9,5 milhões|
|Erro Absoluto Médio (%)|<span style="background-color: 00FF00;"> 6,03%</span>|12,2%|<span style="background-color: 00FF00;"> 6,03%</span>|

Além disso, os coeficientes do Excel saíram-se bem em comparação com os pesos de recurso no modelo de treinamento do Azure:

||Coeficientes do Excel|Pesos de recursos do Azure|
|---|:---:|:---:|
|Interceptação/desvio|19470209,88|19328500|
|Recurso A|0,832653063|0,834156|
|Recurso B|11071967,08|11007300|
|Recurso C|25383318,09|25140800|

## Próximas etapas

Queríamos utilizar o serviço Web do Azure ML no Excel. Nossos analistas de negócios usam o Excel, e precisávamos de uma maneira de chamar o serviço Web do Azure ML com uma linha de dados do Excel e fazer com que ele retornasse o valor previsto para o Excel.

Também queríamos otimizar nosso modelo, usando as opções e os algoritmos disponíveis no Azure ML.

### Integração com o Excel
Nossa solução foi operacionalizar nosso modelo de regressão do Azure ML criando um serviço Web com base no modelo treinado. Em alguns minutos, o serviço Web foi criado e pudemos chamá-lo diretamente no Excel para retornar um valor de receita prevista.

A seção *Painel de Serviços Web* inclui uma pasta de trabalho do Excel que pode ser baixada. A pasta de trabalho vem pré-formatada com a API do serviço Web e informações de esquema inseridas. Quando você clica em *Baixar Pasta de Trabalho do Excel*, ela é aberta e você pode salvá-la em seu computador local.

![][1]
 
Com a pasta de trabalho aberta, copie os parâmetros predefinidos para a seção azul Parâmetro, conforme mostrado abaixo. Depois que os parâmetros forem inseridos, o Excel chamará o serviço Web do Azure ML, e os rótulos pontuados previstos serão exibidos na seção verde Valores Previstos. A pasta de trabalho continuará a criar previsões para parâmetros com base em seu modelo treinado para todos os itens de linha inseridos em Parâmetros. Para obter mais informações sobre como usar esse recurso, confira [Utilizando um serviço Web do Aprendizado de Máquina do Azure no Excel](machine-learning-consuming-from-excel.md).

![][2]
 
### Otimização e experimentos adicionais
Agora que tínhamos uma linha de base com nosso modelo do Excel, otimizamos nosso Modelo de Regressão Linear do Azure ML. Usamos o módulo [Seleção de Recursos com Base em Filtro][filter-based-feature-selection] para melhorar nossa seleção de elementos de dados iniciais e isso nos ajudou a obter uma melhoria de desempenho de 4,6% de Erro Absoluto Médio. Para projetos futuros, usaremos esse recurso, que pode nos poupar semanas na iteração de atributos de dados para localizar o conjunto certo de recursos a serem usados para modelagem.

Em seguida, planejamos incluir algoritmos adicionais, como [Bayesiano][bayesian-linear-regression] ou [Árvores de Decisão Aumentadas][boosted-decision-tree-regression] em nosso experimento para comparar o desempenho.

Se você quiser experimentar a regressão, um bom conjunto de dados para tentar é o conjunto de dados de exemplo de Regressão de Eficiência de Energia, que tem muitos atributos numéricos. O conjunto de dados é fornecido como parte dos conjuntos de dados de exemplo no Estúdio AM. Você pode usar diversos módulos de aprendizado para prever a Carga de Aquecimento ou a Carga de Resfriamento. O gráfico abaixo é uma comparação de desempenho de diferentes aprendizados de regressão em relação ao conjunto de dados de Eficiência Energética, prevendo a Carga de Resfriamento de variável de destino:

|Modelo|Erro Absoluto Médio|Erro Quadrado Médio de Raiz|Erro Absoluto Relativo|Erro Quadrado Relativo|Coeficiente de Determinação
|---|---|---|---|---|---
|Árvore de Decisão Aumentada|0,930113|1,4239|0,106647|0,021662|0,978338
|Regressão Linear (Gradiente Descendente)|2,035693|2,98006|0,233414|0,094881|0,905119
|Regressão de Rede Neural|1,548195|2,114617|0,177517|0,047774|0,952226
|Regressão Linear (Quadrados Mínimos Comuns)|1,428273|1,984461|0,163767|0,042074|0,957926  

## Principais observações 

Aprendemos muito executando os experimentos de regressão do Excel e do Aprendizado de Máquina do Azure em paralelo. Criar o modelo de linha de base no Excel e compará-lo a modelos usando a [Regressão Linear][linear-regression] do Azure ML nos ajudou a aprender sobre o Azure ML e descobrimos oportunidades para melhorar o desempenho de seleção e modelagem de dados.

Também descobrimos que é aconselhável usar a [Seleção de Recursos com Base em Filtro][filter-based-feature-selection] para acelerar projetos de previsão futuros. Aplicando a seleção de recursos a seus dados, você pode criar um modelo aprimorado no Azure ML com melhor desempenho geral.

A capacidade de transferir a previsão de análise preditiva do Azure ML para o Excel permite sistematicamente um aumento significativo na capacidade de fornecer resultados com êxito para um amplo público de negócios.


## Recursos
São listados alguns recursos para ajudá-lo a trabalhar com a regressão:

* Regressão no Excel. Se você nunca experimentou a regressão no Excel, este tutorial facilita tudo: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Regressão versus previsão. Tyler Chessman escreveu um artigo no blog explicando como realizar a previsão de série temporal no Excel, que contém uma boa descrição para iniciantes sobre a regressão linear. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts)  
* 	Regressão linear de quadrados mínimos simples: falhas, problemas e armadilhas. Para obter uma introdução e uma discussão sobre regressão: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 

<!---HONumber=AcomDC_0601_2016-->