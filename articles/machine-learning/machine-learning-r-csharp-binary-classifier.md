<properties 
	pageTitle="Classificação binária | Microsoft Azure" 
	description="Classificador binário" 
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
	ms.date="05/03/2016" 
	ms.author="jaymathe"/>



#Classificador binário

Suponha que você tenha um conjunto de dados e gostaria de prever uma variável dependente binária com base em variáveis independentes. "Regressão logística" é uma técnica estatística popular usada para essas previsões. Aqui, a variável dependente é binária ou dicotômica, e p é a probabilidade de presença da característica de interesse.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Um cenário simples poderia ser um pesquisador tentar prever se um aluno em potencial provavelmente aceitará uma oferta de entrada de uma universidade com base em informações (GPA no ensino médio, renda familiar, estado de residência, sexo). O resultado previsto é a probabilidade do aluno em potencial aceitar sua oferta de admissão. Esse [serviço Web](https://datamarket.azure.com/dataset/aml_labs/log_regression) se ajusta ao modelo de regressão logística para os dados e gera o valor de probabilidade (y) para cada uma das observações nos dados.
  
>Este serviço Web poderia ser consumido por usuários – potencialmente por meio de um aplicativo móvel, de um site ou até mesmo em um computador local, por exemplo. Mas a finalidade do serviço Web é também servir como um exemplo de como o Aprendizado de Máquina do Azure pode ser usado para criar serviços Web sobre o código R. Com apenas algumas linhas de código R e cliques de botão dentro do Estúdio de Aprendizado de Máquina do Azure, um experimento pode ser criado com código R e publicado como um serviço Web. O serviço Web pode ser publicado no Azure Marketplace e consumido por dispositivos e usuários em todo o mundo – sem qualquer infraestrutura configurada pelo autor do serviço Web.
  

##Consumo do serviço Web  
O serviço Web dá os valores previstos da variável dependente com base em variáveis independentes para todas as observações. O serviço Web espera que o usuário final insira seus dados como cadeias de caracteres em que as linhas são separadas por vírgula (,) e as colunas são separadas por ponto e vírgula (;). O serviço Web espera 1 linha por vez e espera que a primeira coluna seja a variável dependente. Um conjunto de dados de exemplo poderia ser assim:

![Dados de amostra][1]

Observações sem uma variável dependente devem ser inseridas como "NA" para y. Os dados de entrada para o conjunto de dados acima seria o seguinte: “1;5;2,1;1;6,0;5.3;2.1,0;5;5,0;3;4,1;2;1,NA;3;4”. O resultado é o valor previsto para cada uma das linhas com base nas variáveis independentes.

>Esse serviço, conforme hospedado no Azure Marketplace é um serviço OData; ele pode ser chamado por meio de métodos POST ou GET.

Há várias maneiras de consumir o serviço de forma automática (os aplicativos de exemplo estão [aqui](http://microsoftazuremachinelearning.azurewebsites.net/BinaryClassifier.aspx)).

###Iniciando o código C# para consumo de serviço Web:

	public class Input
	{
   		public string value;
	}

	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
		byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
		return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
		var input = new Input() { value = TextBox1.Text };
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
>Este serviço Web foi criado usando o Aprendizado de Máquina do Azure. Para obter uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de testes e [publicação de serviços Web](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). Abaixo está uma captura de tela do teste que criou o serviço Web e o exemplo de código para cada um dos módulos dentro do teste.

De dentro do Aprendizado de Máquina do Azure, um novo teste em branco foi criado e dois módulos [Executar Script R][execute-r-script] foram levados ao espaço de trabalho. Esse serviço Web executa um teste de Aprendizado de Máquina do Azure com script R subjacente. Há 2 partes para esse experimento: a definição de esquema e o modelo de treinamento + pontuação. O primeiro módulo define a estrutura esperada do conjunto de entrada, em que a primeira variável é a variável dependente e as variáveis restantes são independentes. O segundo módulo se encaixa em um modelo de regressão logística genérico para os dados de entrada.

![Fluxo de teste][2]

####Módulo 1:

	#Schema definition  
	data <- data.frame(value = "1;2;3,1;5;6,0;8;9", stringsAsFactors=FALSE) 
	maml.mapOutputPort("data");  

####Módulo 2:
	#GLM modeling   
	data <- maml.mapInputPort(1) # class: data.frame  
	
	data.split <- strsplit(data[1,1], ",")[[1]] 
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) 
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE) 
	data.split <- as.data.frame(t(data.split)) data.split <- 
	data.matrix(data.split) 
	data.split <- data.frame(data.split) 
	
	model <- glm(data.split$V1 ~., family='binomial', data=data.split)  
	out <- data.frame(predict(model,data.split, type="response")) 
	pred1 <- as.data.frame(out) 
	group <- array(1:nrow(pred1)) 
	for (i in 1:nrow(pred1))  
		{
		if(as.numeric(pred1[i,])>0.5) {group[i]=1} 
		else {group[i]=0}
		} 
	pred2 <- as.data.frame(group) 
	maml.mapOutputPort("pred2");  


##Limitações
Este é um exemplo muito simples de um serviço Web de classificação binária. Como pode ser visto no código de exemplo acima, nenhuma captura de erro é implementada e o serviço pressupõe que tudo é uma variável binária/contínua (nenhum recurso categórico é permitido), uma vez que o serviço produz apenas valores numéricos no momento da criação do serviço Web. Além disso, atualmente o serviço lida com um tamanho de dados limitado, devido à natureza da solicitação/resposta da chamada do serviço Web e ao fato de que o modelo é ajustado sempre que o serviço Web é chamado.

##Perguntas frequentes
Para obter as perguntas frequentes sobre o consumo do serviço Web ou a publicação no Azure Marketplace, consulte [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-binary-classifier/binary1.png
[2]: ./media/machine-learning-r-csharp-binary-classifier/binary2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0504_2016-->