<properties 
	pageTitle="Diferença no teste de proporções | Azure" 
	description="Diferença no teste de proporções" 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="jaymathe"/> 


#Diferença no teste de proporções




Duas proporções são estatisticamente diferentes? Suponha que um usuário deseje comparar dois filmes para determinar se um filme tem uma proporção significativamente maior de 'curtidas' quando comparado a o outro. Com uma amostra grande, pode haver uma diferença estatisticamente significativa entre as proporções 0,50 e 0,51, enquanto que, com uma amostra pequena, talvez não haja dados suficientes para determinar se essas proporções são, de fato, diferentes. 

Este [serviço Web]( https://datamarket.azure.com/dataset/aml_labs/prop_test) realiza um teste de hipóteses da diferença de duas proporções com base na entrada do usuário do número de sucessos e do número total de tentativas para os dois grupos de comparação. Um cenário seria esse serviço Web ser chamado de dentro de um aplicativo de comparação de filme, informando ao usuário, com base nas classificações de filme, se um dos filmes realmente foi 'curtido' com mais frequência do que o outro.

>Embora esses serviços Web possam ser consumidos pelos usuários - potencialmente através de um aplicativo móvel, site ou mesmo um computador local, por exemplo, a finalidade do serviço Web também é servir como um exemplo de como o AM do Azure pode ser usado para criar serviços Web sobre código R. Com apenas algumas linhas de código R e cliques de botão dentro do Estúdio AM do Azure, um experimento pode ser criado com código R e publicado como um serviço Web. O serviço Web pode ser publicado no Azure Marketplace e consumido por dispositivos e usuários em todo o mundo - sem qualquer infraestrutura configurada pelo autor do serviço Web.


##Consumo do serviço Web

Esse serviço aceita quatro argumentos e forma uma hipótese de teste de proporções.

Os argumentos de entrada são:

* Successes1: número de eventos de sucesso na amostra 1
* Successes2: número de eventos de sucesso na amostra 2
* Total1: tamanho da amostra 1
* Total2: tamanho da amostra 2

A saída do serviço é o resultado do teste de hipóteses juntamente com o valor da estatística do qui-quadrado, df, valor p, proporção em 1/2 amostra e limites de intervalo de confiança.

>Esse serviço conforme hospedado no Microsoft Azure Marketplace é um serviço OData; ele pode ser chamado por meio de métodos POST ou GET. 

Há várias maneiras de consumir o serviço de forma automática (aplicativos de exemplo estão [aqui](http://microsoftazuremachinelearning.azurewebsites.net/DifferenceInProportionsTest.aspx )).

###Iniciando o código C# para consumo de serviço Web:

	public class Input{
	public double Recency;
	public double Frequency;
	public double Monetary;
	public double Time;
	public double Class;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
        System.Diagnostics.Debug.WriteLine("AuthenticationHeaderValue" + new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray)));
        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }
       
	void Main()
	{
  	var input = new Input(){Recency =1, Frequency=0,Monetary=0,Time=1, Class= 0};
	var json = JsonConvert.SerializeObject(input);
	var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
       
  	var httpClient = new HttpClient();
   	httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
   	httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
  	var query = httpClient.PostAsync(acitionUri,new StringContent(json));
  	var result = query.Result.Content;
  	var scoreResult = result.ReadAsStringAsync().Result;
  	scoreResult.Dump();
	}

##Criação de serviço Web

>Este serviço Web foi criado usando o Azure ML. Para uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de experiências e [publicação de serviços Web,](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consulte [azure.com/ml](http://azure.com/ml). Abaixo está uma captura de tela do teste que criou o serviço Web e o exemplo de código para cada um dos módulos dentro do teste.

De dentro do AM do Azure, foi criado uma novo teste em branco com dois "Scripts R de Executar". No primeiro módulo, o esquema de dados é definido, enquanto o segundo módulo usa o comando prop.test dentro do R para realizar o teste de hipóteses para duas proporções. 


###Fluxo de teste:

![Experiment flow][2]


####Módulo 1:
	####Schema definition  
	data.set=data.frame(successes1=50,successes2=60,total1=100,total2=100);
	maml.mapOutputPort("data.set"); #send data to output port
	dataset1 = maml.mapInputPort(1) #read data from input port
	

####Módulo 2:

	test=prop.test(c(dataset1$successes1[1],dataset1$successes2[1]),c(dataset1$total1[1],dataset1$total2[1])) #conduct hypothesis test

	result=data.frame(
	result=ifelse(test$p.value<0.05,"The proportions are different!",
	"The proportions aren't different statistically."),
	ChiSquarestatistic=round(test$statistic,2),df=test$parameter,
	pvalue=round(test$p.value,4),
	proportion1=round(test$estimate[1],4),
	proportion2=round(test$estimate[2],4),
	confintlow=round(test$conf.int[1],4),
	confinthigh=round(test$conf.int[2],4)) 

	maml.mapOutputPort("result"); #output port
	

##Limitações 

Este é um exemplo muito simples para teste de diferença em duas proporções. Como se pode ver no exemplo de código acima, nenhuma captura de erro é implementada e o serviço presume que todas as variáveis são contínuas.

##Perguntas frequentes
Para perguntas frequentes sobre o consumo do serviço Web ou a publicação no marketplace, consulte [aqui](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img1.png
[2]: ./media/machine-learning-r-csharp-difference-in-two-proportions/hyptest-img2.png

<!--HONumber=46--> 
 