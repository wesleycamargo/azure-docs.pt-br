<properties title="Lexicon Based Sentiment Analysis" pageTitle="Etapa 1: Análise de sentimento baseada em léxico | Azure" description="Análise de sentimento baseada em léxico" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jaymathe" /> 



#Análise de sentimento baseada em léxico 

 


Como analisar atitude e a opinião do usuário em relação a marcas ou a tópicos nas redes sociais online, como postagens no Facebook, tweets, análises, etc. A análise de sentimento fornece um método para analisar essas perguntas.

Em particular, existem dois métodos gerais para análise de sentimento, um utiliza o algoritmo de aprendizado supervisionado e o outro pode ser tratado como aprendizado sem supervisão. Algoritmo de aprendizado supervisionado geralmente cria um modelo de classificação em um grande corpus anotado e sua precisão baseia-se principalmente na qualidade da anotação e geralmente o processo de treinamento levará muito tempo. Além disso, quando aplicamos o algoritmo a outro domínio, o resultado geralmente não é válido. Comparado ao aprendizado supervisionado, léxico baseado aprendizado sem supervisão usa um dicionário sentimento, que não requer o armazenamento de um grande corpus de dados e treinamento, tornando todo o processo muito mais rápido. 

Nosso [serviço](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) é construído com base no léxico MPQA (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), que é um dos léxicos de subjetividade mais usados. Há 5097 palavras negativas e 2533 palavras positivas em MPQA. E todas essas palavras são anotadas como tendo uma polaridade forte ou fraca. Todo corpus inteiro está sob a licença pública geral GNU. O serviço Web pode ser aplicado a qualquer frase curta, como tweets, postagens de Facebook, etc. 

>Embora esses serviços Web possam ser consumidos pelos usuários - potencialmente através de um aplicativo móvel, site ou mesmo um computador local, por exemplo, a finalidade do serviço Web também é servir como um exemplo de como o AM do Azure pode ser usado para criar serviços Web sobre código R. Com apenas algumas linhas de código R e cliques de botão dentro do Estúdio AM do Azure, um experimento pode ser criado com código R e publicado como um serviço Web. O serviço Web pode ser publicado no Azure Marketplace e consumido por dispositivos e usuários em todo o mundo - sem qualquer infraestrutura configurada pelo autor do serviço Web.

##Consumo do serviço Web

Os dados de entrada podem ser qualquer texto, mas o serviço Web funciona melhor com frases curtas. A saída é um valor numérico entre -1 e 1. Qualquer valor abaixo de 0 indica que o sentimento do texto é negativo; acima de 0 indica que é positivo. O valor absoluto do resultado indica a intensidade do sentimento associado. 

>Esse serviço conforme hospedado no Microsoft Azure Marketplace é um serviço OData; ele pode ser chamado por meio de métodos POST ou GET. 

Há várias maneiras de consumir o serviço de forma automática (aplicativos de exemplo estão [aqui](http://microsoftazuremachinelearning.azurewebsites.net/)).

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


A entrada é "Hoje é um bom dia.", a saída será "1", que indica um sentimento positivo associado à frase de entrada. 

##Criação de serviço Web
>Este serviço Web foi criado usando o Azure ML. Para uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de experiências e [publicação de serviços Web,](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-publish-web-service-to-azure-marketplace/), consulte [azure.com/ml](http://azure.com/ml). Abaixo está uma captura de tela do teste que criou o serviço Web e o exemplo de código para cada um dos módulos dentro do teste.


De dentro do AM do Azure, um novo teste em branco foi criado. A figura a seguir mostra o fluxo de teste do léxico com base na análise de sentimento. O "sent_dict.csv é o léxico de subjetividade MPQA, e é definido como uma entrada do "Script R de Executar". Outra entrada é uma revisão de amostra do conjunto de dados de análise Amazon para teste, em que realizamos seleção, modificação do nome de coluna e as operações de divisão.  Usamos o pacote de hash para armazenar o léxico de subjetividade na memória e acelerar o processo de cálculo de pontuação. O texto inteiro será indexado pelo pacote "tm" e comparado com a palavra no dicionário sentimento. Por fim, uma pontuação será calculada adicionando o peso de cada palavra subjetiva no texto. 

###Fluxo de teste:

![experimenmt flow][2]


####Módulo 1:
	
	# Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame
    # install hash package
    install.packages("src/hash_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE)
    library(tm)
    library(stringr)
    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)
    #  compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
	polarity_ratio <- 0
	polarity_total <- 0
	not <- 0
	sentence <- tolower(dataset2[m,1])
	if (nchar(sentence) > 0){
		token_array <- scan_tokenizer(sentence)
		for (j in 1:length(token_array)){
			word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
		    for (k in 1:length(negation_word)){
		      if (word == negation_word[k]){
		        not <- (not+1) %% 2

			  }
		    }
			if (word != ""){
			    if (!is.null(sent_dict[[word]])){
			      polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
			      polarity_total <- polarity_total + abs(sent_dict[[word]])
			    }
			}
		  
		}
	}
	if (polarity_total > 0){
		result <- c(result, polarity_ratio/polarity_total)
	}else{
		result<- c(result,0)
	}
    }
    # Sample operation
    data.set <- data.frame(result)
    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")
	


##Limitações

Da perspectiva do algoritmo, léxico com base em análise de sentimento é uma ferramenta de análise do sentimento geral, pode não funcionar melhor que o método de classificação para campos específicos. O problema de negação não é bem processado. Nós codificamos várias palavras de negação no nosso programa, mas uma maneira melhor é usar um dicionário de negação e criar algumas regras. O serviço Web tem um melhor desempenho em frases curtas e simples, como tweets e postagens do Facebook, do que em frases longas e complexas, como a análise Amazon. 

##Perguntas frequentes
Para perguntas frequentes sobre o consumo do serviço Web ou a publicação no marketplace, consulte [aqui](http://azure.microsoft.com/pt-br/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png








