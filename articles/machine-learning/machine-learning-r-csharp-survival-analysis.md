<properties 
	pageTitle="Análise de sobrevivência com o Aprendizado de Máquina do Azure | Microsoft Azure" 
	description="Probabilidade de ocorrência de evento de análise de sobrevivência" 
	services="machine-learning" 
	documentationCenter="" 
	authors="zhangya" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/01/2015" 
	ms.author="zhangya"/>


#Análise de sobrevivência 

Em muitos cenários, o resultado principal em avaliação é o momento de um evento de interesse. Em outras palavras, a pergunta "quando esse evento ocorrerá?" é feita. Como exemplo, considere situações em que os dados descrevem o tempo decorrido (dias, anos, quilometragem, etc.) até o evento de interesse (recidiva da doença, recebimento de grau PhD, falha nas pastilhas de freio) ocorrer. Cada instância nos dados representa um objeto específico (um paciente, uma estudante, um carro, etc.).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Esse [serviço Web](https://datamarket.azure.com/dataset/aml_labs/survivalanalysis) responde à pergunta "qual é a probabilidade de o evento de interesse ocorrer no tempo n para o objeto x?" Ao fornecer um modelo de análise de sobrevivência, esse serviço Web permite aos usuários fornecer dados para treinar o modelo e testá-lo. O tema principal do teste é modelar a extensão do tempo decorrido até que ocorra o evento de interesse.

>Este serviço Web poderia ser consumido por usuários – potencialmente por meio de um aplicativo móvel, de um site ou até mesmo em um computador local, por exemplo. Mas a finalidade do serviço Web é também servir como um exemplo de como o Aprendizado de Máquina do Azure pode ser usado para criar serviços Web sobre o código R. Com apenas algumas linhas de código R e cliques de botão dentro do Estúdio de Aprendizado de Máquina do Azure, um experimento pode ser criado com código R e publicado como um serviço Web. O serviço Web pode ser publicado no Azure Marketplace e consumido por dispositivos e usuários em todo o mundo – sem qualquer infraestrutura configurada pelo autor do serviço Web.

##Consumo do serviço Web

O esquema de dados de entrada do serviço Web é mostrado na tabela a seguir. São necessárias seis informações como entrada: dados de treinamento, dados de teste, tempo de interesse, o índice da dimensão de "tempo", o índice da dimensão de "evento" e os tipos de variáveis (contínua ou fator). Os dados de treinamento são representados por uma sequência, em que as linhas são separadas por vírgulas e as colunas são separadas por ponto e vírgula. O número de recursos de dados é flexível. Todos os elementos na cadeia de caracteres de entrada devem ser numéricos. Nos dados de treinamento, a dimensão de "tempo" indica o número de unidades de tempo (dias, anos, quilometragem, etc.) decorridas desde o ponto de partida do estudo (o recebimento de programas de tratamento de drogas pelo paciente, o início do doutorado de um aluno, o início do uso de um carro, etc.) até o evento de interesse (o paciente voltar a usar drogas, o aluno obter o grau de PhD, as pastilhas do freio do carro falharem, etc.) ocorrer. A dimensão de "evento" indica se o evento de interesse ocorre no final do estudo. Um valor de "evento=1" significa que o evento de interesse ocorre no período indicado pela dimensão de "tempo"; "evento=0" significa que o evento de interesse não ocorreu até o momento indicado pela dimensão de "tempo".

- trainingdata - uma cadeia de caracteres. As linhas são separadas por vírgulas; as colunas são separadas por ponto e vírgula. Cada linha inclui a dimensão de "tempo", a dimensão de "evento" e as variáveis do preditor.
- testingdata - Uma linha de dados que contém variáveis de previsão para um determinado objeto.
- time\_of\_interest - O tempo de interesse decorrido n.
- index\_time - O índice da coluna da dimensão de "tempo" (a partir de 1).
- index\_event - O índice da coluna da dimensão de "evento" (a partir de 1).
- variable\_types - Uma cadeia de caracteres com ponto e vírgula como separador. 0 representa variáveis contínuas e 1 representa variáveis de fator.


A saída é a probabilidade de um evento ocorrer em um determinado tempo.

>Esse serviço, conforme hospedado no Azure Marketplace é um serviço OData; ele pode ser chamado por meio de métodos POST ou GET.

Há várias maneiras de consumir o serviço de forma automática (os aplicativos de exemplo estão [aqui](http://microsoftazuremachinelearning.azurewebsites.net/SurvivalAnalysis.aspx)).

###Iniciando o código C# para consumo de serviço Web:
	public class Input
	{
	        public string trainingdata;
	        public string testingdata;
	        public string timeofinterest;
	        public string indextime;
	        public string indexevent;
	        public string variabletypes;
	}

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
	        byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
	        return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
	}
	
	void Main()
	{
	        var input = new Input() { trainingdata = TextBox1.Text, testingdata = TextBox2.Text, timeofinterest = TextBox3.Text, indextime = TextBox4.Text, indexevent = TextBox5.Text, variabletypes = TextBox6.Text };
	        var json = JsonConvert.SerializeObject(input);
	        var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
	        var httpClient = new HttpClient();
	
	        httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
	        httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
	
	        var response = httpClient.PostAsync(acitionUri, new StringContent(json));
	        var result = response.Result.Content;
		    var scoreResult = result.ReadAsStringAsync().Result;
	}




A interpretação desse teste é a seguinte: Supondo que o objetivo dos dados seja modelar o tempo decorrido até o retorno para o uso de drogas para os pacientes que receberam um dos dois programas de tratamento. O resultado das leituras do serviço Web: para pacientes com 35 anos, dois tratamentos anteriores para drogas, recebendo o programa de tratamento residencial longo e com o uso de heroína e cocaína, a probabilidade de voltar a usar drogas é de 95,64% até o dia 500.

##Criação de serviço Web

>Este serviço Web foi criado usando o Aprendizado de Máquina do Azure. Para obter uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de testes e [publicação de serviços Web](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). Abaixo está uma captura de tela do teste que criou o serviço Web e o exemplo de código para cada um dos módulos dentro do teste.

De dentro do Aprendizado de Máquina do Azure, um novo teste em branco foi criado e dois módulos [Executar Scripts R][execute-r-script] foram levados ao espaço de trabalho. O esquema de dados foi criado com um [Executar Script R][execute-r-script] simples, que define o esquema de dados de entrada para o serviço Web. Esse módulo é, então, vinculado ao segundo módulo [Executar Script R][execute-r-script], que faz a maior parte do trabalho. Esse módulo faz o pré-processamento de dados, a criação de modelo e as previsões. Na etapa de pré-processamento de dados, os dados de entrada representados por uma cadeia de caracteres longa são transformados e convertidos em uma estrutura de dados. Na etapa de construção do modelo, um pacote R externo "survival\_2.37-7.zip" é instalado para realizar a análise de sobrevivência. Em seguida, a função de "coxph" é executada após uma série de tarefas de processamento de dados. Os detalhes da função "coxph" para a análise de sobrevivência podem ser lidos na documentação de R. Na etapa de previsão, uma instância de teste é fornecida para o modelo treinado com a função "surfit" e a curva de sobrevivência para esta instância de teste é produzida como a variável "curva". Por fim, a probabilidade do tempo de interesse é obtida.

###Fluxo de teste:

![fluxo de teste][1]

####Módulo 1:

    #Data schema with example data (replaced with data from web service)
    trainingdata="53;1;29;0;0;3,79;1;34;0;1;2,45;1;27;0;1;1,37;1;24;0;1;1,122;1;30;0;1;1,655;0;41;0;0;1,166;1;30;0;0;3,227;1;29;0;0;3,805;0;30;0;0;1,104;1;24;0;0;1,90;1;32;0;0;1,373;1;26;0;0;1,70;1;36;0;0;1”
    testingdata="35;2;1;1"
    time_of_interest="500"
    index_time="1"
    index_event="2"
    
    # 0 - continuous; 1 -  factor
    variable_types="0;0;1;1"

    sampleInput=data.frame(trainingdata,testingdata,time_of_interest,index_time,index_event,variable_types)

    maml.mapOutputPort("sampleInput"); #send data to output port
	
####Módulo 2:

    #Read data from input port
    data <- maml.mapInputPort(1) 
    colnames(data) <- c("trainingdata","testingdata","time_of_interest","index_time","index_event","variable_types")

    # Preprocessing training data
    traindingdata=data$trainingdata
    y=strsplit(as.character(data$trainingdata),",")
    n_row=length(unlist(y))
    z=sapply(unlist(y), strsplit, ";", simplify = TRUE)
    mydata <- data.frame(matrix(unlist(z), nrow=n_row, byrow=T), stringsAsFactors=FALSE)
    n_col=ncol(mydata)

    # Preprocessing testing data
    testingdata=as.character(data$testingdata)
    testingdata=unlist(strsplit(testingdata,";"))

    # Preprocessing other input parameters
    time_of_interest=data$time_of_interest
    time_of_interest=as.numeric(as.character(time_of_interest))
    index_time = data$index_time
    index_event = data$index_event
    variable_types = data$variable_types

    # Necessary R packages
    install.packages("src/packages_survival/survival_2.37-7.zip",lib=".",repos=NULL,verbose=TRUE)
    library(survival)

    # Prepare to build model
    attach(mydata)

    for (i in 1:n_col){ mydata[,i]=as.numeric(mydata[,i])} 
    d_time=paste("X",index_time,sep = "")
    d_event=paste("X",index_event,sep = "")
    v_time_event <- c(d_time,d_event)
    v_predictors = names(mydata)[!(names(mydata) %in% v_time_event)]

    variable_types = unlist(strsplit(as.character(variable_types),";"))

    len = length(v_predictors)
    c="" # Construct the execution string
    for (i in 1:len){
    if(i==len){
    if(variable_types[i]!=0){ c=paste(c, "factor(",v_predictors[i],")",sep="")}
     else{ c=paste(c, v_predictors[i])}
    }else{
    if(variable_types[i]!=0){c=paste(c, "factor(",v_predictors[i],") + ",sep="")}
    else{c=paste(c, v_predictors[i],"+")}
    }
    }
    f=paste("coxph(Surv(",d_time,",",d_event,") ~")
    f=paste(f,c)
    f=paste(f,", data=mydata )")

    # Fit a Cox proportional hazards model and get the predicted survival curve for a testing instance 
    fit=eval(parse(text=f))

    testingdata = as.data.frame(matrix(testingdata, ncol=len,byrow = TRUE),stringsAsFactors=FALSE)
    names(testingdata)=v_predictors
    for (i in 1:len){ testingdata[,i]=as.numeric(testingdata[,i])}

    curve=survfit(fit,testingdata)

    # Based on user input, find the event occurrence probability
    position_closest=which.min(abs(prob_event$time - time_of_interest))

    if(prob_event[position_closest,"time"]==time_of_interest){# exact match
    output=prob_event[position_closest,"prob"]
    }else{# not exact match
    if(time_of_interest>max(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else if(time_of_interest<min(prob_event$time)){
    output=prob_event[position_closest,"prob"]
    }else{output=(prob_event[position_closest,"prob"]+prob_event[position_closest+1,"prob"])/2}
    }

    #Pull out results to send to web service
    output=paste(round(100*output, 2), "%") 
    maml.mapOutputPort("output"); #output port




##Limitações

Este serviço Web aceita apenas valores numéricos como variáveis de recurso (colunas). A coluna "evento" pode ter apenas o valor 0 ou 1. A coluna "hora" precisa ser um inteiro positivo.

##Perguntas frequentes
Para obter as perguntas frequentes sobre o consumo do serviço Web ou a publicação no Azure Marketplace, consulte [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-survival-analysis/survive_img2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
 

<!---HONumber=AcomDC_1203_2015-->