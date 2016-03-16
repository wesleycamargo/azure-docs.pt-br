<properties 
	pageTitle="Modelo de cluster | Microsoft Azure" 
	description="Modelo de cluster" 
	services="machine-learning" 
	documentationCenter="" 
	authors="FrancescaLazzeri" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/25/2016" 
	ms.author="lazzeri"/>


#Modelo de cluster    

Como podemos prever grupos de titulares de cartão de crédito para reduzir o risco de cancelamento de uma dívida para os emissores de cartão de crédito? Como podemos definir grupos de traços de personalidade de funcionários para melhorar seu desempenho no trabalho? Como os médicos podem classificar pacientes em grupos com base nas características de suas doenças? A princípio, todas essas perguntas podem ser respondidas por meio da análise de cluster.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
   
A análise de cluster classifica um conjunto de observações em dois ou mais grupos desconhecidos que se excluem mutuamente com base nas combinações de variáveis. O objetivo da análise de cluster é descobrir um sistema de organizar observações, geralmente as pessoas ou suas características, em grupos, em que os membros dos grupos compartilham de propriedades em comum. Esse [serviço](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) usa a metodologia de K-Means, uma técnica de clustering comumente usada, para agrupar dados arbitrários em grupos. Esse serviço Web usa os dados e o número de clusters k como entrada e produz previsões de a quais dos grupos k cada observação pertence.

>Este serviço Web poderia ser consumido por usuários – potencialmente por meio de um aplicativo móvel, de um site ou até mesmo em um computador local, por exemplo. Mas a finalidade do serviço Web é também servir como um exemplo de como o Aprendizado de Máquina do Azure pode ser usado para criar serviços Web sobre o código R. Com apenas algumas linhas de código R e cliques de botão dentro do Estúdio de Aprendizado de Máquina do Azure, um experimento pode ser criado com código R e publicado como um serviço Web. O serviço Web pode ser publicado no Azure Marketplace e consumido por dispositivos e usuários em todo o mundo – sem qualquer infraestrutura configurada pelo autor do serviço Web.

##Consumo do serviço Web   
Esse serviço Web agrupa os dados em um conjunto de grupos k e gera a atribuição de grupo para cada linha. O serviço Web espera que o usuário final insira seus dados como cadeias de caracteres em que as linhas são separadas por vírgula (,) e as colunas são separadas por ponto e vírgula (;). O serviço Web espera 1 linha por vez. Um conjunto de dados de exemplo poderia ser assim:

![Dados de amostra][1]

Suponha que o usuário desejasse separar esses dados em três grupos que se excluam mutuamente. Os dados de entrada para o conjunto de dados acima seria o seguinte: value = “10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4”; k=”3”. A saída é a associação a um grupo prevista para cada uma das linhas.

>Esse serviço, conforme hospedado no Azure Marketplace é um serviço OData; ele pode ser chamado por meio de métodos POST ou GET.

Há várias maneiras de consumir o serviço de forma automática (os aplicativos de exemplo estão [aqui](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx)).

###Iniciando o código C# para consumo de serviço Web:

	public class Input
	{
	        public string value;
	        public string k;
	}
	
	public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
	{
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { value = TextBox1.Text, k = TextBox2.Text };
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

De dentro do Aprendizado de Máquina do Azure, um novo teste em branco foi criado e dois módulos [Executar Script R][execute-r-script] foram levados ao espaço de trabalho. O esquema de dados foi criado com um simples [Executar Script R][execute-r-script]. Em seguida, o esquema de dados foi vinculado à seção de modelo de cluster, criada novamente com um [Executar Script R][execute-r-script]. No [Executar Script R][execute-r-script] usado para o modelo de cluster, o serviço Web utiliza a função "k-means", que é predefinida no [Executar Script R][execute-r-script] do Aprendizado de Máquina do Azure.
   

     
![Fluxo de teste][3]

####Módulo 1: 
	#Enter the input data as a string 
	mydata <- data.frame(value = "1; 3; 5; 6; 7; 7, 5; 5; 6; 7; 2; 1, 3; 7; 2; 9; 56; 6, 1; 4; 5; 26; 4; 23, 15; 35; 6; 7; 12; 1, 32; 51; 62; 7; 21; 1", k=5, stringsAsFactors=FALSE)
	
	maml.mapOutputPort("mydata");     
	

####Módulo 2:
	# Map 1-based optional input ports to variables
	mydata <- maml.mapInputPort(1) # class: data.frame

	data.split <- strsplit(mydata[1,1], ",")[[1]]
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- sapply(data.split, strsplit, ";", simplify = TRUE)
	data.split <- as.data.frame(t(data.split))

	data.split <- data.matrix(data.split)
	data.split <- data.frame(data.split)

	# K-Means cluster analysis
	fit <- kmeans(data.split, mydata$k) # k-cluster solution

	# Get cluster means 
	aggregate(data.split,by=list(fit$cluster),FUN=mean)
	# Append cluster assignment
	mydatafinal <- data.frame(t(fit$cluster))
	n_col=ncol(mydatafinal)
	colnames(mydatafinal) <- paste("V",1:n_col,sep="")

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("mydatafinal");
   
 
##Limitações
Este é um exemplo muito simples de um serviço Web de clustering. Como pode ser visto no código de exemplo acima, nenhuma captura de erro é implementada e o serviço pressupõe que tudo é uma variável contínua (nenhum recurso categórico é permitido), uma vez que o serviço apenas produz valores números no momento da criação do serviço Web. Além disso, atualmente o serviço lida com um tamanho de dados limitado, devido à natureza da solicitação/resposta da chamada do serviço Web e ao fato de que o modelo é ajustado sempre que o serviço Web é chamado.

##Perguntas frequentes
Para obter as perguntas frequentes sobre o consumo do serviço Web ou a publicação no Azure Marketplace, consulte [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_0302_2016-->