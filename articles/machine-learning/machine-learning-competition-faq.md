<properties
	pageTitle="Perguntas frequentes sobre a competição do Aprendizado de Máquina | Microsoft Azure"
	description="Perguntas frequentes sobre as competições do Microsoft Cortana Analytics Suite."
	services="machine-learning"
	documentationCenter=""
	authors="hning86"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/11/2016"
	ms.author="haining;chlovel;garye"/>

# Perguntas frequentes sobre as competições do Microsoft Cortana Analytics Suite

**Onde posso fazer perguntas gerais sobre a ciência de dados?**

Use nosso [fórum do Aprendizado de Máquina do Microsoft Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning).

**Como faço para participar de uma competição?**

Crie uma conta gratuita ou paga no [Aprendizado de Máquina do Microsoft Azure](https://studio.azureml.net/?selectAccess=true&o=2%22%20\t%20%22_blank). Siga o tutorial de concorrência e assista ao breve vídeo.

**É necessário ser um cientista de dados para participar?**

Não. Na verdade, incentivamos entusiastas de dados, pessoas que têm curiosidade a respeito de cientistas de dados e outros cientistas de dados não especialistas para participar de nossa competição. Criamos documentos de suporte para permitir que todos possam participar.

**Quanto tempo isso levará?**

É possível participar de nossa competição e criar um modelo de dados em 10 a 15 minutos. Refinar seu experimento preditivo pode levar mais de 15 minutos, dependendo de sua familiaridade com o aprendizado de máquina.

**Como minha pontuação é calculada? É ele calculada de maneira diferente durante a competição e após seu encerramento? Quais são as pontuações públicas e privadas?**

1.  Todo o conjunto de dados foi dividido aleatoriamente com a estratificação em dados de treinamento (60%) e de teste (os 40% restantes). A divisão aleatória é estratificada em área geográfica + segmento + subgrupo para garantir que as distribuições de rótulos nos dados de treinamento e de teste são consistentes em cada região.  

2.  Os dados de treinamento foram carregados e fornecidos a você como parte do Experimento Inicial na configuração do módulo de Leitor.

3.  Os dados de teste foram divididos ainda mais em dados de testes público e privado, usando a mesma estratificação (60% para teste público e 40% para teste privado).

4.  Os dados de teste público foram usados para a fase inicial de pontuação. O resultado é chamado de pontuação pública e é isso que você obtém em seu histórico de envio ao enviar sua entrada. Isso foi feito todas as vezes em que você enviou uma entrada. Essa pontuação pública é usada para classificá-lo no placar de líderes público.

5.  Os dados de teste privado foram usados para a fase final de pontuação após o encerramento da competição. Eles são chamados de pontuação privada.

6.  Para cada participante, cinco de suas entradas com as pontuações públicas mais altas foram selecionadas automaticamente para calcular as pontuações privadas. A entrada com a pontuação privada mais alta foi selecionada para entrar na classificação final, que, em última análise, determinou os vencedores do prêmio.

**É necessário pagar por isso?**

Não, você pode configurar um espaço de trabalho gratuito no Aprendizado de Máquina do Microsoft Azure para competir. Tenha em mente de que as contas Convidado não têm a capacidade de salvar dados; portanto, certifique-se de enviar sua entrada antes que as 8 horas gratuitas expirem.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Como faço para atualizar para obter uma assinatura paga?**

É possível comprar ou atualizar sua assinatura nesta página no Microsoft Azure Marketplace. Veja [essa página](https://azure.microsoft.com/pricing/details/machine-learning/) para acessar as perguntas frequentes sobre preços.

**O que significa o botão Experimentar Belize?**

(Incluir instruções do portal dos serviços Web e captura de tela com etapas para voltar)

**O que acontecerá se eu ganhar uma competição?**

A Microsoft verificará os resultados do placar de líderes privado e, logo em seguida, entrará em contato com você. Certifique-se de que o endereço de email em seu perfil do usuário está atualizado.

**Como faço para receber a recompensa se eu ganhar uma competição?**

Se você for um vencedor do concurso, precisará assinar uma declaração de qualificação, licença e isenção. Este formulário reitera as Regras da Competição. Os vencedores precisarão preencher um formulário Fiscal dos EUA W-9 ou um formulário W-8BEN se não forem contribuintes dos EUA.

<!---HONumber=AcomDC_0323_2016-->