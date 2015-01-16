<properties title="Multivariate Linear Regression" pageTitle="Regressão linear multivariada | Azure" description="Regressão linear multivariada" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 


#Regressão linear multivariada   
 

 
Suponha que você tenha um conjunto de dados e gostaria de prever rapidamente uma variável dependente y para cada indivíduo (i) com base em outras variáveis independentes. 
Regressão linear é uma técnica estatística popular usada para essas previsões. Aqui, a variável dependente y é considerada um valor contínuo.    

Um cenário simples poderia ser um em que o pesquisador estivesse tentando prever o peso de um indivíduo (y) com base em sua altura (x). Um cenário mais avançado poderia ser um em que o Pesquisador tivesse informações adicionais para a pessoa (como peso, sexo, raça) e tentasse prever o peso dessa pessoa. Esse [serviço Web]( https://datamarket.azure.com/dataset/aml_labs/multivariate_regression) se ajusta ao modelo de regressão linear para os dados e gera o valor previsto (y) para cada uma das observações nos dados.

>Embora esses serviços Web possam ser consumidos pelos usuários - potencialmente através de um aplicativo móvel, site ou mesmo um computador local, por exemplo, a finalidade do serviço Web também é servir como um exemplo de como o AM do Azure pode ser usado para criar serviços Web sobre código R. Com apenas algumas linhas de código R e cliques de botão dentro do Estúdio AM do Azure, um experimento pode ser criado com código R e publicado como um serviço Web. O serviço Web pode ser publicado no Azure Marketplace e consumido por dispositivos e usuários em todo o mundo - sem qualquer infraestrutura configurada pelo autor do serviço Web.  

#Consumo do serviço Web  
O serviço Web dá os valores previstos da variável dependente com base em variáveis independentes para todas as observações. O serviço Web espera que o usuário final insira seus dados como cadeia de caracteres em que linhas são separadas por vírgula (,) e as colunas são separadas por ponto e vírgula (;). O serviço Web espera 1 linha por vez e que a primeira coluna seja a variável dependente. Um conjunto de dados de exemplo poderia ser assim:

![Sample data][1]

Observações sem uma variável dependente devem ser inseridas como "NA" para y. Os dados de entrada para o conjunto de dados acima seriam a seguinte cadeia de caracteres: "10;5;2,18;1;6,6;5.3;2.1,7;5;5,22;3;4,12;2;1,NA;3;4". O resultado é o valor previsto para cada uma das linhas com base nas variáveis independentes. 

>Esse serviço conforme hospedado no Microsoft Azure Marketplace é um serviço OData; ele pode ser chamado por meio de métodos POST ou GET. 

Há várias maneiras de consumir o serviço de forma automática (aplicativos de exemplo estão [aqui](http://microsoftazuremachinelearning.azurewebsites.net/MultipleLinearRegressionService.aspx )).

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


#Criação do serviço Web  
>Este serviço Web foi criado usando o Azure ML. Para uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de experiências e [publicação de serviços Web,](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consulte [azure.com/ml](http://azure.com/ml). Abaixo está uma captura de tela do teste que criou o serviço Web e o exemplo de código para cada um dos módulos dentro do teste.


De dentro de AM do Azure, um novo teste em branco foi criado e dois "Scripts R de Executar" foram levados ao espaço de trabalho. Esse serviço Web executa um teste de aprendizado de máquina do Azure com script R subjacente.  Ou seja, existem duas partes deste teste: definição de esquema, modelo de treinamento + pontuação.  O primeiro módulo define a estrutura esperada do conjunto de entrada, em que a primeira variável é a variável dependente e as variáveis restantes são independentes. O segundo módulo se encaixa em um modelo de regressão linear genérico para os dados de entrada.  
  
![Experiment flow][3]

####Módulo 1:
 
####Definição de esquema  
	data <- data.frame(value = "1;2;3,4;5;6,7;8;9", stringsAsFactors=FALSE) maml.mapOutputPort("data");  

####Módulo 2:
####Modelagem de LM   
	data <- maml.mapInputPort(1) # class: data.frame  
  
	data.split <- strsplit(data[1,1], ",")[[1]]  
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)  
	data.split <- as.data.frame(t(data.split)) 
	data.split <- data.matrix(data.split) 
	data.split <- data.frame(data.split) 
	model <- lm(data.split)  

	out=data.frame(predict(model,data.split))  
	out <- data.frame(t(out))
	maml.mapOutputPort("out");  
 
##Limitações
Este é um exemplo muito simples de um serviço Web de regressão linear múltipla. Como pode ser visto do código de exemplo acima, nenhuma captura de erro é implementada e o serviço pressupõe que tudo é uma variável contínua (nenhum recurso categórico é permitido), uma vez que o serviço apenas produz valores números no momento da criação do serviço Web. Além disso, o serviço atualmente lida com um tamanho de dados limitado, devido à natureza de solicitação/resposta da chamada de serviço Web e ao fato de que o modelo é ajustado sempre que o serviço Web é chamado. 

##Perguntas frequentes
Para perguntas frequentes sobre o consumo do serviço Web ou a publicação no marketplace, consulte [aqui](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img1.png
[2]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img2.png
[3]: ./media/machine-learning-r-csharp-multivariate-linear-regression/multireg-img3.png
