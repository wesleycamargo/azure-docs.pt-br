<properties
   pageTitle="Faça uma pergunta que você possa responder com dados - Ciência de Dados para Iniciantes | Microsoft Azure"
   description="Saiba como formular uma pergunta da ciência de dados no Vídeo Ciência de Dados para Iniciantes 3. Inclui uma comparação das perguntas de classificação e regressão."
   keywords="escolher uma pergunta, perguntas da ciência de dados, formular a pergunta, formular perguntas, perguntas de regressão, perguntas de classificação, pergunta inteligente"
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
   ms.date="06/28/2016"
   ms.author="cgronlun;brohrer;garye"/>

# Ciência de Dados para Iniciantes - vídeo 3: Faça uma pergunta que você possa responder com dados 

Saiba como formular uma pergunta da ciência de dados no Vídeo Ciência de Dados para Iniciantes 3. Esse vídeo inclui uma comparação de perguntas para os algoritmos de classificação e regressão.

Para aproveitar ao máximo a série, assista aos vídeos na ordem. [Acesse a lista de vídeos](#other-videos-in-this-series)

> [AZURE.VIDEO data-science-for-beginners-ask-a-question-you-can-answer-with-data]

## Transcrição: Faça uma pergunta que você possa responder com dados

Bem-vindo ao terceiro vídeo da série "Ciência de Dados para Iniciantes".

Nele, você obterá algumas dicas para formular uma pergunta que possa responder com dados.

Você pode obter mais desse vídeo se primeiro observar dois vídeos anteriores na série: "As cinco perguntas que a ciência de dados pode responder" e "Seus dados estão prontos para a ciência de dados?"

## Faça uma pergunta inteligente

Falamos sobre como a ciência de dados é o processo de usar nomes (também chamados de categorias ou rótulos) e números para prever uma resposta para uma pergunta. Mas não pode ser uma pergunta qualquer; deve ser uma *pergunta inteligente.*

Uma pergunta vaga não precisa ser respondida com um nome ou um número. Uma pergunta inteligente sim.

Imagine que você encontrou uma lâmpada mágica com um gênio que responderá de verdade qualquer pergunta feita. Mas é um gênio mau e ele tentará tornar sua resposta o mais vaga e confusa que puder para se dar bem. Você deseja forçá-lo com uma pergunta incontestável para que ele não possa fazer nada, exceto dizer o que você deseja saber.

Se você fizesse uma pergunta vaga, como: "O que acontecerá com minhas ações?", o gênio poderia responder: "O preço mudará". É uma resposta sincera, mas não é muito útil.

Mas se fizesse uma pergunta direta, como: "Qual será o preço de venda de minhas ações na próxima semana?", o gênio não poderia fazer nada, exceto dar uma resposta específica e prever um preço de venda.

## Exemplos de resposta: dados de destino

Depois de formular sua pergunta, verifique para saber se você tem exemplos de resposta em seus dados.

Se nossa pergunta for: "Qual será o preço de venda de minhas ações na próxima semana?", então, teremos que assegurar que nossos dados incluam o histórico dos preços das ações.

Se nossa pergunta for: "Qual carro em minha frota terá problemas primeiro?", então, será preciso assegurar que nossos dados incluam informações sobre as falhas anteriores.

![Dados de destino - exemplos de resposta. Formule uma pergunta da ciência de dados.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-target-data.png)

Esses exemplos de respostas são chamados de destino. Um destino é o que estamos tentando prever sobre os futuros pontos de dados, seja uma categoria seja um número.

Se você não tiver nenhum dado de destino, precisará obter algum. Você não conseguirá responder à sua pergunta sem ele.

## Reformular sua pergunta

Às vezes, você pode reformular sua pergunta para obter uma resposta mais útil.

A pergunta: "É o ponto de dados A ou B?" prevê a categoria (nome ou rótulo) de algo. Para respondê-la, usamos um *algoritmo de classificação*.

A pergunta: "Quanto?" ou "Quantos?” prevê um valor. Para respondê-la, usamos um *algoritmo de regressão*.

Para ver como podemos transformar isso, vejamos a pergunta: "Qual notícia é mais interessante para este leitor?" Ela solicita uma previsão de uma opção entre muitas possibilidades — em outras palavras, "É A, B, C ou D?" — e usaria um algoritmo de classificação.

Mas, essa pergunta poderá ser mais fácil de responder se você reformulá-la como: "O quanto interessante é cada história nesta lista para este leitor?" Agora, você pode dar a cada artigo uma pontuação numérica e será fácil identificar o artigo com a pontuação mais alta. Isto é uma reformulação da pergunta de classificação em uma pergunta de regressão ou do tipo Quanto?

![Reformule sua pergunta. Pergunta de classificação versus pergunta de regressão.](./media/machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data/machine-learning-data-science-classification-question-vs-regression-question.png)

Como você faz uma pergunta é uma dica sobre qual algoritmo poderá dar uma resposta.

Você descobrirá que certas famílias de algoritmos — como aquelas em nosso exemplo de notícia — estão intimamente relacionadas. Você pode reformular sua pergunta para usar o algoritmo que fornece a resposta mais útil.

Porém, o mais importante é fazer uma pergunta inteligente — a pergunta que você pode responder com dados. E verifique se você tem os dados certos para respondê-la.

Falamos sobre alguns princípios básicos para fazer uma pergunta que você pode responder com dados.

Verifique outros vídeos em "Ciência de Dados para Iniciantes" no Aprendizado de Máquina do Microsoft Azure.

## Outros vídeos nesta série

*Ciência de dados para iniciantes* é uma breve introdução à ciência de dados em cinco vídeos curtos.


  * Vídeo 1: [As cinco perguntas que a ciência de dados responde](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md)
  * Vídeo 2: [Seus dados estão prontos para a ciência de dados?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md)
  * Video 3: Faça uma pergunta que você possa responder com dados
  * Vídeo 4: [Preveja uma resposta com um modelo simples](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md)
  * Vídeo 5: [Copie o trabalho de outras pessoas para fazer a ciência de dados](machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md)

## Próximas etapas

  * [Tenha sua primeira experiência da ciência de dados com o Aprendizado de Máquina do Azure](machine-learning-create-experiment.md)
  * [Obtenha uma introdução ao Aprendizado de Máquina no Microsoft Azure](machine-learning-what-is-machine-learning.md)

<!---HONumber=AcomDC_0706_2016-->