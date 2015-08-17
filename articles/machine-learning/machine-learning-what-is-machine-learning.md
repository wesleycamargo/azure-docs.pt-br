<properties
    pageTitle="O que é o Aprendizado de Máquina no Azure? | Microsoft Azure"
    description="Explica conceitos básicos do serviço de Aprendizado de Máquina totalmente gerenciado, uma tecnologia de nuvem que você pode usar para criar, operacionalizar e lucrar com soluções."
	services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="neerajkh"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/23/2015"
    ms.author="cgronlun;tedway;olgali"/>


# Introdução ao aprendizado de máquina no Microsoft Azure

## O que aprendizado da máquina?

O aprendizado de máquina usa computadores para executar modelos preditivos, que por sua vez obtêm informações por meio de dados existentes para prever tendências, resultados e comportamentos futuros.

Essas estimativas ou previsões de aprendizado de máquina podem tornar aplicativos e dispositivos mais inteligentes. Quando você faz compras online, o aprendizado de máquina ajuda a recomendar outros produtos que podem lhe agradar com base no que você já comprou. Ao passar seu cartão de crédito, o aprendizado de máquina compara a transação a outras transações contidas em um banco de dados, e ajuda o banco a detectar eventuais fraudes.

## O que é o Aprendizado de Máquina no Microsoft Azure?

O Aprendizado de Máquina do Azure é um serviço de análise de previsão poderoso baseado em nuvem que permite criar rapidamente modelos preditivos e implantá-los como soluções de análise.

O Aprendizado de Máquina do Azure não apenas fornece ferramentas para criar modelos de análise preditiva, mas também fornece um serviço totalmente gerenciado que você pode usar para publicar seus modelos preditivos como serviços Web prontos para consumo. O Aprendizado de Máquina do Azure fornece ferramentas para criar soluções de análise preditiva completas na nuvem: criar, testar, operacionalizar e gerenciar modelos preditivos rapidamente. Você não precisa comprar nenhum hardware nem gerenciar máquinas virtuais manualmente.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## O que é análise preditiva?

A análise preditiva usa várias técnicas estatísticas - neste caso, aprendizado de máquina - para analisar os dados atuais ou coletados em busca de padrões ou tendências, para prever eventos futuros.

O Aprendizado de Máquina do Azure é uma maneira especialmente poderosa de fazer análise preditiva: você pode trabalhar em uma biblioteca de algoritmos pronta para uso, criar modelos em um PC conectado à Internet sem a compra de equipamentos ou infraestrutura adicionais e implantar sua solução preditiva rapidamente. Você também pode encontrar exemplos e soluções prontos para uso no [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning) ou [Galeria do Aprendizado de Máquina](http://gallery.azureml.net/).

## Crie soluções completas de aprendizado de máquina em nuvem

O Aprendizado de Máquina do Azure tem tudo o que você precisa para criar soluções de análise preditiva em nuvem, desde uma biblioteca de algoritmos grande, passando por um estúdio para criação de modelos e incluindo até uma maneira fácil de implantar o modelo como um serviço Web.

### Estúdio de Aprendizado de Máquina: criar modelos preditivos

Crie modelos preditivos no [Estúdio de Aprendizado de Máquina](machine-learning-what-is-ml-studio.md), uma ferramenta baseada em navegador, arrastando, soltando e conectando módulos.

![O que é análise preditiva: exemplo de um experimento de análise preditiva no Estúdio de Aprendizado de Máquina do Azure](./media/machine-learning-what-is-machine-learning/AzureMLStudio.png)

* Use uma ampla biblioteca de [algoritmos e módulos de aprendizado de máquina](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) no Estúdio de Aprendizado de Máquina para começar imediatamente seus modelos preditivos. Escolha em uma biblioteca de experimentos de exemplo, pacotes R e Python e algoritmos de ponta provenientes de empresas da Microsoft, como Xbox e Bing. Estenda os módulos do Estúdio com seus próprios scripts [R](machine-learning-r-quickstart.md) e [Python](machine-learning-execute-python-scripts.md) personalizados.
* Na [Galeria da Comunidade de Aprendizado de Máquina](machine-learning-gallery-how-to-use-contribute-publish.md), você pode começar o Aprendizado da Máquina do Azure e aprender com outros participantes da comunidade. Teste experimentos criados por outros usuários, faça perguntas ou envie comentários sobre experimentos ou publique experimentos de sua própria autoria. Você também pode compartilhar links para experimentos por meio de redes sociais, como o LinkedIn e o Twitter.  

	![Tente exemplos de experimento de previsão ou contribua com o seu próprio na Galeria de Aprendizado da Máquina do Azure](./media/machine-learning-what-is-machine-learning/AzureMLGallery.png)

### Operacionalizar soluções de análise preditiva: comprar serviços Web ou publicar os seus próprios

* Adquira no [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning) serviços Web prontos para consumo, como Recomendações, Análise de texto e Detecção de anomalias.

* Operacionalize seus modelos de análise preditiva:
    * [Publicar serviços Web](machine-learning-publish-a-machine-learning-web-service.md)
    * [Treinar e repetir o treino de modelos por meio de APIs](machine-learning-retrain-models-programmatically.md)
    * [Gerenciar pontos de extremidade de serviço Web](machine-learning-create-endpoint.md)
    * [Dimensionar serviços Web](machine-learning-scaling-endpoints.md)
    * [Consumir serviços Web](machine-learning-consume-web-services.md)

## Terminologia e conceitos-chave de aprendizado de máquina
### Exploração de dados, análise descritiva e análise preditiva

**Exploração de dados** é o processo de coleta de informações sobre um conjunto de dados grande e geralmente não estruturado, buscando encontrar características para análise com foco específico. **Mineração de dados** refere-se à mineração de dados automatizada.

**Análise descritiva** é o processo de análise de um conjunto de dados para gerar um resumo do que aconteceu. A grande maioria da análise de negócios - como relatórios de vendas, métricas de Web e análises de redes sociais - são descritivas.

**Análise preditiva** é o processo de criação de modelos por meio de dados atuais ou históricos para prever resultados futuros.


### Aprendizado supervisionado e não supervisionado
 Os algoritmos de **aprendizado supervisionado** são treinados com dados rotulados – em outras palavras, dados compostos de exemplos das respostas desejadas. Por exemplo, um modelo que identifica o uso fraudulento de cartão de crédito seria treinado por meio de um conjunto de dados no qual aqueles pontos de dados indicando os encargos fraudulentos e válidos conhecidos foram rotulados. Na maioria das vezes, o aprendizado de máquina é supervisionado.

 O **aprendizado não supervisionado** é usado em dados sem rótulos e o objetivo é localizar relações existentes nos dados. Por exemplo, você talvez queira localizar agrupamentos de dados demográficos de clientes com hábitos de compra semelhantes.

### Treinamento e avaliação de modelo
Um modelo de aprendizado de máquina é uma abstração da pergunta que você está tentando responder ou o resultado que você deseja prever. Os modelos são treinados e avaliados por meio de dados existentes.

#### Treinamento por meio de dados
No Aprendizado de Máquina do Azure, um modelo é criado por meio de um módulo de algoritmos que processa os dados de treinamento e módulos funcionais, por exemplo, um módulo de pontuação.

No aprendizado supervisionado, se você for treinar um modelo de detecção de fraudes, você usará um conjunto de transações rotuladas como fraudulentas ou então como válidas. Você dividirá seu conjunto de dados aleatoriamente, usando uma parte dele para treinar o modelo e a outra parte para testar ou avaliar tal modelo.

#### Dados de avaliação
A partir do momento em que você tem um modelo treinado, avalie esse modelo usando os dados de teste restantes. Você utiliza os dados cujos resultados você já conhece, para que você possa determinar se o seu modelo prevê com precisão.

### Outros termos comuns de aprendizado de máquina

* **algoritmo**: Um conjunto independente de regras usadas para resolver problemas por meio do processamento de dados, cálculo, ou raciocínio automatizado.
* **dados categóricos**: Dados organizados em categorias e que podem ser divididos em grupos. Por exemplo, um conjunto de dados categórico para carros poderia especificar ano, marca, modelo e preço.
* **classificação**: Um modelo para organizar os pontos de dados em categorias com base em um conjunto de dados para o qual os agrupamentos de categorias já são conhecidos.
* **engenharia de recursos**: O processo de extração ou seleção de recursos relacionados a um conjunto de dados, para otimizar o conjunto de dados e aprimorar os resultados. Por exemplo, dados de passagens aéreas poderiam ser otimizados classificando-os em dias de semana e feriados. Consulte [Engenharia e seleção de recursos no Aprendizado de Máquina do Azure](machine-learning-feature-selection-and-engineering.md).
* **módulo**: Um elemento funcional em um modelo de Estúdio de Aprendizado de Máquina, como por exemplo o módulo Inserir Dados, que permite a inserção e edição de pequenos conjuntos de dados. Um algoritmo também é um tipo de módulo no Estúdio de Aprendizado de Máquina.
* **modelo**: Para aprendizado supervisionado, um modelo é o produto de um experimento de aprendizado de máquina constituído de um conjunto de dados de treinamento, um módulo de algoritmo e módulos funcionais, como um módulo do Modelo de Pontuação.
* **dados numéricos**: Dados que têm significado, como medidas (dados contínuos) ou contagens (dados discretos). Também conhecidos como *dados quantitativos*.
* **partição**: O método pelo qual você divide dados em exemplos. Consulte [Partição e Exemplo](https://msdn.microsoft.com/library/azure/dn905960.aspx) para obter mais informações.
* **previsão**: Uma previsão é uma estimativa de um valor ou valores de um modelo de aprendizado de máquina. Você também pode ver o termo "pontuação prevista"; no entanto, pontuações previstas não são a saída final de um modelo. Uma avaliação do modelo vem a seguir da pontuação.
* **regressão**: Um modelo para prever um valor contínuo com base em variáveis independentes, como prever o preço de um carro com base em seu ano e marca.
* **pontuação**: um valor previsto gerado por meio de uma classificação treinada ou um modelo de regressão, usando o [módulo Modelo de Pontuação](https://msdn.microsoft.com/library/azure/dn905995.aspx) no Estúdio de Aprendizado de Máquina. Os modelos de classificação também retornam uma pontuação para a probabilidade do valor previsto. Depois de ter gerado pontuações por meio de um modelo, você pode avaliar a precisão desse modelo usando o [módulo Avaliar modelo](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **exemplo**: uma parte de um conjunto de dados deve ser representativa do todo. Exemplos podem ser selecionados aleatoriamente ou com base em recursos específicos do conjunto de dados.



## Próximas etapas
Você pode aprender os fundamentos da análise de previsão e do aprendizado da máquina usando um [tutorial passo a passo](machine-learning-create-experiment.md) e [aproveitando os exemplos](machine-learning-sample-experiments.md).


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/

<!---HONumber=August15_HO6-->