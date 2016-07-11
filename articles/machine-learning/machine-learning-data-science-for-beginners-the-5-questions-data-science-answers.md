<properties
   pageTitle="As cinco perguntas da ciência de dados - Ciência de dados para iniciantes | Microsoft Azure"
   description="Obtenha uma breve introdução da ciência de dados com Ciência de dados para iniciantes, cinco vídeos curtos que começam com As cinco perguntas que a ciência de dados responde."
   keywords="fazendo ciência de dados, introdução à ciência de dados, ciência de dados para iniciantes, tipos de perguntas, perguntas de ciência de dados e algoritmos de ciência de dados"
   services="machine-learning"
   documentationCenter="na"
   authors="brohrer-ms"
   manager="paulettm"
   editor="cjgronlund"/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/24/2016"
   ms.author="cgronlun;brohrer;garye"/>

# Ciência de dados para iniciantes, vídeo 1: As cinco perguntas que a ciência de dados responde

Obtenha uma breve introdução à ciência de dados com *Ciência de dados para iniciantes* em cinco vídeos curtos. Esta série de vídeos é útil se você estiver interessado em fazer ciência de dados - ou trabalhar com pessoas que fazem ciência de dados - e quer começar com os conceitos mais básicos.

Para aproveitar ao máximo a série, assista aos vídeos na ordem. [Acesse a lista de vídeos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-series-the-5-questions-data-science-answers]

## Transcrição: As cinco perguntas que a ciência de dados responde

Olá! Bem-vindo à série de vídeos *Ciência de dados para iniciantes*.

O ciência de dados pode ser intimidadora, portanto, vou apresentar os conceitos básicos sem equações ou jargão de programação de computador.

Neste primeiro vídeo, falaremos sobre "As cinco perguntas que a ciência de dados responde".

A ciência de dados usa números e nomes (também conhecidos como categorias ou rótulos) para prever as respostas às perguntas.

Isso pode surpreender você, mas *há apenas cinco perguntas que a ciência de dados responde*:

  * Isso é A ou B?
  * Isso é estranho?
  * Quanto custa – ou – Quantas?
  * Como isso é organizado?
  * O que devo fazer agora?

  Cada uma dessas perguntas é respondida por uma família separada de métodos de aprendizado de máquina, chamados de algoritmos.


É útil pensar em um algoritmo como uma receita, e seus dados como os ingredientes. Um algoritmo informa como combinar e mesclar os dados para obter uma resposta. Os computadores são como o liquidificador. Eles fazem o trabalho mais pesado do algoritmo, e fazem isso bem rápido.

## Pergunta 1: Isso é A ou B? usa algoritmos de classificação

Vamos começar com a pergunta: Isso é A ou B?

![Algoritmos de classificação: Isso é A ou B?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-classification-algorithms.png)

Essa família de algoritmos é chamada de classificação de duas classes.

Ela é útil para qualquer pergunta que tenha apenas duas respostas possíveis.

Por exemplo:

  *	Este pneu falhará nos próximos 1000 Km: Sim ou não?
  *	O que traz mais clientes: um cupom de $5 ou 25% de desconto?

Essa questão também pode ser refeita para incluir mais do que duas opções: Isso é A ou B ou C ou D etc.? Isso é chamado de classificação multiclasse e é útil quando você tem vários — ou vários milhares — de respostas possíveis. A classificação multiclasse escolhe a mais provável.

## Pergunta 2: Isso é estranho? usa algoritmos de detecção de anomalias

A próxima pergunta que a ciência de dados pode responder é: Isso é estranho? Essa pergunta é respondida por uma família de algoritmos chamados de detecção de anomalias.

![Algoritmos de detecção de anomalias: Isso é estranho?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-anomaly-detection-algorithms.png)


Se você tiver um cartão de crédito, você já se beneficiou da detecção de anomalias. Sua empresa de cartão de crédito analisa os padrões de compra, para que possam alertar você sobre uma possíveis fraudes. Os encargos "estranhos" podem ser uma compra em uma loja onde você normalmente não compra, ou uma compra de um item muito caro.

Essa questão pode ser útil de várias formas. Por exemplo:

  *	Se você tiver um carro com medidores de pressão, talvez queira saber: Esse medidor está lendo normalmente?
  *	Se você estiver monitorando internet, talvez queria saber: Esta mensagem da internet é normal?

A detecção de anomalias sinaliza comportamentos ou eventos incomuns ou inesperados. Ela dá dicas sobre onde procurar problemas.



## Pergunta 3: Quanto custa? ou Quantos? usa algoritmos de regressão

O Aprendizado de máquina também pode prever a resposta para Quanto custa? ou Quantos? A família de algoritmo que responde a essa pergunta é chamada de regressão.

![Algoritmos de regressão: Quanto custa? ou Quantos?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-regression-algorithms.png)


Algoritmos de regressão fazem previsões numéricas, como:

  *	Qual será a temperatura na próxima terça-feira?
  *	Como serão as vendas em meu quarto trimestre?

Eles ajudam a responder qualquer pergunta cuja resposta seja um número.

## Pergunta 4: Como isso é organizado? usa algoritmos de clustering

Agora, as duas últimas perguntas são um pouco mais avançadas.

Às vezes, você deseja compreender a estrutura de um conjunto de dados - Como isso é organizado? Para essa pergunta, não há exemplos para os quais você já conhece os resultados.

Há muitas maneiras de revelar a estrutura dos dados. Uma abordagem é o clustering. Ele separa os dados em "grupos" naturais, para facilitar a interpretação. Com o clustering, não há uma resposta correta.

![Algoritmos de clustering: Como isso é organizado?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-clustering-algorithms.png)

Exemplos comuns de perguntas com clustering são:

  *	Quais expectadores gostam dos mesmos tipos de filmes?
  *	Quais modelos de impressora falham da mesma forma?

Ao compreender como os dados são organizado, você pode entender melhor, e prever, comportamentos e eventos.

## Pergunta 5: O que devo fazer agora? usa algoritmos de aprendizado de reforço

A última pergunta, o que devo fazer agora?, usa uma família de algoritmos chamada de aprendizado de reforço.

Aprendizado de reforço foi inspirado pelo modo como o cérebro dos ratos e dos humanos responde ao sofrimento e às recompensas. Esses algoritmos aprendem com os resultados e decidem sobre a próxima ação.

Normalmente, o aprendizado de reforço é uma boa opção para sistemas automatizados que precisam tomar muitas decisões pequenas sem orientação humana.

![Algoritmos de aprendizado de reforço: O que devo fazer agora?](./media/machine-learning-data-science-for-beginners-the-5-questions-data-science-answers/machine-learning-data-science-reinforcement-learning-algorithms.png)

As perguntas que eles respondem são sempre sobre qual ação deve ser executada, geralmente por um computador ou um robô. Os exemplos abrangem:

  *	Se eu for um sistema de controle de temperatura de uma casa: ajusto a temperatura ou deixo como está?
  *	Se eu for um carro automático: no farol amarelo, freio ou acelero?
  *	Para um robô aspirador: continuo a aspiração ou volto para a estação de carregamento?

Algoritmos de aprendizado de reforço coletam os dados conforme eles surgem, aprendendo por tentativa e erro.

Então é isso, As cinco perguntas que a ciência de dados pode responder.



## Outros vídeos nesta série

*Ciência de dados para iniciantes* é uma breve introdução à ciência de dados em cinco vídeos curtos. Confira os outros quatro vídeos:

  * Vídeo 2: [Seus dados estão prontos para a ciência de dados?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) **Disponível agora.**
  * Video 3: [Faça uma pergunta que você possa responder com dados](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md). **Disponível agora.**
  * Vídeo 4: [Preveja uma resposta com um modelo simples](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md). **Disponível agora.**
  * Vídeo 5: [Copie o trabalho de outras pessoas para fazer a ciência de dados](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md). Disponível em 30 de junho.

## Próximas etapas

  * [Tenha sua primeira experiência da ciência de dados com o Aprendizado de Máquina do Azure](machine-learning-create-experiment.md)
  * [Obtenha uma introdução ao Aprendizado de Máquina no Microsoft Azure](machine-learning-what-is-machine-learning.md)

<!---HONumber=AcomDC_0629_2016-->