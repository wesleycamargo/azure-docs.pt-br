<properties 
	pageTitle="Analisando a Variação do Cliente usando o Aprendizado de Máquina do Microsoft Azure | Azure" 
	description="Estudo de caso do desenvolvimento de um modelo integrado para analisar e pontuar a insatisfação do cliente" 
	services="machine-learning" 
	documentationCenter="" 
	authors="" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/06/2014" 
	ms.author="barga"/>

# Analisando a Variação do Cliente usando o Aprendizado de Máquina do Microsoft Azure


Este documento apresenta uma referência para implementação de um projeto de análise de variação do cliente - criado usando o Estúdio de Aprendizado de Máquina do Azure - e discute também modelos genéricos associados para a resolução holística do problema de variação do cliente industrial. Também medimos a precisão de modelos criados usando o Aprendizado de Máquina do Azure e avaliamos o que é necessário para mais desenvolvimento.  

##O problema da variação do cliente
Empresas no mercado consumidor e em todos os setores empresariais precisam lidar com a variação. Algumas vezes a variação é excessiva e influencia decisões de política. A solução tradicional é prever índices de variação de alta propensão e tratar de suas necessidades por meio de um serviço de concierge, campanhas de marketing ou fazendo uso de isenções especiais. Essas abordagens podem variar de indústria para indústria e mesmo de um grupo de consumidores específico para outro dentro de uma determinada indústria (p. ex., telecomunicações). O fator comum é que as empresas precisam minimizar esses esforços especiais para a retenção de clientes. Assim, uma metodologia natural seria classificar cada cliente com a probabilidade de variação e o endereço dos N maiores. Os maiores clientes podem ser os mais lucrativos; em cenários mais sofisticados, uma função de lucro é aplicada durante a seleção de candidatos para isenção especial. Todavia, essas considerações são somente uma parte da estratégia holística para gerenciar a variação. As empresas também precisam levar em consideração o risco (e a tolerância a risco associada), o nível e o custo de intervenção, além da segmentação plausível de clientes.  

##Abordagens e perspectiva industriais
O gerenciamento sofisticado da variação é um sinal de uma indústria madura. O exemplo clássico é o da indústria de telecomunicações, na qual sabe-se que os assinantes frequentemente mudam de um provedor para outro. Essa variação voluntária é uma preocupação primária. Além disso, os provedores acumularam conhecimento significativo sobre *motivadores de variação*, que são os fatores que levam os clientes a mudar. Por exemplo, a escolha de telefone ou dispositivo é um motivador bem conhecido no setor de telefonia móvel. Como resultado, uma política popular é subsidiar o preço de um aparelho para novos assinantes enquanto cobra-se o preço integral para assinantes existentes, caso queiram trocar seu aparelho por um melhor. Historicamente, essa política fez com que clientes mudassem de um provedor para outro para obter um novo desconto, o que, por sua vez, fez com que os provedores refinassem suas estratégias. A alta volatilidade nas ofertas de aparelhos é um fator que invalida muito rapidamente modelos de variação baseados em modelos de aparelhos atuais. Além disso, os telefones celulares não são apenas dispositivos de telecomunicações, mas são também questão de estilo (considere o iPhone), sendo que esses fatores sociais de previsão estão fora do escopo dos conjuntos de dados comuns de telecomunicações. O resultado final para a criação de um modelo é que você não pode criar uma política sólida apenas eliminando as causas da variação. Na verdade, uma estratégia contínua de modelagem, incluindo modelos clássicos, quantificando variáveis divididas por categoria (p. ex., Árvores de Decisão), é **essencial**.

Usando conjuntos de big data com seus clientes, as organizações estão realizando análises de big data, em particular a detecção de variação baseada em big data, como uma abordagem eficaz para o problema. Você pode saber mais sobre a abordagem de big data para o problema de variação na seção Recomendações sobre ETL.  

##Metodologia para Modelagem da Variação do Cliente
Um processo comum para descobrir a variação do cliente é apresentado nos diagramas 1 a 3:  

1.	Um modelo de risco permite que você considere como as ações afetam as probabilidades e o risco.
2.	Um modelo de intervenção permite que você considere como o nível de intervenção poderia afetar a probabilidade de variação e a quantidade de CLV (valor de vida útil do cliente).
3.	Essa análise leva a uma análise qualitativa que é escalonada para uma campanha de marketing proativa, destinada a certos segmentos entre os clientes de modo a realizar a oferta ideal.  

![][1]

Essa abordagem de antecipação é o melhor modo de gerenciar a variação, mas é acompanhada por um grande nível de complexidade: precisamos desenvolver um arquétipo multimodelo e traçar dependências entre os modelos. A interação entre os modelos pode ser encapsulada conforme mostrado no diagrama a seguir:  

![][2]
 
*Figura 4: Arquétipo multimodelo unificado*  

As interações entre os diferentes modelos são a chave para fornecermos uma abordagem holística para a retenção de clientes.  Cada modelo obrigatoriamente degrada-se com o tempo; portanto, a arquitetura é um circuito implícito (semelhante ao arquétipo definido pelo padrão CRISP-DM de mineração de dados, [***3***]).  
 
O ciclo total de segmentação/decomposição de risco-decisão-marketing ainda é uma estrutura generalizada, que é aplicável a muitos problemas empresariais. A análise de variação é somente um exemplo altamente representativo desse grupo de problemas, porque demonstra todas as características de um problema empresarial complexo que não permite uma solução preditiva simplificada. Os aspectos sociais da abordagem moderna para a variação não são especialmente destacados na abordagem, mas os aspectos sociais são encapsulados no arquétipo de modelagem, do mesmo modo que seriam em qualquer outro modelo.  

Uma adição interessante nesse caso é a análise de big data. As empresas de telecomunicações e varejo coletam dados de seus clientes de modo exaustivo, de modo que podemos prever facilmente que a necessidade de uma conectividade multimodelo se tornará uma tendência comum, dadas as tendências emergentes como a "Internet das coisas" e os dispositivos ubíquos, que permitem que a empresa aplique soluções inteligentes em múltiplos níveis.  

 
#Implementando o Arquétipo de Modelagem em Estúdio AM do Microsoft Azure 
Dado o problema descrito acima, como podemos implementar uma abordagem integrada de modelagem e pontuação? Nesta seção, demonstraremos como fizemos isso usando o Estúdio AM de Nuvem do Microsoft Azure.  

A abordagem multimodelo é essencial ao projetar um arquétipo global para variação. Até mesmo a parte de pontuação (preditiva) da abordagem deve ser multimodelo.  

O diagrama a seguir mostra o protótipo que criamos, que emprega quatro algoritmos de pontuação diferentes em Estúdio AM de Nuvem para prever a variação. O motivo para usar uma abordagem multimodelo não é somente criar um classificador de conjunto para aumentar a precisão, mas também proteger contra over-fitting e aprimorar a seleção prescritiva de recursos.  

![][3]
 
*Figura 5: Protótipo de uma abordagem de modelagem de variação*  

As próximas seções oferecerão mais detalhes sobre o protótipo de modelo de pontuação que implementamos usando o Estúdio AM de Nuvem.  

##Seleção e Preparação de Dados
Os dados usados para criar os modelos e atribuir pontuação aos clientes foram obtidos por meio de uma solução vertical de CRM, com os dados ofuscados para proteger a privacidade dos clientes. Os dados em si contêm informações sobre 8.000 assinaturas nos EUA, e combinam três fontes: dados de provisionamento (metadados de assinatura), dados de atividade (uso do sistema) e dados de suporte ao cliente. Os dados não incluem nenhuma informação relacionada à empresa sobre os clientes; por exemplo, não inclui metadados sobre fidelidade ou pontuação de crédito.  

Para simplificar, os processos de ETL e limpeza dos dados estão fora do escopo, já que presumimos que a preparação dos dados já foi realizada em outra instância.   

A seleção de recursos para a modelagem é baseada na pontuação preliminar de significância do conjunto de índices de previsão, incluído no processo que utiliza o módulo de floresta aleatória. Para a implementação em Estúdio AM de Nuvem, calculamos a média, a mediana e os intervalos para recursos representativos. Por exemplo, adicionamos agregados aos dados qualitativos, como valores mínimos e máximos para a atividade de usuário.    

Também coletamos informações de tempo dos últimos seis meses. Utilizamos somente os seis meses precedentes porque analisamos os dados para um ano e estabelecemos que, mesmo se tivessem existido tendências estatisticamente significativas, o efeito da variação diminui muito após seis meses.  

O ponto mais importante é que o processo todo, incluindo ETL, seleção de recursos e modelagem, foi implementado no Estúdio AM de Nuvem, usando fontes de dados no Microsoft Azure.   

Os diagramas a seguir ilustram os dados que foram usados:  

![][4]
 
*Figura 6: Extrato de fonte de dados (ofuscado)*  

![][5]

 
*Figura 7: Recursos extraídos da fonte de dados*
 
##Algoritmos usados no protótipo

Usamos os quatro algoritmos de aprendizado de máquina a seguir para criar o protótipo (sem personalização):  

1.	Regressão Logística (LR)
2.	Árvore de decisão aumentada (BT) 
3.	Perceptron médio (AP) 
4.	Máquina de vetor de suporte (SVM)  


O diagrama a seguir ilustra uma parte da superfície de design do teste, que indica a sequência na qual os modelos foram criados:  

![][6]  

 
*Figura 8: Criando modelos AM no Estúdio AM de Nuvem*  

##Métodos de pontuação
Classificamos os quatro modelos por pontuação usando um conjunto de dados de treinamento rotulado.  

Também enviamos o conjunto de dados de pontuação para um modelo comparável criado usando a edição de área de trabalho do SAS Enterprise Miner 12, além de medirmos a precisão do modelo SAS e de todos os quatro modelos AM de Nuvem do Azure.  

#Resultados 
Nesta seção, apresentamos nossas descobertas sobre a precisão dos modelos, com base no conjunto de dados de pontuação.  

##Precisão e exatidão da pontuação
Geralmente, a implementação no AM de Nuvem do Azure é inferior à do SAS em termos de precisão, em cerca de 10 a 15% (AUC).  

Todavia, a métrica mais importante na variação é a taxa de classificação incorreta: ou seja, dos N maiores índices de variação conforme previsto pelo classificador, quais deles realmente **não** apresentaram variação e, ainda assim, receberam tratamento especial?  O diagrama a seguir compara essa taxa de erro na classificação em todos os modelos:  

![][7]

 
*Figura 9: Área do protótipo do Passau sob a curva * 

###Usando a área sob a curva ou AUC (Area Under Curve) para comparar resultados
A área sob a curva (AUC) é uma métrica que representa uma medição global da *separabilidade* entre as distribuições de pontos para populações positivas e negativas. Ela é similar ao gráfico ROC (Receiver Operator Characteristic) tradicional, mas uma diferença importante é que a métrica da AUC não exige que você escolha um valor de limite. Em vez disso, ela resume os resultados relativos a **todas** as escolhas possíveis. Em contraste, o gráfico ROC tradicional exibe a taxa de resultados positivos no eixo vertical e a taxa de falsos positivos no eixo horizontal, sendo que o limite de classificação varia.   

A área sob a curva (AUC) geralmente é usada como uma medida de valor para algoritmos diferentes (ou sistemas diferentes), porque permite que os modelos sejam comparados por meio do valor de AUC que apresentam. Essa é uma abordagem popular na meteorologia, ciências biológicas e muitos outros setores da indústria. Assim, a AUC representa uma ferramenta popular para avaliar o desempenho de um classificador.  

###Comparando as taxas de erro na classificação
Comparamos as taxas de erro na classificação no conjunto de dados em questão, usando os dados de CRM de aproximadamente 8.000 assinaturas.  

-	A taxa de erro na classificação da SAS foi de 10 a 15%.
-	A taxa de erro na classificação AM do Azure foi de 15 a 20% para os 200 a 300 maiores índices de variação.  

Na indústria de telecomunicações, é importante tratar somente daqueles clientes que apresentam o maior risco de variação, oferecendo a eles um serviço de concierge ou outro tratamento especial. Quanto a isso, a implementação AM de Nuvem do Azure atinge resultados similares aos do modelo SAS.  

Pelo mesmo token, a exatidão é mais importante que a precisão, porque estamos interessados, principalmente, em classificar corretamente os índices de variação em potencial.  

O diagrama a seguir, da Wikipédia, representa a relação em um gráfico vívido, fácil de entender:  

![][8]
 
*Figura 10: Troca entre exatidão e precisão*

###Resultados de exatidão e precisão para modelo de árvore aumentada  

A tabela a seguir exibe os resultados brutos da pontuação, utilizando o protótipo AM de Nuvem do Azure para o modelo de árvore de decisão aumentada de duas categorias, que é o modelo com maior exatidão entre os quatro modelos disponíveis:  

![][9]

*Figura 11: Características da árvore aumentada*

##Comparação de desempenho 
Comparamos a velocidade com a qual os dados receberam sua pontuação, usando os modelos de AM de Nuvem do Azure e um modelo comparável criado usando a edição de área de trabalho do SAS Enterprise Miner 12.1.  

A tabela a seguir resume o desempenho dos algoritmos:  

*Tabela 1. Desempenho geral (exatidão) dos algoritmos * 

**LR**|	**BT**|	**AP**|	**SVM**|
--|--|--|--|
Modelo médio|	O melhor modelo|	Baixo desempenho|	Modelo médio

Os modelos hospedados na Nuvem AM do Azure tiveram melhor desempenho que a SAS, 15 a 25% em velocidade de execução, mas a exatidão foi bastante parecida.  

#Discussão e recomendações
Na indústria de telecomunicações, diversas práticas surgiram para analisar a variação:  

-	As métricas se derivam de quatro categorias essenciais:
	-	**Entidade (p. ex., assinatura)**. Forneça informações básicas sobre a assinatura e/ou cliente que é sujeito a variação.
	-	**Atividade**. Obtenha todas as informações de uso possíveis relacionadas à entidade. Por exemplo, o número de logons, etc.
	-	**Atendimento ao cliente**. Colete informações por meio dos logs de suporte do cliente para indicar se a assinatura teve problemas ou interações com o atendimento ao cliente. 
	-	**Dados empresariais e de concorrência**. Obtenha quaisquer informações possíveis sobre o cliente (podem estar indisponíveis ou difíceis de acompanhar).
-	Use a importância como critério para acelerar a seleção de recursos. Isso significa que a árvore de floresta aumentada/aleatória é sempre uma abordagem promissora.  

O uso das quatro categorias acima cria a ilusão de ser uma simples abordagem *determinística*, baseada em índices formados por fatores razoáveis divididos por categoria, caso seja suficiente identificar os clientes apresentando risco de variação. Infelizmente, apesar de esse conceito parecer plausível, ele não é verdadeiro. O motivo é que a variação é um efeito temporal e os fatores que contribuem para a variação estão, geralmente, em estados transientes. O que leva um cliente a considerar a possibilidade de sair hoje pode ser diferente do motivo de amanhã e, com certeza, será diferente do motivo que ele terá para isso daqui a seis meses. Assim, um modelo *probabilístico* é necessário.  
 
Essa observação importante muitas vezes é ignorada pela empresa, que geralmente prefere uma abordagem orientada por BI (Business Intelligence) para a análise, principalmente porque é algo mais fácil de vender e permite automação simples.  

Todavia, a promessa de análise self-service usando o AM de Nuvem do Azure é que as quatro categorias de informação, classificadas pela divisão ou departamento, tornem-se uma fonte valiosa de recursos para aprendizado de máquina sobre variação.  
 
Outra capacidade animadora que acompanha o AM de Nuvem do Azure é a de adicionar um módulo personalizado ao repositório de módulos pré-definidos já disponíveis. Essa capacidade, essencialmente, cria uma oportunidade de selecionar bibliotecas e criar modelos para mercados verticais. Esse é um diferencial importante do AM de Nuvem do Azure no mercado.  

Esperamos tratar desse tópico no futuro, especialmente com relação à análise de Big Data.
  
#Conclusão
Este documento descreve uma abordagem sensível para a resolução de um problema comum, a variação do cliente, usando uma estrutura genérica. Consideramos um protótipo para atribuir pontuação a modelos e o implementamos, usando o AM de Nuvem do Microsoft Azure. Por fim, avaliamos a exatidão e o desempenho do protótipo da solução com relação a algoritmos comparáveis em SAS.  

**Para mais informações:**  

Este documento foi útil para você? Por favor, nos dê seu feedback. Avalie em uma escala de 1 (ruim) a 5 (excelente), como você classificaria este documento e o porquê de ter dado essa classificação? Por exemplo:  

-	Você está dando uma classificação alta devido a ter bons exemplos, excelentes capturas de tela, escrita clara ou por outro motivo? 
-	Você está dando uma classificação baixa devido a exemplos ruins, capturas de tela distorcidas ou escrita confusa?  

Seu feedback nos ajudará a melhorar a qualidade dos white papers que produzirmos.   

[Enviar feedback](mailto:sqlfback@microsoft.com).
 
#Referências
[1] Predictive analytics: beyond the predictions, W. McKnight, Information Management, July/August 2011, p.18-20.  

[2] http://en.wikipedia.org/wiki/Accuracy_and_precision   
 
[3] http://www.the-modeling-agency.com/crisp-dm.pdf   

[4] Big Data Marketing  

[5] http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn
 
#Apêndice

![][10]
 
*Figura 12 - Instantâneo de uma apresentação de protótipo de variação*
  

[1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png

<!--HONumber=46--> 
