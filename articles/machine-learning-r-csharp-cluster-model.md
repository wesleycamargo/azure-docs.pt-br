<properties 
	pageTitle="Modelo de cluster | Azure" 
	description="Modelo de cluster" 
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
	ms.date="02/12/2015" 
	ms.author="jaymathe"/> 


#Modelo de cluster    



Como podemos prever grupos de titulares de cartão de crédito para reduzir o risco de cancelamento de uma dívida para os emissores de cartão de crédito?  Como podemos definir grupos de traços de personalidade de funcionários para melhorar seu desempenho no trabalho? Como os médicos podem classificar pacientes em grupos com base em características de seus doenças? Em princípio, todas essas perguntas podem ser respondidas por meio da análise de cluster.    
   
Na prática, a análise de cluster classifica um conjunto de observações em dois ou mais grupos desconhecidos que se excluem mutuamente com base nas combinações de variáveis. O objetivo da análise de cluster é descobrir um sistema de organizar observações, geralmente as pessoas ou suas características, em grupos, em que os membros dos grupos compartilham de propriedades em comum. Esse [serviço](https://datamarket.azure.com/dataset/aml_labs/k_cluster_model) usa a metodologia de K-Means, uma técnica de clustering comumente usada, para agrupar dados arbitrários em grupos. Esse serviço Web usa os dados e o número de clusters k como entrada e produz previsões de a quais dos grupos k cada observação pertence. 

>Embora esses serviços Web possam ser consumidos pelos usuários - potencialmente através de um aplicativo móvel, site ou mesmo um computador local, por exemplo, a finalidade do serviço Web também é servir como um exemplo de como o AM do Azure pode ser usado para criar serviços Web sobre código R. Com apenas algumas linhas de código R e cliques de botão dentro do Estúdio AM do Azure, um experimento pode ser criado com código R e publicado como um serviço Web. O serviço Web pode ser publicado no Azure Marketplace e consumido por dispositivos e usuários em todo o mundo - sem qualquer infraestrutura configurada pelo autor do serviço Web.  

#Consumo do serviço Web   
Esse serviço Web agrupa os dados em um conjunto de grupos k e gera a atribuição de grupo para cada linha. O serviço Web espera que o usuário final insira seus dados como cadeia de caracteres em que linhas são separadas por vírgulas (,) e as colunas são separadas por ponto e vírgula (;). O serviço Web espera 1 linha por vez. Um conjunto de dados de exemplo poderia ser assim:

![Sample data][1]

Suponha que o usuário desejasse separar esses dados em três grupos que se excluam mutuamente. Os dados de entrada para o conjunto de dados acima seria o seguinte: valor = "10;5;2,18;1;6,7;5;5,22;3;4,12;2;1,10;3;4"; k="3". A saída é a associação a um grupo prevista para cada uma das linhas.

>Esse serviço conforme hospedado no Microsoft Azure Marketplace é um serviço OData; ele pode ser chamado por meio de métodos POST ou GET. 

Há várias maneiras de consumir o serviço de forma automática (aplicativos de exemplo estão [aqui](http://microsoftazuremachinelearning.azurewebsites.net/ClusterModel.aspx )).

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
>Este serviço Web foi criado usando o Azure ML. Para uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de experiências e [publicação de serviços Web,](http://azure.microsoft.com/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consulte [azure.com/ml](http://azure.com/ml). Abaixo está uma captura de tela do teste que criou o serviço Web e o exemplo de código para cada um dos módulos dentro do teste.

De dentro de AM do Azure, um novo teste em branco foi criado e dois "Scripts R de Executar" foram levados ao espaço de trabalho. O esquema de dados foi criado com um "Script R de Executa" simples; em seguida, o esquema de dados foi vinculado à seção de modelo de cluster, novamente criada com um "Script R de Executar". No "Script R de Executar" usado para o modelo de cluster, o serviço Web então utiliza a função "k-means", que é predefinida no "Script R de Executar" do AM do Azure.    
   

     
![Experiment flow][3]

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

	# K-Means Cluster Analysis
	fit <- kmeans(data.split, mydata$k) # k-cluster solution

	# get cluster means 
	aggregate(data.split,by=list(fit$cluster),FUN=mean)
	# append cluster assignment
	mydatafinal <- data.frame(t(fit$cluster))
	n_col=ncol(mydatafinal)
	colnames(mydatafinal) <- paste("V",1:n_col,sep="")

	# Select data.frame to be sent to the output Dataset port
	maml.mapOutputPort("mydatafinal");
   
 
#Limitações
Este é um exemplo muito simples de um serviço Web de clustering. Como pode ser visto do código de exemplo acima, nenhuma captura de erro é implementada e o serviço pressupõe que tudo é uma variável contínua (nenhum recurso categórico é permitido), uma vez que o serviço apenas produz valores números no momento da criação do serviço Web. Além disso, o serviço atualmente lida com um tamanho de dados limitado, devido à natureza de solicitação/resposta da chamada de serviço Web e ao fato de que o modelo é ajustado sempre que o serviço Web é chamado. 

##Perguntas frequentes
Para perguntas frequentes sobre o consumo do serviço Web ou a publicação no marketplace, consulte [aqui](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-cluster-model/cluster-img1.png
[2]: ./media/machine-learning-r-csharp-cluster-model/cluster-img2.png
[3]: ./media/machine-learning-r-csharp-cluster-model/cluster-img3.png

<!--HONumber=46--> 
