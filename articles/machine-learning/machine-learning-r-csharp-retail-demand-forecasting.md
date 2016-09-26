<properties 
	pageTitle="Previsão - ETS + STL | Microsoft Azure" 
	description="Previsão - ETS + STL" 
	services="machine-learning" 
	documentationCenter="" 
	authors="xueshanz" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/17/2016" 
	ms.author="yijichen"/>

#Previsão - ETS + STL  

Este [serviço Web](https://datamarket.azure.com/dataset/aml_labs/demand_forecast) implementa os modelos de Decomposição de tendência sazonal (STL) e Ajuste exponencial (ETS) para produzir previsões com base nos dados históricos fornecidos pelo usuário. A demanda de um produto específico aumentará neste ano? Posso prever as vendas do meu produto para a temporada de Natal para que eu possa planejar efetivamente meu estoque? Modelos de previsão são adequados para responder a essas perguntas. Considerando os dados passados, esses modelos examinam tendências ocultas e a sazonalidade para prever tendências futuras.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
>Este serviço Web poderia ser consumido por usuários – potencialmente por meio de um aplicativo móvel, de um site ou até mesmo em um computador local, por exemplo. Mas a finalidade do serviço Web é também servir como um exemplo de como o Aprendizado de Máquina do Azure pode ser usado para criar serviços Web sobre o código R. Com apenas algumas linhas de código R e cliques de botão dentro do Estúdio de Aprendizado de Máquina do Azure, um experimento pode ser criado com código R e publicado como um serviço Web. O serviço Web pode ser publicado no Azure Marketplace e consumido por dispositivos e usuários em todo o mundo – sem qualquer infraestrutura configurada pelo autor do serviço Web.
 
##Consumo do serviço Web 

Este serviço aceita quatro argumentos e calcula as previsões. Os argumentos de entrada são:

* Frequência - indica a frequência dos dados brutos (diários/semanais/mensais/trimestrais/anuais).
* Horizonte - período de tempo de previsão do futuro.
* Data - adicionar novos dados de série de tempo para o tempo.
* Valor - adicionar os novos valores de dados de série de tempo.

A saída do serviço são os valores de previsão calculados.
 
A amostrada de entrada poderia ser:

* Frequência - 12
* Horizonte - 12
* Data - 15/1/2012;15/2/2012;15/3/2012;15/4/2012;15/5/2012;15/6/2012;15/7/2012;15/8/2012;15/9/2012;15/10/2012;15/11/2012;15/12/2012; 15/1/2013;15/2/2013;15/3/2013;15/4/2013;15/5/2013;15/6/2013;15/7/2013;15/8/2013;15/9/2013;15/10/2013;15/11/2013;15/12/2013; 15/1/2014;15/2/2014;15/3/2014;15/4/2014;15/5/2014;15/6/2014;15/7/2014;15/8/2014;15/9/2014
* Valor - 3.479;3,68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511; 3.429;3,51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509

>Esse serviço, conforme hospedado no Azure Marketplace é um serviço OData; ele pode ser chamado por meio de métodos POST ou GET.

Há várias maneiras de consumir o serviço de forma automática (os aplicativos de exemplo estão [aqui](http://microsoftazuremachinelearning.azurewebsites.net/StlEtsForecasting.aspx)).

###Iniciando o código C# para consumo de serviço Web:

	public class Input
	{
	        public string frequency;
	        public string horizon;
	        public string date;
	        public string value;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };         var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}


##Criação de serviço Web 

>Este serviço Web foi criado usando o Aprendizado de Máquina do Azure. Para obter uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de testes e [publicação de serviços Web](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). Abaixo está uma captura de tela do teste que criou o serviço Web e o exemplo de código para cada um dos módulos dentro do teste.

De dentro do Aprendizado de Máquina do Azure, um novo teste em branco foi criado. Os dados de entrada de amostra foram carregados com um esquema de dados predefinido. Há um módulo [Executar Script R][execute-r-script] vinculado ao esquema de dados, que gera modelos de previsão STL e ETS usando as funções ‘stl’, ‘ets’ e ‘forecast’ em R.

###Fluxo de teste:

![Fluxo de teste][2]

####Módulo 1:
 
	# Add in the CSV file with the data in the format shown below 
![Dados de amostra][3]

####Módulo 2:

	# Data input
	data <- maml.mapInputPort(1) # class: data.frame
	library(forecast)
	
	# Preprocessing
	colnames(data) <- c("frequency", "horizon", "dates", "values")
	dates <- strsplit(data$dates, ";")[[1]]
	values <- strsplit(data$values, ";")[[1]]
	
	dates <- as.Date(dates, format = '%m/%d/%Y')
	values <- as.numeric(values)
	
	# Fit a time series model
	train_ts<- ts(values, frequency=data$frequency)
	fit1 <- stl(train_ts,  s.window="periodic")
	train_model <- forecast(fit1, h = data$horizon, method = 'ets')
	plot(train_model)
	
	# Produce forcasting
	train_pred <- round(train_model$mean,2)
	data.forecast <- as.data.frame(t(train_pred))
	colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")
	
	# Data output
	maml.mapOutputPort("data.forecast");

##Limitações 

Este é um exemplo muito simples para a previsão ETS + STL. Como se pode ver no código de exemplo acima, nenhuma captura de erro é implementada e o serviço presume que todas as variáveis são valores contínuos/positivos e a frequência deve ser um inteiro maior que 1. O comprimento dos vetores de data e valor deve ser o mesmo, e o comprimento da série temporal deve ser maior que 2 * frequência. A variável de data deve seguir o formato 'mm/dd/aaaa'.

##Perguntas frequentes
Para obter as perguntas frequentes sobre o consumo do serviço Web ou a publicação no Azure Marketplace, consulte [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img1.png
[2]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img2.png
[3]: ./media/machine-learning-r-csharp-retail-demand-forecasting/retail-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0914_2016-->