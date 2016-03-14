<properties 
	pageTitle="Pacote de serviço Web de distribuição normal | Microsoft Azure" 
	description="Pacote de serviço Web de distribuição normal" 
	services="machine-learning" 
	documentationCenter="" 
	authors="ireiter" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2016" 
	ms.author="ireiter"/>

#Pacote de distribuição normal



O Pacote de Distribuição Normal é um conjunto de serviços Web de exemplo ([Gerador](https://datamarket.azure.com/dataset/aml_labs/ndg7), [Calculadora de Quantil](https://datamarket.azure.com/dataset/aml_labs/ndq5), [Calculadora de Probabilidade](https://datamarket.azure.com/dataset/aml_labs/ndp5)) que ajudam gerando e manipulando distribuições normais. Os serviços permitem gerar uma sequência de distribuição normal de qualquer tamanho, calcular quantidades por meio de uma determinada probabilidade e calcular probabilidade por meio de um determinado quantil. Cada um dos serviços emite saídas diferentes com base no serviço selecionado (consulte a descrição abaixo). O Pacote de Distribuição Normal baseia-se em funções R qnorm, rnorm e pnorm incluídas no pacote de estatísticas R.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

>Este serviço Web poderia ser consumido por usuários – potencialmente por meio de um aplicativo móvel, de um site ou até mesmo em um computador local, por exemplo. Mas a finalidade do serviço Web é também servir como um exemplo de como o Aprendizado de Máquina do Azure pode ser usado para criar serviços Web sobre o código R. Com apenas algumas linhas de código R e cliques de botão dentro do Estúdio de Aprendizado de Máquina do Azure, um experimento pode ser criado com código R e publicado como um serviço Web. O serviço Web pode ser publicado no Azure Marketplace e consumido por dispositivos e usuários em todo o mundo – sem qualquer infraestrutura configurada pelo autor do serviço Web.
 

##Consumo do serviço Web
O Pacote de Distribuição Normal inclui os três serviços a seguir.

###Calculadora de quantil de distribuição normal
Esse serviço aceita quatro argumentos de uma distribuição normal e calcula o quantil associado.

Os argumentos de entrada são:

* p - uma única probabilidade de um evento com distribuição normal. 
* Média – a média de distribuição normal.
* SD – o desvio padrão da distribuição normal. 
* Lado – L para o lado inferior da distribuição e U para o lado superior da distribuição.

A saída do serviço é o quantil calculado associado à probabilidade determinada.

###Calculadora de probabilidade de distribuição normal
Esse serviço aceita quatro argumentos de uma distribuição normal e calcula a probabilidade associada.

Os argumentos de entrada são:

* q - um único quantil de um evento com distribuição normal. 
* Média – a média de distribuição normal.
* SD – o desvio padrão da distribuição normal. 
* Lado – L para o lado inferior da distribuição e U para o lado superior da distribuição.

A saída do serviço é a probabilidade calculada associada ao quantil determinado.

###Gerador de distribuição normal
Este serviço aceita três argumentos de uma distribuição normal e gera uma sequência aleatória de números distribuídos normalmente. Os argumentos a seguir devem ser fornecidos a ele na solicitação:

* n - o número de observações. 
* Média – a média de distribuição normal.
* sd – o desvio padrão da distribuição normal. 

A saída do serviço é uma sequência de tamanho n com uma distribuição normal com base na média e em argumentos sd.

>Esse serviço, conforme hospedado no Azure Marketplace é um serviço OData; ele pode ser chamado por meio de métodos POST ou GET.

Há várias maneiras de consumir o serviço de forma automática (os aplicativos de exemplo são: [Gerador](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionGenerator.aspx), [Calculadora de probabilidade](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionProbabilityCalculator.aspx), [Calculadora de quantil](http://microsoftazuremachinelearning.azurewebsites.net/NormalDistributionQuantileCalculator.aspx)).

###Iniciando o código C# para consumo de serviço Web:

###Calculadora de quantil de distribuição normal
	public class Input
	{
	        public string p;
	        public string mean;
	        public string sd;
	        public string side;
	}
	
	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { p = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
	    	var scoreResult = result.ReadAsStringAsync().Result;
	}


###Calculadora de probabilidade de distribuição normal
	public class Input
	{
	        public string q;
	        public string mean;
	        public string sd;
	        public string side;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { q = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text, side = TextBox4.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}

###Gerador de distribuição normal
	public class Input
	{
	        public string n;
	        public string mean;
	        public string sd;
	}
	
    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { n = TextBox1.Text, mean = TextBox2.Text, sd = TextBox3.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}


##Criação de serviço Web 
>Este serviço Web foi criado usando o Aprendizado de Máquina do Azure. Para obter uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de testes e [publicação de serviços Web](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml).

Abaixo está uma captura de tela do teste que criou o serviço Web e o exemplo de código para cada um dos módulos dentro do teste.

###Calculadora de quantil de distribuição normal

Fluxo de teste:

![Fluxo de teste][2]
 
	#Data schema with example data (replaced with data from web service)
	data.set=data.frame(p=0.1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	if (param$p < 0 ) {
	print('Bad input: p must be between 0 and 1')
	param$p = 0
	} else if (param$p > 1) {
	print('Bad input: p must be between 0 and 1')
	param$p = 1
	}
	q = qnorm(param$p,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	q = 2* param$mean - q
	} else if (param$side =='L') {
	q = q
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(q)
	
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###Calculadora de probabilidade de distribuição normal
Fluxo de teste:

![Fluxo de teste][3]
 
 	#Data schema with example data (replaced with data from web service)
	data.set=data.frame(q=-1,mean=0,sd=1,side='L');
	maml.mapOutputPort("data.set"); #send data to output port
	
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	prob = pnorm(param$q,mean=param$mean,sd=param$sd)

	if (param$side == 'U'){
	prob = 1 - prob
	} else if (param$side =='B') {
	prob = ifelse(prob<=0.5,prob * 2, 1)
	} else if (param$side =='L') {
	prob = prob
	} else {
	print("Invalid side choice")
	}

	output = as.data.frame(prob)
	
	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");
	
###Gerador de distribuição normal
Fluxo de teste:

![Fluxo de teste][4]

	#Data schema with example data (replaced with data from web service)
	data.set=data.frame(n=50,mean=0,sd=1);
	maml.mapOutputPort("data.set"); #send data to output port
	
	# Map 1-based optional input ports to variables
	dataset1 <- maml.mapInputPort(1) # class: data.frame

	param = dataset1
	dist = rnorm(param$n,mean=param$mean,sd=param$sd)

	output = as.data.frame(t(dist))

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("output");

##Limitações 

Estes são exemplos muito simples sobre a Distribuição Normal. Como se pode ver no exemplo de código acima, pouca captura de erro é implantada.

##Perguntas frequentes
Para obter as perguntas frequentes sobre o consumo do serviço Web ou a publicação no Azure Marketplace, consulte [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-normal-distribution/normal-img1.png
[2]: ./media/machine-learning-r-csharp-normal-distribution/normal-img2.png
[3]: ./media/machine-learning-r-csharp-normal-distribution/normal-img3.png
[4]: ./media/machine-learning-r-csharp-normal-distribution/normal-img4.png
 

<!---HONumber=AcomDC_0302_2016-->