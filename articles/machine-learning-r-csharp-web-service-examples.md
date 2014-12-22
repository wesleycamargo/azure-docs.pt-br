<properties title="Sample Web Services Built with R on Azure ML and Published to Marketplace" pageTitle="Exemplo de serviços Web criados com R no AM do Azure e publicados no Marketplace | Azure" description="Sample Web Services Built with R on Azure ML and Published to Marketplace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/13/2014" ms.author="jaymathe" /> 


#Exemplo de serviços Web criados com R no AM do Azure e publicados no Marketplace




Com o Studio de aprendizado de máquina do Azure, os usuários podem escrever código R e, com alguns cliques de botão, publicá-lo como um serviço Web para outras pessoas e dispositivos o consumirem em todo o mundo. De produzir calculadoras simples que fornecem funcionalidade de estatística até a criação de um preditor de análise de sentimento de mineração de texto personalizado, por exemplo, usuários do R novos e experientes podem aproveitar a facilidade com que o AM do Azure pode operacionalizar código R. Embora esses serviços Web possam ser consumidos por usuários, potencialmente por meio de um aplicativo móvel ou um site, a finalidade desses serviços Web também é servir como exemplo de como o AM do Azure pode operacionalizar scripts R para fins analíticos e a criação de serviços Web sobre código R.

Nesta página, você encontrará um amplo conjunto de exemplo de serviços Web criados usando o AM do Azure e publicados no Microsoft Azure Marketplace. Cada serviço Web tem um documento abrangente anexado, incorporando exemplos de conjuntos de dados para testar os serviços e explicando como o usuário pode criar seu próprio serviço semelhante. 

![Workflow][1]

Considere os seguintes cenários:

####Cenário 1: Modelo genérico 
Um usuário trabalha com um modelo genérico que pode ser aplicado a dados de um novo usuário, como uma previsão básica de dados de série temporal ou um método de R personalizado com análises avançadas. Esse usuário publica o modelo como um serviço Web para que outros possam consumi-lo com seus dados.

* [Classificador binário](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-r-csharp-binary-classifier/)
* [Modelo de cluster](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-r-csharp-cluster-model/)
* [Regressão linear multivariada](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-r-csharp-multivariate-linear-regression/)
* [Ajuste exponencial de previsão](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-r-csharp-forecasting-exponential-smoothing/)
* [Previsão ETS + STL](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-r-csharp-retail-demand-forecasting/)
* [ARIMA (Forecasting-AutoRegressive Integrated Moving Average, média móvel integrada de previsão-autorregressão)](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-r-csharp-arima/)
* [Análise de sobrevivência](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-r-csharp-survival-analysis/)

####Cenário 2: Modelo treinado - dados específicos 
Um usuário tem dados que fornecem previsões úteis por meio de código R, como uma grande amostra de questionários personalidade agrupados por meio de um algoritmo de médias k para prever os dados da pesquisa de saúde ou tipo de personalidade do usuário que podem ser usados para prever o risco de uma pessoa para câncer pulmão com um pacote R de análise de sobrevivência. O usuário publica os dados por meio de um serviço Web que prevê o resultado de um novo usuário.

####Cenário 3: Modelo treinado - dados genéricos 
Um usuário possui dados genéricos (por exemplo, um corpus de texto) que permitem desenvolver um serviço Web que pode ser aplicado genericamente a diferentes tipos de cenários e casos de uso.

* [Análise de sentimento baseada em léxico](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-r-csharp-lexicon-based-sentiment-analysis/)

####Cenário 4: Calculadora avançada 
Um usuário fornece cálculos avançados ou simulações, que não requerem qualquer modelo treinado ou ajuste de um modelo aos dados do usuário.

* [Diferença no teste de proporções](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-r-csharp-difference-in-two-proportions/)
* [Pacote de distribuição normal](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-r-csharp-normal-distribution/)
* [Pacote de distribuição binomial](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-r-csharp-binomial-distribution/)

##Perguntas frequentes
Para perguntas frequentes sobre o consumo do serviço Web ou a publicação no marketplace, consulte [aqui](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-web-service-examples/base1.png


