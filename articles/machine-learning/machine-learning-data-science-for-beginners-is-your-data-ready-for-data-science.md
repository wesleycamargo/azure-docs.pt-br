<properties
   pageTitle="Seus dados estão pronto para ciência de dados? - avaliação dos dados | Microsoft Azure"
   description="Saiba sobre os quatro critérios para os dados estarem prontos para a ciência de dados. O vídeo Ciência de Dados para Iniciantes 2 tem exemplos concretos para ajudar na avaliação dos dados básicos."
   keywords="avaliação dos dados, dados relevantes, avaliar dados, preparar dados, critérios dos dados, dados prontos"
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
   ms.date="06/27/2016"
   ms.author="cgronlun;brohrer;garye"/>


# Seus dados estão prontos para a ciência de dados? Vídeo Ciência de Dados para Iniciantes 2

Saiba como avaliar seus dados para assegurar que eles atendem aos critérios básicos da ciência de dados.

> [AZURE.VIDEO data-science-for-beginners-series-is-your-data-ready-for-data-science]

## Transcrição: Seus dados estão prontos para a ciência de dados?

Bem-vindo ao "Seus dados estão prontos para a ciência de dados?", o segundo vídeo na série *Ciência de Dados para Iniciantes*.

Antes da ciência de dados poder fornecer as respostas desejadas, você precisa dar-lhe algum material bruto de alta qualidade com o qual trabalhar. Assim como fazer uma pizza, quanto melhores os ingredientes com os quais você inicia, melhor o produto final.

## Critérios dos dados

Assim, no caso da ciência de dados, existem alguns componentes que precisamos para reunir.

Precisamos de dados que sejam:

  *	Relevantes
  *	Conectado
  *	Precisos
  *	Suficientes para trabalhar

## Seus dados são relevantes?

Então, o primeiro princípio - precisamos de dados relevantes.

![Dados relevantes versus dados irrelevantes - avaliar dados](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-relevant-and-irrelevant-data.png)

Veja a tabela à esquerda. Encontramos sete pessoas fora dos bares de Boston, medimos seu nível de álcool no sangue, a média de rebatidas do Red Sox no último jogo e o preço do leite na loja de conveniência mais próxima.

São todos dados perfeitamente legítimos. O único problema é que não são relevantes. Não há nenhuma relação óbvia entre esses números. Se eu fornecesse o preço atual do leite e a média de rebatidas do Red Sox, não seria possível estimar o teor de álcool no meu sangue.

Agora, veja a tabela à direita. Desta vez, medimos a massa corporal de cada pessoa e contamos o número de bebidas que beberam. Os números em cada linha agora são relevantes entre si. Se eu fornecesse a massa corporal e o número de margueritas bebidas, você poderia fazer uma estimativa do teor de álcool em meu sangue.

## Você tem dados conectados?

O próximo ingrediente serão os dados conectados.

![Dados conectados versus dados desconectados - critérios dos dados](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-connected-vs-disconnected-data.png)

Aqui estão alguns dados relevantes sobre a qualidade dos hambúrgueres: temperatura da churrasqueira, peso do hambúrguer e a classificação na revista de alimentos local. Mas observe as lacunas na tabela à esquerda.

A maioria dos conjuntos de dados não tem alguns valores. É comum ter buracos como esses e há maneiras de solucioná-los. Mas se houver muitas falhas, seus dados começarão a parecer um queijo suíço.

Se você vir a tabela à esquerda, há muitos dados ausentes e é difícil propor algum tipo de relação entre o peso do hambúrguer e a temperatura da churrasqueira. Este é um exemplo de dados desconectados.

A tabela à direita, no entanto, está completa - um exemplo de dados conectados.

## Seus dados são precisos?

O próximo ingrediente que precisamos é a precisão. Aqui estão os quatro destinos que gostaríamos de atingir com setas.

![Dados precisos versus dados imprecisos - critérios dos dados](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-inaccurate-vs-accurate-data.png)

Veja o destino à direita superior. Temos um agrupamento rígido em torno do alvo. Claro, isso é preciso. Estranhamente, na linguagem da ciência de dados, nosso desempenho no destino logo abaixo também é considerado preciso.

Se você fosse mapear centro dessas setas, veria que está muito próximo do alvo. As setas estão distribuídas em torno do destino, portanto, são consideradas imprecisas, mas estão centralizadas em torno do alvo, então, são consideradas precisas.

Agora, veja o destino à esquerda superior. Aqui, nossas setas estão muito próximas umas das outras, um agrupamento rígido. Elas são exatas, mas são imprecisas porque o centro está longe do alvo. E, naturalmente, as setas no destino à esquerda inferior são imprecisas e inexatas. O arqueiro precisa de mais prática.

## Você tem dados suficientes com os quais trabalhar?

Por fim, o ingrediente 4 - precisamos ter dados suficientes.

![Você tem dados suficientes para a análise? Avaliação dos dados](./media/machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science/machine-learning-data-science-barely-enough-data.png)

Pense em cada ponto de dados na tabela como sendo uma pincelada em uma pintura. Se você tiver apenas algumas, a pintura poderá ser bastante confusa – será difícil dizer o que é.

Se você adicionar mais algumas pinceladas, então, a pintura começará a ficar um pouco mais nítida.

Quando você tem pinceladas insuficientes, pode ver apenas o bastante para tomar algumas decisões amplas. É algum lugar que eu desejaria visitar? É claro, parece água limpa — sim, é para onde irei em férias.

Conforme você adiciona mais dados, a imagem fica mais clara e você pode tomar decisões mais detalhadas. Agora, posso ver três hotéis na orla à esquerda. Você sabe, realmente gosto dos recursos de arquitetura de um no primeiro plano. Ficarei lá, no terceiro andar.

Com dados relevantes, conectados, precisos e suficientes, temos todos os ingredientes necessários para fazer uma ciência de dados com alta qualidade.

Verifique os outros quatro vídeos em *Ciência de Dados para Iniciantes* no Aprendizado de Máquina do Microsoft Azure.


## Outros vídeos na série

*Ciência de Dados para Iniciantes* é uma breve introdução à ciência de dados em cinco vídeos curtos.

  * Vídeo 1: [As cinco perguntas que a ciência de dados pode responder](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md). **Disponível agora.**
  * Video 3: [Faça uma pergunta que você possa responder com dados](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md). **Disponível agora.**
  * Vídeo 4: [Preveja uma resposta com um modelo simples](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md). **Disponível agora.**
  * Vídeo 5: [Copie o trabalho de outras pessoas para fazer a ciência de dados](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md). Disponível em 30 de junho.


## Próximas etapas

  * [Tenha sua primeira experiência da ciência de dados com o Aprendizado de Máquina do Azure](machine-learning-create-experiment.md)
  * [Obtenha uma introdução ao Aprendizado de Máquina no Microsoft Azure](machine-learning-what-is-machine-learning.md)

<!---HONumber=AcomDC_0629_2016-->